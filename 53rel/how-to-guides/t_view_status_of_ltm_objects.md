## View Status of LTM objects

### Overview
When you manage your BIG-IP devices with BIG-IQ software, you can review the status of applications across your data centers using the BIG-IQ REST API.

### Prerequisites

Make certain that the following prerequisites have been met.

- All BIG-IP devices are operational and have the services provisioned that will be managed by the BIG-IQ Centralized Management device.
- The BIG-IQ Centralized Management device is operational, has completed the setup wizard, and completed any other needed configuration.
- Trust has been established between the BIG-IP device and the BIG-IQ Centralized Management device, and the current configuration of the BIG-IP device has been discovered on the BIG-IQ Centralized Management device.

Note: When you perform these tasks using the example code provided, review the listed IP addresses and change them as appropriate for your environment. For example, if you are not running the script directly on the BIG-IQ device, change localhost to be the IP address of the BIG-IQ Centralized Management device.

### Search for an application's configuration objects
You can use this call to locate the configuration objects on a managed device. Using the IP address, hostname, or another specific property, you can get information about the configuration objects on the managed device.

#### 1. Locate a list of configuration object kinds
You can retrieve a list of available kinds for LTM, Network, and System objects. A typical GET looks like this:
```
GET https://localhost/mgmt/cm/adc-core/utility/object-kinds

Results:
{
    "items": [
		...
        {
            "displayName": "Virtual Server",
            "itemKind": "cm:adc-core:working-config:ltm:virtual:adcvirtualstate"
        },
        ...
    ]
    "generation": 0,
    "lastUpdateMicros": 0
}
```

#### 2. Locate a virtual server by address
You can retrieve a listing of the current configuration values for a virtual server (including its selfLink), by sending a query to its IP address. A typical query looks like this:
```
GET https://localhost/mgmt/shared/index/config?$filter=allContent eq '10.145.195.168*' and kind eq 'cm:adc-core:working-config:ltm:virtual:adcvirtualstate'

Results:
{
    "totalItems": 1,
    "items": [
        {
            "translateAddress": "enabled",
            "sourcePort": "preserve",
            "state": "enabled",
            "addressStatus": "yes",
            "mask": "255.255.255.255",
            "vlansEnabled": "disabled",
            "autoLasthop": "default",
            "kind": "cm:adc-core:working-config:ltm:virtual:adcvirtualstate",
            "mirror": "disabled",
            "id": "8fbe1abd-a87d-30e9-a1ca-904f0c2018b6",
            "partition": "Common",
            "lastUpdateMicros": 1479501532321481,
            "defaultSslPersistenceReference": {
                "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/persistence/ssl/c6f31f8f-27f7-3554-808c-f3c133ebe861"
            },
            "nat64": "disabled",
            "name": "owa-app",
            "connectionLimit": 0,
            "generation": 4,
            "poolReference": {
                "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/8928f8af-3bfb-3a1f-ab25-471ccef7a1c0"
            },
            "sourceAddress": "0.0.0.0/0",
            "destinationFullPath": "10.145.195.168:80",
            "profilesCollectionReference": {
                "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual/8fbe1abd-a87d-30e9-a1ca-904f0c2018b6/profiles",
                "isSubcollection": true
            },
            "gtmScore": 0,
            "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual/8fbe1abd-a87d-30e9-a1ca-904f0c2018b6",
            "ipProtocol": "tcp",
            "rateLimitMode": "object",
            "deviceReference": {
                "id": "4d01b7c8-63d5-465f-8ef1-cc8795f32abb",
                "name": "bigip1.pdsea.f5net.com",
                "link": "https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/4d01b7c8-63d5-465f-8ef1-cc8795f32abb",
                "machineId": "4d01b7c8-63d5-465f-8ef1-cc8795f32abb",
                "kind": "shared:resolver:device-groups:restdeviceresolverdevicestate"
            },
            "rateLimit": "disabled",
            "translatePort": "enabled",
            "sourceAddressTranslation": {
                "type": "none"
            }
        }
    ]
}
```
#### 3. Get the latest availability of the Virtual Server
To view the current availability status of a virtual server, simply add '/stats' to the end of the virtual server's selfLink and send a GET on that URL.
A typical GET looks like this:
```
GET https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual/8fbe1abd-a87d-30e9-a1ca-904f0c2018b6/stats

Results:
{
    "entries": {
        "status.enabledState": {
            "description": "enabled",
            "lastUpdateMicros": 1479501254572669
        },
        "status.statusReason": {
            "description": "The virtual server is available",
            "lastUpdateMicros": 1479501254572670
        },
        "status.availabilityState": {
            "description": "available",
            "lastUpdateMicros": 1479501254572669
        },
        "lastRefreshMicros": {
            "value": 1479510554396833,
            "lastUpdateMicros": 1479510554400702,
            "updateType": "BASIC"
        }
    },
    "generation": 76,
    "lastUpdateMicros": 1479510554400702,
    "kind": "cm:adc-core:working-config:ltm:virtual:8fbe1abd-a87d-30e9-a1ca-904f0c2018b6:stats:restworkerstats",
    "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual/8fbe1abd-a87d-30e9-a1ca-904f0c2018b6/stats"
}
```
> **Note:**
> - The response is a record of the statistics since the last collection time. See *lastRefreshMicros.value* for a unix timestamp representing the last time statistics were collected for this object's device.
> - *status.availabilityState* indicates whether the virtual server's pool has been marked available by monitors attached to the virtual server's pool, pool members, or nodes.
> - The location of these statistics could change in the future.


### Walk from Virtual Server to Nodes
There are a couple of mechanisms for locating nodes that are used by a virtual server.
1. Using the *referenceKind* URL parameter
2. Manually walking each reference
```
Virtual Server -> Pool -> Pool Members -> Nodes
```

#### 1. Using the referenceKind URL parameter
For this method, use the *referenceMethod* 'resourceReferencesKind', to specify the virtual server as the *referenceLink* and the node kind as the *referenceKind*. A typical GET looks like this:
```
GET https://localhost/mgmt/shared/index/config?referenceMethod=resourceReferencesKind
&referenceKind=cm:adc-core:working-config:ltm:node:adcnodestate
&referenceLink=https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual/8fbe1abd-a87d-30e9-a1ca-904f0c2018b6
&referenceDepth=3
&inflate=true

Results:
{
    "selfLink": "https://localhost/mgmt/shared/index/config?referenceKind=cm:adc-core:working-config:ltm:node:adcnodestate&inflate=true&referenceMethod=resourceReferencesKind&referenceLink=https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual/8fbe1abd-a87d-30e9-a1ca-904f0c2018b6&referenceDepth=3",
    "totalItems": 1,
    "items": [
        {
            "address": "10.10.10.9",
            "connectionLimit": 0,
            "isEphemeral": false,
            "rateLimit": "disabled",
            "ratio": 1,
            "sessionConfig": "user-enabled",
            "stateConfig": "user-up",
            "fqdn": {
                "addressFamily": "ipv4",
                "isAutoPopulate": false,
                "downInterval": 5,
                "interval": "3600"
            },
            "partition": "Common",
            "deviceReference": {
                "id": "4d01b7c8-63d5-465f-8ef1-cc8795f32abb",
                "name": "bigip1.pdsea.f5net.com",
                "kind": "shared:resolver:device-groups:restdeviceresolverdevicestate",
                "machineId": "4d01b7c8-63d5-465f-8ef1-cc8795f32abb",
                "link": "https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/4d01b7c8-63d5-465f-8ef1-cc8795f32abb"
            },
            "name": "10.10.10.9",
            "id": "befccffd-0928-3b58-b5d8-6e83f40e074e",
            "generation": 1,
            "lastUpdateMicros": 1479501043301183,
            "kind": "cm:adc-core:working-config:ltm:node:adcnodestate",
            "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/node/befccffd-0928-3b58-b5d8-6e83f40e074e"
        }
    ]
}
```

#### 2. Manually walking each reference
For this method, send a GET to the virtual server. A typical GET looks like this:
```
GET https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual/8fbe1abd-a87d-30e9-a1ca-904f0c2018b6

Results:
{
	...
    "name": "owa-app",
    "partition": "Common",
    "poolReference": {
        "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/8928f8af-3bfb-3a1f-ab25-471ccef7a1c0"
    },

    ...
}
```
Now send get a GET to the *poolReference* link to retrieve the pool. A typical GET looks like this:
```
GET https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/8928f8af-3bfb-3a1f-ab25-471ccef7a1c0

Results:
{
	...
    "membersCollectionReference": {
        "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/8928f8af-3bfb-3a1f-ab25-471ccef7a1c0/members",
        "isSubcollection": true
    },
    "name": "owa-pool",
    "partition": "Common"
    ...
}
```
Now send a GET to the *membersCollectionReference* link to retrieve a list of the pool's members. A typical GET looks like this:
```
GET https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/8928f8af-3bfb-3a1f-ab25-471ccef7a1c0/members

Results:
{
    "items": [
        {
            ...
            "nodeReference": {
                "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/node/befccffd-0928-3b58-b5d8-6e83f40e074e"
            },
            "partition": "Common",
            "name": "10.10.10.9:8000",
            ...
        },
        {
            ...
            "nodeReference": {
                "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/node/befccffd-0928-3b58-b5d8-6e83f40e074e"
            },
            "partition": "Common",
            "name": "10.10.10.9:8002",
            ...
        },
        {
            ...
            "nodeReference": {
                "link": "https://localhost/mgmt/cm/adc-core/working-config/ltm/node/befccffd-0928-3b58-b5d8-6e83f40e074e"
            },
            "partition": "Common",
            "name": "10.10.10.9:8001",
            ...
        }
    ],
    "generation": 4,
    "kind": "cm:adc-core:working-config:ltm:pool:members:adcpoolmembercollectionstate",
    "lastUpdateMicros": 1479501141331454,
    "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/pool/8928f8af-3bfb-3a1f-ab25-471ccef7a1c0/members"
}
```
Now you can retrieve the node using the *nodeReference* from each member. To get a member's node reference, you can send a GET request to each nodeReference link. A typical GET looks like this:
```
GET https://localhost/mgmt/cm/adc-core/working-config/ltm/node/befccffd-0928-3b58-b5d8-6e83f40e074e

Results:
{
    "address": "10.10.10.9",
    "connectionLimit": 0,
    "isEphemeral": false,
    "rateLimit": "disabled",
    "ratio": 1,
    "sessionConfig": "user-enabled",
    "stateConfig": "user-up",
    "fqdn": {
        "addressFamily": "ipv4",
        "isAutoPopulate": false,
        "downInterval": 5,
        "interval": "3600"
    },
    "partition": "Common",
    "deviceReference": {
        "id": "4d01b7c8-63d5-465f-8ef1-cc8795f32abb",
        "name": "bigip1.pdsea.f5net.com",
        "kind": "shared:resolver:device-groups:restdeviceresolverdevicestate",
        "machineId": "4d01b7c8-63d5-465f-8ef1-cc8795f32abb",
        "link": "https://localhost/mgmt/shared/resolver/device-groups/cm-adccore-allbigipDevices/devices/4d01b7c8-63d5-465f-8ef1-cc8795f32abb"
    },
    "name": "10.10.10.9",
    "id": "befccffd-0928-3b58-b5d8-6e83f40e074e",
    "generation": 1,
    "lastUpdateMicros": 1479501043301183,
    "kind": "cm:adc-core:working-config:ltm:node:adcnodestate",
    "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/node/befccffd-0928-3b58-b5d8-6e83f40e074e"
}
```
### Searching for non-conformant configuration
If you have a company policy regarding acceptable configurations, you can use the $filter parameter to search for a particular invalid configuration.

For example, if your company policy prohibits autoLasthop on virtual servers to be 'enabled', you could issue the following query to locate invalid virtuals servers:

> **Note:**
> In this case, for simplicity we use $select=selfLink,autoLasthop to suppress all properties except for the *selfLink* and *autoLasthop*.

```
GET https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual?$filter=autoLasthop eq 'enabled'&$select=selfLink,autoLasthop

Results:
{
    "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual",
    "totalItems": 1,
    "items": [
        {
            "autoLasthop": "enabled",
            "selfLink": "https://localhost/mgmt/cm/adc-core/working-config/ltm/virtual/8fbe1abd-a87d-30e9-a1ca-904f0c2018b6"
        }
    ],
    "generation": 26,
    "kind": "cm:adc-core:working-config:ltm:virtual:adcvirtualcollectionstate",
    "lastUpdateMicros": 1479756371933830
}
```

### API Reference
