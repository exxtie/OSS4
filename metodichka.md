---

title: Методичка к ЛР4 по СПО

автор:

 Баснак Е.А
 2022, Москва

реквизиты для пожертвований (автору на покушать):

 Сбербанк: 4276 5300 1483 9828
 Тинькофф: 5536 9139 8163 6364

больше всех автора поблагодарили:

 1. Симич А.Б - 209.84

 2. Кочурова В.А. - 200

 3. Солдатов М.Д. - 200

 4. Казиков А.П. - 200
 
 5. Изотов А.А. - 199.98

---

## Установка пакетов

Первым делом необходимо установить нужные пакеты. На всякий случай стоит также обновить имеющиеся пакеты и репозитории.

Оба действия выполняются от имени суперпользователя.

Для входа в режим суперпользователя используется команда:

`su -l`

### Обновление

`apt-get update`

`apt-get dist-upgrade`

Установка новых пакетов производится командой `apt-get install`

### Установка необходимых пакетов

`apt-get install net-snmp-clients net-tools rrd-utils`

Можно прописать 3 команды `apt-get install` с установкой каждого пакета отдельно.

---

## Создание файлов скриптов

Сначала необходимо создать папки **bin** и **stat** в каталоге `/var/www/`.

Также в каталоге `/var/www/webapps/` необходимо создать каталог **scripts**.

Для создания нового каталога используется команда:

`mkdir <directory_name>`

где **<directory_name>** - имя нового каталога.

В папке `/var/www/bin/` создаём файлы **log-local-rrd.sh**, **log-local.sh**, **log-snmp-rrd.sh**, **log-snmp.sh**, **weblog.sh** (**weblog.sh** - имя файла, содержашего скрипт получения данных журнала веб-сервера, оно может и наверное должно отличаться от моего)

Файлы с расширением .sh - скрипты. Содержимое этих файлов, кроме **weblog.sh**, берется из примеров Фетисова, находящихся по ссылке ["я ссылка"](http://lab-00.edu.cbias.ru) (можно найти на общей странице лаб по Unix на вкладке **"Пример программ и результаты их работы"**).

##### Файл и его подпись в списке:

|     **Файл**     |                                                            **Подпись**                                                            |
|:----------------:|:---------------------------------------------------------------------------------------------------------------------------------:|
|   log-local.sh   |         ["Запись лога статистики локальной системы"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?../../bin/log-local.sh)        |
|    log-snmp.sh   |          ["Запись лога статистики интерфейса SNMP"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?../../bin/log-snmp.sh)          |
| log-local-rrd.sh | ["Запись лога статистики локальной системы в базу RRD"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?../../bin/log-local-rrd.sh) |
|  log-snmp-rrd.sh |   ["Запись лога статистики интерфейса SNMP в базу RRD"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?../../bin/log-snmp-rrd.sh)  |

В файлаx **log-snmp.sh** и **log-snmp-rrd.sh** необходимо немного изменить переменные **N** и **HOST**. Для этого используется следующая команда (но прежде необходимо выйти из режима суперпользователя с помощью команды `exit`):

`ssh -i ~/.ssh/<key_file> manager@192.168.230.230 get-SNMP`

где `<key_file>` - имя secret.key, скачанного в прошлой лабораторной работе, т.е. подключение к **manager** происходит аналогично этому подключению в лр №2.

Указанная выше команда выведет нужные значения для переменных **N(Port)** и **HOST**

##### Пример вывода

```
SNMP data source:


Host:   192.168.222.141
Port:   20
```

Теперь необходимо снова войти в режим суперпользователя:

`su -l`

Скрипт **weblog.sh** должен иметь следующее содержание:

```
#!/bin/bash
LOG_FILE=/var/www/stat/web.log
RES=`ssh -F /var/www/bin/config -i /var/www/bin/<key_file> lab-NN@192.168.230.230 get-logs`
echo "$RES" > "$LOG_FILE"
```

где **<key_file>** - имя закрытого ключа для подключения к тестовому серверу, созданного в прошлой лабораторной работе;

**config** - файл конфигурации ssh,

**NN** - номер варианта (можно посмотреть в приглашении командной строки).

Для работы этого скрипта не хватает ключа **<key_file>** и файла конфигурации **config**. Ключ необходимо скопировать в каталог `/var/www/bin/` из каталога, в который он был помещен в прошлой ларбораторной работе (скорее всего это каталог `/home/<user_name>/.ssh/`, где **<user_name>** - имя вашего пользователя, созданного в прошлой лр) (нужный ключ, скорее всего, называется **id_ed25519**). 

Файл конфигурации **config** необходимо создать в каталоге `/var/www/bin/` и добавить в него следующее:

```
Host *
StrictHostKeyChecking no
UserKnownHostsFile=/dev/null
```

---

## Скрипты для вывода данных 

Для вывода данных по запросам браузера предлагается установить в систему для запуска с помощью lighttpd несколько скриптов. 

В папке `/var/www/webapps/scripts/` создаём файлы **cgi-local.sh**, **cgi-snmp.sh**, **cgi-local-html.sh**, **cgi-local-html-table.sh**, **cgi-snmp-html.sh**, **cgi-snmp-html-table.sh**, **cgi-local.rrd**, **cgi-snmp.rrd**, **cgi-weblog.sh** и cgi-cat.sh

Содержимое этих файлов также можно взять из примеров Фетисова: ["я снова ссылка"](http://lab-00.edu.cbias.ru)

##### Файл и его подпись в списке

|         **Файл**        |                                                               **Подпись**                                                              |
|:-----------------------:|:--------------------------------------------------------------------------------------------------------------------------------------:|
|       cgi-local.sh      |               ["Простой скрипт статистики локальной системы"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-local.sh)              |
|    cgi-local-html.sh    |              ["HTML-скрипт статистики локальной системы"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-local-html.sh)             |
| cgi-local-html-table.sh | ["HTML-скрипт с выводом таблицей статистики локальной системы"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-local-html-table.sh) |
|       cgi-snmp.sh       |                ["Простой скрипт статистики интерфейса SNMP"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-snmp.sh)                |
|     cgi-snmp-html.sh    |               ["HTML-скрипт статистики интерфейса SNMP"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-snmp-html.sh)               |
|  cgi-snmp-html-table.sh |   ["HTML-скрипт с выводом таблицей статистики интерфейса SNMP"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-snmp-html-table.sh)  |
|      cgi-local.rrd      |              ["Выдача графиков статистики локальной системы"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-local.rrd)             |
|       cgi-snmp.rrd      |               ["Выдача графиков статистики интерфейса SNMP"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-snmp.rrd)               |
|      cgi-weblog.sh      |          ["Пример скрипта выборки данных из журнала веб-сервера"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-weblog.sh)         |
|        cgi-cat.sh       |                        ["Скрипт показа кода скриптов"](http://lab-00.edu.cbias.ru/scripts/cgi-cat.sh?cgi-cat.sh)                       |

#### cgi-weblog.sh

Основную проблему в данной лабораторной работе представляет скрипт выборки данных из журнала веб-сервера **cgi-weblog.sh**. Его придется редактировать самостоятельно, но у Фетисова имеется пример данного скрипта. Также в репозитории лежит мой пример скрипта, основанный на примере Фетисова.

Для получения своего варианта задания необходимо ввести следующую команду (перед вводом команды выходим из режима суперпользователя с помощью команды `exit`):

`ssh -i ~/.ssh/<key_file> manager@192.168.230.230 get-task`

где `<key_file>` - имя secret.key, скачанного в прошлой лабораторной работе.

Прежде, чем продолжить работу, необходимо снова войти в режим суперпользователя:

`su -l`

Полученное задание советую скопировать и заменить задание из файла-примера своим. Главное не забыть закомментировать все строки задания (это делается добавлением символа '#' в начале строки).

#### Несколько советов

* переменной `LOG_FILE` должно быть присвоено значение `/var/www/stat/web.log`;

* если планируется использовать новые переменные для выполнения задания, их необходимо прописывать до строчки `cat <<END`;

* для вывода переменной, использованной до строчки `cat <<END`, на экран, необходимо прописать `$<name>`, где **<name** - имя переменной, между тегами `<pre>` и `</pre>`;

* пример данного скрипта можно посмотреть в файле **cgi-weblog.txt** в данном репозитории;

* для проверки регулярных выражений можно использовать сайт ["regex101"](https://regex101.com).

#### Настройки lighttpd

Для запуска скриптов как веб-программ следует разрешить это в настройках ***lighttpd*** (расположенных в каталоге `/etc/lighttpd/`):

* нужно подключить модуль ***mod_cgi*** веб-сервера, раскомментировав строку `include "conf.d/cgi.conf"` в файле **modules.conf**;

* найти задать в подключенном файле конфигурации модуля ***mod_cgi*** (`conf.d/cgi.conf'`) секцию параметров вида:

    ```
    cgi.assign      = ( ".pl"  => "/usr/bin/perl",
                              ...
                        ".rrd" => "/usr/bin/rrdcgi",
                        ".sh" => "/bin/bash" )
    ```

* добавить расширения файлов скриптов в параметр `static-file.exclude-extensions` (его необходимо найти в файле) в файле ***lighttpd.conf***:

    `static-file.exclude-extensions = ( ".php", ".pl", ".fcgi", ".sh", ".rrd" )`

Изменения настроек вступают в силу после перезапуска ***lighttpd***. Это можно сделать командой reboot с полным перезапуском системы:

`reboot`

---

## Псевдопользователи

Для обеспечения безопасности системы должны соблюдаться определённые правила выполнения скриптов.

Сбор статистики должен выполняться от имени непривилегированного пользователя. Обычно для подобных задач создаётся отдельный псевдопользователь с ограниченными по сравнению с обычными пользователями системы правами. Псевдопользователь не должен иметь возможности удалённого входа в систему и не должен иметь возможности изменения скриптов.

Создание псевдопользователя достаточно простой процесс, который выполняется командой:

`useradd -r -M <user_name>`

где **-r** - опция для создания системного пользователя;

**-M** - опция для отмены создания домашнего каталога пользователя;

**<user_name>** - имя псевдопользователя.

Получение и запись журнала веб-сервера также должны выполняться от имени непривилегированного пользавтеля. Желательно создать отдельного псевдопользователя, отличного от псевдопользователя сбора статистики. Это делается также, как и в предыдущем случае.

В конце концов у меня появились 2 новых пользователя: **statusr** для сбора статистики и **webuser** для получения и записи журнала веб-сервера, далее я буду использовать их в качестве примеров.

---

## Права файлов и каталогов

Одна из самых сложных частей работы - задание верных прав для файлов скриптов и файлов логов (собранных данных). 

### Возвращаемся в каталог `/var/www/bin/`

Сейчас владельцем и группой имеющихся там файлов является пользователь root. Необходимо изменить группу этих файлов на созданных нами псевдопользователей. Это делается командой:

`chown root:<pseudouser_name> <file_name>`

где **root** - пользователь, который будет владеть данным файлом (в нашем случае root уже является владельцем, и после выполнения команды он останется владельцем);

**<pseudouser_name>** - имя созданного ранее псевдопользователя;

**<file_name>** - имя файла, владельца или группу которого мы хотим изменить.

Псевдопользователь ***statusr*** должен быть указан в качестве группы для файлов **log-local.sh**, **log-local-rrd.sh**, **log-snmp.sh**, **log-snmp-rrd.sh**

Псевдопользователь ***webuser*** должен быть указан в качестве группы для файлов **weblog.sh**, **config** и ключа для подключения к тестовому серверу.

Права всех файлов, кроме **config** и ключа, должны быть **-r-xr-xr--**. Установим данные права на все файлы в каталоге:

`chmod 0554 *`

где **0554** - восьмиричная запись числа 0101101100, которое соответствует необходимым параметрам доступа;

Теперь зададим права **-r--r-----** для файла **config** и ключа:

`chmod 0440 config <key_file>`

где **<key_file>** - имя ключа (скорее всего **id_ed25519**).

\* - обозначение всех файлов в папке.

### Переходим в каталог `/var/www/webapps/scripts/`

В данном каталоге также необходимо изменить группу всех файлов на **lighttpd**:

`chown root:lighttpd *`

Права доступа к файлам также необходимо поменять. Права доступа должны быть следующими **-r--r-----**. Это можно сделать следующей командой:

`chmod 0440 *`

### В каталоге `/var/www/webapps/`

Необходимо создать файл **index.html**, который будет содержать html-код страницы отображения результатов работы скриптов в браузере. Его содержимое также можно зайти в примерах Фетисова по ссылке ["и снова я ссылка"](http://lab-00.edu.cbias.ru) во вкладке **"Код этой страницы"**.

На момент написания данной методички в **index.html** не хватает одной строки: `<li><a href="/scripts/cgi-weblog.sh">Выборка данных из журнала веб-сервера</a></li>`, которая должна быть написана после строки `<li><a href="/scripts/cgi-snmp.rrd">Выдача графиков статистики интерфейса SNMP</a></li>`

Необходимо добавить её самостоятельно, либо взять содержимое файла **index.html** из репозитория.

Изменим группу, к которой относится этот файл, на ***lighttpd***:

`chown root:lighttpd index.html`

Также тут надо создать каталог png. В качестве группы, которой принадлежит данный каталог, необходимо указать ***lighttpd***:

`chown root:lighttpd png`

Права доступа для каталога png должны быть **drwxrwx---**, это делается командой:

`chmod 0770 png`

### Создание группы для псевдопользователей

Создаём новую группу пользователей, в которой будут содержаться пользователи statusr и webuser. Для создания группы используется команда `groupadd <group_name>`. Я создал группу **pseulab4**, в которую далее надо добавить созданных ранее псевдопользователей. 

Для добавления пользователей в группу откроем файл `/etc/group`, найдем там запись `pseulab4:x:<идентификатор>:` и допишем в эту строку имена созданных ранее псевдопользователей. В итоге получится что-то подобное:

`pseulab4:x:502:statusr, webuser`


### Переходим в каталог `/var/www/`

#### Каталог **stat**

Для каталога `/var/www/stat/` необходимо указать группу ***pseulab4***, которая была создана в предыдущем пункте:

`chown root:pseulab4 stat`

Права доступа к этому каталогу: **dr-xrwxr-x**:

`chmod 0575 stat`

#### Каталог **bin**

Права доступа к этому каталогу должны быть: **drwxr-xr-x** или **drwxr-x--x**:

Если по-умолчанию у вас отсутствует последний **x**, можете задать права следующей командой:

`chmod 0751 bin`

---

## Запуск скриптов на выполнение от имени псевдопользователя

Для выполнения команд от имени другого пользователя, к которому мы не имеем доступа напрямую (как наши псевдопользователи), необходимо воспользоваться командой ***sudo***, которая по умолчанию отключена в используемой системе. Чтобы исправить это необходимо открыть файл `/etc/sudoers` и расскомментировать строчку:

`WHEEL_USERS ALL=(ALL) ALL`

которая подписана комментарием `## Uncomment to allow members of group wheel to execute any command`

Есть вероятность, что файл `/etc/sudoers` помечен "только для чтения" и изменения в него внести не получится. В этом случае необходимо добавить разрешение на изменение файла:

`chmod u+w /etc/sudoers`

После внесения необходимых изменений, выключим возможность редактирования файла, тем самым вернум режим "только для чтения":

`chmod u-w /etc/sudoers`

Теперь, когда мы можем воспользоваться командой sudo, можно приступить к запуску скриптов.

Для запуска скрипта с какой-то периодичностью необходимо использовать утилиту crontab. Crontab - это текстовый файл, в котором хранится список команд, которые должны выполняться в указанное пользователем время. Crontab проверит дату и время выполнения сценария или команды, разрешения на выполнение и сделает это в фоновом режиме. У каждого пользователя может быть свой собственный файл crontab. Если пользователь **user_x** задаст в своем crontab файле выполнение скрипта **script_1**, то данные скрипт будет выполняться с заданной периодичностью от имени пользователя, который владеет данным crontab файлом, т.е. от имени пользователя **user_x**.

Таким образом можно сделать вывод, что для запуска скриптов от имени псевдопользователя, необходимо задать нужные скрипты в crontab файле этого псевдопользователя.

Редактирование crontab файла запускается командой `crontab -e`. Задача сводится к запуску данной команды от имени псевдопользователя. Это можно сделать командой:

`sudo -u <pseudouser_name> crontab -e`

где **sudo** - команда sudo, выполнение которой мы недавно разрешили;

**-u** - опция sudo, означающая выполнение команды, которая будет задана далее от имени другого пользователя;

**<pseudouser_name>** - имя пользователя, который будет выполнять команду;

**crontab -e** - открытие crontab файла на редактирование.

---

Для пользователя ***statusr*** файл должен иметь следующее содержимое:

```
#minute (0-59),
#|      hour (0-23),
#|      |       day of the month (1-31),
#|      |       |       month of the year (1-12),
#|      |       |       |       day of the week (0-6 with 0=Sunday).
#|      |       |       |       |       commands
*       *       *       *       *       /var/www/bin/log-local.sh
*       *       *       *       *       /var/www/bin/log-local-rrd.sh
*/2     *       *       *       *       /var/www/bin/log-snmp.sh
*/2     *       *       *       *       /var/www/bin/log-snmp-rrd.sh
```

где \* - означает выполнение каждую единицу данного разряда (для минут - каждую минуту, для часов - каждого часа и тд);

\*/2 - означате выполнение каждую вторую единицу данного разряда (для минут - выполнение каждые две минуты).

---

Для пользователя ***webuser*** файл должен иметь следующее содержимое:

```
#minute (0-59),
#|      hour (0-23),
#|      |       day of the month (1-31),
#|      |       |       month of the year (1-12),
#|      |       |       |       day of the week (0-6 with 0=Sunday).
#|      |       |       |       |       commands
1-59/10 *       *       *       *       /var/www/bin/weblog.sh
```

где **1-59/10** означает выполнение каждую 1, 11, 21, 31, 41 и 51 минуты часа. 

Данная периодичность настраивается в зависимости от варианат. Скрипт должен выполняться каждые 10 минут со смещением, которое определяется последней цифрой идентификатора виртуального сервера (**VEID**), который указан в первом письме Фетисова, с данными к лабораторным работам. Например: **VEID: 230141**, следовательно смещение равно 1, поэтому в минутах написано **1-59\10**. Для изменения смещения необходимо изменить первую цифру задания времени, т.е. изменить единицу на смещение, соответствующее последней цифре **VEID**, студента, читающего эту методичку.

Для проверки имеющихся crontab файлов можно использовать команду `crontab -l`, которая выводит содержимое crontab файла без возможности его редактирования:

`sudo -u <pseudouser_name> crontab -l`

---

## Проверка работы

Посмотреть результаты работы можно на странице `http://lab-NN.edu.cbias.ru`, где **NN** - номер вашего варианта.
