Цель:
реализовать свой миникластер на 3 ВМ.

Описание/Пошаговая инструкция выполнения домашнего задания:
На 1 ВМ создаем таблицы test для записи, test2 для запросов на чтение.
Создаем публикацию таблицы test и подписываемся на публикацию таблицы test2 с ВМ №2.
На 2 ВМ создаем таблицы test2 для записи, test для запросов на чтение.
Создаем публикацию таблицы test2 и подписываемся на публикацию таблицы test1 с ВМ №1.
3 ВМ использовать как реплику для чтения и бэкапов (подписаться на таблицы из ВМ №1 и №2 ).

ДЗ сдается в виде миниотчета на гитхабе с описанием шагов и с какими проблемами столкнулись.

* реализовать горячее реплицирование для высокой доступности на 4ВМ. Источником должна выступать ВМ №3. Написать с какими проблемами столкнулись.

Критерии оценки:
Выполнение ДЗ: 10 баллов

5 баллов за задание со *
плюс 2 балл за красивое решение
минус 2 балл за рабочее решение, и недостатки указанные преподавателем не устранены