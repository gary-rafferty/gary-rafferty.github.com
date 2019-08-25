---
layout: single
title: CloudFormation and StackMaster (part 1)
categories: [infrastructure]
tags: [aws,cloudformation,stackmaster]
---

As part of some recent infrastructure upgrades, I've been spending quite some
time in the land of CloudFormation. If you're unfamiliar with CloudFormation, it
is an AWS offering that allows you to describe and provision your infrastructure
using a common language, modelled in JSON or YAML.

When I first encountered CloudFormation a few years ago, I found it quite daunting, 
but over the years I've really warmed to it. We use it heavily in Lonely Planet
to manage some large and complex stacks.
These days we use an open source tool called StackMaster to sprinkle some
missing niceties atop it.

In this post, the first of two, I will describe CloudFormation, and in part two,
we will look at using StackMaster to make things a little more convenient.

## What is CloudFormation?

Infrastructure as code.
CloudFormation provides you with a language to describe the AWS resources
that make up your stack. Describing your AWS resources in a code format allows
you to check it into version control and to have a single source of truth. 
Changes can easily be peer reviewed, deployed, and if necessary, rolled back to
a previous point.

A stack is made up of many AWS resources. Let's look at the resources that might
make up a simple stack.

* Route 53 routes incoming request to your load balancer.
* Elastic Load Balancer directs traffic to your EC2 instances.
* Your EC2 instance hosts your application that queries your RDS database.
* Your RDS database is running the PostgreSQL engine.

To manually create this little stack from scratch, you could log into your AWS
console and use the UI for each Amazon service, or, you might opt to use the AWS
CLI. Either way, it's cumbersome and error prone, and the effort grows with your
stack.

A better way do provision this stack would be to create it using CloudFormation.

Let's look at how we might describe the above stack using a JSON CloudFormation
template.

### A starting point

```json
{
  "AWSTemplateFormatVersion" : "2010-09-09",
  "Description" : "",
  "Parameters" : {},
  "Resources" : {},
  "Outputs" : {}
}
```

This is your starting point. The JSON object contains four records. 

1. AWSTemplateFormatVersion. This defined the capabilities of the template. For
   now, the value above is the only supported value.
2. Description. Self explanatory.
3. Parameters. Values passed to the stack at runtime. (See the roll-steps below for an example)
4. Resources. AWS resources and their properties.
5. Outputs. The values returned from the stack.

The most important part are your resources, so let's look at how we might
describe the resources we mentioned above.

The format for a resource is as follows.

```json
"Resources" : {
  "Logical ID" : {
    "Type" : "Resource type",
    "Properties" : {
      Set of properties
    }
  }
}
```

The logical ID is used to identify the resource (and this can be used for
referencing in another resource). Type defines what the resource is, and the
properties are additional options to be applied to the resource. 
Here's how we might create the AWS resources for our simple stack example above.

```json
"Resources" : {
  "WebServer" : {
    "Type" : "AWS::EC2::Instance",
    "Properties" : {
      "ImageId" : "ami-123456789abcdef",
      "KeyName" : "testkey",
      "BlockDeviceMappings" : [
        {
          "DeviceName" : "/dev/sdm",
          "Ebs" : {
            "VolumeType" : "io1",
             "Iops" : "200",
             "DeleteOnTermination" : "false",
             "VolumeSize" : "20"
          }
        }, {
          "DeviceName" : "/dev/sdk",
          "NoDevice" : {}
        }
      ]
    }
  },
  "Database" : {
    "Type" : "AWS::RDS::DBInstance",
    "Properties" : {
      "AllocatedStorage": "120",
      "DBInstanceClass": "db.m4.large",
      "DBInstanceIdentifier": "My database",
      "DBName": "mydb_development",
      "Engine": "postgres",
      "EngineVersion": "9.6",
      "LicenseModel": "postgresql-license",
      "StorageType": "gp2"
    }
  },
  "LoadBalancer" : {
    "Type" : "AWS::ElasticLoadBalancing::LoadBalancer",
    "Properties" : {
      "CrossZone" : "true",
      "Listeners" : [
        {
          "LoadBalancerPort" : "80",
          "InstancePort" : "81",
          "Protocol" : "HTTP"
        },
        {
          "LoadBalancerPort" : "443",
          "InstancePort" : "80",
          "Protocol" : "HTTPS",
          "SSLCertificateId" : "CertificateId"
        }
      ],
      "HealthCheck" : {
        "Target" : "HTTP:80/okcomputer/all",
        "HealthyThreshold" : "3",
        "UnhealthyThreshold" : "2",
        "Interval" : "10",
        "Timeout" : "5"
      },
      "SecurityGroups" : "security-group-id",
      "Subnets" : [ "subnet-id" ]
    }
  },
  "DatabaseDNS" : {
    "Type" : "AWS::Route53::RecordSet",
    "Properties" : {
      "HostedZoneName" : "your.hosted.zone.",
      "Name" :  "db.hostname.com",
      "ResourceRecords" : [ { "Fn::GetAtt" : [ "Database", "Endpoint.Address" ] } ],
      "TTL" : "300",
      "Type" : "CNAME"
    },
    "DependsOn" : "Database"
  },
  "LoadBalancerDNS" : {
    "Type" : "AWS::Route53::RecordSet",
    "Properties" : {
      "HostedZoneName" : "your.hosted.zone.",
      "Name" :  "app.hostname.com",
      "ResourceRecords" : [ { "Fn::Join" : [ "", [ { "Fn::GetAtt" : [ "LoadBalancer", "DNSName" ] }, "." ] ] } ],
      "TTL" : "300",
      "Type" : "CNAME"
    },
    "DependsOn" : "LoadBalancer"
  }
}
```

The different resource types have their own requirements around which properties
are mandatory, but the CloudFormation documentation for each is very detailed.

* [RDS instance](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-rds-database-instance.html)
* [EC2
instance](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html)
* [Elastic Load
Balancer](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-elb.html)
* [Route 53 record
set](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-route53-recordset.html)

We define an EC2 instance to which we can deploy. We specify the AMI to use, and we attach an EBS.
Next we define our RDS database, specifying size, db engine, instance class etc.
A load balancer follows. I've left out autoscaling/launch-configurations to keep the example simple.
Finally, we define some DNS entries for the load balancer and the db.

I've pieced together the above example from some real-world code, so I've had to snip out a few resources, but the template above should give you a good indication
of how you can defined different resource types, how we can specify dependencies using the `DependsOn` entry, and additionally, how we can
use CloudFormation functions to dynamically retrieve properties of a created resource. Look the end DNS entries above and you will see that
we are retrieving the database endpoint address and the load balancer dns name once they have been created by CloudFormation.

Rolling out the template is just a case of using the AWS CLI tool for
[CloudFormation](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/create-stack.html). 
From the official documentation, here's how to create the stack.

```
aws cloudformation create-stack \
  --stack-name myteststack \
  --template-body file:///home/testuser/mytemplate.json \ 
  --parameters ParameterKey=Parm1,ParameterValue=test1

{
  "StackId" :
  "arn:aws:cloudformation:us-west-2:123456789012:stack/myteststack/330b0120-1771-11e4-af37-50ba1b98bea6"
}

```

We name the stack, pass the template location, and additionally, any parameters that we would like to include. CloudFormation returns the unique ARN for the stack once it has been created.

## Some painpoints

Describing your entire stack in a codified format is extremely powerful and it really streamlines infrastructure productivity, but even from our
simple and contrived example above, there are a few pain points.
1. No preview of changes before create/update. As you can imagine, this can be extremely painful if something slips through unnoticed.
2. Resource ids need to be retrieved beforehand (things like AMI IDs or SSL cert ARNs) and then passed as parameters.

Historically, we solved both problems by writing code to 
1. Diff JSON templates to give us an indication of changes.
2. Use the AWS CLI / Ruby SDK to lookup resources and pass them onto the templates.

Enter [StackMaster](https://github.com/envato/stack_master), self described as "The missing CloudFormation tool".
Since introducing StackMaster, we have managed to leverage dynamic resolvers and
stack previews and reduced our infrastructure supporting codebase significantly.

[Part two]({% post_url 2019-08-22-Stackmaster %}) of this little series will discuss how we use some StackMaster features to make infrastructure management a little smoother.
