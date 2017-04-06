****************************************
Cloudboot Architecture
****************************************


Architecture Diagram
======================


.. image:: cloudboot_arch.png
    :align: center
    :alt: Cloudboot System Architecture


As shown above, Cloudboot has six functional modules. Modules are oosely-coupled and communicate with each other via API.


Functional Modules
===================

.. csv-table::
    :header: Module Name, Description
    :widths: 5, 20

    UI, "web-based user interface, for user input, information display and query such as provision progress, operation logs, search, etc."
    Server, "provides API service to UI module"
    Hardware, "hardware configuration and management module"
    CMDB, "lightweight configuration management system for server hardware, OS templates, configuration and user information"
    BootOS, "boot os with config agent to collect and config hardware settings of target server"
    Base Service, "provide base service like dhcp/tftp/http/dns/samba for OS provision"





OS Provision Workflow
======================

1. user submit OS provisioning task via web UI
2. boot server manually or by remote control interface
3. target server boot into BootOS
4. agent in BootOS start automatically, check if target server is in provision queue
5. if not, sleep 60 seconds, then check again until server is listed in provision queue
6. *Server* check if target supports hardware configuration
7. if not, notify user to configure hardware manually
8. *BootOS agent* fetch configuration template from *Server* and perform hardware configuration
9. *BootOS agent* upload MAC address information, *Server*  generate pxe related file
10. *BootOS agent* boot target into network boot mode, start OS provisioning
11. Install OS according to OS templage
12. Run post installation script
13. Update provisioning progress, update CMDB

.. image:: cloudboot_os_flow.png
    :align: center
    :alt: COS Provision Workflow




New Hardware Support Workflow
==============================

Cloudboot configures server hardware via open standard interface, any x86 server compatible with the interface can be configured automatically. To support hardware configuration of new server module:
1. x86 server vendor submit hardware configuration implementation to CloudJ/iDCOS
2. iDCOS verify the implementation and push the update to IDCStore
3. End user update Cloudboot with new supported server online, or install it by offline package

.. image:: cloudboot_upgrade.png
    :align: center
    :alt: COS Provision Workflow