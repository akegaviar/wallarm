.. _configure-ip-blocking-ru:

================
Блокировка по IP
================

.. warning:: Функция блокировки по IP является экспериментальной.
             Функция может быть изменена без предупреждения.

В подавляющем большинстве случаев блокировка по запросам является более
предпочтительным вариантом, чем блокировка по IP-адресам. Тем не менее, есть
ряд случаев, когда необходимо использовать именно блокировку по IP.

Такими случаями являются:

* Необходимость снизить нагрузку, создаваемую запросами атакующих.
* Обработка трафика в асинхронном режиме.
* Наличие дополнительных ресурсов, не находящихся под защитой WAF.

Мы предлагаем воспользоваться готовым скриптом блокировки по IP --
``block_with_iptables.rb`` -- на основе которого вы также можете создать свой
собственный скрипт. Для успешного применения блокировок по IP фильтрующему
узлу нужно регулярно получать из облака Wallarm обновленный список адресов,
принадлежащих атакующим и подлежащих блокировке. 

.. note:: Также есть возможность организовать белый список IP-адресов. Если
          адрес находится в белом списке, то запросы с него будут
          пропускаться к серверу приложения без проверки на принадлежность
          к черному списку. 

Настройте блокировку по IP
~~~~~~~~~~~~~~~~~~~~~~~~~~

#. Обратитесь в `службу технической поддержки <https://my.wallarm.com/>`_
   с запросом на создание системного пользователя для доступа к информации о
   черных списках.

#. Установите пакет ``wallarm_extra_scripts``. Он находится в репозитории
   Wallarm. 

   Для установки пакета выполните команду:

   .. list-table::
      :widths: 10 30
       :header-rows: 1

	   * - Операционная система
	     - Команда
	   * - Debian 7.x (wheezy)
	     - .. code-block:: command

	          apt-get install wallarm-extra-scripts

	   * - Debian 8.x (jessie)
	     - .. code-block:: command

	          apt-get install wallarm-extra-scripts

	   * - Ubuntu 14.04 LTS (trusty)
	     - .. code-block:: command

	          apt-get install wallarm-extra-scripts

	   * - Ubuntu 16.04 LTS (xenial)
	     - .. code-block:: command

	          apt-get install wallarm-extra-scripts

	   * - CentOS 6.x
	     - .. code-block:: command 

	          yum install wallarm-extra-scripts

	   * - CentOS 7.x
	     - .. code-block:: command

	          yum install wallarm-extra-scripts

   Cкрипт ``block_with_iptables.rb`` будет установлен автоматически.
   Предлагаемый скрипт при каждом запуске создает или обновляет цепочку
   ``wallarm_blacklist`` в таблице ``filter``. Для каждого заблокированного
   адреса добавляется правило ``REJECT``.

#. Создайте и сконфигурируйте таблицы ``iptables``, указав какой трафик будет
   блокироваться. Например, для блокировки всего трафика, поступающего на 80
   и 443 порты, выполните команды:

   .. code-block:: command

      iptables -N wallarm_check

   .. code-block:: command

      iptables -N wallarm_blacklist

   .. code-block:: command

      iptables -A INPUT -p tcp --dport 80 -j wallarm_check

   .. code-block:: command

      iptables -A INPUT -p tcp --dport 443 -j wallarm_check

   .. code-block:: command
      
      iptables -A wallarm_check -j wallarm_blacklist
  
#. Настройте регулярный запуск скрипта. Например, чтобы создать файл
   ``/etc/cron.d/wallarm-blacklist-sync`` и настроить запуск скрипта каждые
   5 минут, выполните команды:

   .. code-block:: command
  
	  PATH=/bin:/sbin:/usr/bin:/usr/sbin
	  */5 *  * * *  root  timeout 90 /usr/share/wallarm-extra-scripts/block_with_iptables.rb »/path/to/log 2>&1
  

#. Если необходимо, настройте мониторинг работы скрипта. Для этого можно
   отслеживать время модификации (``mtime``) файла
   ``/tmp/.wallarm.blacklist-sync.last``, которое будет изменяться при каждом
   успешном запуске скрипта.

#. Добавление адресов в белый список. 

   Чтобы внести несколько IP-адресов в белый список, выполните следующую
   команду для диапазона адресов. Вместо ``1.2.3.4/30``, укажите нужное значение:

   .. code-block:: command

      iptables -I wallarm_check -s 1.2.3.4/30 -j RETURN
  
   Чтобы внести один IP-адрес в белый список, замените значение ``1.2.3.4/32`` на необходимый адрес:

   .. code-block:: command

      iptables -I wallarm_check -s 1.2.3.4/32 -j RETURN