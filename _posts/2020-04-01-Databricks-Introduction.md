---
layout: single
title: Databricks - An Introduction
categories: [bigdata]
tags: [databricks, bigdata]
---

I recently started learning about Databricks, a unified data analytics platform.
The platform is large and this is a relatively new domain to me, so these are my
introductory notes.

Databricks' goal is to provide businesses with a single platform for cloud based
data engineering. A single, scaleable, platform that can be used for data engineering, machine learning, analytics, and collaboration.

The platform is composed of multiple components; each targeting a different
problem. 

1. Data Science Workspace
2. Unified Data Service
3. Enterprise Cloud Service

### Data Science Workspace

A workspace that supports the entire machine learning lifecycle,
by allowing different data practioners to work within the same space. This helps
to minimise compatibility issues between tools and technologies.  
The collaborative notebooks support a variety of languages, provide
visualisations, are shareable, and support scheduling.  
A managed MLFlow offering allows you to run, track, and deploy experiments
without concern for scalability or reliability.

### Unified Data Service

A platform that allows practioners to scaleably ingest
data from a number of sources, to define and run pipelines on that data, and to build
reliable data lakes.   
This is composed of the Databricks Runtime, an engine built atop an optimized
Apache Spark, and Delta Lake, reliable lifecycle management for data lakes.  
There are a huge variety of integrators built that allow you to easily ingest
data from not only databases, but also from applications (Google Analytics,
Stripe, MailChimp) and filestorage
(Dropbox, S3 etc).

### Enterprise Cloud Service

Production-ready, enterprise level administration that can be deployed to AWS or
Azure. Full operational visibility across usage, billing, audit trails etc.  
  
The [Databricks Academy](https://academy.databricks.com/), where I am learning,
offers self-paced learning and cert'ing. There are a range of courses covering
topics such as Apache Spark, ETL, and Machine Learning. That being said, they all look
quite expensive, so unless your company provides you with a training budget,
they might be cost prohibitive.  
