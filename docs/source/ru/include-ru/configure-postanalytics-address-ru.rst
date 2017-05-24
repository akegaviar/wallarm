.. _configure-postanalytics-address-ru:

Добавьте в :file:`/etc/nginx-wallarm/conf.d/wallarm.conf` адреса серверов
постаналитики:

.. code-block:: ini

     upstream wallarm_tarantool {
        server <ip1>:3313;
        server <ip2>:3313;
        ...
        server <ipN>:3313;
    }

    ...

    wallarm_tarantool_upstream wallarm_tarantool;