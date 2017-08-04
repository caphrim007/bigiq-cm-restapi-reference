## Terminating APM Sessions  


### Overview
You can use the REST API implemented on BIG-IQ to kill/terminate sessions on one or more BIG-IP devices.  There are three types of parameters that can be used to kill sessions:

 - Cluster Names - the names of a Cluster on BIG-IP systems
 - Access Groups - the name of BIG-IP APM Access Groups 
 - Usernames - the name of users that have established APM sessions 

### Prerequisites
Before attempting to use the API to terminate sessions, you should be sure the following prerequisites have been met:

 - All BIG-IP devices are operational and have the services provisioned that will be managed by the BIG-IQ Centralized Management system.
 - 	 The BIG-IQ Centralized Management system is operational, has completed the setup wizard, and completed any other needed configuration.	  
 - Trust has been established between the BIG-IP device and the BIG-IQ Centralized Management system.	 The APM service is discovered for the BIG-IP device in BIG-IQ Centralized Management system.  
 - The APM Configuration is imported, if access group name needs to be used as input criteria.


----------


### Preliminary API calls 
Before the API call that actually performs session termination is carried out, you may wish to use API calls to determine:

 - If a device is managed and trust is established
 - If Access Discovery was completed for a device
 - The Access Configuration was imported for a device


### Overview of API 
Here's a outline of the process in which sessions are terminated.

1. If desired or needed, perform a REST call to determine the current state of the BIG-IP device using one of the GetDevInfo request and checking the responses. Depending on the mechanism used to kill sessions, parts of the response to the GetDevInfo request will be used as input parameters to kill sessions.
2. Perform a POST that is specific to the mechanism you wish to perform. There are five variants of REST calls: 

- Killing Sessions by AccessGroup and Username (*KillAccessGroupAndUsername*)
This call allows you to kill all sessions by specifying a username and an Access Group name. 

- Kill Sessions by Clusters and Username (*KillClusterAndUsername*) This call allows you to kill all sessions by indicated username and matched BIG-IP cluster name. This is a re

- Kill Sessions in one or more BIG-IP clusters (without username) (*KillCluster*) This call allows you to kill sessions on one more more BIG-IP clusters without specifying a username.

- Killing Sessions by BIG-IP Device Reference and Username (*KillDeviceReferenceAndUsername*) This call allows you to kill all sessions on one more BIB-IP systems using a device reference or set of device references.  This call is not recommended if other calls can be used. 

- Kill All Sessions using Device Reference (without username) (*KillDeviceReference*) This call allows you to kill all sessions on one more BIB-IP systems using a device reference or set of device references.  This call is not recommended if other calls can be used. 

3. Monitor the task using GET methods until the status has reached a value of FINISHED, FAILED or CANCELLED. When the GET method status value is FINISHED and the currentStep value is DONE, the kill sessions is completed.

## Kill Sessions Post (KillPost)
There is a single Post request that is used to kill sessions.  The parameters of this request for this post are shown in the table below. The various REST calls that should be used require a request that users some subset of this parameters of this post.


Parameter | Description | Required
----------------------------------------| --------------------------------------------------
action | action value has to be KILL_BY_USER, KILL_ALL_SESSIONS or KILL_BY_LIST_OF_SESSIONS | Yes
deviceReferences | list of device references |
accessGroupNames | list of access config group names |
clusterNames | list of cluster names |
userName | Case sensitive field name. user name of user whose sessions needs to be killed.| Only if action is KILL_BY_USER
sessions | list of one or more session info object, with each object containing device reference and list of sesion ids | Only if action is KILL_BY_LIST_OF_SESSIONS
status | As part of response, status denotes the status of the task. It can be STARTED, FINISHED, FAILED, CANCELLED or CANCEL_REQUESTED |
result | As part of response, result denotes whether kill sessions action was COMPLETE or FAILED |
errorMessage | This can contain error message during failure |


## KillAccessGroupAndUsername (username, list of AccessGroups)
To use this API you need to have the username and an access group name.

```
POST:  https://<mgmtip>/mgmt/cm/access/tasks/kill-sessions
{
   "action":"KILL_BY_USER",
   "userName":"user2",
   "accessGroupNames":[
      "TestGroup1",
      "TestGroup2",
   ]
}
```
The following is the response JSON from the previous POST method:
```
{
  "action": "KILL_BY_USER",
  "currentStep": "RESOLVE_DEVICES",
   "accessGroupNames":[
      "TestGroup1",
      "TestGroup2",
   ]
  "generation": 4,
  "id": "1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "identityReferences": [
    {
      "link": "https://localhost/mgmt/shared/authz/users/admin"
    }
  ],
  "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate",
  "lastUpdateMicros": 1479242595185322,
  "name": "kill-access-sessions",
  "ownerMachineId": "adf1e56b-bf8c-472a-9b2d-e2dd7199ffbd",
  "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions/1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "startDateTime": "2016-11-15T12:42:31.294-0800",
  "status": "STARTED",
  "userName": "user2",
  "userReference": {
    "link": "https://localhost/mgmt/shared/authz/users/admin"
  },
  "username": "admin"
}
```

## KillClusterAndUsername (Username, list of ClusterNames)

 To kill session for all sessions within a cluster that are associated with a username..  You might wish to find the clustername of an associated device by use of the FindDevClustername response from the DevInfo request.
```
POST:  https://<mgmtip>/mgmt/cm/access/tasks/kill-sessions
{
   "action":"KILL_BY_USER",
   "userName":"user2",
   "clusterNames":[
      "BlueCluster",
      "RedCluster"
   ]
}
```
The following is the response JSON from the previous POST method:
```
{
  "action": "KILL_BY_USER",
  "currentStep": "RESOLVE_DEVICES",
   "clusterNames":[
      "BlueCluster",
      "RedCluster"
   ]
  "generation": 4,
  "id": "1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "identityReferences": [
    {
      "link": "https://localhost/mgmt/shared/authz/users/admin"
    }
  ],
  "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate",
  "lastUpdateMicros": 1479242595185322,
  "name": "kill-access-sessions",
  "ownerMachineId": "adf1e56b-bf8c-472a-9b2d-e2dd7199ffbd",
  "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions/1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "startDateTime": "2016-11-15T12:42:31.294-0800",
  "status": "STARTED",
  "userName": "user2",
  "userReference": {
    "link": "https://localhost/mgmt/shared/authz/users/admin"
  },
  "username": "admin"
}
```




## KillCluster (list of ClusterNames) 
This REST call kills all sessions associated with the cluster name or cluster names irrespective of any usernames.  
```
POST:  https://<mgmtip>/mgmt/cm/access/tasks/kill-sessions
{
   "action":"KILL_ALL_SESSIONS",
   "clusterNames":[
      "BlueCluster",
      "RedCluster"
   ]
}
```
The following is the response JSON from the previous POST method:
```
{
  "action": "KILL_ALL_SESSIONS",
  "currentStep": "RESOLVE_DEVICES",
   "clusterNames":[
      "BlueCluster",
      "RedCluster"
   ]
  "generation": 4,
  "id": "1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "identityReferences": [
    {
      "link": "https://localhost/mgmt/shared/authz/users/admin"
    }
  ],
  "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate",
  "lastUpdateMicros": 1479242595185322,
  "name": "kill-access-sessions",
  "ownerMachineId": "adf1e56b-bf8c-472a-9b2d-e2dd7199ffbd",
  "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions/1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "startDateTime": "2016-11-15T12:42:31.294-0800",
  "status": "STARTED",
  "userReference": {
    "link": "https://localhost/mgmt/shared/authz/users/admin"
  },
  "username": "admin"
}
```


## KillDeviceReferenceAndUsername ( list of Device References, Username) 

To use this call, you must have the name of the user and a device reference.  If you don't have a device reference, you can get it by looking at the FindDevRef response of the DevInfo post request.

Note: To kill sessions in an device that is part of cluster, then it is recommended to use cluster name instead of device references. Refer to prerequisites section for more details.

```
POST:  https://<mgmtip>/mgmt/cm/access/tasks/kill-sessions
{
   "action":"KILL_BY_USER",
   "userName":"user2",
   "deviceReferences":[
      {
         "link":"https://localhost/mgmt/cm/system/machineid-resolver/901695c8-f405-489f-9996-54f7b21da642"
      },
      {
         "link":"https://localhost/mgmt/cm/system/machineid-resolver/3f320100-2177-42e0-8a46-2e33cd3366d"
      }
   ]
}
```
The following is the response JSON from the previous POST method:
```
{
  "action": "KILL_BY_USER",
  "currentStep": "RESOLVE_DEVICES",
  "deviceReferences": [
      {
         "link":"https://localhost/mgmt/cm/system/machineid-resolver/901695c8-f405-489f-9996-54f7b21da642"
      },
      {
         "link":"https://localhost/mgmt/cm/system/machineid-resolver/3f320100-2177-42e0-8a46-2e33cd3366d"
      }
  ],
  "generation": 4,
  "id": "1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "identityReferences": [
    {
      "link": "https://localhost/mgmt/shared/authz/users/admin"
    }
  ],
  "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate",
  "lastUpdateMicros": 1479242595185322,
  "name": "kill-access-sessions",
  "ownerMachineId": "adf1e56b-bf8c-472a-9b2d-e2dd7199ffbd",
  "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions/1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "startDateTime": "2016-11-15T12:42:31.294-0800",
  "status": "STARTED",
  "userName": "user2",
  "userReference": {
    "link": "https://localhost/mgmt/shared/authz/users/admin"
  },
  "username": "admin"
}
```
## KillDeviceReference (list of DeviceReferences)

Note: To kill sessions in an device that is part of cluster, then it is recommended to use cluster name instead of device references. Refer to example in next section.

```
POST:  https://<mgmtip>/mgmt/cm/access/tasks/kill-sessions
{
   "action":"KILL_ALL_SESSIONS",
   "deviceReferences":[
      {
         "link":"https://localhost/mgmt/cm/system/machineid-resolver/901695c8-f405-489f-9996-54f7b21da642"
      },
      {
         "link":"https://localhost/mgmt/cm/system/machineid-resolver/3f320100-2177-42e0-8a46-2e33cd3366d"
      }
   ]
}
```
The following is the response JSON from the previous POST method:
```
{
  "action": "KILL_ALL_SESSIONS",
  "currentStep": "RESOLVE_DEVICES",
  "deviceReferences": [
      {
         "link":"https://localhost/mgmt/cm/system/machineid-resolver/901695c8-f405-489f-9996-54f7b21da642"
      },
      {
         "link":"https://localhost/mgmt/cm/system/machineid-resolver/3f320100-2177-42e0-8a46-2e33cd3366d"
      }
  ],
  "generation": 4,
  "id": "1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "identityReferences": [
    {
      "link": "https://localhost/mgmt/shared/authz/users/admin"
    }
  ],
  "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate",
  "lastUpdateMicros": 1479242595185322,
  "name": "kill-access-sessions",
  "ownerMachineId": "adf1e56b-bf8c-472a-9b2d-e2dd7199ffbd",
  "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions/1834e57c-94a2-42eb-860a-1d5cf67ba9bf",
  "startDateTime": "2016-11-15T12:42:31.294-0800",
  "status": "STARTED",
  "userReference": {
    "link": "https://localhost/mgmt/shared/authz/users/admin"
  },
  "username": "admin"
}
```




# Checking the Response 
 
Once the any of the calls to the various Kill requests is performed, the result of the operation should be checked. 
**Note**: it make take some time for the operation to execute,  and a status to become available.

To check the status, one should perform GET requests on the selfLink returned from the response JSON, and perform them in a loop until the status reaches one of the following:

- FINISHED
- CANCELLED
- FAILED 

Use a select option to reduce the content of the returned JSON to a manageable amount. In addition to the status, result should have the value of COMPLETE.  For a task to be successful, response should have values of status as FINISHED and result as COMPLETE.


To get select fields in the response use below query
```
GET: https://<mgmtip>/mgmt/cm/access/tasks/kill-sessions/1834e57c-94a2-42eb-860a-1d5cf67ba9bf?$select=status,result,resultDetails,errorMessage
```

To get complete response use below query
```
GET: https://<mgmtip>/mgmt/cm/access/tasks/kill-sessions/1834e57c-94a2-42eb-860a-1d5cf67ba9bf
```

### Sample of Successful Response
The following is an sample successful response JSON from the GET method:
```
 {
  "action": "KILL_BY_LIST_OF_SESSIONS",
  "result": "COMPLETE",
  "currentStep": "DONE",
  "endDateTime": "2016-11-15T12:25:35.764-0800",
  "generation": 4,
  "id": "1ab38f85-0178-4ce5-af08-ea5f5fd3ac5e",
  "identityReferences": [
    {
      "link": "https://localhost/mgmt/shared/authz/users/admin"
    }
  ],
  "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate",
  "lastUpdateMicros": 1479241535815782,
  "name": "kill-access-sessions",
  "ownerMachineId": "adf1e56b-bf8c-472a-9b2d-e2dd7199ffbd",
  "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions/1ab38f85-0178-4ce5-af08-ea5f5fd3ac5e",
  "sessions": [
    {
      "sessionIds": [
        "2a5d7604",
        "875f7fed"
      ],
      "deviceReference": {
        "link": "https://localhost/mgmt/cm/system/machineid-resolver/901695c8-f405-489f-9996-54f7b21da642"
      }
    }
  ],
  "startDateTime": "2016-11-15T12:25:35.610-0800",
  "status": "FINISHED",
  "userReference": {
    "link": "https://localhost/mgmt/shared/authz/users/admin"
  },
  "username": "admin"
}
```

### Sample of Failed Response
The following is sample of failed task response JSON from the GET method:
```
{
    "items": [
        {
            "accessGroupNames": [
                "TestGroup123"
            ],
            "action": "KILL_BY_USER",
            "currentStep": "KILL",
            "deviceReferences": [
                {
                    "link": "https://localhost/mgmt/cm/system/machineid-resolver/217270dc-83bc-469d-9b84-36d7ebf2f6be"
                }
            ],
            "endDateTime": "2016-11-30T14:32:23.395-0800",
            "errorMessage": "Internal Error occurred",
            "generation": 3,
            "id": "b3c02bb2-594e-44e1-bce7-eceb607111fb",
            "identityReferences": [
                {
                    "link": "https://localhost/mgmt/shared/authz/users/admin"
                }
            ],
            "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate",
            "lastUpdateMicros": 1480545143445735,
            "name": "kill-access-sessions",
            "ownerMachineId": "da4a4ca7-19f9-4a31-a1c2-004d5557ff10",
            "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions/b3c02bb2-594e-44e1-bce7-eceb607111fb",
            "startDateTime": "2016-11-30T14:32:23.295-0800",
            "status": "FAILED",
            "userName": "user2",
            "userReference": {
                "link": "https://localhost/mgmt/shared/authz/users/admin"
            },
            "username": "admin"
        }
    ],
    "generation": 4,
    "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskcollectionstate",
    "lastUpdateMicros": 1480545143451463,
    "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions"
}
```

### Common Errors
When an error occurs, review the BIG-IQ Centralized Management user interface for device management to determine the details of the failure. In addition to using the user interface, some error information can be determined from the REST API response JSON as shown in the following error.

#### Error generated when an incorrect URI is sent in the REST request.
```
{
  "code": 404,
  "message": "Public URI path not registered",
  "referer": "192.168.101.130",
  "restOperationId": 19541801,
  "errorStack": [
    "com.f5.rest.common.RestWorkerUriNotFoundException: Public URI path not registered",
    "at com.f5.rest.workers.ForwarderPassThroughWorker.cloneAndForwardRequest(ForwarderPassThroughWorker.java:250)",
    "at com.f5.rest.workers.ForwarderPassThroughWorker.onForward(ForwarderPassThroughWorker.java:106)",
    "at com.f5.rest.workers.ForwarderPassThroughWorker.onQuery(ForwarderPassThroughWorker.java:409)",
    "at com.f5.rest.common.RestWorker.callDerivedRestMethod(RestWorker.java:1071)",
    "at com.f5.rest.common.RestWorker.callRestMethodHandler(RestWorker.java:1040)",
    "at com.f5.rest.common.RestServer.processQueuedRequests(RestServer.java:1467)",
    "at com.f5.rest.common.RestServer.access$000(RestServer.java:53)",
    "at com.f5.rest.common.RestServer$1.run(RestServer.java:333)",
    "at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)",
    "at java.util.concurrent.FutureTask.run(FutureTask.java:262)",
    "at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$201(ScheduledThreadPoolExecutor.java:178)",
    "at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:292)",
    "at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)",
    "at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)",
    "at java.lang.Thread.run(Thread.java:745)\n"
  ],
  "kind": ":resterrorresponse"
}
```

#### Task failure when action is not provided.
Task creation will not happen, when required data is missing in the input JSON during POST. Due to this reason, you will not see id or selfLink in the response for validation failures.

```
{
    "code": 400,
    "message": "action is missing.",
    "originalRequestBody": "{\"clusterNames\":[\"ca-cluster\"],\"id\":\"2e4b7a58-0016-44bd-ad4b-fd5a72202ce8\",\"status\":\"CREATED\",\"name\":\"kill-access-sessions\",\"generation\":1,\"lastUpdateMicros\":1480543050548655,\"kind\":\"cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate\",\"selfLink\":\"https://localhost/mgmt/cm/access/tasks/kill-sessions/2e4b7a58-0016-44bd-ad4b-fd5a72202ce8\"}",
    "referer": "192.168.85.81",
    "restOperationId": 30692644,
    "kind": ":resterrorresponse"
}
```

#### Task failure when required input is not available.
```
{
    "code": 400,
    "message": "Request should have atleast one of these fields populated: accessGroupNames , clusterNames , deviceReferences",
    "originalRequestBody": "{\"action\":\"KILL_ALL_SESSIONS\",\"id\":\"d28291d1-3547-4843-8b44-2331e3e40f4e\",\"status\":\"CREATED\",\"name\":\"kill-access-sessions\",\"generation\":1,\"lastUpdateMicros\":1480542944805994,\"kind\":\"cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate\",\"selfLink\":\"https://localhost/mgmt/cm/access/tasks/kill-sessions/d28291d1-3547-4843-8b44-2331e3e40f4e\"}",
    "referer": "192.168.85.81",
    "restOperationId": 30687454,
    "kind": ":resterrorresponse"
}
```

#### Task failure when non-existing or invalid device reference is provided
```
{
    "action": "KILL_BY_LIST_OF_SESSIONS",
    "currentStep": "RESOLVE_DEVICES",
    "endDateTime": "2016-11-30T13:58:42.866-0800",
    "errorMessage": "item not found at /cm/system/machineid-resolver/901695c8-f405-489f-9996-54f7b21da642",
    "generation": 2,
    "id": "89645318-b29f-4a46-9cf8-6f844ebe72f3",
    "identityReferences": [
        {
            "link": "https://localhost/mgmt/shared/authz/users/admin"
        }
    ],
    "kind": "cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate",
    "lastUpdateMicros": 1480543122917061,
    "name": "kill-access-sessions",
    "ownerMachineId": "d5d58cdd-f5b5-4379-9d12-08e28253a16f",
    "result": "PENDING",
    "selfLink": "https://localhost/mgmt/cm/access/tasks/kill-sessions/89645318-b29f-4a46-9cf8-6f844ebe72f3",
    "sessions": [
        {
            "deviceReference": {
                "link": "https://localhost/mgmt/cm/system/machineid-resolver/901695c8-f405-489f-9996-54f7b21da642"
            }
        }
    ],
    "startDateTime": "2016-11-30T13:58:42.860-0800",
    "status": "FAILED",
    "userReference": {
        "link": "https://localhost/mgmt/shared/authz/users/admin"
    },
    "username": "admin"
}
```

#### Task failure when session info is empty for KILL_BY_LIST_OF_SESSIONS action
```
{
    "code": 400,
    "message": "deviceReference is missing",
    "originalRequestBody": "{\"action\":\"KILL_BY_LIST_OF_SESSIONS\",\"sessions\":[{}],\"id\":\"66d1b009-4998-4f24-bb30-00f86c185652\",\"status\":\"CREATED\",\"name\":\"kill-access-sessions\",\"generation\":1,\"lastUpdateMicros\":1480543895438425,\"kind\":\"cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate\",\"selfLink\":\"https://localhost/mgmt/cm/access/tasks/kill-sessions/66d1b009-4998-4f24-bb30-00f86c185652\"}",
    "referer": "192.168.85.81",
    "restOperationId": 30713876,
    "kind": ":resterrorresponse"
}
```

#### Task failure when session ids are missing for KILL_BY_LIST_OF_SESSIONS action
```
{
    "code": 400,
    "message": "sessionIds are missing for https://localhost/mgmt/cm/system/machineid-resolver/217270dc-83bc-469d-9b84-36d7ebf2f6be",
    "originalRequestBody": "{\"action\":\"KILL_BY_LIST_OF_SESSIONS\",\"sessions\":[{\"deviceReference\":{\"link\":\"https://localhost/mgmt/cm/system/machineid-resolver/217270dc-83bc-469d-9b84-36d7ebf2f6be\"}}],\"id\":\"b7f2a143-0440-4cfd-96c3-a17a107856d5\",\"status\":\"CREATED\",\"name\":\"kill-access-sessions\",\"generation\":1,\"lastUpdateMicros\":1480544881158043,\"kind\":\"cm:access:tasks:kill-sessions:accesskillsessionstaskitemstate\",\"selfLink\":\"https://localhost/mgmt/cm/access/tasks/kill-sessions/b7f2a143-0440-4cfd-96c3-a17a107856d5\"}",
    "referer": "192.168.85.81",
    "restOperationId": 233634,
    "kind": ":resterrorresponse"
}
```


# Determine the current status of the BIG-IP device
You can determine the status of BIG-IP devices by performing a specific GET request.
Perform a GET method on the machineid-resolver URI to determine if the BIG-IP device already has trust established. You will want to use the filter option to narrow the returned JSON information to just this particular BIG-IP device. If the value of the items element is not an empty list [], then the trust has already been established. If the value of the items element is an empty list, you must establish trust before you can attempt to discover the device.



# Get info on managed BIG-IP devices   (GetDevInfo)

This set of API calls and operations allow one to retrieve information on managed BIGIP Devices.  
There utility is based on one GET request, and a number of queries of the response.

## The GetDevInfo request 
An API call may be used to get information on BIG-IP devices under management. This API call may be used with our without a filter. When used without a filter, ALL devices under management are returned.  A filter, specifying IP addresses may also be used to get a subset of devices.  
A call to the following URI will return all devices:

    GET: https://<mgmtip>/mgmt/cm/system/machineid-resolver

A call to the following URI (with a filter), will return a subset of all devices:

	GET: https://<mgmtip>/mgmt/cm/system/machineid-resolver?$filter=('address'+eq+'10.255.4.124')
    


## The GetDevInfo response 

The following is the response JSON from the GET method.  Each of the items under cm-access-allBigIpDevices corresponds to individual managed BIG-IP devices for which BIG-IQ Access is enabled. 
```
{
    "items": [
    {
        "uuid": "98901455-6384-47cd-bc41-00a39dfe338f",
        "deviceUri": "https://10.192.123.69:443",
        "machineId": "98901455-6384-47cd-bc41-00a39dfe338f",
        "state": "ACTIVE",
        "address": "10.255.4.124",
        "httpsPort": 443,
        "hostname": "bluebigipveha1.labf.com",
        "version": "12.1.0",
        "product": "BIG-IP",
        "edition": "Final",
        "build": "0.0.1354",
        "restFrameworkVersion": "12.1.0-0.0.1354",
        "managementAddress": "10.192.123.69",
        "mcpDeviceName": "/Common/bluebigipveha1",
        "trustDomainGuid": "5189f81c-96be-4449-b4110050560102e7",
        "properties": {
            "cm:gui:module": [
                "Access",
                "BigIPDevice",
                "adc"
            ],
            "modules": [
                "All Access managed BIG-IP devices"
            ],
            "cm-adccore-allbigipDevices": {
                "supportsBadgerEnhs": true,
                "supportsRest": true,
                "supportsAlpineEnhs": true,
                "lastDiscoveredDateTime": "2016-11-10T19:06:14.804Z",
                "imported": true,
                "clusterName": "BlueCluster",
                "restrictsPortTranslationStatelessVirtual": true,
                "requiresDhcpProfileInDhcpVirtualServer": true,
                "importStatus": "FINISHED",
                "discoveryStatus": "FINISHED",
                "importedDateTime": "2016-11-10T19:14:39.003Z",
                "lastUserDiscoveredDateTime": "2016-11-10T19:06:14.804Z",
                "modules": [
                    "All Access managed BIG-IP devices"
                ],
                "cm:gui:module": [
                    "Access",
                    "BigIPDevice",
                    "adc"
                ],
                "discovered": true,
                "supportsClassification": true
            },
            "cm-bigip-allBigIpDevices": {
                "shared:resolver:device-groups:discoverer": "d5d58cdd-f5b5-4379-9d12-08e28253a16f",
                "cm:gui:module": [
                    "BigIPDevice"
                ],
                "modules": []
            },
            "cm-bigip-allDevices": {
                "shared:resolver:device-groups:discoverer": "d5d58cdd-f5b5-4379-9d12-08e28253a16f",
                "cm:gui:module": [],
                "modules": []
            },
            "cm-access-allBigIpDevices": {
                "discovered": true,
                "imported": true,
                "clusterName": "BlueCluster",
                "supportsRest": true,
                "supports_13_0_Enhs": false,
                "supportsCascadeEnhs": true,
                "lastDiscoveredDateTime": "2016-11-10T19:15:18.963Z",
                "lastUserDiscoveredDateTime": "2016-11-10T19:15:18.963Z",
                "cm:access:access-group-name": "TestGroup",
                "cm:access:source-device": true,
                "cm:access:access-group-device-link": "https://localhost/mgmt/shared/resolver/device-groups/CA/devices/98901455-6384-47cd-bc41-00a39dfe338f",
                "cm:access:import-version": "12.1.0",
                "cm:access:access-group-link": "https://localhost/mgmt/shared/resolver/device-groups/TestGroup",
                "importedDateTime": "2016-11-10T19:17:04.459Z",
                "discoveryStatus": "FINISHED",
                "importStatus": "FINISHED",
                "cm:gui:module": [
                    "Access"
                ],
                "modules": [
                    "All Access managed BIG-IP devices"
                ]
            },
            "cm-bigip-cluster_BlueCluster": {
                "clusterName": "BlueCluster",
                "shared:resolver:device-groups:discoverer": "da4a4ca7-19f9-4a31-a1c2-004d5557ff10",
                "cm:gui:module": [],
                "modules": []
            },
            "cm-access-allDevices": {
                "clusterName": "BlueCluster",
                "cm:gui:module": [
                    "Access"
                ],
                "modules": [
                    "All Access managed BIG-IP devices"
                ]
            },
            "TestGroup": {
                "discovered": true,
                "imported": false,
                "supportsRest": true,
                "supports_13_0_Enhs": false,
                "supportsCascadeEnhs": true,
                "discoveryStatus": "FINISHED",
                "lastDiscoveredDateTime": "2016-10-26T04:15:56.356Z",
                "lastUserDiscoveredDateTime": "2016-10-26T04:15:56.356Z",
                "cm:access:all-bigip-device-link": "https://localhost/mgmt/shared/resolver/device-groups/cm-access-allBigIpDevices/devices/98901455-6384-47cd-bc41-00a39dfe338f",
                "cm:access:import-version": "12.1.0",
                "cm:access:source-device": true,
                "cm:gui:module": [
                    "Access"
                ],
                "modules": [
                    "All Access managed BIG-IP devices"
                ]
            },
            "cm-adccore-allDevices": {
                "cm:gui:module": [],
                "modules": []
            }
        },
        "isClustered": false,
        "isVirtual": true,
        "isLicenseExpired": false,
        "slots": [
            {
                "volume": "HD1.1",
                "product": "BIG-IP",
                "version": "12.1.0",
                "build": "0.0.1354",
                "isActive": true
            },
            {
                "volume": "HD1.3",
                "product": "BIG-IP",
                "version": "12.0.0",
                "build": "0.0.606",
                "isActive": false
            }
        ],
        "generation": 67,
        "lastUpdateMicros": 1479332833705505,
        "kind": "shared:resolver:device-groups:restdeviceresolverdevicestate",
        "selfLink": "https://localhost/mgmt/cm/system/machineid-resolver/98901455-6384-47cd-bc41-00a39dfe338f"
    }
    ],
    "generation": 0,
    "lastUpdateMicros": 0,
    "selfLink": "http://localhost:8100/cm/system/machineid-resolver/?$filter=%27address%27+eq+%2710.192.123.198%27"
}
```

Checking if Trust is established
-----------------------------

In the response to the GET method, you see trust is established since the following data is found in the list:
```
"properties": {
    "cm:gui:module": [
        "BigIPDevice"
    ]
```

##Checking  if Access Discovery is completed 
In the response to the GET method, if the Access value is found in the list, the Access Policy Manager service has already been discovered; the adc value represents the Local Traffic service and this must be found in order to continue with the Access Policy Manager discovery workflow.

```
"properties": {
    "cm:gui:module": [
        "BigIPDevice",
        "adc",
        "Access"
    ]
```

## Check if Access Configuration is Imported
In the response to the GET method, you see access import is done if value of imported property is true in cm-access-allBigIpDevices:

```
"properties": {
    "cm-access-allBigIpDevices": {
        "imported": true
    }
}
```

## Find Access Group Name of the device (FindDevAccessGroupName)
This is applicable only if the device is imported. In the response to the GET method, value of cm:access:access-group-name property contains the access group name. This property is present in cm-access-allBigIpDevices, which is present inside properties field value. In this example, access group name is TestGroup:

```
"properties": {
    "cm-access-allBigIpDevices": {
        "cm:access:access-group-name": "TestGroup"
    }
}
```


## Find Cluster Name of a device that is part of Cluster (FindDevClustername)
This is applicable only if the device is discovered and part of cluster.

In the response to the GET method, value of clusterName property contains the cluster name. This property is present in cm-access-allBigIpDevices, which is present inside properties field value. In this example, cluster name is BlueCluster:

```
"properties": {
    "cm-access-allBigIpDevices": {
        "clusterName": "BlueCluster"
    }
}
```

## Find device reference of an device (FindDevRef)
In the response to the GET method, value of selfLink is the device reference of the device.

```
{
	"selfLink": "https://localhost/mgmt/cm/system/machineid-resolver/98901455-6384-47cd-bc41-00a39dfe338f"
}
```

##List All Access Config Groups
To get a list of all access config group name, perform following GET on device groups resolver API with filter to retrieve only access config group. In the response, groupName refers to access config group name.

```
GET: https://<mgmtip>/mgmt/shared/resolver/device-groups/?$filter='properties/cm:access:access_group'+eq+'true'&$select=groupName,displayName
```

The following is the response JSON from the GET method:
```
{
    "selfLink": "https://localhost/mgmt/shared/resolver/device-groups",
    "totalItems": 1,
    "items": [
        {
            "displayName": "TestGroup",
            "groupName": "TestGroup"
        },
        {
            "displayName": "TestGroup2",
            "groupName": "TestGroup2"
        }
    ],
    "generation": 23,
    "kind": "shared:resolver:device-groups:devicegroupcollectionstate",
    "lastUpdateMicros": 1479942921954266
}
```

### API Reference