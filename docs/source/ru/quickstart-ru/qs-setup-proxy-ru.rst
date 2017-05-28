.. _qs-setup-proxy-ru:

============================================
Настройте правила проксирования и фильтрации
============================================

Для обработки HTTP-запросов Wallarm использует веб- и прокси-сервер NGINX
с дополнительными модулями анализа трафика. Настройка NGINX-Wallarm аналогична
настройке стандартного модуля NGINX.

Отличие состоит в использовании других режимов обработки и иной страницы
информирования клиента о блокировке запроса.

Для настройки правил проксирования и фильтрации необходимо:

#. Отредактировать файл конфигурации NGINX-Wallarm.
#. Перезапустить NGINX-Wallarm.

Отредактируйте файл конфигурации NGINX-Wallarm
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :widths: 10 25
   :header-rows: 1

   * - Операционная система
     - Путь
   * - Debian/Ubuntu
     - :file:`/etc/nginx-wallarm/sites-available`

       символическая ссылка: :file:`/etc/nginx-wallarm/sites-enabled`
   * - CentOS
     - :file:`/etc/nginx-wallarm/conf.d`
   
Для каждой группы доменов, которые должны обрабатываться однотипно, принято
создавать отдельный файл конфигурации, в котором присутствует блок ``server``.

По умолчанию автоматически создается файл :file:`default` или :file:`default.conf`
следующего содержания:

.. code-block:: conf

    #
    # by default, proxy all to 127.0.0.1:8080
    #

    server {
            listen 80 default_server;
            listen [::]:80 default_server ipv6only=on;
            #listen 443 ssl;

            server_name localhost;

            #ssl_certificate cert.pem;
            #ssl_certificate_key cert.key;

            root /usr/share/nginx-wallarm/html;

            index index.html index.htm;

            # wallarm_mode monitoring;
            # wallarm_instance 1;
        
            location = /wallarm-status {
                    allow 127.0.0.1;
                    allow ::1;
                    deny all;
                    wallarm_status on;
            }

            location / {
                    proxy_pass http://127.0.0.1:8080;
                    include proxy_params;
            }
    }


Пример отредактированного файла конфигурации
--------------------------------------------

В качестве примера рассмотрим файл конфигурации для простейшего случая при
следующих допущениях:

* обрабатывается только HTTP-трафик. Запросы по протоколу HTTPS отсутствуют.
* запросы осуществляются к двум доменам -- example.com и www.example.com.
* все запросы нужно передавать на сервер 10.80.0.5.
* все входящие запросы меньше 1 МБ (значение по умолчанию).
* нет запросов, которые обрабатываются дольше 60 секунд
  (значение по умолчанию).
* система должна работать в режиме мониторинга.
* клиенты обращаются к фильтрующему узлу напрямую, не через промежуточный
  HTTP-балансировщик.

Файл конфигурации в этом случае будет выглядеть следующим образом:

.. code-block:: conf

    server {
      listen 80;
      listen [::]:80 ipv6only=on;

      # the domains for which traffic is processed
      server_name example.com; 
      server_name www.example.com;

      root /usr/share/nginx-wallarm/html;
      index index.html index.htm;

      # turn on the monitoring mode of traffic processing
      wallarm_mode monitoring; 
      # wallarm_instance 1;

      # turn on the technical page with node statistics
      location = /wallarm-status {
        allow 127.0.0.1;
        allow ::1;
        deny all;
        wallarm_status on;
            }

      location / {
        # setting the address for request forwarding
        proxy_pass http://10.80.0.5; 
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      }
    }

Тонкая настройка
----------------

Чтобы произвести настройку NGINX при других исходных условиях, обратитесь
к `руководству по настройке NGINX <https://nginx.org/ru/docs/beginners_guide.html>`__.

Информация о настройках фильтрующего узла, приведена в разделе
:ref:`configure-parameters-ru`.

Перезапустите NGINX-Wallarm
~~~~~~~~~~~~~~~~~~~~~~~~~~~

После того, как вы сохранили отредактированный файл, перезапустите NGINX
командой:


.. code-block:: command

   $ /etc/nginx-wallarm/sites-enabled # service nginx-wallarm reload

Проверьте, что фильтрующий узел работает и пропускает через себя трафик:
:ref:`qs-check-operation-ru`.