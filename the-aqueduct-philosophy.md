---
description: Making data scientists heroes
---

# The Aqueduct Philosophy

The last 10 years have seen an explosion in tooling, interest, and efficacy of machine learning. Starting with MLLib on Spark and moving into the vibrant ecosystem of Python-based machine learning packages, the ability to build highly effective, high-performance machine learning models has become widespread.&#x20;

Much of the innovation over the last 10 years has (rightly so) focused on processing and featurizing data, experimenting with different model architectures, and effectively building large-scale models. This is inline with our own experiences at UC Berkeley (**ADD LINK**), where the data science curriculum we've worked on teaches students the basics of SQL queries, data preparation, modeling in SciKit-Learn and PyTorch, and model evaluation -- all within the context of a Jupyter notebook. Interestingly, while we were at Berkeley we noticed that a large majority of the students taking the data science curriculum were coming from a diverse set of backgrounds across the university.

What this means that today, there are thousands of data scientists who have domain expertise in their home field and are capable of building interesting, business-critical machine learning models to make their organizations more effective.

### The Missing Link

The part of the lifecycle that's received comparatively little attention in recent years is prediction, or inference -- what happens once a model is built?&#x20;

What we've observed by and large is that data teams rely on a medley of other tools -- task orchestration systems like Airflow, low-level cloud infrastructure like Docker & Kubernetes, and hand-built connectors to data systems -- to get predictions made and shared with stakeholders. This set of tools is not only cumbersome and slow, it's also provides very little visibility into errors and failures, which means that debugging is a painful process.

The data scientists we've been working with, however, are capable of much more -- the combination of ML expertise and business knowledge makes data scientists uniquely suited to significantly improve team processes, business decisions, and customer interactions at every company... if only we can get the infrastructure out of their way.

### What is Prediction Infrastructure?

The problems we've heard from data scientists consistently come from one source -- trying to force machine learning and data science workfloads into infrastructure built for different use cases.  We think this is an anti-pattern: Data scientists aren't software engineers, and making data scientists successful is going to require thinking tooling to meet them where they are. Some of the companies we admire the most have done exactly this (e.g., Weights & Biases for data scientists, Vercel & Netlify for web developers).&#x20;

_**Prediction infrastructure** is infrastructure built to automate the process of deploying models, connect them to data and business systems, and help evaluate and quantify their performance._&#x20;

### Why Aqueduct?

Aqueduct the first prediction infrastructure for data scientists.&#x20;

The existing tools for deploying models are not designed with data scientists in mind -- they assume the user will casually build Docker containers, deploy Kubernetes clusters, and writes thousands of lines of YAML to deploy a single model. Data scientists are by and large not interested in doing that.&#x20;

Aqueduct is designed for data scientists, with three core design principles in mind:

* _Simplicity_: Data scientists should be able to deploy models with tools they're comfortable with and without having to learn how to use complex, low-level infrastructure systems.
* _Connectedness_: Data science and machine learning can have the greatest impact when everyone in the business has access, and data scientists shouldn't have to bend over backwards to make this happen.
* _Confidence_: Having the whole business benefit from your works means that data scientists should be able to sleep peacefully, knowing that things are working as expected -- and they'll be alerted as soon as that changes.&#x20;
