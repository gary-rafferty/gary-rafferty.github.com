---
layout: single
title: Instance size flexibility for EC2 Reserved Instances
categories: [software]
tags: [aws,ec2]
---

Reserved EC2 instances (RIs) can be used to reserve instance compute capacity
for a fixed term, in exchange for a sizeable discount.

I have previously written about reserving [RDS instances]({% post_url
2019-07-30-Reserved-RDS-Instances%}). This post focuses on EC2 reservations, and
specifically, how to use AWS' _normalization factor_ to make things easier and
more beneficial.

## What is the Normalization Factor

The Normalization Factor (NF) can be thought of as a measure of compute usage per
hour, but only within the context of billing.

Looking at the table below, if you are using a t2.nano instance for one hour,
then you have consumed 0.25 units.

| Instance Size | Normalization Factor |
| ------------- | -------------------- |
| nano | 0.25 |
| micro | 0.5 |
| small | 1 |
| medium | 2 |
| large | 4 |
| xlarge | 8 |
| 2xlarge | 16 |
| 4xlarge | 32 |
| 8xlarge | 64 |
| 10xlarge | 80 |
| 16xlarge | 128 |
| 32xlarge | 256 |

Where this comes in to play, is when your reservation discounts are being
applied to the account.

## The older mechanism

Previously, your RI must exactly match an instance for a discount to be applied.
In the above example, if you had purchased a reservation of a single t2.small instance, 
this would not have matched your usage of the t2.nano, so no discount would be
applied. 

#### Reservation

| Instance Type | Instance Family |
| ------------- | --------------- |
| t2.small      | t2              |

#### Usage

| Instance Type | Instance Family |
| ------------- | --------------- |
| t2.nano       | t2              |

This billing mechanism led to wastage across RIs and required a lot of upfront planning on
how your infrastructure requirements may change over the course of your resevation term.

## The newer, more flexible, mechanism

Using the newer mechanism, your usage is taken in terms of compute units, and is
applied to all sizes of instances within an instance family. 
Back to our above example. 
Your usage of the t2.nano instance would have clocked up 0.25 units. 
A portion of your reservation belonging to your t2.small instance can be applied
to your t2.nano usage.

## Keeping it in the family

As mentioned above, the discounts can be applied to all sizes within a family.
Amazon also allows you to combine instances.

By reserving a single t2.small instance, we have an NF of 1 to play with.
This could be split between two t2.nano instances and one t2.micro. This makes
for much easier forecasting of cost and usage across your infrastructure layer.
