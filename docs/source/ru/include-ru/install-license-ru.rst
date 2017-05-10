.. _install_license_ru:

Wallarm отправляет лицензионный ключ на адрес электронной почты, указываемый
при создании учетной записи Wallarm.

#. Скопируйте содержимое ключа в файл ``/etc/wallarm/license.key``
#. Установите права доступа для ключа:

  .. code-block:: command

     chmod 0640 /etc/wallarm/license.key

  .. code-block:: command

     chown root:wallarm /etc/wallarm/license.key