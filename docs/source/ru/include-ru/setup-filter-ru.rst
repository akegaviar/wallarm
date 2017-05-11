.. _setup-filter-ru:

Снимите комментарии со строк ``wallarm_mode`` в файле
``/etc/nginx-wallarm/conf.d/wallarm.conf``.

Для этого необходимо удалить символы ``#`` в начале строки.

Пример содержимого файла:

.. code-block:: ini

	#
	# Wallarm module specific parameters
	#

	wallarm_mode monitoring;
	# wallarm_mode_allow_override on;
