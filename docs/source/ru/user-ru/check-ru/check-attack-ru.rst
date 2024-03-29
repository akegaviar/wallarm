.. _check-attack-ru:

===============
Проверьте атаки
===============

На вкладке :guilabel:`Атаки` веб-интерфейса размещается информация об
атаках и инцидентах. По умолчанию выводятся данные за последний день.

Вы можете воспользоваться поиском или выбрать период вручную.

Wallarm автоматически группирует связанные между собой вредоносные запросы
в единую сущность. Например, Wallarm видит ряд запросов на один домен с атакой
на один и тот же параметр. Такие запросы объединяются в одну сущность,
и на самом высоком уровне в окне :guilabel:`Атаки` показаны именно они. 

В столбце :guilabel:`Тип` показаны тип и объект атаки.

Если нужно определить, является некоторая группа атакой или ложным
срабатыванием, просмотрите отдельные запросы в каждой группе.

Проверьте отдельные запросы
~~~~~~~~~~~~~~~~~~~~~~~~~~~

В столбце :guilabel:`Кол-во` на строке интересующей вас атаки нажмите
на правую угловую скобку :guilabel:`>`.

Веб-интерфейс выведет следующие данные:

* :guilabel:`Вектор атаки` -- как именно злоумышленник
  пытался осуществить атаку.
* :guilabel:`Статус` -- статус ответа сервера.
* :guilabel:`Размер` -- размер ответа сервера.
* :guilabel:`Время` -- время ответа сервера.

Проверьте отдельные запросы в сыром виде
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. В столбце :guilabel:`Кол-во` на строке интересующей вас атаки нажмите
   на правую угловую скобку :guilabel:`>`.
#. Нажмите на значок увеличительного стекла.

Веб-интерфейс выведет полное содержание запроса.

Пометьте запрос как ложное срабатывание
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. В столбце :guilabel:`Кол-во` на строке интересующей вас атаки нажмите
   на правую угловую скобку :guilabel:`>`.

#. В правом столбце на строке запроса нажмите :guilabel:`Ошибка?`.

Wallarm уберет из списка все связанные с этой атакой запросы и перенастроит
правила фильтрации трафика. Впредь подобные запросы не будут считаться атаками.

.. seealso::

   - :ref:`use-search-ru`
   - :ref:`use-filter-ru`