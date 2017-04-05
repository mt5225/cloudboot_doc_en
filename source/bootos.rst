***************
BootOS
***************

About BootOS
=============

BootOS is a in-memory operation system. After loaded into system, BootOS could perform hardware configuration tasks such as:

    * upgrade server BIOS
    * modify RAID setting
    * configure OOB
    * disk partition
    * create file system

|

How It Works
^^^^^^^^^^^^^^

server power on -> start PXE process -> load BootOS kernel -> load initrd -> extract rootfs -> switch to rootfs -> run init -> start agent -> start shell

|

Functions
^^^^^^^^^^^^^^

* build-in agent to perform server configuration and provisioning tasks
* collect hardware information and upload to Cloudboot server
* receive configuration template from Cloudboot server and perform hardware configuration task on target server
* upload MAC address information to  Cloudboot server for PXE setting auto generation


|

Features
^^^^^^^^^^^^^^

* customized in-memory OS based on centos 6.7 kernel, includes latest hardware support
* include rpm/yum for easy extend
* running entirely in memory, donot store data on disk
* be able to collection and configure server hardware information such as RAIN/OOB/BIOS, etc.

|

Configure BootOS
=================

Basic Configure
^^^^^^^^^^^^^^^^^

Configure boot environment for BootOS::

    # cat /opt/cloudboot/var/lib/tftpboot/pxelinux.cfg/default
    DEFAULT menu.c32
    PROMPT 0
    TIMEOUT 30

    LABEL bootos
    MENU LABEL ^BootOS
    MENU DEFAULT
    KERNEL http://osinstall.idcos.com/bootos/vmlinuz
    APPEND initrd=http://osinstall.idcos.com/bootos/initrd.img console=tty0 selinux=0 biosdevname=0 SERVER_ADDR=http://osinstall.idcos.com
    IPAPPEND 2

Parameters in above boot configuration file:

    * ``TIMEOUT 30``, wait for 3 seconds before boot into BootOS
    * load vmlinuz and initrd.img by http instead of tftp
    * ``biosdevname=0``, force network adapter naming to follow ethX standard
    * ``SERVER_ADDR=http://osinstall.idcos.com``, callback URL, adjust according to target environment
    * ``IPAPPEND 2``, force naming PXE network adapter as eth0

    .. note::
        **SERVER_ADDR** and **IPAPPEND 2** are required for BootOS boot properly.


Developer Mode
^^^^^^^^^^^^^^^

The difference between user mode and developer mode is:

    * in user mode, after BootOS agent collect and upload hardware informatin to Cloudboot server, Cloudboot server will check if it in Cloudboot's support list, if not, BootOS agent will *NOT* perform hardware configuration on target server.
    * in developer mode, BootOS agent will *ALWAYS* perform hardware configuration on target server.

To switch to developer mode, add ``DEVELOPER=1`` setting in PXE configuration file, as following::

    # cat /opt/cloudboot/var/lib/tftpboot/pxelinux.cfg/default
    DEFAULT menu.c32
    PROMPT 0
    TIMEOUT 30

    LABEL bootos
    MENU LABEL ^BootOS
    MENU DEFAULT
    KERNEL http://osinstall.idcos.com/bootos/vmlinuz
    APPEND initrd=http://osinstall.idcos.com/bootos/initrd.img console=tty0 selinux=0 biosdevname=0 SERVER_ADDR=http://osinstall.idcos.com DEVELOPER=1
    IPAPPEND 2


Advance Options
^^^^^^^^^^^^^^^^