*********************************************
Provision Job Status Query
*********************************************

Request
^^^^^^^^^^^^^^^^

.. csv-table:: Request
    :header: Field, Description
    :widths: 5, 10

    URL, "http://localhost:8083/api/osinstall/v1/device/isInInstallList"
    encode, UTF-8
    method, HTTP POST
    payload, application/json

Payload
^^^^^^^^

.. csv-table:: Payload
    :header: Field, Type, Required, Description
    :widths: 5, 5, 5, 15

    Sn,string,yes,device serial number


Payload Sample 
^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "Sn": "test"
    }



Code Sample (PHP)
^^^^^^^^^^^^^^^^^^

.. code-block:: php

   <?php
        $data = array("Sn" => "test");
        $str = json_encode($data);

        $ch = curl_init('http://localhost:8083/api/osinstall/v1/device/isInInstallList');
        curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
        curl_setopt($ch, CURLOPT_POSTFIELDS, $str);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array(
            'Content-Type: application/json',
            'Content-Length: ' . strlen($str))
        );

        $result = curl_exec($ch);
        echo $result;
    ?>


Response 
^^^^^^^^^^^

.. csv-table:: Response Format
    :header: Field, Description
    :widths: 5, 10

    Status, success or failure
    Content.Result, "
    * ``true`` device is in provision queue
    * ``false`` device is NOT in provision queue"
    Message, return message


Sample Response Message
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "Content": {
            "Result": "true"
        },
        "Message": "Device is in provisioning queue",
        "Status": "success"
    }