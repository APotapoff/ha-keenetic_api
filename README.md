# Компонент Home Assistant для роутеров Keenetic

Интеграция для Home Assistant, предоставляющая полный контроль и мониторинг за роутерами Keenetic через API.

## Возможности

Интеграция создает широкий спектр объектов и сервисов для управления вашим роутером:

| Домен          | Объект                 | Описание |
| :------------- | :--------------------- | :------- |
| `binary_sensor` | Status                 | Статус роутера |
| `button`        | Reboot                 | Кнопка перезагрузки |
| `device_tracker`| Device tracker client  | Трекер подключенных устройств |
| `image`         | QR WiFi                | QR-код Wi-Fi сети |
| `select`        | Policy client          | Выбор политики для клиента |
| `sensor`        | CPU load               | Загрузка процессора |
| `sensor`        | Memory load            | Загрузка памяти |
| `sensor`        | Uptime                 | Время работы (аптайм) |
| `sensor`        | WAN IP adress          | WAN IP-адрес |
| `sensor`        | Temperature 2.4G Chip  | Температура чипа 2.4 ГГц |
| `sensor`        | Temperature 5G Chip    | Температура чипа 5 ГГц |
| `sensor`        | Clients wifi           | Количество клиентов Wi-Fi |
| `switch`        | Interface              | Управление интерфейсами |
| `switch`        | Port Forwarding        | Управление пробросом портов |
| `update`        | Update router          | Обновление прошивки |
| `service`       | Request api            | Прямой запрос к API |
| `service`       | Backup router          | Создание резервной копии |

---

## Установка

### Способ 1: Через HACS (Рекомендуется)

1. Перейдите в раздел **HACS** -> **Интеграции** в вашем Home Assistant.
2. Нажмите на три точки в правом верхнем углу и выберите **Пользовательские репозитории**.
3. Добавьте URL этого репозитория: `https://github.com/malinovsku/ha-keenetic_api`
4. Выберите категорию **Интеграция** и нажмите **Добавить**.
5. Вернитесь в раздел **Интеграции** HACS, найдите **Keenetic API** и нажмите **Загрузить**.
6. **Перезагрузите** сервер Home Assistant.

### Способ 2: Ручная установка

1. Скачайте архив последней версии со вкладки [Releases](https://github.com/malinovsku/ha-keenetic_api/releases).
2. Распакуйте его и скопируйте папку `keenetic_api` в директорию `config/custom_components` вашего Home Assistant.
3. **Перезагрузите** сервер Home Assistant.

## Настройка

1. После перезагрузки перейдите в **Настройки** -> **Устройства и службы** -> **Добавить интеграцию**.
2. Найдите интеграцию **Keenetic API** и добавьте ее.
3. Следуйте инструкциям мастера настройки, указав:
    * **IP-адрес** или **хостнейм** вашего роутера Keenetic
    * **Имя пользователя** и **пароль** для доступа к API
4. После базовой настройки вы можете включить дополнительные объекты в дополнительных настройках интеграции.

![Дополнительные настройки](images/optional_settings.png)

---

## Сервисы

### `keenetic_api.request_api`

Универсальный сервис для выполнения прямых запросов к API роутера Keenetic. Аналогичен вызовам `/webcli/rest`. Результат возвращается в панели разработчика и доступен для использования в автоматизациях и шаблонах.

**Параметры сервиса:**

| Параметр    | Обязательный | Описание                                                                 |
| :---------- | :----------- | :----------------------------------------------------------------------- |
| `device_id` | Да           | ID устройства роутера в Home Assistant.                                  |
| `method`    | Да           | HTTP-метод (`GET`, `POST`, `PUT`, `DELETE`).                             |
| `endpoint`  | Да           | Конечная точка API (например, `/rci/show/interface/WifiMaster0`).        |

**Пример вызова в YAML:**

```yaml
service: keenetic_api.request_api
data:
  device_id: router_keenetic_1234
  method: GET
  endpoint: /rci/show/interface/WifiMaster0
```

## Создание дополнительных объектов

С помощью сервиса keenetic_api.request_api можно так же создавать свои сенсоры, переключатели, числа и другое https://www.home-assistant.io/integrations/template/#trigger-based-template-binary-sensors-buttons-images-numbers-selects-and-sensors из шаблонных объектов

```yaml 
  - trigger:
      - platform: time_pattern
        minutes: /5
    action:
      - service: keenetic_api.request_api
        data:
          device_id: 
          method: GET
          endpoint: /rci/interface/WifiMaster0
        response_variable: resp
    sensor:
      - name: Keenetic idle-timeout Wifi 2.4G
        unique_id: Keenetic idle-timeout Wifi 2.4G
        unit_of_measurement: 's'
        state: >
            {% if resp.response['idle-timeout'] is defined%}
            {{resp.response['idle-timeout']}}
            {%else%}
            600
            {%endif%}
```
__Вам нравится этот проект? Поставьте звезду ⭐ на GitHub!__