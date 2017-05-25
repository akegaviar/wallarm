.. _cofigure-monitor-ru:

======================================
Настройте мониторинг фильтрующего узла
======================================

Вся информация о статусе системы проходит через локально установленный агент
``collectd``. В случае отказа или нестабильной работы агента могут некорректно
отображаться статистические данные в веб-интерфейсе Wallarm и некорректно
работать внешние системы мониторинга.

Способы получения параметров системы
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Запуск Nagios-совместимых скриптов. Значение каждого параметра можно
  получить из ``collectd`` при помощи ``collectd_nagios``. Некоторые параметры
  можно получать напрямую: в этом случае в описании указано, какой скрипт
  необходимо запустить.

* Получение потока сырых данных из ``collectd``. Любые системы, которые могут
  принимать данные из ``collectd`` при помощи одного из write-плагинов.
  Например, это может быть другой ``collectd``, ``graphite`` или ``logstash``.

* Уведомления о достижении каким-либо параметром порогового значения.
  Уведомления настраиваются в файле ``/etc/collectd/conf.d/traps.conf`` или
  ``/etc/collectd.d/traps.conf``.
  Возможные методы для отправки уведомлений:

    * NSCA и NSCA-ng.
    * SNMP TRAP.
    * почтовые сообщения.
    * собственные скрипты.

Настройте NGINX
~~~~~~~~~~~~~~~

Отказ или нестабильная работа модуля NGINX-Wallarm может привести к полному
или частичному отказу в обслуживании пользовательских запросов.

Для целей мониторинга требуется, чтобы NGINX для определенных запросов
отдавал страницу со статистикой. Для настройки используйте параметр
``wallarm_status`` в файле конфигурации NGINX.

При нестандартных настройках может потребоваться внести изменения в
:file:`/etc/collectd/conf.d/nginx-wallarm.conf`.

:command:`requests`
-------------------

Количество запросов, которые были проанализированы. В этот счетчик не попадают
запросы, которые не анализировались. Не анализируются запросы, для которых
``wallarm_mode`` имеет значение ``off``.

**collectd**:

.. code-block:: command

   curl_json-wallarm_nginx/gauge-requests

.. code-block:: command 

   curl_json-wallarm_nginx/derive-requests

:command:`attacks`
------------------

Количество запросов, в которых были были обнаружены атаки.

**collectd**:

.. code-block:: command

   curl_json-wallarm_nginx/gauge-atacks

.. code-block:: command

   curl_json-wallarm_nginx/derive-attacks

:command:`abnormal`
-------------------

Количество запросов, которые расценены как нетипичные для приложения.

**collectd**:

.. code-block:: command

   curl_json-wallarm_nginx/gauge-abnormal

.. code-block:: command

   curl_json-wallarm_nginx/derive-abnormal`
 
:command:`blocked`
------------------

Количество запросов, которые были заблокированы системой.

**collectd**:

.. code-block:: command

   curl_json-wallarm_nginx/gauge-blocked

.. code-block:: command

   curl_json-wallarm_nginx/derive-blocked

:command:`tnt_errors`
---------------------

Количество запросов, которые не были добавлены в локальное хранилище
Tarantool. Для этих запросов учитываются параметры блокировок, однако
они не будут видны в веб-интерфейсе и не будут учитываться в статистических
и поведенческих проверках.

**collectd**:

.. code-block:: command

   curl_json-wallarm_nginx/gauge-tnt_errors

.. code-block:: command

   curl_json-wallarm_nginx/derive-tnt_errors

:command:`time_detect`
----------------------

Суммарное время, потраченное на анализ запросов.

**collectd**:

.. code-block:: command

   curl_json-wallarm_nginx/gauge-time_detect

.. code-block:: command

   curl_json-wallarm_nginx/derive-time_detect`

:command:`time_tnt`
-------------------

Суммарное время, потраченное на запись запросов в локальное хранилище.

**collectd**:

.. code-block:: command

   curl_json-wallarm_nginx/gauge-time_tnt

.. code-block:: command

   curl_json-wallarm_nginx/derive-time_tnt

Настройте Tarantool
~~~~~~~~~~~~~~~~~~~

Tarantool используется для хранения результатов анализа запросов. Отказ или
нестабильная работа Tarantool может привести к отказу следующего функционала:

* Выгрузка данных об атаках в облако. В результате атаки перестанут
  отображаться в веб-интерфейсе.
* Обнаружение поведенческих атак (атаки перебора).
* Обучение структуре приложения.

Перечисленные ниже функции работают независимо и не сломаются:

* Проксирование HTTP-запросов.
* Блокировки атак (в режимах :guilabel:`блокировка` и 
  :guilabel:`агрессивный`).

Если используется нестандартный адрес или порт Tarantool, необходимо внести
изменения в ``/etc/collectd/conf.d/wallarm-tarantool.conf``.

:command:`fibers`
----------------

Внутри Tarantool запущено два фоновых процесса:

* ``wallarm.cleanup thread``
* ``wallarm.cleanup guardian``

Эти процессы отвечают за очистку старых запросов при превышении порога
использования памяти. Их падение или нестабильная работа могут привести
к достижению верхнего предела выделенной памяти и невозможности сохранять
новые запросы.

**Nagios**: ``/usr/lib/nagios/plugins/check_wallarm_tarantool_fibers``

**collectd**: нет

:command:`timeframe_size`
-------------------------

Интервал времени, в течение которого запросы хранятся в Tarantool. Зависит от
количества памяти, выделенной для Tarantool и от размера и характера
обрабатываемых HTTP-запросов. Чем меньше интервал, тем хуже работают алгоритмы
обнаружения, которым необходим доступ к историческим данным. В результате
злоумышленник может выполнять атаки перебора быстрее, оставаясь незамеченным.
При этом будет получено меньше данных об истории поведения атакующего.

**Nagios**: ``/usr/lib/nagios/plugins/check_wallarm_tarantool_timeframe``

**collectd**: 

.. code-block:: command

   wallarm-tarantool/gauge-timeframe_size

:command:`export_delay`
-----------------------

Задержка между записью запроса в Tarantool и выгрузкой обнаруженных атак
в облако.

**Nagios**: ``/usr/lib/nagios/plugins/check_wallarm_export_delay``

**collectd**: 

.. code-block:: command

   wallarm-tarantool/gauge-export_delay

:command:`export_drops`
-----------------------

Количество запросов, которые были удалены до анализа скриптами выгрузки
атак. В этот счетчик попадают все удаленные запросы, независимо от наличия
и количества атак в них. Так как интервалы удаления запросов и интервал
запроса счетчика удаленных запросов могут не совпадать, для trap'ов
рекомендуется использовать настройку ``wallarm-tarantool/gauge-export_drops_flag``.

**collectd**:

.. code-block:: command

   wallarm-tarantool/gauge-export_drops

.. code-block:: command

   wallarm-tarantool/derive-export_drops

:command:`export_drops_flag`
----------------------------

Флаг, сигнализирующий о том, что за последнюю итерацию были удалены запросы,
не проанализированные скриптами выгрузки атак.

**collectd**:

.. code-block:: command

   wallarm-tarantool/gauge-export_drops_flag
