# Сессионный проект по безопасности GNU/Linux

Автор: Песня Иван Александрович. Бухгалтерский шифр обучающегося: **372344**.

Работа выполнена в РЕД ОС 8.0.3 Standard Server Minimal, aarch64, без графической оболочки.

## Выполненные разделы

| Раздел | Настройки и результат | Файлы для проверки |
| --- | --- | --- |
| 1. Сеть | DHCP, имя хоста `mephi-2026.domain.local`. Ping до `8.8.8.8`: четыре ответа, потерь нет. | `ping.out`, `history.out` |
| 2. Программное обеспечение | Система обновлена через dnf. Установлены nginx и libcap-ng-utils. Пакет tcpdump скачан в `/tmp` командой `dnf download` и установлен через `rpm -ivh`. | `dnf.out`, `history.out` |
| 3. Файловые системы | На втором диске `/dev/vdb` создан один раздел ext4 с меткой `MEPHI_WEB`. Он монтируется в `/mephi-web`; в fstab указана метка тома. | `fstab`, `stat.out` |
| 4. Сервисы | nginx запущен и включён в автозагрузку. Сообщения сервиса за текущую загрузку сохранены. | `journalctl.out`, `history.out` |
| 5.1. Права доступа | В `/data/mephi-2026` разработчики user1, user2 и user3 с UID 5501, 5502 и 5503 могут читать и изменять общие файлы. Кураторы curator1 и curator2 входят в curators с GID 4444 и имеют доступ на чтение. Наследование прав настроено через setgid и default ACL. Остальным доступ закрыт. | `passwd`, `group`, `stat.out`, `history.out` |
| 5.2. Привилегии tcpdump | Setuid отсутствует. Заданы `cap_net_admin,cap_net_raw=ep`; user1 проверил захват пакета. | `getcap.out`, `history.out` |
| 5.3. SELinux | Режим Enforcing. Для `/mephi-web` и его содержимого настроен постоянный тип `httpd_sys_content_t`. | `getenforce.out`, `stat.out`, `history.out` |
| 6.1. Локальный вход | Вход кураторов через консоль запрещён модулем pam_access в сервисе login. Разработчики и root могут входить. | `history.out` |
| 6.2. Пароли | Пароли не короче 12 символов, в том числе у root. Срок смены для root и пяти пользователей: каждые 90 дней. | `pwquality.conf`, `shadow`, `history.out` |
| 7. Веб-страница | nginx отдаёт `/mephi-web/index.html` с текстом `Hello from Student: 372344`. | `curl.out`, `mephi-screenshot.png` |

## Проверка после перезагрузки

После перезагрузки проверены монтирование диска, автозапуск nginx, права доступа, capabilities tcpdump, режим SELinux и ограничения консольного входа. Запрос `curl http://localhost/` возвращает:

```text
Hello from Student: 372344
```

## Состав комплекта

| Файл | Содержимое |
| --- | --- |
| `mephi-screenshot.png` | Снимок терминала с именем хоста, режимом SELinux и ответом веб-сервера. |
| `history.out` | Выполненные команды. |
| `ping.out` | Результат ping до 8.8.8.8. |
| `dnf.out` | История транзакций dnf. |
| `stat.out` | Атрибуты каталогов `/data/mephi-2026` и `/mephi-web`. |
| `journalctl.out` | Журнал nginx за текущую загрузку. |
| `getcap.out` | Capabilities tcpdump. |
| `getenforce.out` | Режим SELinux. |
| `curl.out` | Ответ веб-сервера. |
| `fstab` | Настройки монтирования файловых систем. |
| `passwd` | Учётные записи пользователей. |
| `shadow` | Парольные данные и сроки действия паролей. |
| `group` | Группы пользователей. |
| `pwquality.conf` | Настройки проверки качества паролей. |
