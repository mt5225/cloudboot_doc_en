*********************************************
Virtual Environment Provision
*********************************************

|

Provision Virtual Host
=======================

In the following example, we create a lightweight KVM host environment.

Hardware Settings
^^^^^^^^^^^^^^^^^^

* x86 server
* Support **Intel® Virtualization Technology** and be turned on in BIOS


System Settings 
^^^^^^^^^^^^^^^^^^^

* RedHat/CentOS 6.0 or higher, with kvm support installed
* Firewall is off
* selinux  is off
* Host and guest OS is in same subnet
* Create a seperated LVM pool for disk allocation
* CloudBoot server could ssh to host using certificate (instead of password)


Host Template
^^^^^^^^^^^^^^

Cloudboot provides a sample templage named ``centos6.7-kvmserver`` for user referance, as shown below:

.. code-block:: bash

    ## create network bridge br0
    # cat > /etc/sysconfig/network-scripts/ifcfg-br0 <<EOF
    DEVICE=br0
    BOOTPROTO=none
    IPADDR=$IPADDR
    NETMASK=$NETMASK
    ONBOOT=yes
    TYPE=Ethernet
    NM_CONTROLLED=no
    TYPE=Bridge
    DELAY=0
    EOF

    # cat > /etc/sysconfig/network-scripts/ifcfg-eth0 <<EOF
    DEVICE=eth0
    BOOTPROTO=none
    ONBOOT=yes
    TYPE=Ethernet
    NM_CONTROLLED=no
    TYPE=Ethernet
    BRIDGE=br0
    EOF

    ### create LVM Pool named guest_images_lvm，edit /opt/cloudboot/etc/cloudboot-server/cloudboot-server.conf to change its settings
    virsh pool-define-as guest_images_lvm logical - - - VolGroup0 /dev/VolGroup0
    virsh pool-autostart guest_images_lvm
    virsh pool-start guest_images_lvm

    ### ssh channel from cloudboot server to host
    test -f /opt/cloudboot/root/.ssh/id_rsa || ssh-keygen -t rsa -f /opt/cloudboot/root/.ssh/id_rsa -C '' -N ''
    chmod 600 /opt/cloudboot/root/.ssh/*
    ssh-copy-id -i /opt/cloudboot/root/.ssh/id_rsa.pub [host_ip_address]


|

Virtual Machine Provision
==========================

* User could create virtual machine directly from CloudBoot UI after provision and refresh the host machine. It also support day 2 operation like start/stop/dicommission.

* While create a new virtual machine, user could specify hardware setting such as nubmer of CPU, memory, disk size, etc.

* OS provision settings for virtual machine is the same as the one for physical machines.

.. note:: CloudBoot use ``MAC address`` as virtual machine's serial number.
