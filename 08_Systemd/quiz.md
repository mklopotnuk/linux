- Какая команда покажет все файлы сервисных модулей в вашей системе, которые в данный момент загружены?

  - [ ] systemctl --type=service

  - [ ] systemctl --type=service --all

  - [ ] systemctl --list-services

  - [ ] system --show-units | grep services
- Вы хотите узнать, какие другие модули Systemd имеют зависимости от определенного модуля. Какую команду вы бы использовали
- [ ] systemd list-dependencies --reverse
- [ ] systemctl list-dependencies --reverse
- [ ] systemctl status my.unit --show-deps
- [ ] systemd status my.unit --show-deps -r
- Как изменить редактор по умолчанию на **vim**.
- [ ] export EDITOR=vim
- [ ] export SYSTEMD_EDITOR=vim
- [ ] export EDITOR=/bin/vim
- [ ] export SYSTEM_EDITOR=/bin/vim
- 
- Для чего нужна система инициализации?
- Как узнать какая система инициализации запускается на системе?
- Что такое юнит?
- Что такое таргет?
- Какой командой можно посмотреть все запущенные сервисы?
- Как узнать таргет по умолчанию в системе?
- Как изменить редактор по умолчанию для systemctl? 
- Как посмотреть какие сервисы входят в таргет?
- Что такое демоны?
- Что такое сервисы?
- Откуда Systemd берёт unit файлы для запуска?
- Как узнать какие опции можно установить для сервиса httpd?
- Как узнать какая команда выполняется при старте сервиса?
- Как изменить текущий таргет системы?
- Как посмотреть все зависимости конкретного юнита?
- Как добавить/убрать сервис из автозагрузки?
- Как посмотреть включен ли сервис?