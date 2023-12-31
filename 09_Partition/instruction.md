# Разбиваем диск

## Создадим разделы

Возьмите флешку и подключите её к **usb** порту. Если вы работаете с виртуальной машиной, то проще будет добавить новый виртуальный жёсткий диск. Для Virtual Box, выберите инструменты > создать.

![add_disk](/Users/egordmitriev/Dropbox/introducion_to_Linux/9_Partion/images/add_disk.png)

Настройки оставьте как на снимке ниже. 

![create_disk](/Users/egordmitriev/Dropbox/introducion_to_Linux/9_Partion/images/create_disk.png)

После того как создастся диск, перейдите в **настройки** вашей виртуальной машины >  **Носители**  > **Добавить новое устройство**

![add_disk_to_machine](/Users/egordmitriev/Dropbox/introducion_to_Linux/9_Partion/images/add_disk_to_machine.png)

Выберите жёсткий диск и далее добавьте только что созданный вами накопитель.

Теперь можно запускать Linux.

Перейдите в суперпользователя и выполните команду

```bash
fdisk -l
```

На экране вы увидите список подключённых к вашему компьютеру устройств. Найдите среди них свою флешку. Проще всего ориентироваться на размер. У меня это **sdb**. Сейчас мы будем разбивать диск **/dev/sdb** на разделы, используя утилиту **fdisk**.

## Первый раздел

```bash
fdisk /dev/sdb
```

Введите **m** для списка доступных команд. Тут лучше это окно оставить, а в новом окне выполнить те же самые действия, но не вводить **m**. В любом случае после строки **Command (m for help):** введите

```bash
p  # вывести подробную информацию о выбранном диске
```

Сравните ещё раз размер и название диска. После введите 

```bash
o
```

На экране вы увидите сообщение "Created a new DOS disklabel" и далее идентификатор. 

Введите n

```bash
n
```

вам будет  предложено выбрать тип раздела. Мы просто нажмём Enter, автоматом выберется основной раздел. Введём номер раздела.

```bash
1
```

Вводим начальный сектор. Опять просто нажмём Enter, выберется ближайший свободный (2048). Далее нас попросят ввести конечный сектор, но тут можно указать просто размер раздела.

```bash
+2G
```

Поздравляем! Вы только что создали один раздел на диске. 

## Второй раздел

Чтобы увидеть созданный раздел, введите **p**.

Создадим ещё пару разделов.

1. Нажимаем **n**
2. Выберем **e**
3. Enter
4. +10G

Нажмите **p**. Сейчас мы создали расширяемый раздел внутри которого будем создавать логические тома.

### Первый логический том.

1. Нажимаем **n**. обратите внимаение, на то, что теперь у нас вместо расширяемого раздела, предлагает выбрать логический том. Это как-раз тот, что мы создали ранее.
2. На этот раз, вместо **e**, нам предлагают **l**. Согласимся на создание логического тома.
3. Enter
4. +4G

### Второй логический том

1. Нажимаем **n**
2. Выбираем **l**
3. Enter
4. +4G

 Введите команду **p** чтобы посмотреть все только что созданные разделы и чтобы изменения вступили в силу, введите **w**.

Теперь введём **fdisk -l** чтобы увидеть все доступные разделы. 

## Файловая система.

Не смотря на то, что мы разбили диск на несколько разделов, использовать это пространство нельзя, пока там не будет файловой системы. Именно файловая система отвечает за структуру и принцип хранения файлов. В большинстве случаев работая с Linux вы будете работать с ext4 или xfs. На самом деле Linux может работать(хранить и открывать файлы) и с другими файловыми системами, но из-за особенностей их работы, установить на них Linux не получится.

Ранее при создании раздела, вам необходимо было указать  номер начального и конечного сектора. Именно в этих секторах(на SSD используются страницы) и будут храниться файлы, фактически - это номер ячейки памяти на диске. Размер сектора диска может быть разный. Чаше встретиться размер в 4Кб, минимальный размер в 512б, который тоже можно часто встретить. Но, файловая система должна хранить таблицу адресов к каждому сектору. И если адресов будет большое количество, то таблица будет занимать много места. А так же если файл находится сразу в нескольких секторах, например, [ 3000, 4005, 20000, 3050, 50000] , то, несложно представить, что скорость с которой будет считываться информация будет в разы меньше. Именно поэтому было  решено объеденить сектора в блоки и именно поэтому в системе диски являются блочными устройствами. Да и файловой системе проще работать с  блоками. 

Допустим, размер сектора - 512б, объединяем 8 секторов в один блок и получаем блок размером 4Кб. Но тут есть существенный недостаток. Если размер файла 10б, то он всё-равно будет занимать один блок.

Файловая система отвечает, во-первых, за алгоритм записи данных, их изменение, и безошибочного оперделения метаданных файла с самим файлам. А во-вторых, за само хранение файлов и информацию о них, собственно то, что храниться на диске. Первая часть программная, за которую отвечает ядро, а вторая - физическая - структура организации хранения информации.

### inode

Теперь давайте поговорим поподробнее о inode - узлах в которых хранится метаинформация файла. В файловой системе xfs inode создаются динамически, в отличие от ext4, в которой inode создаются на каждые несколько килобайт, подробнее можно посмотреть введя команду **man mke2fs** затем **/-i**. По умолчанию на каждые 16Кб создаётся одна inode. При этом сама inode занимает минимум 256 байт. Это кажется несущественным пока не произведём некоторые расчёты.

на каждый 1 Mb иноды занимают 16 Kb<br>на каждый 1 Gb иноды занимают 16 Mb<br>на каждый 1 Tb иноды занимают 16 Gb

При настройках по умолчанию, на каждый мегабайт дискового пространства, вы можете записать 64 файла. Этого более чем достаточно, но если вы собираетесь на диске хранить почту, то лучше заранее побеспокоиться о количестве inode, или использовать xfs.

## Создание файловой системы в разделе

Все настройки которые будут прменены к файловой системе ext4 можно посмотреть введя команду 

```bash
less /etc/mke2fs.conf
```

Для начала выведем список всех доступных дисков. 

Можно использовать команду **fdisk -l**, а можно

```bash
lsblk
```

Данная команда позволит вывести списко подключённых блочных устройств.

Для того чтобы на одном из разделов создать файловую систему, достаточно указать её тип через точку

```bash
mkfs.ext4 /dev/sdb1
```

На экране появится следующая информация.

![mkfs sdb1](/Users/egordmitriev/Dropbox/introducion_to_Linux/9_Partion/images/mkfs sdb1.png)

Для того чтобы увидеть более подробную информацию о файловой системе диска, воспользуйтесь командой 

```bash
tune2fs -l /dev/sdb1
```

### Check point

- [ ] Примените команду **tune2fs** к разделам без файловой системы.
- [ ] На оставшихся двух логических томах создайте файловую систему **ext4**.
- [ ]  Количество созданных блоков умножте на размер одного блока.
- [ ] Получившееся значение разделите на количество созданных inode.

## Монтаж и установка "под ключ".

Задачу которую мы будем решать позволит сохранить данные пользователей в случае переустановки системы. Мы вынесем домашнюю дирректорию (/home) со всеми вложенными папками на другой жёсткий диск, а затем примонтируем её к основной системе, так, что система будет продолжать работать с этой дирректорией как и прежде, но все записи будут сохраняться на другом диске. Это позволит при переустановке системы сохранить все данные и в случае необходимости, восстановить хранящуюся там информацию. 

Приступим. Переносить все данные мы будем на один из логических томов. У меня это sdb5. Для начала необходимо примонтировать том.

```bash
mount /dev/sdb5 /mnt
```

Неплохо будет посмотреть размер занимаемый директорией /home

```bash
du -h /home | tail -1
```

Так же посмотрим какие процессы используют в текущий момент /home

```bash
lsof +D /home
```

+D позволяет посмотреть все субдиректории. Нельзя производить демондаж до тех пор пока хоть один процесс обращается к /home. Все процессы которые вы видите в списке относятся к графическому интерфейсу, поэтому необходимо разлогиниться и работать из виртуального терминала. Если вы в работаете от суперпользователя, сначала нажмите Ctrl+D, а затем введите команду.

```bash
gnome-session-quit --no-prompt
```

Откройте виртуальный терминал Ctrl+F3 для витруальных машин (Alt+Ctrl+F3). Залогинтесь под рутом и ещё раз введите команду 

```bash
lsof +D /home
```

Если у вас показались какие-то процессы, используйте **kill**. Убедитесь что диск на который вы собираетесь копировать данные примонтирован. Напомню, точка монтирования /mnt

```bash
df -h
```

Переносим файлы.

```bash
mv -v /home/* /mnt
```

Проверим наличие файлов

```bash
ll /mnt
```

Проверим размер.

```bash
du -h /mnt | tail -1
```

Теперь отмонтируем sda5, чтобы примотрировать его на место /home

```bash
umount /dev/sdb5
```

```bash
mount /dev/sdb5 /home
```

Казалось бы на этом можно закончить, но если вы перезагрузите систему, директория **/home** будет пустой, чтобы диск автоматически монтировался в нужное место при старте системы необходимо сказать ей об этом. Эту информацию необходимо записать в файл **/etc/fstab**. 

```bash
less /etc/fstab
```

Здесь указывается UUID(идентификатор файловой системы), точка монтирования, тип файловой системы и ещё несколько опций. Поскольку мы будем вносить дополнительные записи в данный файл, на всякий случай создадим бэкап.

```bash
cp /etc/fstab{,.bkp}
```

 Узнать UUID можно при помощи утилиты **blkid**

```bash
blkid /dev/sdb5
```

Чтобы получить только UUID разделим строку по пробелу **-d'  '** и получим второй столбец **-f2**.

```bash
blkid /dev/sdb5 | cut -d' ' -f2
```

Теперь добавим полученную строку в конец файла **/etc/fstab/**.

```bash
blkid /dev/sdb5 | cut -d' ' -f2 >> /etc/fstab
```

Откроем этот файл и добавим недостающие опции через пробел.

```bash
nano /etc/fstab
```



- Полсле UUID допишите точку монтирования - **/home**
- Затем тип файловой системы - **ext4**
- далее опции монтирования. Напишем **noatime** чтобы в inode не писалась информация о последнем доступе к файлу - это существенно сократит частоту записи на диск. Хотя можно оставить **default**.
- Далее пишем цифру, которая будет говорить нужно ли создавать **dump**(мы не будем создавать резервную копию), ставим **0**.
- Ещё одна цифра обозначает флаг проверки файловой системы. Тоже ставим **0**.

Сохраняем и закрываем редактор. Теперь можно перезагрузить компьютер **reboot**. При загрузке операционная система смотрит **fstab** и считывает информацию какую файловую систему куда монтировать.

Если при редактировании **fstab** вы допустили ошибку, операционная система не загрузится, но предложит вам ввести пароль **root** и вы сможете используя текстовый интерфейс исправить причину поломки.  

### Check point

- [ ] Командой **du -h** посмотрите куда примонтирован ваш накопитель

- [ ] Закомментируйте последнюю запись в **fstab** которую вы добавили 
- [ ] Перезагрузите компьютер.
- [ ] Графический интерфейс входа в систему подгрузится, но войти не даст.
- [ ] Используя виртуальный терминал, создайте в папке **home**, папку с именем вашего пользователя. Если не получается войти в виртуальный терминал, перезагрузите компьютер, но не логинтесь в графическом интерфейсе.
- [ ] Не забудьте изменить владельца только что созданной директории. Перезагрузите компьютер. 
- [ ] Залогинтесь и убедитесь, что всё работает.
- [ ] Завершите текущую сессию **gnome-session-quit --no-prompt**. Войдите в виртулаьный терминал.
- [ ] Смонтируйте диск на котором хранилась старая папка **home**.
- [ ] Перенесите все данные с примонтированного диска в директорию **/home**.

