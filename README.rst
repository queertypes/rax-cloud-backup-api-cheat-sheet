**************************************
Rackspace Cloud Backup API Cheat Sheet
**************************************

.. image:: https://github.com/queertypes/rax-cloud-backup-api-cheat-sheet/raw/master/cloud-backup.png
    :alt: Cloud Server Backup Agent

:version: 0.4.3
:reference: http://docs.rackspace.com/rcbu/api/v1.0/rcbu-devguide/content/overview.html

This is a collection of notes for using the Rackspace `Cloud Backup`_ API.

.. _Cloud Backup: http://docs.rackspace.com/rcbu/api/v1.0/rcbu-devguide/content/overview.html

========
Building
========

You can build the cheat sheet locally by running and installing `rst2pdf`_:

.. code-block:: bash

    rst2pdf cheat-sheet.rst

.. _rst2pdf: http://rst2pdf.ralsina.com.ar/

When printing the cheat sheet, it's helpful to remove the table of
contents. To do so, just edit the cheat-sheet.rst and remove the
following lines:

.. code-block:: rst

    **Table of Contents**

    .. contents::
        :local:
        :depth: 2

    .. raw:: pdf

        PageBreak


Rebuild the document and it's ready for printing!

======
Author
======

If you have any questions, corrections, or contributions, please
contact Allele Dev <allele.dev@gmail.com>.
