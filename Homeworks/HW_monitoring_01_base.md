# Домашняя работа к занятию "10.01. Зачем и что нужно мониторить"

[Домашнее задание](https://github.com/netology-code/mnt-homeworks/tree/MNT-13/10-monitoring-01-base)

## Основная часть

1. Я бы выделил следующий минимальный объем метрик: 
 - Загузку CPU
 - Количество свободной и занятой памяти RAM
 - Свободное место на жестких дисках
 - Общее количество http-ответов
 - Количество количетсво ответов с кодами(200, 500, 400)
 - Возможно разные метрики по безопасности, раз осущетсвляется какой-то расчет с выдачей текстовых отчетов, то возможно заказчику будет интересно узнать информацию по авторизации.
 - Для определения быстродействия - время выполнения запросов на предоставление отчета
 
2. Если менеджеру интересно, можно предоставить расшифровку метрик, возможно даже отобразить данную информацию в системе мониторинга. 
Дополнение: Для определения качества ослуживания я предложу ему дополнительно вывести метрики из соглашения SLO, например количесво запросов/мин, с которым сервис может обеспечеть безотказную работу, количество произошедших сбоев системы, время реагирования и устранения неполадок в системе. Если это web приложение, то я бы включил количество ответов с кодом 4** и 5**. 

3. Тут бы я предложил или использовать бесплатную версию системы пеехватчика ошибок в облаке(Sentry), или менее красивое решение - написать скрипт и парсить логи с выдачей важной информации на выходе.

4. В приведенной формуле отсутствуют коды 3**(редирект), которые в свою очередь, не являются ошибками, возможно в формулу стоит добавить информационные коды 1**. 
(summ_2xx_requests + summ_3xx_requests)/summ_all_requests
