# Dataset Ingestion Guide

Once you have your database, the SciCat API server up and running, several options of ingesting a dataset into SciCat exist - ranging from quick, one-time ingestion via CURL to a fully automized ingestion setup using python wrappers for SciCat API access based on [`pydantic`]([https://pydantic.dev/docs/validation/latest/concepts/models/]) (a python validation class to ensure valid formats).

Another example that uses Jupyter Notebook in SciCatLive can be found [here]([https://github.com/SciCatProject/scicatlive/blob/main/services/jupyter/config/notebooks/pyscicat.ipynb) which includes how to authenticate, create a dataset, add datablocks and upload an attachement.

## The `CURL` command
The highest chance to make a successful request to one of SciCats endpoints is to learn from swagger. Browse and see what the syntax is, make sure you have a valid token, provide the correct fields and do not provide one of the forbidden fields. In the following we give a skeleton for examples.

### Simple GET request
with a known `pid` as authenticated user (replace placeholders)

```bash
curl -X "GET" \
  "http://"${URL}"/api/v4/datasets/${pid}" \
  -H "accept: application/json"
  -H 'Authentication: Bearer "${TOKEN}"'
```
If you are only interested in public records just drop the last line.

### GET identifiers of ```origdatablocks``` (OIDs) of a dataset

The dataset is identified by its pid, we use v4 API and first define a filter

```bash
export URL="valid_url"
export TOKEN="valid_token"
export pid="example_pid"

# -- GET the oids ------------------                                                                                                                     
FILTER_JSON='{                                                                                                                                                                                                   
  "where": { "pid": "'${pid}'" },                                                                                                                                                                                
  "include": [                                                                                                                                                                                                   
    {                                                                                                                                                                                                            
      "relation": "origdatablocks",                                                                                                                                                                              
      "scope": {                                                                                                                                                                                                 
        "fields": ["_id"],                                                                                                                                                                                       
        "limit": 20,                                                                                                                                                                                             
        "order": "filename ASC"                                                                                                                                                                                  
      }                                                                                                                                                                                                          
    }                                                                                                                                                                                                            
  ],                                                                                                                                                                                                             
  "fields": ["datasetName"],                                                                                                                                                                                     
  "limit": 10                                                                                                                                                                                                    
}'

curl -G "${URL}/api/v4/datasets" \
  --data-urlencode "filter=${FILTER_JSON}" \
  -H "Accept: application/json" \
  -H "Authorization: Bearer ${TOKEN}"

```

### Create a dataset 
Make sure the json is formatted OK. Some fields are mandatory, you can check in swagger to see which fields are mandatory or not, see also [swagger documentation](../../swagger/index.md#tips-and-tricks).
```bash
curl -X 'POST' \
  'https://${URL}/api/v4/datasets' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "ownerGroup": "string",
  "accessGroups": [
    "string"
  ],
  "instrumentGroup": "string",
  "owner": "string",
  "ownerEmail": "user@example.com",
  "orcidOfOwner": "string",
  "contactEmail": "string",
  "sourceFolder": "string",
  "sourceFolderHost": "string",
  "size": 0,
  "packedSize": 0,
  "numberOfFiles": 0,
  "numberOfFilesArchived": 0,
  "creationTime": "2026-04-22T10:54:53.642Z",
  "validationStatus": "string",
  "keywords": [
    "string"
  ],
  "description": "string",
  "datasetName": "string",
  "classification": "string",
  "license": "string",
  "isPublished": false,
  "techniques": [],
  "sharedWith": [],
  "relationships": [],
  "datasetlifecycle": {},
  "scientificMetadata": {},
  "scientificMetadataSchema": "string",
  "scientificMetadataValid": true,
  "comment": "string",
  "dataQualityMetrics": 0,
  "principalInvestigators": [
    "string"
  ],
  "startTime": "2026-04-22T10:54:53.642Z",
  "endTime": "2026-04-22T10:54:53.642Z",
  "creationLocation": "string",
  "dataFormat": "string",
  "proposalIds": [
    "string"
  ],
  "sampleIds": [
    "string"
  ],
  "instrumentIds": [
    "string"
  ],
  "inputDatasets": [
    "string"
  ],
  "usedSoftware": [
    "string"
  ],
  "jobParameters": {},
  "jobLogData": "string",
  "runNumber": "string",
  "pid": "string",
  "type": "string"
}'

```
Note, that datafiles and attachments related to a SciCat dataset need to be POSTed separtely as they are a priori independent entities.

### Adding datafiles 
Associated datafiles are organised in data blocks. There are original datablocks and only datablocks. The latter are obsolete and functionality has fully moved to `origdatablocks`. To attach your metadata of these associated datafiles to the dataset use e.g. `/api/v4/origdatablock`. Note, that the dataset to which the blocks belong are indicated by `dataasetId` which corresponds to the `pid` field of the dataset itself. The command can look like (and placeholders replaced):

```bash
curl -X 'POST' \
  'http://localhost:3000/api/v4/origdatablocks' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "ownerGroup": "string",
  "accessGroups": [
    "string"
  ],
  "instrumentGroup": "string",
  "size": 0,
  "chkAlg": "string",
  "dataFileList": [
    {
      "path": "string",
      "size": 0,
      "time": "2026-04-22T11:28:56.870Z",
      "chk": "string",
      "uid": "string",
      "gid": "string",
      "perm": "string",
      "type": "string"
    }
  ],
  "isPublished": true,
  "datasetId": "string"
}'
```
Also note, that the `ownerGroup` must match the same field in the dataset. Same holds for attachments.

### Adding attachments

Here too a POST request will ingest attachments to the dataset, e.g. like this (with placeholders replaced):

```bash
curl -X 'POST' \
  'http://localhost:3000/api/v4/attachments' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "ownerGroup": "string",
  "accessGroups": [
    "string"
  ],
  "instrumentGroup": "string",
  "thumbnail": "string",
  "caption": "string",
  "relationships": [
    {
      "targetId": "string",
      "targetType": "dataset",
      "relationType": "is attached to"
    }
  ],
  "isPublished": true,
  "aid": "string"
}'
```


## Pythonic way: python sdk
The python software development kit, sdk, is entirely generated from the backend based on the OpenAPI initiative and swagger definitions. Find more info [here](https://www.piwheels.org/project/scicat-sdk-py/).

## Pythonic way: pyscicat
`pyscicat` has the same functionality as the python sdk but is meant to be more user friendly and maintained by [dmreyno](https://pypi.org/user/dmcreyno/). Some intuitive examples and its documentation how to ingest can be found [here](https://www.scicatproject.org/pyscicat/howto/ingest.html).

## Pythonic way: sciteacean
Scitacean is a high level Python package for downloading and uploading datasets from and to SciCat.

See the [documentation](https://www.scicatproject.org/scitacean/) for installation and usage instructions.

If you need help, have a look at our [GitHub discussions](https://github.com/SciCatProject/scitacean). For questions, please start a Q&A discussion if you can't find an answer.
