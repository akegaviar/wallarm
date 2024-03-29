.. _installation-vmware-ru:

=======================================
Установите с использованием VMware vApp
=======================================

Фильтрующий узел может быть установлен в виде VMware vApp.

Функциональность фильтрующего узла, установленного в виде VMware vApp,
полностью идентична функциональности других вариантов установки.

Необходимые условия
~~~~~~~~~~~~~~~~~~~

Система виртуализации должна удолетворять следующим требованиям:

* Поддержка технологии vApp.
* vCenter версии 5.5 и выше.
* ESXi версии 5.5 и выше.

Права доступа:

* Наличие доступа к консоли vCenter с правами на создание OVF-шаблонов
* Наличие доступа в облако Wallarm с правами на создание узлов

Получите ссылку на OVF-шаблон
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Откройте `https://my.wallarm.com <https://my.wallarm.com>`_.
#. Нажмите :guilabel:`Настройки`.
#. Нажмите :guilabel:`Узлы`.
#. Нажмите :guilabel:`Получить ссылку на OVF-шаблон`.

В буфер обмена скопируется путь к OVF-шаблону. Ссылка действительна в течение
24 часов.

.. note:: У фильтрующего узла автоматически изменятся реквизиты доступа. Если
          старый узел еще находится в строю, он потеряет возможность
          взаимодействовать с Wallarm API.

Разверните OVF-шаблон для VMware
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Зайдите в vCenter через vSphere Client или vSphere Web Service.
#. Нажмите :guilabel:`File`.
#. Нажмите :guilabel:`Deploy OVF template`.
#. Скопируйте из буфера обмена путь к OVF-шаблону.
#. Укажите на этапе :guilabel:`Name and Location` название, под которым
   виртуальная машина будет видна в интерфейсе vSphere Client.
#. На этапе :guilabel:`Deployment configuration` выберите конфигурацию:

    * :guilabel:`up to 10mbps` предназначена для тестовых инсталляций.
      Использует 1 виртуальный процессор и 512 МБ памяти.
    * :guilabel:`10-50mbps` рассчитана на нагрузку до 50 Мбит трафика.
      Использует 4 виртуальных процессора и 16 ГБ памяти.
    * :guilabel:`50-100mbps` рассчитана на нагрузку до 100 Мбит трафика.
      Использует 8 виртуальных процессоров и 32 ГБ памяти.

#. Настройте на этапе :guilabel:`Properties` параметры фильтрующего узла.
   Эти параметры будут использоваться только для конфигурации при первом
   запуске.

    * :guilabel:`Hostname`: имя сервера.
    * :guilabel:`HTTP hosts`: список доменов, которые будут обрабатываться.
      Запросы, у которых значение заголовка Host не входит в указанный список,
      не будут передаваться на бэкенды и будут заблокированы.
    * :guilabel:`HTTP backends`: список IP-адресов, на которые будут
      пересылаться запросы.

#. При первой загрузке виртуальной машины в консоли сервера появится
   предложение установки пароля для root. Необходимо использовать не менее
   8 символов, маленькие, большие буквы и цифры.

Дополнительные настройки
~~~~~~~~~~~~~~~~~~~~~~~~

#. После первой загрузки необходимо выполнить одно из перечисленных ниже
   действий:

    * Подождать 15 минут пока выгрузятся специфичные для для фильтрующего узла
      файлы.
    * Вручную запустить ``sync-node.sh``.

#. Дополнительные настройки NGINX описаны в разделе :ref:`configure-parameters-ru`.