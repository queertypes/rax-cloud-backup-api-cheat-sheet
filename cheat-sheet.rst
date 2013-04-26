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



------------------------
Enable Volume Encryption
------------------------

------------
Delete Agent
------------

===============
User Operations
===============

---------------------------
Get All Agents for the User
---------------------------

===============================
Backup Configuration Operations
===============================

------
Create
------

------
Update
------

---------
Fetch One
---------

------------------
Fetch All for User
------------------

-------------------
Fetch All for Agent
-------------------

--------------
Enable/Disable
--------------

------
Delete
------

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
