---
layout: single
title: CloudFront Adventures
categories: [aws]
tags: [aws, cloudfront]
---

Some notes from a recent CloudFront implementation.

CloudFront is Amazon's CDN (Content Delivery Network). An efficient and
affordable mechanism of distributing content globally.  

I recently spent some time deploying a CloudFront distribution to serve audio
assets for our phrasebook series. These assets live in a public S3 bucket, but by serving them via
CloudFront, the assets are replicated across the various edges and served to the
user from the closest one. This allows you to maintain a single S3 source, and have CloudFront act as a
proxy for incoming requests.

![cloudfront to s3](/assets/images/cloudfront/proxy.png)

The CloudFront node in the above image is replicated across the network.

> Amazon CloudFront uses a global network of 210 Points of Presence (199 Edge Locations and 11 Regional Edge Caches) in 78 cities across 37 countries

## CloudFormation

We are heavy users of CloudFormation and StackMaster for orchestrating and managing 
our infrastructure. Defining the stack for the described setup requires us to
define a number of resources.

### S3 bucket

The S3 bucket configuration might look something like this

```json
  "S3Bucket": {
    "Type": "AWS::S3::Bucket",
    "Properties": {
      "BucketName": "mybucket",
      "WebsiteConfiguration": {
        "IndexDocument": "index.html",
        "ErrorDocument": "error.html"
      }
    }
  }
```

This creates a simple S3 bucket that is configured to act as a static website.

### S3 access policy

You will also want to ensure that your CloudFront IAM role has access to the
bucket contents.

```json
  "BucketPolicy": {
    "Type": "AWS::S3::BucketPolicy",
    "Properties": {
      "Bucket": "mybucket",
      "PolicyDocument": {
        "Statement": [
          {
            "Sid": "1",
            "Effect": "Allow",
            "Principal": {
              "AWS": "ARN for your CloudFront access identity"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::mybucket/*"
          }
        ]
      }
    }
  }
```

### CloudFront distribution

The distribution sets your buckets as the origin, and specifies additional
properties such as CNAME aliases, SSL certs, supported HTTP versions etc.

```json
  "CloudFrontDistribution": {
    "Type": "AWS::CloudFront::Distribution",
    "Properties": {
      "DistributionConfig": {
        "Aliases": [ "sub.domain.com" ],
        "Origins" : [ {
          "DomainName" : "S3 website domain name",
          "Id" : "myOrigin",
          "CustomOriginConfig": {
            "HTTPPort": 80,
            "HTTPSPort": 443,
            "OriginKeepaliveTimeout": 5,
            "OriginProtocolPolicy": "http-only",
            "OriginReadTimeout": 30
          }
        }],
        "HttpVersion": "http2",
        "Enabled" : "true",
        "DefaultRootObject" : "index.html",
        "DefaultCacheBehavior" : {
          "AllowedMethods" : [ "GET", "HEAD" ],
          "TargetOriginId" : "myOrigin",
          "ForwardedValues" : {
            "QueryString" : "false",
            "Cookies" : { "Forward" : "none" }
          },
          "ViewerProtocolPolicy" : "redirect-to-https"
        },
        "PriceClass" : "PriceClass_All",
        "ViewerCertificate": {
          "AcmCertificateArn": "arn for your ssl cert",
          "MinimumProtocolVersion": "TLSv1.1_2016",
          "SslSupportMethod": "sni-only"
        }
      }
    }
  }
```

Most of the distribution properties will be self-explanatory but one important
one is the `PriceClass` property. As per Amazon's documentation

> If you specify PriceClass_All, CloudFront responds to requests for your objects from all CloudFront edge locations.
> If you specify a price class other than PriceClass_All, CloudFront serves your objects from the CloudFront edge location that has the lowest latency among the edge locations in your price class. Viewers who are in or near regions that are excluded from your specified price class may encounter slower performance.

For more information in which regions are includes in each price class, check the
[documentation](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/PriceClass.html).

## Invalidations

If you need to remove files across the edges, you have two options.
Invalidations, or Versioning.  
Amazon recommends versioning for files that will need to be frequently updated.
Our particular use-case does not require the files to change very often, so we can
make do with Invalidations.  

The quickest and easiest way to invalidate files is to use the CLI and to
specify the distribution id and the path(s) to invalidate.

```bash
aws cloudfront create-invalidation --distribution-id $CDN_DISTRIBUTION_ID --paths "/*"
```

Be aware that there is a cost associated with invalidations. 
> The first 1,000 invalidation paths that you submit per month are free; you pay for each invalidation path over 1,000 in a month.

## Summary

![cloudfront architecture diagram](/assets/images/cloudfront/architecture.png)

Overall, CloudFront is a fast and efficient way to serve files globally. It's
incredibly easy to implement and configure, and integrates seamlessly with
other Amazon services such as S3, ACM, EC2.

In this particular use-case, the CloudFront/S3 combination means that we do not
need to maintain load balancers, proxies, or cross-region replication.  
A single stack definition creates the distribution & bucket, and then we can use
the AWS CLI to populate the bucket with the assets.
