Домашнее задание
Работа с журналами

Цель:
уметь работать с журналами и контрольными точками
уметь настраивать параметры журналов

Описание/Пошаговая инструкция выполнения домашнего задания:
Настройте выполнение контрольной точки раз в 30 секунд.
10 минут c помощью утилиты pgbench подавайте нагрузку.
Измерьте, какой объем журнальных файлов был сгенерирован за это время. Оцените, какой объем приходится в среднем на одну контрольную точку.
Проверьте данные статистики: все ли контрольные точки выполнялись точно по расписанию. Почему так произошло?
Сравните tps в синхронном/асинхронном режиме утилитой pgbench. Объясните полученный результат.
Создайте новый кластер с включенной контрольной суммой страниц. Создайте таблицу. Вставьте несколько значений. Выключите кластер. Измените пару байт в таблице. Включите кластер и сделайте выборку из таблицы. Что и почему произошло? как проигнорировать ошибку и продолжить работу?

Критерии оценки:
Выполнение ДЗ: 10 баллов
плюс 2 балла за красивое решение
минус 2 балла за рабочее решение, и недостатки указанные преподавателем не устранены


Рекомендуем сдать до:
