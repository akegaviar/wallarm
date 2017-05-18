.. _update-docker-ru:

=========================
Обновите Docker-контейнер
=========================

Для обновления модулей Wallarm, установленных в контейнере Docker,
вам потребуется выполнить следующие действия:

#. Загрузить обновленный образ.
#. Остановить текущий контейнер.
#. Запустить контейнер на новом образе.

Загрузите обновленный образ
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Выполните команду:

.. code-block:: command

   docker pull wallarm/node

Остановите текущий контейнер
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Выполните команду:

.. code-block:: command

   docker stop <container name>

Запустите контейнер на новом образе
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Выполните команду:

.. code-block:: command

   docker run -d -v /etc/wallarm/license.key -v /etc/wallarm/node.yaml -e NGINX_BACKEND=93.184.216.34 wallarm/node

.. seealso::

   - :ref:`installation-docker-ru`