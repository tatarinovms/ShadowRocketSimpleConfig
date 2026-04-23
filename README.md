# ShadowRocketSimpleConfig

Простой и эффективный набор правил маршрутизации для клиента ShadowRocket на iOS.

## Описание

Этот репозиторий содержит оптимизированную конфигурацию для ShadowRocket с правилами маршрутизации для различных категорий сервисов:

### Списки правил

- **ai.list** — AI-сервисы (ChatGPT, Claude, Gemini, Copilot и другие)
- **main.list** — Основные сервисы (социальные сети, видео, медиа, новости, инструменты)
- **meta.list** — Мятные сервисы
- **psn.list** — PlayStation Network
- **telegram.list** — Мессенджер от Паши
- **whatsapp_cidr_ipv4.list** — IP-адреса зелёного мессенджера
- **rudirect.list** — Правила перенаправления русских сервисов

## Как использовать ShadowRocket на iOS

### Шаг 1: Установка ShadowRocket

1. Откройте App Store на вашем iPhone/iPad
2. Найдите приложение ShadowRocket
3. Нажмите "Загрузить" и дождитесь установки

### Шаг 2: Добавление конфигурации

#### Через URL
1. Откройте приложение ShadowRocket
2. Нажмите на вкладку Config
3. Нажмите "+" в правом верхнем углу
4. В поле "URL" введите:

Для скачивания с GitHub:
   ```
   https://raw.githubusercontent.com/tatarinovms/ShadowRocketSimpleConfig/master/baseline.conf
   ```
Для скачивания с GitVerse:
   ```
   https://gitverse.ru/api/repos/tatarinovms/ShadowRocketSimpleConfig/raw/branch/main/baseline.conf
   ```
5. Нажмите "Download"

### Шаг 3: Настройка прокси сервера

1. В ShadowRocket перейдите в Home (Servers)
2. Нажмите "+" для добавления своего сервера
3. Выберите тип вашего прокси (SS, V2Ray, Trojan, etc.)
4. Введите данные вашего сервера
5. В поле Remark введите название Proxy
6. Нажмите "Save"

### Шаг 4: Активация

1. Перейдите в Home
2. Выберите в поле Global Routing - Config
3. Переключите тумблер вверху в состояние — Включено

## Автоматическое обновление

Конфигурация может автоматически обновляться. Для этого перейдите в раздел Config, нажмите на baseline.conf и выберите в меню Update.
