.. _configure-marker-ru:

=================
Настройте маркеры
=================

На вкладке :guilabel:`Настройки` --> :guilabel:`Маркеры` вы можете задать
маркеры.

С помощью маркеров можно разметить HTTP/HTTPS-пакеты так, чтобы Wallarm
обрабатывал их особым образом.

Сценарии использования маркеров:

* Для аутентификации сканера и запуска проверки уязвимостей веб-приложения.
* Для инициализации поиска уязвимостей во вновь добавленных компонентах
  веб-приложения или API -- распространенный сценарий для заказчиков,
  использующих Continuous Integration/Continuous Delivery.
* Для обновления профиля приложения. Эта возможность полезна для заказчиков,
  желающих как можно быстрее развернуть новые функции веб-приложения или API.

Создайте маркер
~~~~~~~~~~~~~~~

Маркер представляет собой 64-битный секретный код, который следует разместить
в HTTP-заголовоке ``X-Wallarm-Marker``.

Например:

.. code-block::

   X-Wallarm-Marker: bdb1fcc94e807fbfa59c79778e6ea1f1cbd2ec8c33557c94a90b39a7491fd004

#. Нажмите :guilabel:`Добавить`.
#. Задайте описание и IP-адрес и маску подсети.

.. note:: Маркер будет использован фильтрующим узлом только в том случае,
          если актуальный IP-адрес и маска подсети совпадут с указанными
          в веб-интерфейсе.

Помеченные запросы будут использованы для обновления профиля приложения.

Маркеры и фаззинг
~~~~~~~~~~~~~~~~~

Фаззинг является методикой провокации нетипичного поведения программы
посредством передачи ей различного рода нетипичных входных данных. Фаззинг с
высокой вероятностью способен вызвать ошибки в работе целевого приложения,
поэтому в системе Wallarm он применяется только для тех запросов, которые
помечены маркерами как допустимые с точки зрения модификации данных.

Таким образом, при наличии уже написанных юнит-тестов обеспечивается более
полное покрытие с точки зрения информационной безопасности, в том числе для
новых компонентов веб-приложения, находящихся на стадии тестирования,
развертывания, а также уже развернутых.

Поддержка продвинутого фаззинга может быть особенно востребована компаниями,
использующими Continuous Integration/Continuous Delivery.

**Задайте фаззер**

#. Добавьте в запрос заголовок ``X-Wallarm-Marker: <маркер>``
#. Добавьте в запрос заголовок ``X-Wallarm-Fuzzer: yes``
#. Задайте дополнительные настройки в заголовке ``X-Wallarm-Fuzzer-Policy``:

   * replace-all ``<N>``
   * add-to-end ``<N>``
   * add-to-begin ``<N>``
   * replace-from-end ``<M>`` ``<N>``
   * replace-from-begin ``<M>`` ``<N>``
   * insert-into-random ``<N>``

Для каждой уязвимости, обнаруженной в процессе проверок, будет создан отчет,
который будет отправлен по почте или на `https://my.wallarm.com <https://my.wallarm.com>`_.
