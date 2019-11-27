---
layout: single
title: Using S3 Lifecycle Policies
categories: [aws]
tags: [aws,s3,glacier]
---

Amazon Simple Storage Service, or S3 as we know it, is a managed service for object storage. It is
used to store all sorts of data, such as images, videos, audio files, database dumps.  
Additional use cases include data backup, disaster recovery, and data archival.

When storing objects in S3, you must choose a class of storage. AWS provides a
number of different options, but the default class is S3 Standard.

### Storage classes

* Amazon S3 Standard (S3 Standard)
* Amazon S3 Intelligent-Tiering (S3 Intelligent-Tiering)
* Amazon S3 Standard-Infrequent Access (S3 Standard-IA)
* Amazon S3 One Zone-Infrequent Access
* Amazon S3 Glacier (S3 Glacier)
* Amazon S3 Glacier Deep Archive (S3 Glacier Deep Archive)

Classes can be associated with use-cases.

| Use Case                   | Class         |
| -------------------------- | ------------- |
| General Purpose            | Amazon S3 Standard (S3 Standard) |
| Unknown or changing access | Amazon S3 Intelligent-Tiering (S3 Intelligent-Tiering) |
| Infrequent access          | Amazon S3 Standard-Infrequent Access (S3 Standard-IA) |
| Infrequent access          | Amazon S3 One Zone-Infrequent Access |
| Archive                    | Amazon S3 Glacier (S3 Glacier) |
| Archive                    | Amazon S3 Glacier Deep Archive (S3 Glacier Deep Archive)|

### Data lifecycle

Requirements for data access can change over time. Let's use database
backups as an example.
Your backups may begin their lifecycle as general purpose. They will need to
be readily available to access should you need to rollback to a particular point
in time, or for restoration purposes. 

As time goes on, the likelihood of needing to access a backup will diminish. At
a certain point in time, the use case transitions from General Purpose to Archive.
Archive data 

As the table above shows, their are two Archive offerings for S3. Glacier,
Glacier Deep Archive. Each comes with their own price and properties, but really
it comes down to how long you intend to archive the data for, and how quickly
you may need to retrieve the data.

|   | Glacier | Glacier Deep Archive |
| - | ------- | -------------------- |
|Minimum storage duration charge | 90 days | 180 days |
|Data retrieval times | Configurable, from minutes to hours | Within 12 hours
|Pricing | $0.005 per GB | $0.002 per GB |

Compared against the S3 Standard cost of $0.025 per GB, there is a significant
saving when using the Archive storage classes.

Transitioning objects from one class to another can be done manually using the
UI, CLI, or APIs, but a better way to automate this, is to create Lifecycle
Policies that define actions you want Amazon S3 to take during an object's lifetime.

### Lifecycle policies

> You can use lifecycle policies to define actions you want Amazon S3 to take during an object's lifetime (for example, transition objects to another storage class, archive them, or delete them after a specified period of time).

Put simply, you can automate the transitioning of objects' storage classes. For
example, you may want to keep all backups created within the last 30 days as S3
Standard, but thereafter, that can be changed to Glacier. That way, they will
still be available for retrieval should you require them, but you will be paying
a fraction of the cost for storage.

Policies can be created using the usual AWS interfaces of Console, CLI, and API.
Here's Amazon's example of creating an S3 bucket with an attached lifecycle policy that transitions the class to Glacier after one day, and then deletes the file after one year.

```json

{
  "AWSTemplateFormatVersion": "2010-09-09",
  "Resources": {
    "S3Bucket": {
      "Type": "AWS::S3::Bucket",
      "Properties": {
        "AccessControl": "Private",
        "LifecycleConfiguration": {
          "Rules": [
            {
              "Id": "GlacierRule",
              "Prefix": "glacier",
              "Status": "Enabled",
              "ExpirationInDays": "365",
              "Transitions": [
                {
                  "TransitionInDays": "1",
                  "StorageClass": "GLACIER"
                }
              ]
            }
          ]
        }
      }
    }
  },
  "Outputs": {
    "BucketName": {
      "Value": {
        "Ref": "S3Bucket"
      },
      "Description": "Name of the sample Amazon S3 bucket with a lifecycle configuration."
    }
  }
}
```

Using Lifecycle policies will automate the transitioning of storage classes and
are an ideal fit for data that follows this type of lifecycle. If you would
prefer to take a more manual approach to transitioning, you can bulk select
objects in the S3 web interface and change class there. Happy archiving!
