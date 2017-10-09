# BMC Remedy 8 - ITSM - Incident - Work Log Update
A common Major Incident Management (MIM) approach is to leverage xMatter to send MIM communications and initiate conference bridges to manage a MIM.  A common request is to update the matching Remedy Incident Ticket work logs with information from this work flow in xMatters.  
This xM Labs Integration allows you to connect an xMatters Communication Plan Form with a Remedy Incident Management instance to update the Remedy work log associated with the ticket.

NOTE: This does not require an existing Remedy Incident Management integration to be in place.

<kbd>
  <img src="https://github.com/xmatters/xMatters-Labs/raw/master/media/disclaimer.png">
</kbd>

# Pre-Requisites
* BMC Remedy ITSM 8.1 **On Premise**
* xMatters onDemand account - If you don't have one, [get one](https://www.xmatters.com)!
* An existing Communication Plan and Form within xMatters onDemand
* xMatters Integration Agent 5.x (5.0 patch 007 or later) and corresponding Integration Agent Utilities - Downloads and install docs are [here](https://support.xmatters.com/hc/en-us/articles/201463419-Integration-Agent-for-xMatters-5-x-xMatters-On-Demand)

# Files
* [xM_xMatters_to_Remedy_Worklog.zip](xM_xMatters_to_Remedy_Worklog.zip) - Zip file containing all necessary Integration Agent files

# How it works
Once the Integration Service is installed, any Communication Plan Form can be configured to send information to the Remedy Work Log of the associated Incident Ticket when the form is used to send a communication using an xMatters Outbound Integration.  This integration depends on two key (configurable) field being present in the layout of the form to represent the Incident ID of the ticket and the Message to be included in the worklog.

# Installation
## 1. Installation and Configuration

This document provides information about installing the xMatters (IT) for xMatters to BMC Remedy Work Log integration. This document also contains complete instructions on how to configure xMatters, BMC Remedy, and the integration components.

Installing the integration service and updating the integration agent

To configure the integration agent for the integration, you must copy the integration components into the integration agent; this process is similar to patching the application, where instead of copying files and folders one by one, you copy the contents of a single folder directly into the integration agent folder (<IAHOME>). The folder structure is identical to the existing integration agent installation, so copying the folder's contents automatically installs the required files to their appropriate locations. Copying these files will not overwrite any existing integrations. This integration includes the following components:

* **configuration.js:** The main configuration file for the integration; includes API connection information and user information.

* **xm_hpd_ws.pwd:** Stores the password for the Remedy API user used by the integration service. If you change the name of this file, you must also update the configuration.js files to point to the correct password file.


Once you have installed the files and folders, you will need to modify the configuration.js file to suit your deployment configuration.

_**Note:** If you have more than one integration agent providing the this service, repeat the following steps for each one._

**To install the integration service:**

1. Copy all of the contents of the /components/integration-agent/ folder from the extracted integration archive to the <IAHOME> folder.

2. Open the IAConfig.xml file found in <IAHOME>/conf and add the following line to the “service-configs” section: <path>remedy81/remedyincident-worklog.xml</path>

3. Open the configuration.js file (now located in <IAHOME>/integrationservices/remedy81/remedyincident-worklog/ folder, and set the values for the following variables:
   * **REMEDY_WS_USERNAME:** The user name to be used for the BMC Remedy API Server; default value is "xmatters".
   * **REMEDY_WS_PASSWORD_FILE:** Location of the password file for the API user; default value is "conf/xm_hpd_ws.pwd".
   * **PROTOCOL:** Protocol (HTTP/HTTPS) for the BMC Remedy API Server endpoint; default value is "HTTP".
   * **MID_TIER_HOSTNAME:** Hostname of the BMC Remedy API Server; default value is "localhost".
   * **MID_TIER_PORT:** Hostname of the BMC Remedy API Server; default value is "8080".
   * **REMEDY_SERVER_NAME:** Hostname of the BMC Remedy API Server; default value is "8080".
   * **GENERIC_TICKET_ID:** Name of the property in the xMatters Communication Plan Form that holds the BMC Incident Ticket ID; default value is "Incident Number".
   * **GENERIC_MESSAGE:** Name of the property in the xMatters Communication Plan form that holds the message to be placed in the work log of the BMC Incident Ticket; default value is "Incident Message Detail".
   
4. Save and close the file.

6. Save and close the files.

7. Restart the integration agent. 

On Windows, the integration agent runs as a Windows Service; on Unix, it runs as a Unix daemon

## 2. Updating the user password

The password for the BMC Remedy user is stored in an encrypted password file, stored in the <IAHOME>/conf subfolder. For the integration to connect to the API, you will have to replace the default value ("password") with the password of the database user specified in the configuration.js file (as described in the table above.)

_**Note:** For more information about the integration agent's IAPassword feature used to encrypt the password, see the xMatters integration agent guide._

To change the encrypted password:

1. Navigate to the <IAHOME>/bin subfolder, and then run the following command, replacing <newPassword> with the password of the BMC Remedy user:

`./iapassword.sh --new "<newPassword>" --old password --file conf/bmccontrolm.pwd`

_**Note:** that if you want to change this password again, you will have to replace "password" in the above command with the existing password._

## 3. Remedy user account for xMatters

The Integration Agent requires a dedicated ITSM user to interact with incidents.

Create an ITSM user with the Incident Master role in BMC Remedy; the user does not need to be Support Staff.


## xMatters set up
1.	Create or identify the Communication Plan Form that needs to be integrated in the xMatters onDemand instance.
2.	Ensure 2 properties are created and present on the form with the following names (case/spaces sensitive)
	* Incident Number
	* Incident Message Detail
3.	Ensure that each property has been configured to “Include in Outbound Integrations”
	* Click on the **Layout** tab of the Form in Developer
	* Click on the **tool/cog** icon beside each property
	* Ensure the “Include in Outbound Integrations” box is checked
4. Create an Event Domain for the Integration
	* Click on the **Developer** Tab
	* Click on **Event Domains**
	* Click on **applications**
	* Click on **Add New** under Integration Services
	* Name your Integration Service: remedyincident-worklog
5. Add an Outbound Integration associated with the Form
	* Click on the **Integration Builder** tab of the parent Communication Plan
	* Click on **Outbound Integrations**
	* Click on the **Add** button
	* Set the Trigger: Event Status Updates
	* Select the Form: **Select your Form from the dropdown**
	* Choose an Action: Send to Integration Agent
	* Select an Integration Service: remedyincident-worklog
	* NOTE: Do not configure a specific Integration Agent 
	* Name your Integration: Remedy Incident-Worklog-Event Status Updates
	* Click **Create Outbound Integration**

# Testing
Test the integration by sending a communication using the configured form and ensuring the Remedy Ticket work log is updated with the contents of the **Incident Message Detail** field

# Troubleshooting
On the xMatters side, logging can be found in the Activity Stream of the Outbound Integration created above; this logging will relate to the call from xMatters onDemand to the Integration Agent for processing on the BMC Remedy server.

On the Remedy side, logging can be found in the <IAHOME>/log/AlarmPoint.txt file; this logging will relate to the call from the Integration Agent to BMC Remedy to update the worklogs 

# Extending this Integration
A few things to note:
1. The Incident ID field does some simple formatting.  This formatting (and the rules of the formatting) can be found in the **formatIncidentID** function of the remedyincident-worklog-callbacks.js file in the <IAHOME>/integrationservices/remedy81/ directory.
2. The integration is designed to be easily extendable for more than just the xMatters event being created by submitting the xMatters form.
	* You can add additional Outbound Integrations in the Integration Builder associated with the Communication Plan in xMatters.  There is code is in place on the Integration Agent side to handle the currently supported Delivery Status and Response Outbound Integration and can easily be extended for any new Outbound Integrations as they are added to the onDemand product.  The functional entry point of apia_callback can be found in the remedyincident-worklog-callbacks.js file in the <IAHOME>/integrationservices/remedy81/ directory
