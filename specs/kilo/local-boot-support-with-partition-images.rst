..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

==========================================
Local boot support with partition images
==========================================

https://blueprints.launchpad.net/ironic/+spec/local-boot-support-with-partition-images

This blueprint proposes to make the subsequent reboot of the baremetal from
local hard disk drive instead of pxe or vmedia, depending on the boot option
selected by the user.


Problem description
===================

At present ironic drivers that deploy partition images require ironic
conductor for subsequent reboots of the node.  The subsequent reboot
of the node will happen either through pxe or using virtual media. There is
no provision in the ironic driver to enable local boot with partition images.

Proposed change
===============

* Ironic driver should read the requested boot option ``local_boot``=``True``
  as capability from nova flavor which should be passed through instance_info
  field.

* Create a boot partition, along with root, swap, etc.

* Create a boot image, similar to boot cd used by ilo_iscsi driver, and
  write it to boot partition.

* Set the boot device to HDD persistently.

* Skip the steps for setting up vmedia and pxe for subsequent boot.


Alternatives
------------

While generating the partition images using disk-image-builder, we can use
"localboot" element, which is available in tripleo-image-elements project.
The downside of using this is that the local boot will be enabled during the
firt boot after node deploy. So it will be resets of the server to enable
localboot. Also the ironic conductor is not aware of this change and continue
to provide the pxe or vmedia boot for the reboot operation done from conductor.

Data model impact
-----------------

None.

REST API impact
---------------

None.

RPC API impact
--------------

None.

Driver API impact
-----------------

None.

Nova driver impact
------------------

None.

Security impact
---------------

None.

Other end user impact
---------------------

Deployer can specify the boot option request in nova flavor as capability.
``local_boot``=``True``

Scalability impact
------------------

None.

Performance Impact
------------------

None.

Other deployer impact
---------------------
Set ``local_boot``:``True`` as capability in node.properties.

Developer impact
----------------

None.

Implementation
==============

Assignee(s)
-----------

Primary assignee:
  faizan-barmawer

Other contributors:
  None

Work Items
----------

* Make necessary changes in ilo_iscsi, pxe_ipmitool driver to handle the
  local boot request.

* Implement the code to create boot partition.

* Implement the code to generate boot image.

* Make necessary changes in ilo_iscsi and pxe_ipmitool driver to skip setting
  up of the configuration for subsequent reboot.

* Set boot device to HDD persistently after the deploy before final reset.

Dependencies
============
https://review.openstack.org/141012
Require this nova virt ironic driver fix to pass down the capabilities from
nova flavor to ironic node's instance info field.

Testing
=======

* Unit testing.

Upgrades and Backwards Compatibility
====================================


Documentation Impact
====================

* Make changes to ironic install guide.

References
==========

