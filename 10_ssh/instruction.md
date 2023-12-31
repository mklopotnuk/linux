secure shell - удобная и безопасноя оболочка для подключения к удалённому серверу через терминал.

Если у вас нет возможности подключиться к удалённому серверу, используйте localhost, физически вы останетесь на своём компьютере, но сможете использовать команды для работы через ssh и получите бесценный опыт.

- ### Сервер <br>
1. Для начала, необходимо убедиться что на сервере установлен ssh пакет.
```
apt list installed | grep ssh
```
установите его при необходимости.

2. Проверьте текущий статус демона **sshd**
```
systemctl status sshd
```
должна показаться информация о том, что процесс запущен.

3. Командой **ip addr show** посмотрите какой адрес имеет ваш компьютер на интерфейсе **loopback**. (обычно это 127.0.0.1).

- ### Клиент<br>
4. Откройте новое окно терминала. Введите
  
```bash
ssh 127.0.0.1 -l username
```
Если вы соединяетесь с сервером впервые, вам будет выдано сообщение о том, что сервер с которым вы собираетесь произвести соединение не имеет ключа на вашем компьютере. Нажмите **y**, после чего вас попросят ввести пароль пользователя от чьего имени вы хотите действовать. После успешного ввода вы подключитесь к серверу и сможете отправлять команды и манипулировать данными на сервере.
> При следующем подключении к серверу вопрос на установку соединения задаваться не будет, поскольку все сервера с которыми вы устанавливали соединения хранят свой ключ в ~/.ssh/known_hosts. Если, вдруг, привычный вам сервер запрашивает разрешение на установку соединения, скорее всего произошла подмена и сервер с которым вы пытаетесь установить соединение не тот, за кого себя выдаёт. Однако, пароль пользователя будет запрашиваться каждый раз.

5. На сервере введите команду **w** и вы увидите подключившегося пользователя.

- ### Копируем информацию себе на компьютер
1. 
-




1. Открой shell от имени рута
2. введите команду ssh-keygen . Когда спросит хотите ли вы использовать кодовую фразу, нажмите enter, чтобы пропустить.
3. Когда спросит куда сохранить приватный ключ, введите имя по умолчанию ~/.ssh/id_rsa
4. Затем попросит ввести кодовую фразу. Дважды нажмите **Enter**
5. Приватный ключ теперь хранится в ~/.ssh/id_rsa и публичный ключ находится в ~/.ssh/id_rsa.pub
6. командой ssh-copy-id server скопируйте только что созданный публичный ключ. После этого пароль запросится только один раз.
7. Теперь при вводе команды ssh server вас не будет запрашивать пароль.
На сервере ключ хранится в ~/.ssh/authorized_keys


## Предотварщая взлом

- Отключите вход суперпользователя по ssh в файле /ect/ssh/sshd_config используя опцию PermittedRootLogin в значение yes
- Использовать публичные/приватные ключи для входа
- Не использовать порт 22 для подключения через ssh
- Разрешать только определённым пользователя подключаться.

### Параметры /etc/ssh/sshd_config
- **AddressFamily** - семейство адресов с которыми будет работать демон (ipv4 или ipv6)
- **ListenAddress** - Определяет в какой сети по IP будет работать ssh. Имеет смысл, когда необходимо организовать доступ по ssh  только по локальным IP адресам, а все внешние запретить для доступа. Если стоит :: или 0.0.0.0 , значит ssh будет работать на всех своих доступных сетях для ipv6 и ipv4 соответственно.
- **HostKey** - Ключи для проверки авторизированных серверов.
- **PermitRootLogin** - Разрешает доступ суперпользователя по ssh. Значение prohibit-password разрешает логиниться только с помощью пользовательских ключей.
- **PublickeyAuthentication** - разрешить логиниться по публичным ключам.
- **PasswordAuthentication** - разрешает логиниться по паролю пользователя
- **X11Forwarding** - Разрешает проброс графики. Небезопасно для клиента.
- **Allow Users** - Список пользователей разделённых пробелом, кто может подключаться через ssh. Обычно этот параметр опускается, нужно прописать вручную.
- **MaxAuthTired** - Максимальное количество попыток авторизации. При достижении половины значения, данные начинают логироваться.
- **MaxSession** - количестко сессий открытых с одного ip.

 

После редактирования файла для вступления изменений в силу дайте команду
```
systemctl restart sshd
```
> Можно использовать заменить restart на reload, что позволит заново считать настройки, но это будет работать не на всех машинах. 

для подлючения от имени пользователя используется влаг -l
```bash
ssh -l username servername
```
но его можно и не использоваеть, тогда подключение будет происходить от текущего пользователя локальной машины.

## Настраиваем альтернативный порт
TLS - transport layer security
443 и 80 порт по умолчанию подключен к веб серверу с TSL. 


##  Задача
1. Откройте shell на server1 и начните редактировать файл конфигурации демона sshd.
2. Найдите строку, отвечающую за порт. Измените порт на 2022.
3. Добавьте несколько пользователей которые могут подключаться через ssh.
4. Сохраните изменения перезагрузите демона.
5. наберите **systemctl status -l sshd**. Вы увидите "permission denied" потому что SSH пытается подключится к порту 2022.
6. Наберите **semanage port -a -t ssh_port_t -p tcp 2022** для того чтобы были применены необходимые настройки для порта 2022.
7. Откройте firewall для порта 2022. Наберите firewall-cmd --add-port=2022/tcp,
8. Наберите systemctl status -l sshd. Теперь sshd слушает на двух портах.
9. ssh -p 2022 user@server1

