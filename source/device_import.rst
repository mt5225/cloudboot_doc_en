***************
Data Import 
***************

Import Device
==============

Request Format
^^^^^^^^^^^^^^^^

.. csv-table:: Request Format
    :header: Field, Description
    :widths: 5, 10

    URL, "http://localhost:8083/api/osinstall/v1/device/add"
    encode, UTF-8
    method, HTTP POST
    payload, JSON

Payload
^^^^^^^^

.. csv-table:: Payload
    :header: Field, Type, Required, Description
    :widths: 5, 5, 5, 15

    Sn,string,yes,device serial number
    Hostname,string,yes,Hostname
    Ip,string,yes,ip address
    ManageIp, string,no, management ip
    NetworkID, int, yes, networks ID
    ManageNetworkID, int,yes, management network ID
    OsID, int,yes, os_configs ID
    HardwareID, int, yes, hardwares ID
    SystemID,int, yes, system_configs ID
    LocationID,int,yes, location ID
    AssetNumber,string,no, asset ID
    AccessToken,string,yes, user access token




Payload Sample 
^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "Sn": "test",
        "Hostname": "idcos-test",
        "Ip": "192.168.0.3",
        "ManageIp": "192.168.1.1",
        "NetworkID": 6,
        "ManageNetworkID": 1,
        "OsID": 2,
        "HardwareID": 1,
        "SystemID": 1,
        "LocationID": 33,
        "AssetNumber": "CB20151216001",
        "AccessToken": "097B55289D87C26FC33C2B0F7F80701D",
    }



Code Sample (PHP)
^^^^^^^^^^^^^^^^^^

.. code-block:: php

    <?php
       $data = array(
        "Sn" => "test",
        "Hostname" => "idcos-test",
        "Ip" => "192.168.0.3",
        "ManageIp" => "192.168.1.1",
        "NetworkID" => 6,
        "ManageNetworkID" => 1,
        "OsID" => 2,
        "HardwareID" => 1,
        "SystemID" => 1,
        "LocationID" => 33,
        "AssetNumber" => "CB20151216001",
        "AccessToken" => "097B55289D87C26FC33C2B0F7F80701D",
        );
        $str = json_encode($data);
        $ch = curl_init('http://localhost:8083/api/osinstall/v1/device/add');
        curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
        curl_setopt($ch, CURLOPT_POSTFIELDS, $str);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array(
            'Content-Type: application/json',
            'Content-Length: ' . strlen($str))
        );

        $result = curl_exec($ch);
        echo curl_error($ch);
        echo $result;
    ?>


Response 
^^^^^^^^^^^

.. csv-table:: Response Format
    :header: Field, Description
    :widths: 5, 10

    Status, success or failures
    Message, return message


Sample Response Message
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "Message": "success",
        "Status": "success"
    }
