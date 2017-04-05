***************
Installation
***************


System Requirements
====================

Cloudboot requires RHEL/Centos 6.0 or higher.

While the system can operate with lower specs, these are the recommendations for the best experience while testing or deploying Cloudboot:

+--------------------------------------+-----------------------------------+
|            Testing                   |         Production                |
+======================================+===================================+
|  * Dual CPU system                   | * Quad core CPU system            |
|  * 2GB RAM                           | *  8GB RAM                        |
|  * 100GB storage                     | * 200GB storage                   |
+--------------------------------------+-----------------------------------+

.. note::
    the storage requirement includes disk space needed for OS media (OS distribution). In real world deploayment, OS media is normally mounted from external/network locations.



System Configuration
=====================

* Turn off OS firewall::

    service iptables stop
    chkconfig iptables off


* Turn off selinux (requires OS reboot)::

    sed -i.bak '/^SELINUX=/cSELINUX=disabled' /etc/sysconfig/selinux


|


Install Cloudboot via RPM Package 
==================================

* Download Cloudboot RPM from http://www.idcos.com/store/x86

* Install RPM Package::

    rpm -ivh cloudboot-$version.x86_64.rpm
    Preparing...                ########################################### [100%]
    1:cloudboot              ########################################### [100%]

|


Setup Cloudboot
=================

Add DCHP subnet 
^^^^^^^^^^^^^^^^

.. code-block:: bash
    :linenos:

    # cat /opt/cloudboot/etc/dhcp/dhcpd.conf
    allow booting;
    allow bootp;
    ddns-update-style none;
    ping-check true;
    ping-timeout 3;
    default-lease-time 120;
    max-lease-time 600;
    authoritative;
    next-server osinstall.idcos.com;
    filename "undionly.kkpxe";
    option domain-name "idcos.com";
    option domain-name-servers 192.168.0.1;
    option root-path "osinstall.idcos.com:/";

    subnet 192.168.0.0 netmask 255.255.255.0 {
        range 192.168.0.101 192.168.0.200;
        option routers 192.168.0.1;
    }

|

.. note::
    Please adjust DHCP subnet setting according to target environment.



Import OS Media
^^^^^^^^^^^^^^^^

Take Centos 6.7 for example::

    mount -o loop CentOS-6.7-x86_64-bin-DVD1.iso /media
    rsync -az /media/ /opt/cloudboot/home/www/centos/6.7/os/x86_64/
    umount /media


Start Cloudboot Service
^^^^^^^^^^^^^^^^^^^^^^^^

::

    service cloudboot start
    Starting dhcpd:                                            [  OK  ]
    Starting dnsmasq:                                          [  OK  ]
    Starting xinetd:                                           [  OK  ]
    Starting nginx:                                            [  OK  ]
    Starting SMB services:                                     [  OK  ]
    Starting mysqld:                                           [  OK  ]
    Starting cloudboot-server:                                 [  OK  ]