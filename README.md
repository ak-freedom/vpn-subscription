# VPN Subscription

Подписка с конфигурациями, прошедшими проверку доступности в сети, из которой выполнялась проверка. Список обновляется после каждого завершённого прогона.

## Добавление в клиент

Основная ссылка — текстовый список URI, по одной конфигурации в строке:

```text
https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/subscription-plain.txt
```

Добавьте её в клиент с поддержкой подписок в виде текстового списка ссылок.

Дополнительная ссылка — для клиентов, которым нужна V2Ray-совместимая Base64-подписка:

```text
https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/subscription.txt
```

Обе версии содержат одинаковые конфигурации в одинаковом порядке и обновляются вместе.
Поддержка формата импорта зависит от приложения, а не только от используемого ядра.

Клиент должен поддерживать протоколы и параметры конкретного ключа, включая транспорт, TLS или Reality. Для клиентов, принимающих только Clash YAML либо JSON-конфигурацию sing-box, потребуется преобразование формата.

## Что содержится в репозитории

- `subscription-plain.txt` — основная подписка: текстовый список URI. Параметры подключения сохраняются; названия содержат страну и результаты измерений.
- `subscription.txt` — дополнительная версия той же подписки в Base64.
- `status.json` — время последней проверки в UTC, количество кандидатов и рабочих конфигураций, число раундов, ошибки загрузки источников и распределение рабочих ключей по протоколам.

Сборщик обрабатывает VLESS, VMess, Trojan, Shadowsocks, SOCKS, WireGuard, Hysteria2, Hysteria, TUIC, AnyTLS и ShadowsocksR в пределах возможностей инструментов проверки. В конкретном обновлении остаются только прошедшие проверку конфигурации; фактический состав указан в `status.json`.


## Группы подписок

Все группы — обычные текстовые списки URI. Они обновляются вместе с общей подпиской;
пустая группа публикуется пустой, чтобы не сохранять старые ключи.

| Группа | Ссылка / правило |
| --- | --- |
| Топ-100 | [fastest](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/fastest.txt): сначала измеренная скорость по убыванию, затем задержка; ключи без измерения скорости идут после измеренных |
| Россия | [russia](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/regions/russia.txt): страна IP сервера — Россия |
| Другие страны | [global](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/regions/global.txt): определённая страна IP сервера вне России |
| Страна неизвестна | [unknown](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/regions/unknown.txt) |
| Скорость от 50 Мбит/с | [fast](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/speed/fast.txt) |
| Скорость от 10 до 50 Мбит/с | [medium](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/speed/medium.txt), верхняя граница не включается |
| Скорость ниже 10 Мбит/с | [slow](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/speed/slow.txt), только положительные измерения |
| Скорость не измерена | [unknown](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/speed/unknown.txt) |
| Задержка до 150 мс | [low](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/latency/low.txt) |
| Задержка свыше 150 до 300 мс | [medium](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/latency/medium.txt) |
| Задержка свыше 300 мс | [high](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/latency/high.txt) |

По протоколам: [vless](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/vless.txt), [vmess](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/vmess.txt), [trojan](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/trojan.txt), [shadowsocks](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/shadowsocks.txt), [socks](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/socks.txt), [hysteria](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/hysteria.txt), [hysteria2](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/hysteria2.txt), [tuic](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/tuic.txt), [wireguard](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/wireguard.txt), [anytls](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/anytls.txt), [ssr](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/protocols/ssr.txt).
Число ключей во всех группах — в [индексе](https://raw.githubusercontent.com/ak-freedom/vpn-subscription/main/groups/index.json).

Названия имеют вид `0001 | 🇺🇸 United States | [102ms 58.8mbps]`.
Если скорость неизвестна, остаётся `[102ms]`. Число в `ms` — медианная задержка
HTTPS-проверок, а не ICMP ping. Нумерация общая, в отдельных группах возможны пропуски.

Скорость — результат ограниченного измерения через xray-knife к speed.cloudflare.com,
а не гарантированная пропускная способность. По умолчанию измеряются до 100 лучших
по задержке ключей, поддерживаемых этим инструментом, последовательно: до 3 МБ
в каждом направлении, до 5 секунд на направление. В названии используется скорость
загрузки; если измерение не удалось или протокол проверяется только Mihomo, выводится только задержка.

Страна определяется по IP адреса сервера через [ipwho.is](https://ipwhois.io/documentation),
результаты кэшируются на 7 дней. Это приблизительная геолокация точки подключения,
не обязательно страна выходного IP. Для домена с адресами в разных странах,
ошибок определения и неизвестных адресов используется `🌐 Unknown`.

Страна сервера не означает работу при российских ограничениях. Метки и группы
blacklist/whitelist не назначаются: для этого нужна отдельная подтверждённая проверка
в сети с соответствующим режимом ограничений. Исходные рекламные подписи не считаются доказательством.
Время измерения дополнительных данных — `metadata_at` в `status.json`; время
проверки доступности — `checked_at`. Обновление оформления не изменяет `checked_at`.

## Как получается подписка

1. Сборщик загружает исходные подписки и локальные списки ключей, разбирает URI, Base64 и поддерживаемые записи Clash YAML локальными модулями.
2. Удаляет повторы, сохраняя различия в портах, SNI, путях и других параметрах подключения.
3. Предварительно проверяет TCP-доступность адресов VLESS, VMess, Trojan, Shadowsocks, ShadowsocksR, SOCKS и AnyTLS с TCP-транспортом и ставит доступные первыми в очередь. Неудача TCP не исключает ключ; UDP-протоколы этот этап пропускают.
4. Выполняет настоящие HTTPS-запросы через каждую конфигурацию. Основные протоколы проверяет xray-knife, дополнительные протоколы и варианты URI — Mihomo.
5. Обычно выполняются три раунда; каждый ключ должен пройти все раунды и все заданные адреса проверки. Фактическое число раундов записано в `status.json`. По умолчанию проверяется `https://www.gstatic.com/generate_204`, от которого требуется ответ HTTP 204.
6. Прошедшие конфигурации сортируются по медианной задержке раундов, публикуются в текстовом виде и в Base64 одним коммитом вместе со сводкой результата.

GitHub хранит готовую подписку и сводку последней завершённой проверки.

Завершённый прогон без рабочих ключей публикует пустую подписку. Если часть источников недоступна, публикуется проверенный результат из остальных, а число ошибок указывается в `status.json`. Прерванный прогон не публикуется. При сбое выгрузки предыдущая версия в репозитории сохраняется — ориентируйтесь на время проверки в `status.json`.

## Ограничения

Успешная проверка подтверждает доступность заданного HTTPS-адреса через ключ в конкретной сети и в момент проверки. Доступность из другой сети, скорость на других ресурсах, все сайты и все режимы UDP отдельно не подтверждаются. Публичные серверы могут перестать работать между обновлениями; их операторы не контролируются сборщиком.

## Инструменты

- [xray-knife](https://github.com/lilendian0x00/xray-knife) — HTTPS-проверки через прокси-ядра.
- [Mihomo](https://github.com/MetaCubeX/mihomo) — дополнительный разбор URI и HTTPS-проверки.

## Основа проекта

За основу взяты [PulseConfigs](https://github.com/drmikecrypto/PulseConfigs) и коллектор из репозитория [dequar/deqwl](https://github.com/dequar/deqwl).
Код разбора, фильтрации и экспорта перенесён в локальные модули сборщика.
Из коллектора адаптирована идея предварительной TCP-проверки VLESS; определение страны и его способ удаления повторов не используются.

Идея отдельных протокольных подписок, топа быстрых серверов и оформления названий — [FlareFeed](https://github.com/svinakraft-maker/FlareFeed).

## Благодарности

Спасибо телеграм-каналу [«Запасной аэродром»](https://t.me/airfield1972) за идею создания подписки и знания, которые помогли её реализовать.
