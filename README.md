# VlasovVO_microservices

VlasovVO microservices repository

## HW№11

- Запустили контейнер `docker run hello-world`, образ докера был скачен из Docker Hub

- `Docker run` Запускает каждый раз новый контейнер из образа.

- `Docker commit` Создает образ из контейнера

- `Docker kill` - Завершаем работу докера

- `docker system df` - отображает информацию о использовании дискового простанства и отображает сколько из него не используется для возможности удаления

- `docker rm` - Удаление контейнера

- `docker rmi` - Удаление образа

## HW№12

- Создали docker host на GCE

- Собрали свой docker образ

- Загрузили образ на Docker hub

- Выполнено доп. задание

## HW№13

- Скачали архив с исходниками. Добавили dockerfile. **UI** собирается не с первого шага, т.к. содержит такие же промежуточные слои вначале что и **Comment**.

### Доп задание

- Команда с уснаовкой переменного окружения:

```bash
docker run -d --network=reddit --network-alias=post_db2 --network-alias=comment_db2 mongo:latest
docker run -d --network=reddit --network-alias=post -e POST_DATABASE_HOST=post_db2 vlasovvo/post:1.0
docker run -d --network=reddit --network-alias=comment -e COMMENT_DATABASE_HOST=comment_db2 vlasovvo/comment:1.0
docker run -d --network=reddit -p 9292:9292 vlasovvo/ui:1.0
```

- Собран образ ui на основе Alpine Linux. Так же были пересобарны образы по рекомендациям hadolint (есть расширение на vscode =) )

```bash
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
vlasovvo/comment    2.0                 0616864f0fad        About a minute ago   759MB
vlasovvo/ui         3.0                 f9d3e7c94293        32 minutes ago       209MB
vlasovvo/ui         2.0                 bc900dd533ef        8 hours ago          460MB
vlasovvo/ui         1.0                 0129fc33377d        10 hours ago         778MB
vlasovvo/comment    1.0                 a2619d4c2ae9        10 hours ago         770MB
vlasovvo/post       1.0                 c06a04726b0d        10 hours ago         102MB
mongo               latest              8bf72137439e        46 hours ago         380MB
ubuntu              16.04               7aa3602ab41e        2 weeks ago          115MB
alpine              3.7                 791c3e2ebfcb        5 weeks ago          4.2MB
ruby                2.2                 6c8e6f9667b2        3 months ago         715MB
python              3.6.0-alpine        cb178ebbf0f2        17 months ago        88.6MB
```

## HW№14

- Добавлены имена сетей и сетевых алиасов в docker-compose

- Добавлена параметризация в docker-compose, изучена работа с environment variables

### Доп. задание

- Добавлен docker-compose.override.yml

## HW№15

- Развернут сервер с docker с помощью docker-machine

- Развернули Gitlab CI с помощью docker-compose

### Доп задание

- Создан playbook для создания runner контейнера и подключения его к Gitlab CI

- Канал для оповещений в Slack: <https://devops-team-otus.slack.com/messages/CB49LM39T>

## HW№16

- Расширил существующий пайплайн в Gitlab

- Определили окружения

Доп задания оставил на потом :worried:

## HW№17

- Ознакомился с мониторингом средствами prometheus

- Ознакомился со сбором метрик состояния микросервисов

- Ознакомился с экспортерами для сбора метрик хоста

### Доп. задание:

- В качестве мониторинга mongoDB использовал проект [percona/mongodb_exporter](https://github.com/percona/mongodb_exporter). Dockerfile расположен в директории `/monitoring/mongodb_exporter`

- В качестве мониторига сервисов использовал [Cloudprober](https://github.com/google/cloudprober). Настроил на нем мониторинг портов в конфиг файле. Файлы расположены в директории `/monitoring/cloudprober`

- Создал `makefile` для сбора и отправки на Docker Hub образов докера. Файл с входом на Docker Hub добавил в `.gitignore`

Ссылка на Docker Hub: https://hub.docker.com/u/vlasovvo/

## HW№18

- Создание мониторигна контейнеров с использованием `cAdvisor`

- Визуализация метрик на основе `Grafana`

- Сбор метрик работы приложения и бизнеc метрик

- Настройка и проверка алертинга c использованием `alertmanager`

### Доп. задания:

- Добавлены в `makefile` новые образа

- Настроен сбор метрик напрямую самим docker. Названия метрик имеют вид `swarm_*`. В качестве дашборда использовал `Docker Engine Metrics`

- Добавлен сбор метрик докеров с использованием стека `influxDB+Telegraf`. По аналогии с `cAdvisor` собираются системные метрики. В качестве дашборда использовал `InfluxDB docker`

- Добавил в `alerts.yml` проверку на 95 перцентиль времени ответа UI. В качестве спама порта 9292 использовал hping3

- Добавил в `alertmanager` отправку оповщений на почту. Пробовал с использованием SMTP сервера mail.ru, но на нем используется 465 порт на TLS, но alertmanager может только в 587 порту в режиме TLS работать, пришлось выбрать Gmail

## HW№19

- Поднял стек EFK для централизованного сбора метрик

- Обновили образы приложений, заменили версию на `logging` в .env

- Рассмотрели возможности `Fluentd` парсинга Json с помощью cобственных регуларных выражений, а так же grok-шаблонов

- Для выполнения доп задания использовались базовые готовые шаблоны: https://github.com/jordansissel/grok/blob/master/patterns/base

- Развернул контейнер с `Zipkin` 

### Доп. задание:

- Bugget сборку сложил в папку `/src/bugged-code-master/`

- Добавил Environment в Dockerfile, т.к. они в нем отсутсвовали. Исправил все 3 `docker_build.sh` что бы образы приложения собирались с тегом `logging-bugged`

- В `Zipkin` в логах видим, что операция `db_find_single_post` выполняется около 3х секунд.

- При изучении кода в файле `post_app.py` в функции `find_post` находим метод `time.sleep`, которая и являлась причиной долгих открытий постов. При удалении данной строки и пересборки образа, посты начали открываться нормально.

## HW№20

- Пройден туториал [k8s the hard way](https://github.com/kelseyhightower/kubernetes-the-hard-way). После прохождения был удален кластер

- Проверены деплоймент файлы из директории `kubernetes/reddit`

## HW№21

- Изучена утилита minikube

- Создан GKE кластер

- Развернуто reddit приложение на GKE

### Доп. задание

- Добавлен файл `/kubernetes/terraform/main.tf` для разворачивания GKE средствами terraform

- Добавлен файл `clusterrole-kubernetes-dashboard.yml` для выдачи прав serviceacount kubernetes-dashboard

## HW№22

- Изучены настройки балансировки, сетевого взаимодействия и подключение удаленных хранилищ

### Доп. Задание

- Создан файл `ui-ingress-secret.yml` описывающий создание секрета в ui-ingress
- Файл `mongo-network-policy.yml` обновлен для доступа к mongo post-сервиса

## HW№23

- Изучен способ развертывания кластера с помощью Helm

- Развернул Gitlab в kubernetes кластере

- Запуск CI/CD в kubernetes

## HW№24

- Установим prometeus на k8s

- Включим kube-service-metrics:

```yml
kubeStateMetrics:
  enabled: true
```

- Включим node-exporter:

```yml
nodeExporter:
  enabled: true
```

- Установим прилолжение на разные namespace:

```bash
helm upgrade reddit-test ./reddit --install
helm upgrade production --namespace production ./reddit --install
helm upgrade staging --namespace staging ./reddit --install
```

- Разобьем собранный в ходе выполнения ДЗ job `reddit-endpoint` на 3 разных job'a:

```yml
      - job_name: 'post-endpoints'
        kubernetes_sd_configs:
          - role: endpoints
        relabel_configs:
          - action: labelmap
            regex: __meta_kubernetes_service_label_(.+)
          - source_labels: [__meta_kubernetes_service_label_app]
            action: keep
            regex: reddit
          - source_labels: [__meta_kubernetes_service_label_component]
            action: keep
            regex: post
          - source_labels: [__meta_kubernetes_namespace]
            target_label: kubernetes_namespace
          - source_labels: [__meta_kubernetes_service_name]
            target_label: kubernetes_name

      - job_name: 'comment-endpoints'
        kubernetes_sd_configs:
          - role: endpoints
        relabel_configs:
          - action: labelmap
            regex: __meta_kubernetes_service_label_(.+)
          - source_labels: [__meta_kubernetes_service_label_app]
            action: keep
            regex: reddit
          - source_labels: [__meta_kubernetes_service_label_component]
            action: keep
            regex: comment
          - source_labels: [__meta_kubernetes_namespace]
            target_label: kubernetes_namespace
          - source_labels: [__meta_kubernetes_service_name]
            target_label: kubernetes_name

      - job_name: 'ui-endpoints'
        kubernetes_sd_configs:
          - role: endpoints
        relabel_configs:
          - action: labelmap
            regex: __meta_kubernetes_service_label_(.+)
          - source_labels: [__meta_kubernetes_service_label_app]
            action: keep
            regex: reddit
          - source_labels: [__meta_kubernetes_service_label_component]
            action: keep
            regex: ui
          - source_labels: [__meta_kubernetes_namespace]
            target_label: kubernetes_namespace
          - source_labels: [__meta_kubernetes_service_name]
            target_label: kubernetes_name
```

- Установим Grafana

- Установим новый [Дашборд](https://grafana.com/dashboards/315) для kubernetes

- Добавим старые дашборды, добавим в них переменную namespace, Исправим формулы в графах с учетом добавленной переменной
  Новый дашборды будет лежать в директории `kubernetes/dashboadrds`

### Первое доп задание

- Добавим alertrules в `customvalues.yml`:

```yml
serverFiles:
  alerts:
    groups:
    - name: alert.rules
      rules:
      - alert: InstanceDown
        expr: up{job=~"kubernetes-nodes"} == 0
        for: 1m
        labels:
          severity: page
        annotations:
          description: '{{ $labels.kubernetes_io_hostname }} of job {{ $labels.job }} has been down for more than 1 minute'
          summary: 'Instance {{ $labels.instance }} down'
      - alert: APIserverDown
        expr: up{job=~"kubernetes-apiservers"} == 0
        for: 1m
        labels:
          severity: page
        annotations:
          description: 'APIserver has been down for more than 1 minute'
```

Включаем `alertmanager`:

```yml
alertmanager:
  enabled: true
```

Заносим конфиг для `alertmanager` в custom_values.yml:

```yml
alertmanagerFiles:
  alertmanager.yml: # |-
    global:
      slack_api_url: 'https://hooks.slack.com/services/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'

    receivers:
      - name: default-receiver
        slack_configs:
         - channel: '#vasily_vlasov'
           send_resolved: true

    route:
      group_wait: 10s
      group_interval: 5m
      receiver: default-receiver
      repeat_interval: 3h
```

Обновляем `prometheus` и получаем оповещения в slack:

![slack](http://ipic.su/img/img7/fs/Novyjtochechnyjrisunok(2).1540405172.png)

### Доп. задание 2

Возможно не самый правильный вариант, но самый быстрый.

- Скачиваем чарт `kibana` 0.0.1 весрии

```bash
helm fetch --untar stable/kibana --version 0.0.1
```

- В скаченную директорию `./Charts/kibana/temlate` переносим yaml файлы c созданием Elasticsearch и fluentd

- Вносим изменения в value.yaml описанные в ДЗ:

```yml
ingress:
  enabled: true
  hosts:
    - reddit-kibana
```

```yml
env:
  ELASTICSEARCH_URL: http://elasticsearch-logging:9200
```

- Переименуем папку `kibana` на `efk` и запускаем из данной директории чарт:

```bash
helm upgrade efk . -f value.yaml --install
```

- Стек запущен
