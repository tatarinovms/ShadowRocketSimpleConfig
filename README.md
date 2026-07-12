[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/tatarinovms/ShadowRocketSimpleConfig)

# ShadowRocketSimpleConfig

Простой и эффективный набор правил маршрутизации для клиента ShadowRocket на iOS.

## Описание

Этот репозиторий содержит оптимизированную конфигурацию для ShadowRocket с правилами маршрутизации для различных категорий сервисов:

### Списки правил

| Файл | Политика | Описание |
| :--- | :--- | :--- |
| **ai.list** | PROXY | AI-сервисы (ChatGPT, Claude, Gemini, Copilot и другие) |
| **main.list** | PROXY | Основные сервисы: соцсети, видео, медиа, новости, инструменты |
| **meta.list** | PROXY | Meta-сервисы: Facebook, Instagram, WhatsApp, Oculus |
| **games.list** | PROXY | Игровые платформы: PlayStation, Xbox, Supercell, Gameloft, игровые CDNs |
| **telegram.list** | PROXY | Telegram, TON, официальные клиенты и IP-диапазоны |
| **fitness.list** | PROXY | Сервисы для спорта и здоровья |
| **youtube.list** | PROXY | YouTube, YouTube Music, связанные CDN |
| **rudirect.list** | DIRECT | Российские локальные сервисы, идущие напрямую |
| **rubanking.list** | DIRECT | Российские банки и финансовые сервисы |
| **ruipchecker.list** | DIRECT | Сервисы проверки IP-адреса |

Политика для каждого списка задаётся один раз в `baseline.conf` через `RULE-SET`. Внутри `.list`-файлов колонка политики не указывается.

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

### Шаг 4: Настройка GeoLite2 Database

1. Settings → GeoLite2 Database
2. Если у вас нет аккаунта на Maxmind и вы используете финальное правило Proxy или Auto
3. В поле Country введите: 

```
https://cdn.jsdelivr.net/gh/P3TERX/GeoLite.mmdb@download/GeoLite2-Country.mmdb
```

Нажмите Update

4. В поле ASN введите: 

```
https://cdn.jsdelivr.net/gh/P3TERX/GeoLite.mmdb@download/GeoLite2-ASN.mmdb
```

Нажмите Update

### Шаг 5: Активация

1. Перейдите в Home
2. Выберите в поле Global Routing - Config
3. Переключите тумблер вверху в состояние — Включено


## Автоматическое обновление

Конфигурация может автоматически обновляться. Для этого перейдите в раздел Config, нажмите на baseline.conf и выберите в меню Update.
