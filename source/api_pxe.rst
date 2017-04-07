*********************************************
PXE Query
*********************************************

Request
^^^^^^^^^^^^^^^^

.. csv-table:: Request Format
    :header: Field, Description
    :widths: 5, 10

    URL, "http://localhost:8083/api/osinstall/v1/report/deviceMacInfo"
    encode, UTF-8
    method, HTTP POST
    payload, application/json

Payload
^^^^^^^^

.. csv-table:: Payload
    :header: Field, Type, Required, Description
    :widths: 5, 5, 5, 15

    Sn,string,yes,device serial number
    MAC,string,yes,device MAC address


Payload Sample 
^^^^^^^^^^^^^^^

.. code-block:: json

    {
       "Sn": "test",
       "Mac": "EA:1F:2d:3a:4H"
    }



Code Sample (PHP)
^^^^^^^^^^^^^^^^^^

.. code-block:: php

  <?php
    $data = array("Sn" => "test","Mac" => "EA:1F:2d:3a:4H");
    $str = json_encode($data);
    $ch = curl_init('http://localhost:8083/api/osinstall/v1/report/deviceMacInfo');
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
    * ``true`` success
    * ``false`` failed"
    Message, return message


Sample Response Message
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "Content": {
            "Result": "true"
        },
        "Message": "success",
        "Status": "success"
    }