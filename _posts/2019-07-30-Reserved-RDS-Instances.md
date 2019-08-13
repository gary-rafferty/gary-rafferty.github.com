---
layout: single
title: Reserving Amazon RDS resources
categories: [software]
tags: [aws,rds]
---

Reserved instances (RIs) can be used to reserve a resource for a one year or a three 
term in exchange for a billing discount - a sizeable discount compared with the standard on-demand price.

An RI is not a physical resource (there is no instance identifier for example), it is merely a
billing mechanism that is applied to your monthly invoice.
AWS supports reservations for a variety of resource types, but this article
focuses solely on RDS for now. 

When forecasting infrastructure spend, there are a number of factors that must
be considered, but often, a simple starting point, is to look at the resources
that you expect to be always-on (24 hours a day, 7 days a week), and then to
multiply by the AWS hourly rate. This will gie you an accurate idea of cost per
month.

As an example, let's assume that our production database is a PostgreSQL server
sitting atop a db.m4.large instance. For brevity, we will assume SingleAZ, and
no read replicas.

| Instance | On-demand price per hour |
| ------------ | ------------- |
| db.m5.large  | $0.247 |

We can expect this resource to cost us

```
0.247 * 24 * 365 = $2163.70 per annum  
                or $180 per month
```

Compare this against the different reserve options for the 1 year term.

| Instance | Payment Option |	Upfront	| Monthly | Savings over On-Demand |
| --------- | ------------- | ------- | ---------------- | ------------- |
| db.m5.large | No Upfront  | $0      | $124.976         | 31%           |
| db.m5.large | Partial Upfront| $714 | $59.495          | 34%           |
| db.m5.large | All Upfront | $1399   | $0.000           | 35%           |

And again, this can be compared with the different reserve options for the 3 year term.

| Instance | Payment Option |	Upfront	| Monthly | Savings over On-Demand |
| --------- | ------------- | ------- | ---------------- | ------------- |
| db.m5.large | Partial Upfront| $1,461 | $40.588        | 55%           |
| db.m5.large | All Upfront | $$2,863   | $0.000         | 56%           |

It can be difficult to commit to a resource for three years, but even looking at
the one year term, it's clear that there are significant savings that can be
availed of. 

The majority of our own database resources are non-transient and are expected to be
available 24x7. As we look at budget allocations and estimations for infrastructure, we would prefer
to take the upfront hit which yields the greatest reward over the year.

An important point too, is that there is flexibility around instance sizing.
As per the RDS RI documentation

> Amazon RDS Reserved Instances provide size flexibility for the MySQL, MariaDB, PostgreSQL, and Amazon Aurora database engines as well as the “Bring your own license” (BYOL) edition of the Oracle database engine. With size flexibility, your RI’s discounted rate will automatically apply to usage of any size in the instance family (using the same database engine). Size flexibility does not apply to Microsoft SQL Server and the License Included (LI) edition of Oracle.

> For example, let’s say you purchased a db.m4.2xlarge MySQL RI in US East (N. Virginia). The discounted rate of this RI can automatically apply to 2 db.m4.xlarge MySQL instances without you needing to do anything.

> The RI discounted rate will also apply to usage to both Single-AZ and Multi-AZ configurations for the same database engine and instance family. For example, let’s say you purchased a db.r3.large PostgreSQL Single-AZ RI in EU (Frankfurt). The discounted rate of this RI can automatically apply to 50% of the usage of a db.r3.large PostgreSQL Multi-AZ instance in the same region.

Purchasing reserved instances can yield significant savings to your AWS bill.
Your own budget / financial stakeholders will likely determine which option are
available to you, but if you know that you can commit to a resource, then this
is an avenue that is well worth exploring.

If you are early stage and still finding your feet, then on-demand will likely
be a better fit for you.
