---
layout: single
title: Migrating a Jenkins job to Serverless
categories: [software]
tags: [serverless,lambda,aws]
---

Describing some recent experiments in migrating small services from Jenkins to
AWS Lambda.

### Overview

Serverless is a computing architecture where the developer can deploy functions of
code without needing to maintain servers/infrastructure. The backend is provided
as a service.
There are many serverless platforms out there, but this post relates to AWS Lambda.
AWS Lambda is an "event-driven, serverless computing platform", often known as
FAAS, or Functions As A Service.

In an effort to decrease the cost of ops, I recently looked at migrating one of
our Jenkins jobs to AWS Lambda. Removing the job from Jenkins would mean that
we no longer needed to build and maintain the agents or AMIs associated with
that job. Moreover, the 'spin-up' time for a Jenkins agents can be 60 seconds
in this case, whereas Lambda functions (for the most part) are executed almost
immediately.

The job in question is relatively small. Each time an issue branch is merged to
master, we would like to assess all open issue branches to ensure that
1. They are not stale (not rebased against master in n days).
2. There are no merge conflicts.

If either of these conditions are not met, we usually notify the team and the
fireman will handle.

As part of migrating this over to Lambda functions, we would also like to ensure
that it runs everytime a developer pushes to a remote issue branch. This will
help to catch issues earlier.

This post will be a high-level overview of the approach taken.

### Tools Used

* Serverless framework - https://serverless.com/
* AWS
  * Lambda
  * DynamoDB
  * SNS
  * SES


### Design
The following sketch illustrates the data flow of the application. 

```
   +--------+
   |        |
   | GitHub |
   |        |
   +----+---+
        |
        |
    Receives Push
        &
 Triggers Push Event
        |
        |
+-------v---------               +------------------------------+
|  Push Webhook  |               |     Webhook Listener lambda  |
+----------------+               +------------------------------+
|                |               |                              |
|  API Gateway   | ------------->| Checks open pull requests for|
|   + Lambda     |               | staleness, or for any merge  |
|                |               | conflicts.                   |
+-------+--------+               +------------------------------+
                                                |
                                              /  \
                              if any problems     if all are clean
                                    |                      |
                          +------------------+    +-----------------+
                          |  notify if new   |    |      notify     |
                          +------------------+    +-----------------+
                          | if no existing   |    | remove any      |
                          | failure entry in |    | failure entries |
                          | dynamoDb, then   |    | from dynamoDb   |
                          | we create one,   |    | and notife the  |
                          | and email the    |    | team            |
                          | team.            |    |                 |
                          +------------------+    +-----------------+

```
When a developer pushes to the repository, Github will trigger a webhook.
The webhook makes an HTTP request to a given endpoint, in this case, our AWS API Gateway.
The request is routed to our listener, which then handles the request and
responds to the sender. Put simply, Github says 'hey someone has push
to the repository, here's the details of the push', and out listener say 'thanks
Github, we'll take it from here'.

At this point, our Lambda functions will assess the lie of the land with open
pull requests and handle accordingly.

### Implementation

I am using the [Serverless](https://serverless.com/) framework to build out the
application. Serverless gives us a nice YAML based DSL that is then used to
1. Create the Cloudformation template that defines the stack
2. Describe the resources that we need and define permissions etc

A lighter version of the actual `serverless.yml` looks something like the
following.

```yaml
service: check-pull-requests

provider:
  name: aws
  runtime: nodejs8.10
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:Query
        - dynamodb:GetItem
        - dynamodb:PutItem
        - dynamodb:DeleteItem
      Resource:
        - "YOUR RESOURCE ARN"

    - Effect: Allow
      Action:
        - SNS:Publish
      Resource:
        - "YOUR RESOURCE ARN"

functions:
  webhookListener:
    handler: handler.webhookListener
    events:
      - http:
          path: webhook
          method: post
          cors: true

resources:
  Description: Service description

  Resources:
    StatusTable:
      Type: 'AWS::DynamoDB::Table'
      Properties:
        ...
        ...

    SNSChannel:
      Type: 'AWS::SNS::Topic'
      Properties:
        ...
        ...

custom:
  snsTopicName: 'PullRequests'
```

I've removed some non-pertinent lines but the main purpose of this file is to
describe your service. We give it a name, tell it which provider we intend to use (AWS or
others), which runtime we intend to use (Node in this case), and what AWS services we require.
There's a DynamoDB table for persisting statuses and an SNS topic for notifying
the worker functions and incoming jobs. We use SES for email notification. This
could be easily swapped out for Slack.

See the Serverless documentation for more information on each.

#### Function
We define a single function which is the entry-point to the service. It expects
a HTTP post request and then invokes the named handler.

Here's a slimmed down example of `handler.js` which accepts the webhook.

```javascript
'use strict';

const webhook = require('./lib/webhook')

module.exports.webhookListener = async (event, context, callback) => {
  return webhook.verify(event.headers, event.body).then( async (body) => {

    // add the payload to a message queue for processing

    return callback(null, {
      statusCode: 200,
      body: JSON.stringify({message: 'OK'})
    });
  });
};
```
The webhook is first checked to verify its authenticity. Then we queue it up for
processing. We give the sender a `200` and we move on.

Another function is responsible for subscribing to the message queue and
handling the payload.

We then have a function that queries our repository over the Github API and asseses the
open pull requests. Results are persisted in the DynamoDB table. We're using
this as a mechanism of only notifying developers when a new problem arises, or
when an existing problem has been resolved. We definitely do not want to notify
developers after every single push the Github.

### Deploy

Deploying our service is quick and painless.

```bash
serverless deploy
```

This will deploy the entire stack, creating resources as needed. During
development however, it is quicker to only deploy the function (assuming it is
only function code that has changed). 

```bash
serverless deploy function --function webhookListener
```

### Thoughts

I've skimmed over some of the domain specific details but overall, the
Serverless framework is really enjoyable to work with. It's simple to create AWS
resources and to get services deployed. There are a host of plugins and you can
use local invocation to simulate the function in the wild.

From a developer point of view, lessening the overhead of maintaining Jenkins
agents and EC2 instances etc for small tasks such as this, meant that we could
spend more time focusing on the problem domain.

As with all these things, your mileage may vary but for ourselves, we have been
seeing good results by utilizing AWS Lambda where appropriate.
