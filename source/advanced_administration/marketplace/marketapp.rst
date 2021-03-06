 .. _marketapp:

========================
Managing MarketPlaceApps
========================

Overview
--------------------------------------------------------------------------------

As mentioned in the :ref:`MarketPlace Overview <marketplace_overview>`, a MarketPlaceApp is a resource that can be either a single image associated with a template, a template associated with one or more images, or a flow composed of one or more templates associated with images. In short, MarketPlaceApps are composed of metadata (a template) and binary data (the images). This guide addresses how you can manage these MarketPlaceApps in OpenNebula, considering both the metadata and the images.

MarketPlaceApps can be managed either using the CLI with the ``onemarketapp`` command or with the Sunstone GUI. In this section we will detail the actions available for MarketPlaceApps in both interfaces. MarketPlaceApps are common OpenNebula objects and respond the the common actions shared by all the OpenNebula objets: `list`, `show`, `create`, `delete`, etc, plus some specific ones.

Listing MarketPlaceApps
--------------------------------------------------------------------------------

Using the CLI:

.. code::

    $ onemarketapp list
     ID NAME                         VERSION  SIZE STAT TYPE  REGTIME               MARKET
      0 ttylinux - kvm                   1.0   40M  rdy  img 06/08/46    OpenNebula Public
      1 ttylinux - VMware                1.1  102M  rdy  img 08/08/16    OpenNebula Public
      2 Carina Environment Manage        1.0  1.2G  rdy  img 05/14/26    OpenNebula Public
      3 Testing gUSE installation        1.0   16G  rdy  img 07/22/86    OpenNebula Public
      4 gUse v3.5.2                    3.5.2   16G  rdy  img 04/02/43    OpenNebula Public
      5 Vyatta Core 6.5R1 - kvm          1.0    2G  rdy  img 07/22/86    OpenNebula Public
      6 gUSE CloudBroker Wrapper         1.0   16G  rdy  img 04/08/43    OpenNebula Public
      7 debian-7.1-amd64-kvm             1.3    5G  rdy  img 07/22/86    OpenNebula Public
      8 Hadoop 1.2 Master                1.0  1.3G  rdy  img 04/07/43    OpenNebula Public
      9 Hadoop 1.2 Slave                 1.0  1.3G  rdy  img 05/18/14    OpenNebula Public


.. todo:: Sunstone

Show a MarketPlaceApp
--------------------------------------------------------------------------------

Using the CLI:

.. code::

    $ onemarketapp show 0
    MARKETPLACE APP 0 INFORMATION
    ID             : 0
    NAME           : ttylinux - kvm
    TYPE           : IMAGE
    USER           : oneadmin
    GROUP          : oneadmin
    MARKETPLACE    : OpenNebula Public
    STATE          : rdy

    PERMISSIONS
    OWNER          : um-
    GROUP          : u--
    OTHER          : u--

    DETAILS
    SOURCE         : http://marketplace.opennebula.systems//appliance/4fc76a938fb81d3517000003/download/0
    MD5            : 04c7d00e88fa66d9aaa34d9cf8ad6aaa
    PUBLISHER      : OpenNebula.org
    PUB. DATE      : Wed Jun  8 22:17:19 137435166546
    VERSION        : 1.0
    DESCRIPTION    : This is a very small image that works with OpenNebula. It's already contextualized. The purpose of this image is to test OpenNebula deployments, without wasting network bandwith thanks to the tiny footprint of this image
    (40MB).
    SIZE           : 40M
    ORIGIN_ID      : -1
    FORMAT         : raw

    IMPORT TEMPLATE


    MARKETPLACE APP TEMPLATE
    IMPORTED="YES"
    IMPORT_ID="4fc76a938fb81d3517000003"
    TAGS="linux, ttylinux,  4.8,  4.10"
    VMTEMPLATE64="Q09OVEVYVCA9IFsgTkVUV09SSyAgPSJZRVMiLFNTSF9QVUJMSUNfS0VZICA9IiRVU0VSW1NTSF9QVUJMSUNfS0VZXSJdCgpDUFUgPSAiMC4xIgpHUkFQSElDUyA9IFsgTElTVEVOICA9IjAuMC4wLjAiLFRZUEUgID0idm5jIl0KCk1FTU9SWSA9ICIxMjgiCkxPR08gPSAiaW1hZ2VzL2xvZ29zL2xpbnV4LnBuZyI="


Not that if we unpack that `VMTEMPLATE64` we obtain the following:

.. code::

    CONTEXT = [ NETWORK  ="YES",SSH_PUBLIC_KEY  ="$USER[SSH_PUBLIC_KEY]"]

    CPU = "0.1"
    GRAPHICS = [ LISTEN  ="0.0.0.0",TYPE  ="vnc"]

    MEMORY = "128"
    LOGO = "images/logos/linux.png"

Which demonstrates the capability of including a template into the appliance's data.

.. todo:: sunstone

Create a New MarketPlaceApp
--------------------------------------------------------------------------------

In order to create a MarketPlaceApp you will need to prepare a new template file with the following attributes:

+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|     Attribute      |                                                                                 Description                                                                                  |
+====================+==============================================================================================================================================================================+
| ``NAME``           | Required                                                                                                                                                                     |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``ORIGIN_ID``      | (**Required**) The ID of the source image. It must reference an available image and it must be in one of the supported datastores.                                           |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``TYPE``           | (**Required**) Must be ``IMAGE``.                                                                                                                                            |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``MARKETPLACE_ID`` | (**Required**) The target marketplace ID. Alternatively you can specify the ``MARKETPLACE`` name.                                                                            |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``MARKETPLACE``    | (**Required**) The target marketplace name. Alternatively you can specify the ``MARKETPLACE_ID`` name.                                                                       |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``DESCRIPTION``    | (Optional) Text description of the MarketPlaceApp.                                                                                                                           |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``PUBLISHER``      | (Optional) If not provided, the username will be used.                                                                                                                       |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``VERSION``        | (Optional) A string indicating the MarketPlaceApp version.                                                                                                                   |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``VMTEMPLATE64``   | (Optional) Creates this template (encoded in base64) pointing to the base image.                                                                                             |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| ``APPTEMPLATE64``  | (Optional) This is the image template (encoded in base64) that will be added to the registered image. It is useful to include parameteres like ``DRIVER`` or ``DEV_PREFIX``. |
+--------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Example:

.. code::

    $ cat marketapp.tpl
    NAME=TTYlinux
    ORIGIN_ID=0
    TYPE=image

    $ onemarketapp create marketapp.tpl -m "OpenNebula Public"
    ID: 40

.. todo:: sunstone

Exporting a MarketPlaceApp
--------------------------------------------------------------------------------

Using the CLI:

The command that exports the MarketPlaceApp is `onemarketapp export` which will return the ID of the new Image **and** the ID of the new associated template. If no template has been defined, it will return `-1`.

.. code::

    $ onemarketapp export 40 from_t1app -d 1
    IMAGE
        ID: 1
    VMTEMPLATE
        ID: -1

.. todo:: Sunstone

Additional Commands
--------------------------------------------------------------------------------

Like any other OpenNebula Resource, MarketPlaceApps respond to the base actions, namely:

* delete
* update
* chgrp
* chown
* chmod
* enable
* disable

Please take a look at the CLI reference to see how to use these actions. In Sunstone this options are also available.

Tuning & Extending
==================

System administrators and integrators are encouraged to modify these drivers in order to integrate them with their datacenter. Please refer to the :ref:`Market Driver Development <devel-market>` guide to learn about the driver details.

