## Pool and Pool Members Management

### Overview

The BIG-IQ system allows users to progammatically enable/disable/force offline pools and pool members using REST API calls.

#### Prerequisites
- Complete the initial setup using the setup wizard so that the BIG-IQ Centralized Management system is operational.
- Discover and establish trust with at least one BIG-IP device provisioned with LTM services.

### Overview of API 

1.	View existing Pool objects.
2.	Create a Pool object
..*	Edit Pool an existing object
3.	View existing Pool Member objects associated with a specific Pool object.
4.	Create a Pool Member object
..1.	Edit a Pool Member 
..2.	Use self service to Enable/Disable/Force Offline
5.	Deploy Pool objects to BIG-IP
..1.	Deploy new Pool and Pool Member objects
..2.	Deploy edits to Pool and Pool Member objects
..3.	Deploy to remove Pool and Pool Member objects.

## Pools

A user can view and filter the pools collection if they have the proper RBAC permissions to perform GET polls on '/mgmt/cm/adc-core/working-config/ltm/pool'.

#### Viewing active pools on a BIG-IQ device

GET `/mgmt/cm/adc-core/working-config/ltm/pool`

Response: 200
```json
{
  "items": [
    {
      "allowNat": true,
      "allowSnat": true,
      "ignorePersistedWeight": false,
      "ipTosToClientControl": "pass-through",
      "ipTosToServerControl": "pass-through",
      "linkQosToClient": 65535,
      "linkQosToServer": 65535,
      "loadBalancingMode": "round-robin",
      "minActiveMembers": 0,
      "queueDepthLimit": 0,
      "enableQueueOnConnectionLimit": false,
      "queueTimeLimit": 0,
      "reselectTries": 0,
      "serviceDownAction": "none",
      "slowRampTime": 10,
      "membersCollectionReference": {
        "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/b045388c-a454-3b53-a190-940d665d0a1a/members",
        "isSubcollection": true
      },
      "partition": "Common",
      "deviceReference": {
        "id": "eb0f77b6-ee63-449c-8e90-ce5498926a1c",
        "name": "ip65.f5.pd.com",
        "kind": "shared:resolver:device-groups:restdeviceresolverdevicestate",
        "machineId": "eb0f77b6-ee63-449c-8e90-ce5498926a1c",
        "link": "https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/eb0f77b6-ee63-449c-8e90-ce5498926a1c"
      },
      "name": "pool-east",
      "description": "LAB",
      "id": "b045388c-a454-3b53-a190-940d665d0a1a",
      "generation": 1,
      "lastUpdateMicros": 1480628497415304,
      "kind": "cm:adc-core:working-config:ltm:pool:adcpoolstate",
      "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/b045388c-a454-3b53-a190-940d665d0a1a"
    },
    {
      "allowNat": true,
      "allowSnat": true,
      "ignorePersistedWeight": false,
      "ipTosToClientControl": "pass-through",
      "ipTosToServerControl": "pass-through",
      "linkQosToClient": 65535,
      "linkQosToServer": 65535,
      "loadBalancingMode": "round-robin",
      "minActiveMembers": 0,
      "queueDepthLimit": 0,
      "enableQueueOnConnectionLimit": false,
      "queueTimeLimit": 0,
      "reselectTries": 0,
      "serviceDownAction": "none",
      "slowRampTime": 10,
      "membersCollectionReference": {
        "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/dcf33ae3-ac2e-3984-95cf-6afd341e90e7/members",
        "isSubcollection": true
      },
      "partition": "Common",
      "deviceReference": {
        "id": "00b1ece7-c700-47ab-8ce0-4bcea4b3b9e9",
        "name": "ip223.f5.pd.com",
        "kind": "shared:resolver:device-groups:restdeviceresolverdevicestate",
        "machineId": "00b1ece7-c700-47ab-8ce0-4bcea4b3b9e9",
        "link": "https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/00b1ece7-c700-47ab-8ce0-4bcea4b3b9e9"
      },
      "name": "pool-west",
      "description": "RESERVE",
      "id": "dcf33ae3-ac2e-3984-95cf-6afd341e90e7",
      "generation": 1,
      "lastUpdateMicros": 1480628465441408,
      "kind": "cm:adc-core:working-config:ltm:pool:adcpoolstate",
      "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/dcf33ae3-ac2e-3984-95cf-6afd341e90e7"
    }
  ],
  "generation": 7,
  "kind": "cm:adc-core:working-config:ltm:pool:adcpoolcollectionstate",
  "lastUpdateMicros": 1480628497456842,
  "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool"
}
```

#### Searching for pools by fields

Users can search the pool collection for a value in a field using exact or wild card matching by appending "?$filter=PROPERTYNAME eq 'searchValue'".

Searching for any pool objects that contain "LAB" in the description would look like:

GET ```/mgmt/cm/adc-core/working-config/ltm/pool?$filter=description eq '*LAB*'```

Response: 200
```json
{
  "totalItems": 1,
  "items": [
    {
      "ignorePersistedWeight": false,
      "membersCollectionReference": {
        "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/b045388c-a454-3b53-a190-940d665d0a1a/members",
        "isSubcollection": true
      },
      "linkQosToServer": 65535,
      "slowRampTime": 10,
      "reselectTries": 0,
      "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/b045388c-a454-3b53-a190-940d665d0a1a",
      "ipTosToServerControl": "pass-through",
      "kind": "cm:adc-core:working-config:ltm:pool:adcpoolstate",
      "deviceReference": {
        "id": "eb0f77b6-ee63-449c-8e90-ce5498926a1c",
        "name": "ip65.f5.pd.com",
        "link": "https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/eb0f77b6-ee63-449c-8e90-ce5498926a1c",
        "machineId": "eb0f77b6-ee63-449c-8e90-ce5498926a1c",
        "kind": "shared:resolver:device-groups:restdeviceresolverdevicestate"
      },
      "minActiveMembers": 0,
      "id": "b045388c-a454-3b53-a190-940d665d0a1a",
      "loadBalancingMode": "round-robin",
      "partition": "Common",
      "lastUpdateMicros": 1480628497415304,
      "allowSnat": true,
      "enableQueueOnConnectionLimit": false,
      "ipTosToClientControl": "pass-through",
      "description": "LAB",
      "name": "pool-east",
      "serviceDownAction": "none",
      "queueTimeLimit": 0,
      "linkQosToClient": 65535,
      "allowNat": true,
      "generation": 1,
      "queueDepthLimit": 0
    }
  ],
  "generation": 7,
  "kind": "cm:adc-core:working-config:ltm:pool:adcpoolcollectionstate",
  "lastUpdateMicros": 1480628497456842,
  "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool"
}
```
#### Creating a pool object with a monitor

POST ```/mgmt/cm/adc-core/working-config/ltm/pool```

BODY
```json
{
   "allowNat":true,
   "allowSnat":true,
   "description":"",
   "ignorePersistedWeight":false,
   "ipTosToClient":null,
   "ipTosToServer":null,
   "linkQosToClient":65535,
   "linkQosToServer":65535,
   "loadBalancingMode":"round-robin",
   "monitorReferences":[],
   "minActiveMembers":0,
   "minUpMembers":null,
   "queueDepthLimit":0,
   "enableQueueOnConnectionLimit":false,
   "queueTimeLimit":0,
   "serviceDownAction":"none",
   "slowRampTime":10,
   "reselectTries":0,
   "membersReference":{
      "link":""
   },
   "profiles":[],
   "requestQueueTimeLimit":0,
   "deviceReference":{
      "link":"https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/465b8fe0-4441-4ece-a8b6-04fcd613ff83"
   },
   "partition":"Common",
   "kind":"cm:adc-core:working-config:ltm:pool:adcpoolstate",
   "name":"new-pool-with-http-monitor",
   "monitorHttpReferences":[
      {
         "name":"http",
         "partition":"Common",
         "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/monitor/http/ad348aed-0309-36d5-b5cd-c5b9e00cbb26"
      }
   ],
   "ipTosToClientControl":"pass-through",
   "ipTosToServerControl":"pass-through"
}
```

Response: 200
```json
{
   "allowNat":true,
   "allowSnat":true,
   "ignorePersistedWeight":false,
   "ipTosToClientControl":"pass-through",
   "ipTosToServerControl":"pass-through",
   "linkQosToClient":65535,
   "linkQosToServer":65535,
   "loadBalancingMode":"round-robin",
   "minActiveMembers":0,
   "queueDepthLimit":0,
   "enableQueueOnConnectionLimit":false,
   "queueTimeLimit":0,
   "reselectTries":0,
   "serviceDownAction":"none",
   "slowRampTime":10,
   "membersCollectionReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/56e0bcd8-b3e7-358b-bf0f-965fc798e507/members",
      "isSubcollection":true
   },
   "monitorHttpReferences":[
      {
         "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/monitor/http/ad348aed-0309-36d5-b5cd-c5b9e00cbb26"
      }
   ],
   "partition":"Common",
   "deviceReference":{
      "id":"465b8fe0-4441-4ece-a8b6-04fcd613ff83",
      "name":"ip66.f5.pd.com",
      "kind":"shared:resolver:device-groups:restdeviceresolverdevicestate",
      "machineId":"465b8fe0-4441-4ece-a8b6-04fcd613ff83",
      "link":"https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/465b8fe0-4441-4ece-a8b6-04fcd613ff83"
   },
   "name":"new-pool-with-http-monitor",
   "id":"56e0bcd8-b3e7-358b-bf0f-965fc798e507",
   "generation":1,
   "lastUpdateMicros":1480549590312880,
   "kind":"cm:adc-core:working-config:ltm:pool:adcpoolstate",
   "selfLink":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/56e0bcd8-b3e7-358b-bf0f-965fc798e507"
}
```

#### Deploying a pool object to a device

This task requires a deviceReference and the REST reference for the pool object to be deployed. Users can configure the task verification by toggling the boolean attribute "skipVerifyConfig". Users can also pause the deployment after evaluation by setting the property "skipDistribution" to "true". 

POST ```/mgmt/cm/adc-core/tasks/deploy-configuration```

BODY
```json
{
   "skipVerifyConfig":false,
   "skipDistribution":false,
   "snapshotReference":null,
   "objectsToDeployReferences":[
      {
         "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/e80bf7e0-41ac-3056-a114-4a1b2ddc0b6c"
      }
   ],
   "name":"add",
   "deploySpecifiedObjectsOnly":false,
   "deviceReferences":[
      {
         "link":"https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/00b1ece7-c700-47ab-8ce0-4bcea4b3b9e9"
      }
   ]
}
```

Response: 202
```json
{
   "skipDistribution":false,
   "deviceReferences":[
      {
         "link":"https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/00b1ece7-c700-47ab-8ce0-4bcea4b3b9e9"
      }
   ],
   "skipVerifyConfig":false,
   "objectsToDeployReferences":[
      {
         "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/e80bf7e0-41ac-3056-a114-4a1b2ddc0b6c"
      }
   ],
   "deploySpecifiedObjectsOnly":false,
   "id":"2120a0bc-b311-407e-844f-f89e151f0bb5",
   "status":"STARTED",
   "name":"add",
   "userReference":{
      "link":"https://localhost/mgmt/shared/authz/users/admin"
   },
   "identityReferences":[
      {
         "link":"https://localhost/mgmt/shared/authz/users/admin"
      }
   ],
   "ownerMachineId":"3b786166-8069-45ae-b633-60e7416ef7a0",
   "taskWorkerGeneration":1,
   "generation":1,
   "lastUpdateMicros":1480703520843810,
   "kind":"cm:adc-core:tasks:deploy-configuration:deployconfigtaskstate",
   "selfLink":"https://localhost/mgmt/cm/adc-core/tasks/deploy-configuration/2120a0bc-b311-407e-844f-f89e151f0bb5"
}
```

The 202 response contains details about the self service task along with the "status" of "STARTED". Post GET polls to the selfLink to check the "status" property to report "FINISHED" or "FAILED".
If the task was configured to have "skipVerifyConfig" to "false", the self service task will eventually be populated with a "verifyConfigReference". Performing a GET on the "verifyConfigReference.link" will provide any warnings and errors found during verification. Only critical errors will prevent users from deploying the objects to the BIG-IP.
If the task was configured to have "skipDistribution" set to "true", the task will pause after an evaluation has been created. This allows users to review their changes before deployment. Users can resume the deployment by posting a PATCH to the task with the following body:
```json
{"skipDistribution": false, "status": "STARTED"}
```

#### Edit a pool object

##### Attaching a new health monitor to the pool.

This requires a proper monitor name and REST reference.

PATCH ```/mgmt/cm/adc-core/working-config/ltm/pool/{id}```

BODY
```json
{
   "monitorHttpReferences":[
      {
         "name":"http",
         "partition":"Common",
         "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/monitor/http/ad348aed-0309-36d5-b5cd-c5b9e00cbb26"
      }
   ]
}
```

Response: 200
```json
{
  "allowNat": true,
  "allowSnat": true,
  "ignorePersistedWeight": false,
  "ipTosToClientControl": "pass-through",
  "ipTosToServerControl": "pass-through",
  "linkQosToClient": 65535,
  "linkQosToServer": 65535,
  "loadBalancingMode": "round-robin",
  "minActiveMembers": 0,
  "queueDepthLimit": 0,
  "enableQueueOnConnectionLimit": false,
  "queueTimeLimit": 0,
  "reselectTries": 0,
  "serviceDownAction": "none",
  "slowRampTime": 10,
  "membersCollectionReference": {
    "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/b045388c-a454-3b53-a190-940d665d0a1a/members",
    "isSubcollection": true
  },
  "monitorHttpReferences": [
    {
      "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/monitor/http/ad348aed-0309-36d5-b5cd-c5b9e00cbb26"
    }
  ],
  "partition": "Common",
  "deviceReference": {
    "id": "eb0f77b6-ee63-449c-8e90-ce5498926a1c",
    "name": "ip65.f5.pd.com",
    "kind": "shared:resolver:device-groups:restdeviceresolverdevicestate",
    "machineId": "eb0f77b6-ee63-449c-8e90-ce5498926a1c",
    "link": "https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/eb0f77b6-ee63-449c-8e90-ce5498926a1c"
  },
  "name": "pool-east",
  "description": "LAB",
  "id": "b045388c-a454-3b53-a190-940d665d0a1a",
  "generation": 2,
  "lastUpdateMicros": 1480630096304382,
  "kind": "cm:adc-core:working-config:ltm:pool:adcpoolstate",
  "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/b045388c-a454-3b53-a190-940d665d0a1a"
}
```

#### Attaching pool objects to role permissions

Role permissions can be updated to give user roles the ability to view/edit/delete pool objects and pool members. 

##### Prerequisites
User must be associated with a "Pool Member Operator" role.

PUT ```/mgmt/shared/authz/users/{user role name}```

BODY
```json
{
...
    resources: [
        ...
        {resourceMask: "/mgmt/cm/adc-core/working-config/ltm/pool/{id}", restMethod: "GET"},
        {resourceMask: "/mgmt/cm/adc-core/working-config/ltm/pool/{id}/members", restMethod: "GET"},
        {resourceMask: "/mgmt/cm/adc-core/working-config/ltm/pool/{id}/members/*", restMethod: "GET"},
        {resourceMask: "/mgmt/cm/adc-core/working-config/ltm/pool/{id}/members/*/*", restMethod: "POST"},
        ...
    ]
...
}
```
Note: Provisioning a user to GET "/mgmt/cm/adc-core/working-config/ltm/pool/{id}" does not automatically grant permissions to subcollections.

#### Removing a pool object and deploying changes

DELETE ```/mgmt/cm/adc-core/working-config/ltm/pool/{id}```

The pool object will no longer exist in the working config of the BIG-IQ. To deploy these changes to a device, the device reference is needed.

POST ```/mgmt/cm/adc-core/tasks/deploy-configuration```

BODY
```json
{  
   "skipVerifyConfig":false,
   "skipDistribution":false,
   "snapshotReference":null,
   "objectsToDeployReferences":[  

   ],
   "name":"removedPool",
   "deviceReferences":[  
      {  
         "link":"https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/00b1ece7-c700-47ab-8ce0-4bcea4b3b9e9"
      }
   ]
}
```

Response: 202
```json
{  
   "skipDistribution":false,
   "deviceReferences":[
      {
         "link":"https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/00b1ece7-c700-47ab-8ce0-4bcea4b3b9e9"
      }
   ],
   "skipVerifyConfig":false,
   "objectsToDeployReferences":[
   ],
   "id":"c1a3501f-293f-45e4-b3a0-29ff6c59203b",
   "status":"STARTED",
   "name":"removedPool",
   "userReference":{
      "link":"https://localhost/mgmt/shared/authz/users/admin"
   },
   "identityReferences":[
      {
         "link":"https://localhost/mgmt/shared/authz/users/admin"
      }
   ],
   "ownerMachineId":"3b786166-8069-45ae-b633-60e7416ef7a0",
   "taskWorkerGeneration":1,
   "generation":1,
   "lastUpdateMicros":1480705617532917,
   "kind":"cm:adc-core:tasks:deploy-configuration:deployconfigtaskstate",
   "selfLink":"https://localhost/mgmt/cm/adc-core/tasks/deploy-configuration/c1a3501f-293f-45e4-b3a0-29ff6c59203b"
}
```
Note: Partial deployment for removing pools from the BIG-IP is not supported.

The 202 response contains details about the self service task along with the "status" of "STARTED". Post GET polls to the selfLink to check the "status" property to report "FINISHED" or "FAILED".


## Pool Members

#### Prerequisites

- A pool object must exist.
- A node object must exist.

#### Viewing pool members

GET ```/mgmt/cm/adc-core/working-config/ltm/pool/{id}/members```

Response: 200
```json
{
  "items": [
    {
      "connectionLimit": 0,
      "port": 80,
      "priorityGroup": 0,
      "rateLimit": "disabled",
      "ratio": 1,
      "sessionConfig": "user-enabled",
      "stateConfig": "user-up",
      "nodeReference": {
        "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/node/36d49760-3d0e-3368-a679-52e79ff44227"
      },
      "partition": "Common",
      "name": "testNode:80",
      "description": "a test member",
      "id": "e93af93a-c397-39ba-853f-c2808b818ef3",
      "generation": 1,
      "lastUpdateMicros": 1480551570546280,
      "kind": "cm:adc-core:working-config:ltm:pool:members:adcpoolmemberstate",
      "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/56e0bcd8-b3e7-358b-bf0f-965fc798e507/members/e93af93a-c397-39ba-853f-c2808b818ef3"
    }
  ],
  "generation": 2,
  "kind": "cm:adc-core:working-config:ltm:pool:members:adcpoolmembercollectionstate",
  "lastUpdateMicros": 1480551570582031,
  "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/56e0bcd8-b3e7-358b-bf0f-965fc798e507/members"
}
```

#### Creating a pool member

POST ```/mgmt/cm/adc-core/working-config/ltm/pool/{id}/members```

BODY
```json
{
   "nodeReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/node/36d49760-3d0e-3368-a679-52e79ff44227"
   },
   "ratio":1,
   "priorityGroup":0,
   "connectionLimit":0,
   "rateLimit":"disabled",
   "name":"testNode:80",
   "description":"a test member",
   "partition":"Common",
   "port":"80"
}
```
Note: Pool names must be in the format "{node reference name}:{port}"

Response: 200
```json
{
   "connectionLimit":0,
   "port":80,
   "priorityGroup":0,
   "rateLimit":"disabled",
   "ratio":1,
   "sessionConfig":"user-enabled",
   "stateConfig":"user-up",
   "nodeReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/node/36d49760-3d0e-3368-a679-52e79ff44227"
   },
   "partition":"Common",
   "name":"testNode:80",
   "description":"a test member",
   "id":"e93af93a-c397-39ba-853f-c2808b818ef3",
   "generation":1,
   "lastUpdateMicros":1480551570546280,
   "kind":"cm:adc-core:working-config:ltm:pool:members:adcpoolmemberstate",
   "selfLink":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/56e0bcd8-b3e7-358b-bf0f-965fc798e507/members/e93af93a-c397-39ba-853f-c2808b818ef3"
}
```

#### Removing a pool member

DELETE ```/mgmt/cm/adc-core/working-config/ltm/pool/{pool-id}/members/{member-id}```

#### Enabling a pool member using self service

POST ```/mgmt/cm/adc-core/tasks/self-service```

BODY
```json
{
   "name":"Self-Service_someNode:80",
   "resourceReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/dcf33ae3-ac2e-3984-95cf-6afd341e90e7/members/a8aedfee-722c-39e1-a464-e8a2d352d8f2"
   },
   "operation":"enable"
}
```

Response: 202
```json
{
   "resourceReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/dcf33ae3-ac2e-3984-95cf-6afd341e90e7/members/a8aedfee-722c-39e1-a464-e8a2d352d8f2"
   },
   "operation":"enable",
   "id":"865ec76a-02a2-47f1-a007-f07010b18177",
   "status":"STARTED",
   "name":"Self-Service_someNode:80",
   "userReference":{
      "link":"https://localhost/mgmt/shared/authz/users/admin"
   },
   "identityReferences":[
      {
         "link":"https://localhost/mgmt/shared/authz/users/admin"
      }
   ],
   "ownerMachineId":"3b786166-8069-45ae-b633-60e7416ef7a0",
   "taskWorkerGeneration":1,
   "generation":1,
   "lastUpdateMicros":1480632847604821,
   "kind":"cm:adc-core:tasks:self-service:selfservicetaskitemstate",
   "selfLink":"https://localhost/mgmt/cm/adc-core/tasks/self-service/865ec76a-02a2-47f1-a007-f07010b18177"
}
```

The 202 response contains details about the self service task along with the "status" of "STARTED". Post GET polls to the selfLink to check the "status" property to report "FINISHED" or "FAILED".

#### Disabling a pool member using self service

POST ```/mgmt/cm/adc-core/tasks/self-service```

BODY
```json
{
   "name":"Self-Service_someNode:80",
   "resourceReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/dcf33ae3-ac2e-3984-95cf-6afd341e90e7/members/a8aedfee-722c-39e1-a464-e8a2d352d8f2"
   },
   "operation":"disable"
}
```

Response: 202
```json
{
   "resourceReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/dcf33ae3-ac2e-3984-95cf-6afd341e90e7/members/a8aedfee-722c-39e1-a464-e8a2d352d8f2"
   },
   "operation":"disable",
   "id":"40f8385a-9f76-4377-9b9e-dccfd1c0d089",
   "status":"STARTED",
   "name":"Self-Service_someNode:80",
   "userReference":{
      "link":"https://localhost/mgmt/shared/authz/users/admin"
   },
   "identityReferences":[
      {
         "link":"https://localhost/mgmt/shared/authz/users/admin"
      }
   ],
   "ownerMachineId":"3b786166-8069-45ae-b633-60e7416ef7a0",
   "taskWorkerGeneration":1,
   "generation":1,
   "lastUpdateMicros":1480632998507447,
   "kind":"cm:adc-core:tasks:self-service:selfservicetaskitemstate",
   "selfLink":"https://localhost/mgmt/cm/adc-core/tasks/self-service/40f8385a-9f76-4377-9b9e-dccfd1c0d089"
}
```

The 202 response contains details about the self service task along with the "status" of "STARTED". Post GET polls to the selfLink to check the "status" property to report "FINISHED" or "FAILED".

#### Force offline a pool member using self service

POST ```/mgmt/cm/adc-core/tasks/self-service```

BODY
```json
{
   "name":"Self-Service_someNode:80",
   "resourceReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/dcf33ae3-ac2e-3984-95cf-6afd341e90e7/members/a8aedfee-722c-39e1-a464-e8a2d352d8f2"
   },
   "operation":"force-offline"
}
```


Response: 202
```json
{
   "resourceReference":{
      "link":"https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/dcf33ae3-ac2e-3984-95cf-6afd341e90e7/members/a8aedfee-722c-39e1-a464-e8a2d352d8f2"
   },
   "operation":"force-offline",
   "id":"bfccaa9d-7add-4ed6-82a3-5e5026ee98a6",
   "status":"STARTED",
   "name":"Self-Service_someNode:80",
   "userReference":{
      "link":"https://localhost/mgmt/shared/authz/users/admin"
   },
   "identityReferences":[
      {
         "link":"https://localhost/mgmt/shared/authz/users/admin"
      }
   ],
   "ownerMachineId":"3b786166-8069-45ae-b633-60e7416ef7a0",
   "taskWorkerGeneration":1,
   "generation":1,
   "lastUpdateMicros":1480633122692313,
   "kind":"cm:adc-core:tasks:self-service:selfservicetaskitemstate",
   "selfLink":"https://localhost/mgmt/cm/adc-core/tasks/self-service/bfccaa9d-7add-4ed6-82a3-5e5026ee98a6"
}
```

The 202 response contains details about the self service task along with the "status" of "STARTED". Post GET polls to the selfLink to check the "status" property to report "FINISHED" or "FAILED".

### API Reference