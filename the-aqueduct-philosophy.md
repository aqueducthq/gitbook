---
description: Making data scientists heroes
---

# The Aqueduct Philosophy

The last 10 years have seen an explosion in the tooling, interest, and applications of machine learning. 
One of the key driving forces behind the broad adoption of machine learning is access to a vibrant open-source ecosystem of software for preparing data and training models. 
From NumPy, Pandas, and Jupyter to scikit-learn, XGBoost, Pytorch, and Tensorflow, the vibrant ecosystem of Python-based data science and machine learning software has made it easier than ever to build machine learning models.

Much of the innovation over the last 10 years has (rightly so) focused on processing and featurizing data, experimenting with different model architectures, and effectively training models. 
This is inline with our own experiences at UC Berkeley, where the data science curriculum we've developed ([Data100](www.ds100.org)) teaches students the basics of SQL queries, exploratory data analysis, data preparation, modeling in SciKit-Learn and PyTorch, and model evaluation -- all within the context of a Jupyter notebook. 

Interestingly, while we developing the Berkeley data science curriculum, we noticed that a many of the students taking the data science courses were coming from disciplines outside of computer science and engineering.
This new generation of highly data literate students with backgrounds in humanities and physical sciences has the opportunity to drive the broad adoption of machine learning and transform the organizations they join. 
Yet, for them to succeed, they will need to overcome the significant engineering hurdles along the last mile of data science and machine learning: deploying, integrating, and supporting their models.


### The Missing Link

The part of the lifecycle that's received comparatively little attention in recent years is prediction, or inference -- what happens once a model is built?&#x20;

What we've observed by and large is that data teams rely on a medley of other tools -- task orchestration systems like Airflow, low-level cloud infrastructure like Docker & Kubernetes, and hand-built connectors to data systems -- to get predictions made and shared with stakeholders. This set of tools is not only cumbersome and slow, it's also provides very little visibility into errors and failures, which means that debugging is a painful process.

The data scientists we've been working with, however, are capable of much more -- the combination of ML expertise and business knowledge makes data scientists uniquely suited to significantly improve team processes, business decisions, and customer interactions at every company... if only we can get the infrastructure out of their way.

### What is Prediction Infrastructure?

The problems we've heard from data scientists consistently come from one source -- trying to force machine learning and data science workflows into infrastructure built for different use cases.  
We think this is an anti-pattern: Data scientists aren't software engineers, and making data scientists successful is going to require thinking tooling to meet them where they are. 
Some of the companies we admire the most have done exactly this (e.g., Weights & Biases for data scientists, Vercel & Netlify for web developers).&#x20;

_**Prediction infrastructure** is infrastructure built to automate the process of deploying models, connect them to data and business systems, and help evaluate and quantify their performance._&#x20;

### Why Aqueduct?

Aqueduct is the first prediction infrastructure for data scientists.&#x20;

The existing tools for deploying models are not designed with data scientists in mind -- they assume the user will casually build Docker containers, deploy Kubernetes clusters, and writes thousands of lines of YAML to deploy a single model. 
Data scientists are by and large not interested in doing that and there are better uses for their skills.&#x20;

Aqueduct is designed for data scientists, with three core design principles in mind:

* _Simplicity_: Data scientists should be able to deploy models with tools they're comfortable with and without having to learn how to use complex, low-level infrastructure systems.
* _Connectedness_: Data science and machine learning can have the greatest impact when everyone in the business has access, and data scientists shouldn't have to bend over backwards to make this happen.
* _Confidence_: Having the whole organization benefit from your works means that data scientists should be able to sleep peacefully, knowing that things are working as expected -- and they'll be alerted as soon as that changes.&#x20;
