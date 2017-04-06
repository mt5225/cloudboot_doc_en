*********************************************
Hardware Configuration 
*********************************************

Cloudboot supports automate hardware configuration as part of provisioning process. To support automate hardware configuration, x86 server vendors needs to provide a set of vendor scripts, which will be executed by BootOS agent during the provisioning.

|

Hardware Configuration Basics
===============================

Server Hardware Settings 
^^^^^^^^^^^^^^^^^^^^^^^^^

Server hardware settings includes RAID, OOB and BIOS:

    * RAID: RAID 0 / RAID 1 / RAID 5 / RAID 10 
    * OOB: user/password/network/license
    * BIOS: VT / HT / NUMA / C-State / Turbo, etc settings

|

Vendor Tools
^^^^^^^^^^^^^^^

Different server vendors have its own tools to configure server hardware, for instance:

.. csv-table::
    :header:  Vendor, Tools, Description
    :widths: 5, 5, 20

    Dell, MegaCli, LSI MegaRAID configuration
    Dell, syscfg, configure BIOS
    Dell, racadm, configure DRAC
    Dell, dsu, configure firmware
    HP, hpacucli, configure HP Smart Array Raid
    HP, conrep, configure BIOS
    HP, hponcfg, configure iLO
    HP, firmware-tools, Online ROM Flash upgrade
    Lenovo, MegaRAID, configure RAID
    Lenovo, asu, configure BIOS

|

Standardized Toolset
=====================

About 
^^^^^^^

Cloudboot provides a standardized hardware configuration toolset, which includes configuration scripts for mainstream x86 server vendors. Toolset is free and opensource, you may use rsync to download it to your Cloudboot instance::

    rsync -azHP --delete mirror.idcos.com::hw-tools /opt/cloudboot/home/www/hw-tools

The toolset source code can be found at https://github.com/idcos/osinstall-hardware


RAID Configuration Script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cloudboot standardlize RAID configuration interface by wrap it into ``raid.sh`` script::

    # /opt/yunji/osinstall/dell/raid.sh
    raid.sh: raid config tool
    Usage: raid.sh [OPTION...]
    -c, --clear                           Clear raid config
    -r, --raid 0/1/5/10                   Raid level for disk
    -d, --disk [0|1,2|3-5|6-|all]         Disk slot num
    -H, --hotspare [0|1,2|3-5|6-|all]     Hotspare disk slot num
    -i, --init                            Initialize all disk
    -h, --help                            Show this help message

Parameters:

    * -c clean old configuration
    * -r set RAID level, support RAID 0/1/5/10
    * -d disk ID, supports single disk, multiple disks (seperated by comma), disk ID range (for instance, ``3-5`` means disk ID 3,4,5, ``6-`` means all disks with id equal or greater than 6)


``raid.sh`` usage examples:

    * create RAID 0 on all disks, then init disks::
    
        /opt/yunji/osinstall/dell/raid.sh -c -r 10 -d all -i

    * create RAID 1 using first two disks, then create RAID 5 using rest of disks::

        /opt/yunji/osinstall/dell/raid.sh -c -r 1 -d 0,1
        /opt/yunji/osinstall/dell/raid.sh -r 5 -d 2- -i

    * create RAID 5 using first 4 disks, then create HotSpare using disk 5::

        /opt/yunji/osinstall/dell/raid.sh -c -r 5 -d 0-4
        /opt/yunji/osinstall/dell/raid.sh -d 5 -i

    
Create Configure Script
===========================

All vendor specific hardware configure script is located under /opt/yunji/osinstall/**vendor**/ folder. Take Dell script for example:


RAID script
^^^^^^^^^^^^^^

::

    # /opt/yunji/osinstall/dell/raid.sh
    raid.sh: raid config tool
    Usage: raid.sh [OPTION...]
    -c, --clear         Clear raid config
    -l, --level         Raid level for all disk
    -s, --size          Set size (default MB) of virtual drive

    Help options:
    -h, --help          Show this help message


OOB Script
^^^^^^^^^^^^^^

::

    # /opt/yunji/osinstall/dell/oob.sh
    oob.sh: oob config tool
    Usage: oob.sh [OPTION...]
    -n, --network       Set the IP address source
    -i, --ip            Set the IP address
    -m, --netmask       Set the Subnet Mask
    -g, --gateway       Set the Default Gateway IP
    -u, --username      Enable user access mode for userid
    -p, --password      Set the user password
    -r, --reset         Instructs the BMC to perform a cold reset

    Help options:
    -h, --help          Show this help message


BIOS Script
^^^^^^^^^^^^^^^^

::

    # /opt/yunji/osinstall/dell/bios.sh
    bios.sh: dell bios config tool
    Usage: bios.sh [OPTION...]
    -t, --virtualization          Enable or disabled Virtualization Technology
    -c, --cstates                 Enable or disabled CPU C-States

    Help options:
    -h, --help          Show this help message

|


Packaging Standard
===================

RPM Dependence
^^^^^^^^^^^^^^^

Specify RPM dependence in SPEC file, take Dell script for example:

    * RAID: depends on ``MegaCli``
    * OOB: depends on ``ipmitool``
    * BIOS: depends on ``syscfg``

Script Path
^^^^^^^^^^^^^

Locate script in vendor folder, for instance::

    # tree
    .
    |-- dell
    |   |-- bios.sh
    |   |-- oob.sh
    |   `-- raid.sh
    |-- hp
    |   |-- bios.sh
    |   |-- oob.sh
    |   `-- raid.sh
    `-- inspur
        |-- bios.sh
        |-- oob.sh
        `-- raid.sh


Query script path in RPM:

``rpm -ql dell-hw-tools``

Command output::

    /opt/yunji/osinstall/dell/bios.sh
    /opt/yunji/osinstall/dell/oob.sh
    /opt/yunji/osinstall/dell/raid.sh


SPEC File
^^^^^^^^^^^

Create SPEC file for RPM generation, for instance::

    # cat dell-hw-tools.spec
    %define __spec_prep_post true
    %define __spec_prep_pre true
    %define __spec_build_post true
    %define __spec_build_pre true
    %define __spec_install_post true
    %define __spec_install_pre true
    %define __spec_clean_post true
    %define __spec_clean_pre true
    %define _binary_filedigest_algorithm 1
    %define _build_binary_file_digest_algo 1
    %define _binary_payload w9.gzdio

    Name: dell-hw-tools
    Version: 0.1
    Release: 1
    Summary: none
    AutoReqProv: no
    BuildRoot: %buildroot
    Prefix: /opt/yunji/osinstall/dell
    Group: default
    License: GPLv3+
    Vendor: CentOS
    URL: none
    Packager: admin@dell.com

    Requires: MegaCli
    Requires: ipmitool
    Requires: syscfg

    %Note
    none

    %prep

    %build

    %install

    %clean

    %files
    %defattr(-,root,root,-)
    /opt/yunji/osinstall/dell/oob.sh
    /opt/yunji/osinstall/dell/raid.sh
    /opt/yunji/osinstall/dell/bios.sh

    %changelog


Create RPM
^^^^^^^^^^^

Build RPM by ``rpmbuild`` command with SPEC configuration file:

    ``rpmbuild -bb dell-hw-tools.spec``

Command output::

    Executing(%prep): /bin/sh -e /var/tmp/rpm-tmp.SyB7Tz
    Executing(%build): /bin/sh -e /var/tmp/rpm-tmp.NR8Yga
    Executing(%install): /bin/sh -e /var/tmp/rpm-tmp.O7eNEK
    Processing files: dell-hw-tools-0.1-1.x86_64
    Checking for unpackaged file(s): /usr/lib/rpm/check-files /root/rpmbuild/BUILDROOT/dell-hw-tools-0.1-1.x86_64
    Wrote: /root/rpmbuild/RPMS/x86_64/dell-hw-tools-0.1-1.x86_64.rpm
    Executing(%clean): /bin/sh -e /var/tmp/rpm-tmp.OoeaBV


Test RPM 
^^^^^^^^^^

To test RPM package generated by ``rpmbuild``, run the following command in BootOS:


``yum install dell-hw-tools``

Command output::

    Loaded plugins: fastestmirror, security
    Setting up Install Process
    Loading mirror speeds from cached hostfile
    base                                                                                                                                                     | 3.7 kB     00:00
    extras                                                                                                                                                   | 3.4 kB     00:00
    idcos                                                                                                                                                    | 2.9 kB     00:00
    idcos/primary_db                                                                                                                                         |  40 kB     00:00
    updates                                                                                                                                                  | 3.4 kB     00:00
    updates/primary_db                                                                                                                                       | 2.7 MB     00:02
    Resolving Dependencies
    --> Running transaction check
    ---> Package dell-hw-tools.x86_64 0:0.1-1 will be installed
    --> Processing Dependency: syscfg for package: dell-hw-tools-0.2-1.x86_64
    --> Processing Dependency: ipmitool for package: dell-hw-tools-0.2-1.x86_64
    --> Processing Dependency: MegaCli for package: dell-hw-tools-0.2-1.x86_64
    --> Running transaction check
    ---> Package MegaCli.noarch 0:8.07.10-1 will be installed
    ---> Package ipmitool.x86_64 0:1.8.11-29.el6_7 will be installed
    ---> Package syscfg.x86_64 0:5.1.0-4.70.1.el6 will be installed
    --> Processing Dependency: srvadmin-isvc for package: syscfg-5.1.0-4.70.1.el6.x86_64
    --> Processing Dependency: srvadmin-hapi for package: syscfg-5.1.0-4.70.1.el6.x86_64
    --> Processing Dependency: srvadmin-deng for package: syscfg-5.1.0-4.70.1.el6.x86_64
    --> Processing Dependency: libdchipm.so.8()(64bit) for package: syscfg-5.1.0-4.70.1.el6.x86_64
    --> Processing Dependency: libdchbas.so.8()(64bit) for package: syscfg-5.1.0-4.70.1.el6.x86_64
    --> Running transaction check
    ---> Package srvadmin-deng.x86_64 0:8.1.0-4.8.1.el6 will be installed
    --> Processing Dependency: srvadmin-omilcore for package: srvadmin-deng-8.1.0-4.8.1.el6.x86_64
    --> Processing Dependency: srvadmin-omilcore for package: srvadmin-deng-8.1.0-4.8.1.el6.x86_64
    ---> Package srvadmin-hapi.x86_64 0:8.1.0-4.10.2.el6 will be installed
    ---> Package srvadmin-isvc.x86_64 0:8.1.0-4.38.1.el6 will be installed
    --> Running transaction check
    ---> Package srvadmin-omilcore.x86_64 0:8.1.0-4.85.1.el6 will be installed
    --> Processing Dependency: smbios-utils-bin for package: srvadmin-omilcore-8.1.0-4.85.1.el6.x86_64
    --> Running transaction check
    ---> Package smbios-utils-bin.x86_64 0:2.2.27-4.4.1.el6 will be installed
    --> Processing Dependency: libsmbios = 2.2.27-4.4.1.el6 for package: smbios-utils-bin-2.2.27-4.4.1.el6.x86_64
    --> Processing Dependency: libsmbios_c.so.2()(64bit) for package: smbios-utils-bin-2.2.27-4.4.1.el6.x86_64
    --> Processing Dependency: libsmbios.so.2()(64bit) for package: smbios-utils-bin-2.2.27-4.4.1.el6.x86_64
    --> Running transaction check
    ---> Package libsmbios.x86_64 0:2.2.27-4.4.1.el6 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ================================================================================================================================================================================
    Package                                        Arch                                Version                                          Repository                            Size
    ================================================================================================================================================================================
    Installing:
    dell-hw-tools                                  x86_64                              0.1-1                                            idcos                                3.3 k
    Installing for dependencies:
    MegaCli                                        noarch                              8.07.10-1                                        idcos                                1.5 M
    ipmitool                                       x86_64                              1.8.11-29.el6_7                                  updates                              464 k
    libsmbios                                      x86_64                              2.2.27-4.4.1.el6                                 idcos                                2.0 M
    smbios-utils-bin                               x86_64                              2.2.27-4.4.1.el6                                 idcos                                119 k
    srvadmin-deng                                  x86_64                              8.1.0-4.8.1.el6                                  idcos                                730 k
    srvadmin-hapi                                  x86_64                              8.1.0-4.10.2.el6                                 idcos                                959 k
    srvadmin-isvc                                  x86_64                              8.1.0-4.38.1.el6                                 idcos                                7.8 M
    srvadmin-omilcore                              x86_64                              8.1.0-4.85.1.el6                                 idcos                                 29 k
    syscfg                                         x86_64                              5.1.0-4.70.1.el6                                 idcos                                436 k

    Transaction Summary
    ================================================================================================================================================================================
    Install      10 Package(s)

    Total download size: 14 M
    Installed size: 43 M
    Is this ok [y/N]:
