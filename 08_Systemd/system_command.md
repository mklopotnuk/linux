

| Команда                                   | Описание                                          |
| ----------------------------------------- | ------------------------------------------------- |
| systemctl --type=service                  | Показывает только сервисные юниты                 |
| systemctl list-units --type=service       | Показывает активные сервисные юниты               |
| systemctl list-units --type=service --all | Показывает юниты как аткивные так и остановленные |
| systemctl --failed --type=service         | Показывает сервисы завершившиеся с ошибкой        |
| systemctl status -l name.service          | Показывает детальную информацию о сервисе         |

