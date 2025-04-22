# DOI minting in SciCat - How to publish datasets

## Introduction
User introduction can be found [here](../doisIntro.md).

## Variables to configure

We repeat here the relevant parts from the [```.env```-file](../backendconfig/index.md#environment-variables) that essentially give handle to the admin-user:

* REGISTER_DOI_URI="https://mds.test.datacite.org/doi"
* REGISTER_METADATA_URI="https://mds.test.datacite.org/metadata"
* DOI_USERNAME="username"
* DOI_PASSWORD="password"

## Full potential with SciCat's APIs

The respective endpoints can be viewed from swagger and are

List of API endpoints one can access:
![swagger screenshot](../swagger/img/swagger_publishedData.png)

### Endpoints

#### post 
Main one is to the post object:

![```post```](../swagger/img/swagger_publishedData_post.png)

Others are

#### count 
![```count```](../swagger/img/swagger_publishedData_count.png)

#### register
![```register```](../swagger/img/swagger_publishedData_register.png)

#### form populate
![```form populate```](../swagger/img/swagger_publishedData_formpopulate.png)

#### resync
![```resync```](../swagger/img/swagger_publishedData_resync.png)
