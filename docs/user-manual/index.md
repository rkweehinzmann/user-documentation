# Welcome to SciCat Users Manual

This is a short guide to most common questions users of SciCat can face. First of all: What is SciCat? And what are its features, how can I use it? SciCat, a _science catalogue_, should serve you to find back your data through its **meta data**. Genrally all datasets in SciCat a priori datasets of _metadata_. In a further step, SciCat _can_ handle access to the actual the experimental data. 

SciCat is a bookkeeping tool accompanying some critical steps during the entire data life cycle which are: getting an overview of datasets for data analysis, for re-analysis, for publishing datasets, and in particular for publication. 

SciCat has several strong points:
* It can be integrated to almost any other service that has REST APIs. Therefore, site-specific applications can be easily integrated. 
* The Data Model of SciCat forsees a schemaless fields for quite different use cases. This concept has been implemented for the main class, Datasets, but is extended to function in the same way for the other classes e.g. Proposlas, Samples, Intstruments and Published Data.
* Its components are based on OpenSource software projects and state of the art technologies. 


For more detailed information on how to run scicat, see [scicatlive documentation](https://www.scicatproject.org/scicatlive/latest/). For more details on how to ingest, setup and deploy information from SciCat, see the [site admin manual](operator/index.md). 

## Features: Quick links to How-To's for users

Your data may be of type raw or derived, you may want to login or just browse what's in the catalogue. Here is how to find more on how to proceed:

* [Login](../login/index.md)
* Search and find your data, see [Datasets How to query](datasets/index.md#how-to-query-datasets)
*   How to change some fields after ingestion
*   How to view history of changes to a dataset
*   How to [group and tag datasets](../datasets/grouping_tagging_ds.md).
*   How to group and tag grouped datasets

## A few more How-To's for users and site-admins
* Where to find the version of the deployed SciCat Frontend? Check [here](about/operatorHowTos.md).



## Structure of SciCat

These main classes determine the functionality of SciCat: 

1. [Datasets](datasets/index.md): This class is the most elaborated one. 
2. [Proposals](proposals.md): are used to link datasets to the proposal under which beamtime was granted.
3. [Instruments](instruments.md): Depending on the science background `instruments` can mean something different.
4. [Samples](samples.md): 

