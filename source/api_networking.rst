*********************************************
Device Networking Query
*********************************************

Request
^^^^^^^^^^^^^^^^

.. csv-table:: Request Format
    :header: Field, Description
    :widths: 5, 10

    URL, "http://localhost:8083/api/osinstall/v1/device/getNetworkBySn"
    encode, UTF-8
    method, HTTP GET
    payload, text/html

Payload
^^^^^^^^

.. csv-table:: Payload
    :header: Field, Type, Required, Description
    :widths: 5, 5, 5, 15

    sn,string,yes,device serial number
    type,string,yes,"specify result format, could be ``json`` or ``raw``, default is ``raw``"


Request Sample 
^^^^^^^^^^^^^^^

::

    http://localhost:8083/api/osinstall/v1/device/getNetworkBySn?sn=test&type=raw




Code Sample (PHP)
^^^^^^^^^^^^^^^^^^

.. code-block:: php

    <?php
        $url = "http://localhost:8083/api/osinstall/v1/device/getNetworkBySn?sn=test&type=raw";
        $content = file_get_contents($url);
        echo $content;
    ?>



Sample Response Message
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    HOSTNAME="idcos-test"
    IPADDR="192.168.0.3"
    NETMASK="255.255.255.0"
    GATEWAY="192.168.0.1"
    VLAN="0"
    Trunk="no"
    Bonding="no"
    HWADDR="53:54:00:99:2D:7C"