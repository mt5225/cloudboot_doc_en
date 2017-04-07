*************
User Related
*************

Login
======

Request
^^^^^^^^^^^^^^^^

.. csv-table:: Request
    :header: Field, Description
    :widths: 5, 10

    URL, "http://localhost:8083/api/osinstall/v1/user/login"
    encode, UTF-8
    method, HTTP POST
    payload, application/json

Payload
^^^^^^^^

.. csv-table:: Payload
    :header: Field, Type, Required, Description
    :widths: 5, 5, 5, 15

    username,string,yes,username
    password,string,yes,password


Payload Sample 
^^^^^^^^^^^^^^^

.. code-block:: json

    {
    "Username": "admin",
    "Password": "admin"
    }



Code Sample (PHP)
^^^^^^^^^^^^^^^^^^

.. code-block:: php

    <?php
        $data = array(
        "Username" => "admin",
        "Password" => "admin",
        );
        $str = json_encode($data);
        $ch = curl_init('http://localhost:8083/api/osinstall/v1/user/login');
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
    Content, user infomation and access token
    Message, return message


Sample Response Message
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "Content": {
            "ID": 1,
            "Username": "admin",
            "Name": "Super Administrator",
            "Role": "Administrator",
            "AccessToken": "097B55289D87C26FC33C2B0F7F80701D"
        },
        "Message": "Login Success",
        "Status": "success"
    }

|


Logout
=======

Request
^^^^^^^^^^^^^^^^

.. csv-table:: Request
    :header: Field, Description
    :widths: 5, 10

    URL, "http://localhost:8083/api/osinstall/v1/user/logout"
    encode, UTF-8
    method, HTTP POST
    payload, application/json

Payload
^^^^^^^^

.. csv-table:: Payload
    :header: Field, Type, Required, Description
    :widths: 5, 5, 5, 15

    AccessToken,string,yes,access token


Payload Sample 
^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "AccessToken": "097B55289D87C26FC33C2B0F7F80701D"
    }



Code Sample (PHP)
^^^^^^^^^^^^^^^^^^

.. code-block:: php

   <?php
        $data = array(
        "AccessToken" => "097B55289D87C26FC33C2B0F7F80701D",
        );
        $str = json_encode($data);
        $ch = curl_init('http://localhost:8083/api/osinstall/v1/user/logout');
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
    Message, return message


Sample Response Message
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: json

    {
        "Message": "User logout success",
        "Status": "success"
    }