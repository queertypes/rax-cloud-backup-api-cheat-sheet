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

============
Authenticate
============

All requests to the API require a valid auth token. Here's how to get one:

**Request**

.. code-block:: bash

    http POST https://identity.api.rackspacecloud.com/v2.0/tokens \
      auth:='{"passwordCredentials": {"username": "myuser", "password": "mypass"}}' \
      content-type:application/json
    # extract response.access.token.id for auth_token
    # extract response.access.serviceCatalog.X.endpoints[0].publicURL 
    #   for storageURL where X is the entry in the serviceCatalog
    #   pertaining to cloud backup
    auth=response.access.token.id
    backup=response.access.serviceCatalog.X.endpoints[0].publicURL


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
                            "internalURL": "snet-storageURL",
                            "publicURL": "storageURL",
                            "region": "DFW"
                        }
                    ],
                    "name": "cloudFiles",
                    "type": "object-store"
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

-----------------
Get Agent Details
-----------------

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
