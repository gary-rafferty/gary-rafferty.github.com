---
layout: single
title: EC2's new vCPU-based on-demand limits
categories: [software]
tags: [aws,ec2]
---

In an effort to simplify the ec2 limit management experience, AWS recently [announced](https://aws.amazon.com/about-aws/whats-new/2019/09/vcpu-based-on-demand-instance-limits-are-now-available-in-amazon-ec2/)
that it was moving on-demand EC2 instance limits away from the existing count-based mechanism, and towards a simplified vCPU-based one.

This is currently 'opt-in' as of September 24th, but will be rolled out between October 24th and November 8th.

The vCPU mapping for instance types is available on the instance details page, but here's how it currently looks.

| Instance Size | vCPUs |
| ------------- | ----- |
| nano          |   1   |
| micro         |   1   |
| small         |   1   |
| medium        |   1   |
| large         |   2   |
| xlarge        |   4   |
| 2xlarge       |   8   |
| 3xlarge       |   12  |
| 4xlarge       |   16  |
| 8xlarge       |   32  |
| 9xlarge       |   36  |
| 10xlarge      |   40  |
| 12xlarge      |   48  |
| 16xlarge      |   64  |
| 18xlarge      |   72  |
| 24xlarge      |   96  |
| 32xlarge      |   128 |

The default limit for vCPUs will be **1152** for standard types (A, C, D, H, I,M, R, T, Z).

> Use your limits in terms of the number of vCPUs for the EC2 instance types to
> launch any combination of instance types that meet your changing application
> needs. For example, with a Standard Instance Family limit of 256 vCPUs, you
> could launch 32 m5.2xlarge instances (32 x 8 vCPUs) or 16 c5.4xlarge instances
> (16 x 16 vCPUs) or any combination of applicable instance types and sizes that
> total 256 vCPUs.

I can see this simplifying some workflows and the management of limits. 
Rather than managing quotas per instance type, we can now manage them per region.

For more details information, check out the announcement
[post](https://aws.amazon.com/blogs/compute/preview-vcpu-based-instance-limits/) from AWS.
