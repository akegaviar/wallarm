.. _configure-postanalytics-ru:

Модуль постаналитики использует находящееся в памяти хранилище Tarantool.

Вам необходимо указать объем оперативной памяти, который будет выделен под
хранилище Tarantool.

**Укажите объем оперативной памяти для Tarantool:**

.. list-table::
   :widths: 10 30
   :header-rows: 1

   * - Операционная система
     - Команда
   * - Debian 7.x (wheezy)
     - :command:`vi /etc/default/wallarm-tarantool`
   * - Debian 8.x (jessie)
     - :command:`vi /etc/default/wallarm-tarantool`
   * - Ubuntu 14.04 LTS (trusty)
     - :command:`vi /etc/default/wallarm-tarantool`
   * - Ubuntu 16.04 LTS (xenial)
     - :command:`vi /etc/default/wallarm-tarantool`
   * - CentOS 6.x
     - :command:`vi /etc/sysconfig/wallarm-tarantool`
   * - CentOS 7.x
     - :command:`vi /etc/sysconfig/wallarm-tarantool`

Количество памяти влияет на качество работы статистических алгоритмов.
Рекомендуемое значение — 75% от общей памяти сервера. Например, если у сервера
32 ГБ памяти, оптимально выделить под хранилище 24 ГБ.

Укажите размер выделяемой памяти в конфигурационном файле Tarantool директивой
``SLAB_ALLOC_ARENA``.

Например:

.. code-block:: ini

   SLAB_ALLOC_ARENA=24

**Перезапустите Tarantool:**

.. list-table::
   :widths: 10 30
   :header-rows: 1

   * - Операционная система
     - Команда
   * - Debian 7.x (wheezy)
     - .. code-block:: command 

         service wallarm-tarantool restart

   * - Debian 7.x (jessie)
     - .. code-block:: command 

          systemctl restart wallarm-tarantool

   * - Ubuntu 14.04 LTS (trusty)
     - .. code-block:: command 

          service wallarm-tarantool restart

   * - Ubuntu 16.04 LTS (xenial)
     - .. code-block:: command 

          service wallarm-tarantool restart

   * - CentOS 6.x
     - .. code-block:: command

         service wallarm-tarantool restart

   * - CentOS 7.x
     - .. code-block:: command

          systemctl restart wallarm-tarantool