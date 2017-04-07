*********************************************
Log Update
*********************************************

Update Provision Job Progress
===============================

.. note::
    This API is used by BootOS agent to update OS provisioning progress and logs.

Request
^^^^^^^^^^^^^^^^

.. csv-table:: Request
    :header: Field, Description
    :widths: 5, 10

    URL, "http://localhost:8083/api/osinstall/v1/report/deviceInstallInfo"
    encode, UTF-8
    method, HTTP POST
    payload, application/json

Payload
^^^^^^^^

.. csv-table:: Payload
    :header: Field, Type, Required, Description
    :widths: 5, 5, 5, 15

    Sn,string,yes,device serial number
    InstallProgress,float(11，2),yes,"
        * valus between 0~1
        * -1 means provision job failed
        * 1 means provision job success
    "
    Title,string,no, message title
    InstallLog,string,no,"message body, requires ``base64encode`` encode"


Payload Sample 
^^^^^^^^^^^^^^^

.. code-block:: json

    {
       "Sn": "test",
       "InstallProgress": 0.1,
       "Title": "enter bootos",
       "InstallLog": "5byA5aeL6L+b5YWlYm9vdG9z"
    }



Code Sample (PHP)
^^^^^^^^^^^^^^^^^^

.. code-block:: php

   <?php
        $data = array("Sn" => "test","InstallProgress" => 0.1,"Title" => "enter bootos","InstallLog" => base64_encode("entering bootos"));
        $str = json_encode($data);
        $ch = curl_init('http://localhost:8083/api/osinstall/v1/report/deviceInstallInfo');
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