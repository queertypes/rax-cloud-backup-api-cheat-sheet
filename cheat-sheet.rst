**************************
Rackspace Cloud Backup API
**************************

Commands below are given using `httpie`_, the Python command line HTTP client.

.. _httpie: https://github.com/jkbr/httpie

**Table of Contents**

.. contents::
    :local:
    :depth: 2


.. raw:: pdf

    PageBreak

===========
Conventions
===========

Let's set a few variable we'll be using for the rest of this cheat
sheet:

.. code-block:: bash

    auth=my-awesome-auth-token-from-a-rackspace-keystone-endpoint
    backup=https://backup.api.rackspacecloud.com/v1.0
    agentId=YourAgentId

For brevity's sake, I've omitted several HTTP headers from API
responses.

============
Authenticate
============

All requests to the API require a valid auth token. Here's how to get one:

**Request**

.. code-block:: bash

    http POST https://identity.api.rackspacecloud.com/v2.0/tokens \
      auth:='{"passwordCredentials": {"username": "myuser", "password": "mypass"}}' \
      content-type:application/json

**Response**

.. code-block:: http

    HTTP/1.1 200 OK
    Connection: keep-alive
    Content-Encoding: gzip
    Content-Type: application/json
    Date: Mon, 22 Apr 2013 17:31:38 GMT
    Front-End-Https: on
    Server: nginx/0.8.55
    Transfer-Encoding: chunked
    VIA: 1.0 Repose (Repose/2.3.5)
    response-source: cloud-auth
    vary: Accept, Accept-Encoding, X-Auth-Token

    {
        "access": {
            "serviceCatalog": [
                [...]
                {
                    "endpoints": [
                        {
                            "publicURL": "storageURL",
                            "tenantId": "000001"
                        }
                    ],
                    "name": "cloudBackup",
                    "type": "rax:backup"
                },
            ],
            "token": {
                "expires": "2013-04-23T09:45:24.000-05:00",
                "id": "auth_token",
            },
            "user": {
                [...]
            }
        }
    }


.. raw:: pdf

    PageBreak


================
Agent Operations
================

These are global operations that affect the state of the entire backup
installation on your machine. Use them with caution. Here's the
synopsis::

    GET /agent/{agentId}
    POST /agent/enable
    POST /agent/encrypt
    POST /agent/delete

The User Operations section details how to acquire the agent ID.

-----------------
Get Agent Details
-----------------

Returns details about your machine and the agent installed.

.. code-block:: bash

    http get $backup/agents

.. code-block:: http

    HTTP/1.1 200 OK
    Content-Length: 1001
    Content-Type: application/json; charset=utf-8

    {
        "AgentVersion": "1.05.006334",
        "Architecture": "64-bit",
        "BackupDatacenter": "None",
        "BackupVaultSize": "0 KB",
        "CleanupAllowed": false,
        "Datacenter": "DFW",
        "Flavor": "RaxCloudServer",
        "IPAddress": "166.78.156.102",
        "IsDisabled": false,
        "IsEncrypted": false,
        "MachineAgentId": 169057,
        "MachineName": "keystone-auth-cache",
        "OperatingSystem": "Ubuntu",
        "OperatingSystemVersion": "12.10",
        "PublicKey": {
            "ExponentHex": 10101,
            "ModulusHex": "im_really_long"
        },
        "Status": "Offline",
        "TimeOfLastSuccessfulBackup": null,
        "UseFailoverUri": false,
        "UseServiceNet": true
    }

---------------------------
Enable or Disable the Agent
---------------------------

Make a request using a JSON body to disable a specific agent::

    http post $backup/agent/enable x-auth-token:$auth \
      MachineAgentId=$agentid Enable=False

.. code-block:: http

    HTTP/1.1 204 No Content

Now to enable that agent again::

    http post $backup/agent/enable x-auth-token:$auth \
      MachineAgentId=$agentid Enable=True

.. code-block:: http

    HTTP/1.1 204 No Content

------------------------
Enable Volume Encryption
------------------------

**Warning**: This is a **one-way** operation. Once you enable
 encryption, there's no going back!

With that said, this is how you enable encryption::

    http post $backup/agent/encrypt MachineAgentId=$agentid \
      EncryptedPasswordHex=long_hash_982239846_hexd_up

------------
Delete Agent
------------

It goes without saying - deleting an agent is permanent. It also
**wipes out all backup data** associated with that agent.

Here we go::

    http post $backup/agent/delete MachineAgentId=$agentid

.. code-block:: http

    HTTP/1.1 204 No Content

===============
User Operations
===============

Synopsis::

    GET /user/agents

---------------------------
Get All Agents for the User
---------------------------

This request lists all agents asasociated with a user.
It gives *a lot* of information:

.. code-block:: bash

    http $backup/user/agents x-auth-token:$auth

.. code-block:: http

    [
        {
            "AgentVersion": "1.05.006334",
            "Architecture": "64-bit",
            "BackupDatacenter": "None",
            "BackupVaultSize": null,
            "CleanupAllowed": false,
            "Datacenter": "DFW",
            "Flavor": "RaxCloudServer",
            "IPAddress": "166.78.156.202",
            "IsDisabled": true,
            "IsEncrypted": false,
            "MachineAgentId": 169057,
            "MachineName": "keystone-auth-cache",
            "OperatingSystem": "Ubuntu",
            "OperatingSystemVersion": "12.10",
            "PublicKey": {
                "ExponentHex": 10101,
                "ModulusHex": "long"
            },
            "Status": "Unknown",
            "TimeOfLastSuccessfulBackup": null,
            "UseFailoverUri": false,
            "UseServiceNet": true
        },
        {
            ...
        }
    ]

===============================
Backup Configuration Operations
===============================

A backup configuration specifies what files are backed up and how
often. This section details how to manage backup configurations
through the API::

    POST /backup-configuration
    GET /backup-configuration

    DELETE /backup-configuration/{backupConfigId}
    PUT /backup-configuration/{backupConfigId}

    GET /backup-configuration/system/{agentId}
    POST /backup-configuration/enable/{backupConfigId}

------
Create
------

Create a backup configuration. It requires a rather rich JSON body to
set all the parameters.

.. code-block:: bash

    http post $backup/backup-configuration x-auth-token:$auth < conf.json

Below is the request body as stored in conf.json:

.. code-block:: json

    {
       "BackupConfigurationName": "Weekly Website Backup",
       "IsActive": true,
       "VersionRetention": 0|30|60,
       "MissedBackupActionId": 1,
       "Frequency": "Hourly,
       "StartTimeHour": 6,
       "StartTimeMinute": 30,
       "StartTimeAmPm": "PM",
       "DayOfWeekId": 4,
       "HourInterval": null,
       "TimeZoneId": "Eastern Standard Time",
       "NotifyRecipients": "raxtestaddress1@gmail.com",
       "NotifySuccess": true,
       "NotifyFailure": true,
       "Inclusions": [
           {
             "FilePath": "C:\\backup_up_file.txt",
             "FileItemType": "File"
           },
           {
             "FilePath": "C:\\backed_up_folder",
             "FileItemType": "Folder"
           }
       ],
       "Exclusions": [
           {
             "FilePath": "C:\\backed_up_folder\\excluded_file.txt",
             "FileItemType": "File"
           },
           {
             "FilePath": "C:\\backed_up_folder\\excluded_folder",
             "FileItemType": "Folder"
           }
       ]
     }

**Response**

.. code-block:: http

    HTTP/1.1 200 OK

------
Update
------

As with POST, this takes a very rich JSON body. This replaces an
existing configuration with a new one.

.. code-block:: bash

    http put $backup/backup-configuration/$backupConfigId x-auth-token:$auth < conf.json

.. code-block:: http

    HTTP/1.1 200 OK

---------
Fetch One
---------

Fetches one backup configuration you've registered.

.. code-block:: bash

    http get $backup/backup-configuration/100850 x-auth-token:$auth


------------------
Fetch All for User
------------------

Fetches all backup configurations you've registered.

.. code-block:: bash

    http get $backup/backup-configuration x-auth-token:$auth

.. code-block:: http

    HTTP/1.1 200 OK

    {
      [
        {
          "BackupConfigurationId": 100850,
          "MachineAgentId": 96674,
          "MachineName": "Web Server",
          "Flavor": "RaxCloudServer",
          "IsEncrypted": false,
          "BackupConfigurationName": "Manual Website Backup",
          "IsActive": true,
          "IsDeleted": false,
          "VersionRetention": 60,
          "BackupConfigurationScheduleId": 98017,
          "MissedBackupActionId": 2,
          "Frequency": "Manually",
          "StartTimeHour": null,
          "StartTimeMinute": null,
          "StartTimeAmPm": "",
          "DayOfWeekId": null,
          "HourInterval": null,
          "TimeZoneId": "Eastern Standard Time",
          "NextScheduledRunTime": null,
          "LastRunTime": "\/Date(1343226053000)\/",
          "LastRunBackupReportId": 80071,
          "NotifyRecipients": "user@rackspace.com",
          "NotifySuccess": false,
          "NotifyFailure": true,
          "Inclusions": [
            {
              "FilePath": "/web/",
              "ParentId": 100850,
              "FileItemType": "Folder",
              "FileId": 2947
            }
          ],
          "Exclusions": [
            {
              "FilePath": "/web/cache/",
              "ParentId": 100850,
              "FileItemType": "Folder",
              "FileId": 2948
            }
          ]
        },
        {
          "BackupConfigurationId": 100928,
          "MachineAgentId": 96685,
          "MachineName": "Database Server",
          "Flavor": "RaxCloudServer",
          "IsEncrypted": false,
          "BackupConfigurationName": "Manual DB Backup",
          "IsActive": true,
          "IsDeleted": false,
          "VersionRetention": 60,
          "BackupConfigurationScheduleId": 98019,
          "MissedBackupActionId": 2,
          "Frequency": "Manually",
          "StartTimeHour": null,
          "StartTimeMinute": null,
          "StartTimeAmPm": "",
          "DayOfWeekId": null,
          "HourInterval": null,
          "TimeZoneId": "Eastern Standard Time",
          "NextScheduledRunTime": null,
          "LastRunTime": "\/Date(1343226074000)\/",
          "LastRunBackupReportId": 80116,
          "NotifyRecipients": "user@rackspace.com",
          "NotifySuccess": false,
          "NotifyFailure": true,
          "Inclusions": [
              {
                "FilePath": "/db/dumps/",
                "ParentId": 100928,
                "FileItemType": "Folder",
                "FileId": 3568
              }
          ],
          "Exclusions": [
              {
                "FilePath": "/db/dumps/tmp/",
                "ParentId": 100928,
                "FileItemType": "Folder",
                "FileId": 3570
              }
          ]
        }
      ]
    }

-------------------
Fetch All for Agent
-------------------

.. code-block:: bash

    http GET $backup/backup-configuration/system/{machineAgentId} x-auth-token:$auth

The repsonse is similar to those above.

--------------
Enable/Disable
--------------

.. code-block:: bash

    # enable
    http POST $backup/backup-configuration/enable/{backupConfigurationId \
      x-auth-token:$auth Enable=true

    # disable
    http POST $backup/backup-configuration/enable/{backupConfigurationId \
      x-auth-token:$auth Enable=false

The response body echoes the configuration.

------
Delete
------

.. code-block:: delete

    http DELETE /backup-configuration/{backupConfigurationId} \
      x-auth-token:$auth

.. code-block:: http

    HTTP/1.1 200 OK

=================
Backup Operations
=================

----------
Start/Stop
----------

-----------
Get Details
-----------

---------------------
Get Completed Backups
---------------------

-----------------
Get Backup Report
-----------------

================================
Restore Configuration Operations
================================

------
Create
------

------
Update
------

-----------------------
Add Inclusion/Exclusion
-----------------------

--------------------------
List Inclusions/Exclusions
--------------------------

------
Delete
------

==================
Restore Operations
==================

----------------------------------
List Backups Available for Restore
----------------------------------

----------
Start/Stop
----------

-----------
Get Details
-----------

----------
Get Report
----------

===================
Activity Operations
===================

------------------
List User Activity
------------------

-------------------
List Agent Activity
-------------------

===============
Help Operations
===============

-------------------
Get Developer Guide
-------------------

------------------------------
List Images Supported by Agent
------------------------------
