*********************************************
Hardware Template
*********************************************


Hardware Template Query
========================

Request
^^^^^^^^^^^^^^^^

.. csv-table:: Request Format
    :header: Field, Description
    :widths: 5, 10

    URL, "http://localhost:8083/api/osinstall/v1/device/getHardwareBySn"
    encode, UTF-8
    method, HTTP POST
    payload, application/json

Payload
^^^^^^^^

.. csv-table:: Payload
    :header: Field, Type, Required, Description
    :widths: 5, 5, 5, 15

    Sn,string,yes,serial number


Payload Sample 
^^^^^^^^^^^^^^^

.. code-block:: json

    {
       "Sn": "test",
    }



Code Sample (PHP)
^^^^^^^^^^^^^^^^^^

.. code-block:: php

  <?php
    $data = array("Sn" => "test");
    $str = json_encode($data);
    $ch = curl_init('http://localhost:8083/api/osinstall/v1/device/getHardwareBySn');
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

    Status, success or failure
    Content.Compamy, vendor name
    Content.Product, product name
    Content.ModelName, module name
    Content.Hardware, configure node
    Content.Hardware[].Name, target name
    Content.Hardware[].Scripts, script node 
    Content.Hardware[].Scripts[].script,"script content, encoded by ``base64decode``"
    Message, return message


Sample Response Message
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "Content": {
            "Company": "Dell",
            "Hardware": [
            {
                "Name": "RAID",
                "Scripts": [
                {
                    "Name": "RAID",
                    "Script": "L29wdC95dW5qaS9vc2luc3RhbGwvZGVsbC9yYWlkLnNoIC1jIC1sIDEw"
                }
                ]
            },
            {
                "Name": "OOB",
                "Scripts": [
                {
                    "Name": "network type",
                    "Script": "L29wdC95dW5qaS9vc2luc3RhbGwvZGVsbC9vb2Iuc2ggLW4gZGhjcA=="
                },
                {
                    "Name": "username",
                    "Script": "L29wdC95dW5qaS9vc2luc3RhbGwvZGVsbC9vb2Iuc2ggLXUgcm9vdA=="
                },
                {
                    "Name": "passowrd",
                    "Script": "L29wdC95dW5qaS9vc2luc3RhbGwvZGVsbC9vb2Iuc2ggLXAgY2Fsdmlu"
                }
                ]
            },
            {
                "Name": "BIOS",
                "Scripts": [
                {
                    "Name": "VT",
                    "Script": "L29wdC95dW5qaS9vc2luc3RhbGwvZGVsbC9iaW9zLnNoIC10IGVuYWJsZQ=="
                },
                {
                    "Name": "C-States",
                    "Script": "L29wdC95dW5qaS9vc2luc3RhbGwvZGVsbC9iaW9zLnNoIC1jIGRpc2FibGU="
                }
                ]
            }
            ],
            "ModelName": "R420",
            "Product": "PowerEdge"
        },
        "Message": "get hardware information success",
        "Status": "success"
    }