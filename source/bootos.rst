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

Parameter description:

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

Use following options to customize BootOS agent behavior::

    # cat /opt/cloudboot/var/lib/tftpboot/pxelinux.cfg/default
    DEFAULT menu.c32
    PROMPT 0
    TIMEOUT 30

    LABEL bootos
    MENU LABEL ^BootOS
    MENU DEFAULT
    KERNEL http://osinstall.idcos.com/bootos/vmlinuz
    APPEND initrd=http://osinstall.idcos.com/bootos/initrd.img console=tty0 selinux=0 biosdevname=0 SERVER_ADDR=http://osinstall.idcos.com PRE=http://osinstall.idcos.com/pre.sh POST=http://osinstall.idcos.com/post.py
    IPAPPEND 2

Parameter description:

    * ``PRE=http://osinstall.idcos.com/pre.sh``, run  *pre.sh* right after agent started
    * ``POST=http://osinstall.idcos.com/post.py``, run *post.py* before system reboot

 .. note::
        pre/post action supports script file as well as binary file

|

Driver Update
===============

.. note::
    User can update BootOS by apply CloudBoot updates, below update process is about update BootOS driver manually

Browse Existing Drivers
^^^^^^^^^^^^^^^^^^^^^^^^^

BootOS is based on Centos and the driver folder remains the same, take network driver for example::

    # ls /lib/modules/`uname -r`/kernel/drivers/net/
    3c59x.ko     b44.ko      cnic.ko   e100.ko       ifb.ko      macvtap.ko     netxen          ppp_generic.ko  r6040.ko    slhc.ko        tg3.ko           virtio_net.ko
    8139cp.ko    benet       cxgb3     enic          igb         mdio.ko        niu.ko          ppp_mppe.ko     r8169.ko    slip.ko        tlan.ko          vmxnet3
    8139too.ko   bna         cxgb4     epic100.ko    igbvf       mii.ko         ns83820.ko      pppoe.ko        s2io.ko     smsc9420.ko    tulip            vxge
    8390.ko      bnx2.ko     cxgb4vf   ethoc.ko      ipg.ko      mlx4           pch_gbe         pppol2tp.ko     sc92031.ko  starfire.ko    tun.ko           vxlan.ko
    acenic.ko    bnx2x       dl2k.ko   fealnx.ko     ixgb        mlx5           pcmcia          pppox.ko        sfc         sundance.ko    typhoon.ko       wan
    amd8111e.ko  bonding     dnet.ko   forcedeth.ko  ixgbe       myri10ge       pcnet32.ko      ppp_synctty.ko  sis190.ko   sungem.ko      usb              wimax
    atl1c        can         dummy.ko  hyperv        ixgbevf     natsemi.ko     phy             qla3xxx.ko      sis900.ko   sungem_phy.ko  veth.ko          wireless
    atl1e        cassini.ko  e1000     i40e          jme.ko      ne2k-pci.ko    ppp_async.ko    qlcnic          skge.ko     sunhme.ko      via-rhine.ko     xen-netfront.ko
    atlx         chelsio     e1000e    i40evf        macvlan.ko  netconsole.ko  ppp_deflate.ko  qlge            sky2.ko     tehuti.ko      via-velocity.ko



Update Driver
^^^^^^^^^^^^^^

Take igb driver for example, first, you need to install developer tools, kernel headers and related libraries::

    yum install kernel-devel kernel-headers gcc make rpm-build wget

Then download latest igb dirver source code and compile::

    mkdir -p /root/rpmbuild/SOURCES/
    wget -P /root/rpmbuild/SOURCES/ https://downloadmirror.intel.com/13663/eng/igb-5.3.2.tar.gz
    tar zxpf /root/rpmbuild/SOURCES/igb-5.3.2.tar.gz
    rpmbuild -bb igb-5.3.2/igb.spec

Finally copy generated rpm file ``/root/rpmbuild/RPMS/x86_64/igb-5.3.2-1.x86_64.rpm`` into BootOS.