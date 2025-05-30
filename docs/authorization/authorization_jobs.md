# Jobs Authorization

Jobs subsystem relies on groups defined in the configuration file for the backend: 


| Configuration Group List | Description |
| ------------------------ | ----------- | 
| ADMIN_GROUPS | Users of the listed groups can create, modify and read any job. They cannot delete jobs. |
| | |
| CREATE_JOB_PRIVILEGED_GROUPS | Users of the listed groups can create and read any job. They can only modify jobs that belong to their user or group depending on the configuration of given job (see Job Create Authorization Table ). They cannot delete jobs. |
| | |
| UPDATE_JOB_PRIVILEGED_GROUPS | Users of the listed groups can modify and read any job. They can only create jobs that belong to their user or group depending on the configuration of given job (see Job Update Authorization Table ). They cannot delete jobs. |
| | |
| DELETE_JOB_GROUPS | Users whose group is listed here are allowed to delete any job |


## CASL ability actions
This is the list of the permission methods available for Jobs and all their endpoints.  
The authorization for jobs is consistently different from all the other endpoints.

### Endpoint Authorization
1. JobCreate
2. JobRead
- JobUpdate
- JobDelete

### (Data) Instance Authorization
1. JobCreateConfiguration (The job's create section of the configuration dictates if the user can create the job)
2. JobCreateOwner (Users with this privilege can create jobs for others)
- JobCreateAny (Users with this privilege can create jobs for any of the users that are defined in the create section of the job configuration)
- JobReadAccess
- JobReadAny
- JobUpdateConfiguration (The job's update section in configuration dictates if the user can update the job)
- JobUpdateOwner (Users with this privilege can update jobs belonging to others)
- JobUpdateAny (Users with this privilege can update any job)

#### Priority
```mermaid
    JobCreate-->JobCreateConfiguration;
    JobCreateConfiguration-->JobCreateAny;
    JobRead-->JobReadAccess;
    JobReadAccess-->JobReadAny;
    JobUpdate-->JobUpdateConfiguration;
    JobUpdateConfiguration-->JobUpdateAny;
    JobDelete;
```

#### Authorization table
| HTTP method | Endpoint | Endpoint Authentication | Anonymous | Authenticated | Create Jobs Groups | Update Jobs Groups | Admin Groups | Delete Groups |
| -------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- |
| POST | Jobs | _JobCreate_ | _JobCreateConfiguration_ | _JobCreateConfiguration_ | Any<br>_JobsCreateOwner_ | __no__ | Any<br>_JobsCreateAny_ | __no__ |
| GET | Jobs | _JobReadMany_ | __no__ | Has Access<br>_JobReadAccess_ | Has Access<br>_JobReadAccess_ |  __no__  | Any<br>_JobReadAny_ | __no__ |
| GET | Jobs/_jid_ | _JobReadOne_ | __no__ | Has Access<br>_JobReadAccess_ | Has Access<br>_JobReadAccess_ |  __no__  | Any<br>_JobReadAny_ | __no__ |
| PATCH | Jobs/_jid_  | _JobUpdate_ | __no__ | _JobUpdateConfiguration_ | __no__ | Owner<br>_JobUpdateOwner_ | Any<br>_JobUpdateAny_ | __no__ |
| DELETE | Jobs/_jid_ | _JobDelete_ | __no__ | __no__ | __no__ | __no__ | __no__ | __no__ |

#### Job Create Authorization Table
The _JobCreateConfiguration_ authorization permissions are configured directly in the __*create*__ section of the job configuration.  
Any positive match will result in the user acquiring _JobCreate_ endpoint authorization, which applies to the jobs endpoint `POST:Jobs` 
  
| Job Create Authorization | Endpoint Authentication Translation | Endpoint Authentication Description | Instance Authentication Translation | Instance Authentication Description |
| --- | --- | --- | --- | --- |
| _#all_ | _#all_ | any user can access this endpoint, both anonymous and authenticated | _#all_ | Any user can create this instance of the job |
| _#datasetPublic_ | _#all_ | any user can access this endpoint, both anonymous and authenticated | _#datasetPublic_ | the job instance will be created only if all the datasets listed are __public__ |
| _#authenticated_ | _#user_ | any valid users can access the endpoint, independently from their groups | _#user_ | any valid users can create this instance of the job |
| _#datasetAccess_ | _#all_ | any user can access this endpoint, both anonymous and authenticated | _#datasetAccess_ | the job instance will be created only if the specified user group or otherwise any of the user's groups has access to all the datasets listed |
| _#datasetOwner_ | _#all_ | any user can access this endpoint, both anonymous and authenticated | _#datasetOwner_ | the job instance will be created only if the specified user group or otherwise any of the user's groups is part of all the datasets' owner group |
| __*@GROUP*__ | _#all_ | any user can access this endpoint, both anonymous and authenticated | __*GROUP*__ | the job instance will be created only if the user belongs to the group specified |
| __*USER*__ | _#all_ | any user can access this endpoint, both anonymous and authenticated  | __*USER*__ | the job instance can be created only by the user indicated |
| #jobAdmin | #all | any user can access this endpoint, both anonymous and authenticated | _#jobAdmin_ | the job instance can be created by users of ADMIN_GROUPS and CREATE_JOB_PRIVILEGED only |
__IMPORTANT__: use option _#all_ carefully, as it allows anybody to create a new job. It is mostly used for debugging and testing.

#### Job Update Authorization Table
The _JobUpdateConfiguration_ authorization permissions are configured directly in the __*update*__ section of the job configuration.  
Any positive match will result in the user acquiring  _JobUpdate_ endpoint authorization, which applies to the jobs endpoint `PATCH:Jobs/id` 
  
| Job Update Authorization | Endpoint Authentication Translation | Endpoint Authentication Description | Instance Authentication Translation | Instance Authentication Description |
| --- | --- | --- | --- | --- |
| _#all_ | _#all_ | any user can access this endpoint, both anonymous and authenticated | _#all_ | Any user can update this job instance |
| _#jobOwnerUser_ | _#user_ | any user can access this endpoint, both anonymous and authenticated | _#jobOwnerUser_ | only the user that is listed in field _ownerUser_ can perform the update |
| _#jobOwnerGroup_ | _#user_ | any user can access this endpoint, both anonymous and authenticated | _#jobOwnerGroup_ | any user that belongs to the group listed in field _ownerGroup_ can perform the update |
| __*@GROUP*__ | __*GROUP*__ | any user can access this endpoint, both anonymous and authenticated | __*GROUP*__ | the job can be updated only by users who belong to the group specified |
| __*USER*__ | __*USER*__ | any user can access this endpoint, both anonymous and authenticated | __*USER*__ | the job can be updated only by the user indicated |
| #jobAdmin | #all | any user can access this endpoint, both anonymous and authenticated | _#jobAdmin_ | the job instance can be created by users of ADMIN_GROUPS and UPDATE_JOB_PRIVILEGED only |

__IMPORTANT__: use option _#all_ carefully, as it allows anybody to update the job. It is mostly used for debugging and testing.

#### Job Authorization priority
The endpoint authorization is the most permissive authorization across all the jobs defined.
The priority between job create and update authorization is as follows:

```mermaid
    all-->user;
    user-->GROUP;
    GROUP-->USER;
    USER-->ADMIN_GROUPS;
```
