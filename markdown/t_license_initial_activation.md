## Initial Activation of license

### Overview
Using the BIG-IQ REST API, you can activate several types of BIG-IP licenses. This endpoint is the common starting point for activating a Pool-style regkey (that is, a  Utility License or a Volume License).  Clients use this endpoint to perform the initial activation of the top-level regkey, then they can perform the other license actions supported by the relevant collection.

1. For automatic activation, the system gets the license text from the F5 server without user intervention. For manual activation, the client is provided with a dossier, which they must then activate at the F5 licensing web portal. The client gets the license text there, and then uses it as input to the collection to complete the manual activation step. 
2. Note: Automatic activation typically involves requiring the user to accept the EULA text before proceeding. For manual activation, this happens on the web portal.
3. Once you have the license text, the license worker determines the kind of license it represents. Using that information, it creates an entry in the appropriate specific license collection. For instance, a purchased pool license regkey creates an entry in /cm/device/licensing/pool/purchased-pool/licenses.
4. A reference to the item in that collection is stored, and made available to the client.
5. For automatic activation, the license worker waits for the activation process to finish in the specific collection. This is typically a multi-step process, as each offering needs to be activated. Eventually, either success or failure is encountered, and that information is propagated to the user. For automatic activation, the client does not need to perform any additional action other than the initial POST, and then wait for the result.
6. For manual activation, the process is necessarily more involved. As before, an item will be created in the relevant license collection based on the initial license text. However, for manual activation, the license worker will be done at that point. The client must go visit that collection (through the provided Reference) and complete manual activation there. The client will need to use that license collection's specific API to finish manual activation

### Prerequisites
Make certain that the following prerequisites have been met.

- The BIG-IQ Centralized Management device is operational, has completed the setup wizard, and completed any other needed configuration.
- The user has either the administrator or device manager role.

Note: When you perform these tasks using the example code provided, review the listed IP addresses and change them as appropriate for your environment. For example, if you are not running the script directly on the BIG-IQ device, change localhost to be the IP address of the BIG-IQ Centralized Management device.


#### 1. Start initial activation of a license.
You can use API calls to begin the license activation process. 
A typical post looks like this:

POST https://ip/mgmt/cm/device/licensing/pool/initial-activation
```
Request:

{
    "regKey" : "MY-REGISTRATION-KEY",
    "name" : "my own freeform name",
    "status" : "ACTIVATING_AUTOMATIC",
}

Response:
{
    "regKey" : "MY-REGISTRATION-KEY",
    "name" : "my own freeform name",
    "status" : "ACTIVATING_AUTOMATIC",
    "message" : "Activation in progress",
}

#### 2. Poll to get status.
After you post the license activation, you should poll to check the activation status. 
A typical post looks like this:

GET https://ip/mgmt/cm/device/licensing/pool/initial-activation/{uuid}

Response:
{
    "regKey" : "MY-REGISTRATION-KEY",
    "name" : "my own freeform name",
    "status" : "ACTIVATING_AUTOMATIC_NEED_EULA_ACCEPT",
    "message" : "Need EULA acceptance in order to continue",
    "eulaText" : "The exact EULA text goes here..."
}

#### 3. Patch to accept EULA.
After you accept the EULA, subsequent polls show the activation process status.  Eventually the activation status returns either ACTIVATION_FAILED or READY.

PATCH https://ip/mgmt/cm/device/licensing/pool/initial-activation/{uuid}

Request:
{
    "status" : "ACTIVATING_AUTOMATIC_EULA_ACCEPTED",
    "eulaText" : "The exact EULA text goes here..."
}

Response:
{
	"regKey" : "MY-REGISTRATION-KEY",
	"name" : "my own freeform name",
	"status" : "ACTIVATING_AUTOMATIC_EULA_ACCEPTED",
	"eulaText" : "The exact EULA text goes here..."
}

#### 4. Patch to provide license text for manual activation
For manual activation, you need to submit the license text to finish the activation process. 
A typical post looks like this: 

PATCH https://ip/mgmt/cm/device/licensing/pool/initial-activation/{uuid}

Request:
{
	"status" : "ACTIVATING_MANUAL_LICENSE_TEXT_PROVIDED",
	"licenseText" : "The exact license text goes here..."
}
Response:
{
	"regKey" : "MY-REGISTRATION-KEY",
	"name" : "my own freeform name",
	"status" : "ACTIVATING_MANUAL_LICENSE_TEXT_PROVIDED",
	"licenseText" : "The exact license text goes here..."
}

#### 5. Patch to re-try a failed activation
Before you re-try the activation, check the log and error messages to find the root cause of the failure.  Some typical reasons would be: wrong registration key or connection error to licensing server. 
A typical post looks like this:

PATCH https://ip/mgmt/cm/device/licensing/pool/initial-activation/{uuid}

Request:
{
	"status" : "ACTIVATING_AUTOMATIC",
}

Response:
{
	"regKey" : "MY-REGISTRATION-KEY",
	"name" : "my own freeform name",
	"status" : "ACTIVATING_AUTOMATIC"
}

#### 6. Remove a failed activation
You can use an API call to remove a failed activation attempt. 
A typical post looks like this:

DELETE https://ip/mgmt/cm/device/licensing/pool/initial-activation/{uuid}

### API Reference