## Part 1. Инструмент **ipcalc**

#### 1.1. Сети и маски
##### Определи и запиши в отчёт:
##### 1) Адрес сети *192.167.38.54/13*

- вводим команду ipcalc ``192.167.38.54/13`` узнаем адрес сети

![Alt Text](pictures/1.png)

##### 2) Перевод маски *255.255.255.0* в префиксную и двоичную запись, */15* в обычную и двоичную, *11111111.11111111.11111111.11110000* в обычную и префиксную

- вызываем команду ``ipcalc 255.255.255.0 | grep -e Adress: -e Netmask:`` двоичная запись: 11111111.11111111.11111111.00000000
префиксная: 24

![Alt Text](pictures/2.png)

- /15 в обычной: 255.254.0.0 в двоичной: 11111111.11111110.00000000.00000000 ``ipcalc 0.0.0.0/15 | grep -e Netmask:``

![Alt Text](pictures/3.png)

- перевод *11111111.11111111.11111111.11110000* в обычной: 255.255.255.240 в префиксной: /28

![Alt Text](pictures/4.png)

##### 3) Минимальный и максимальный хост в сети *12.167.38.4* при масках: */8*, *11111111.11111111.00000000.00000000*, *255.255.254.0* и */4*

- Минимальный и максимальный хост в сети *12.167.38.4* с указанными выше масками

![Alt Text](pictures/5.png)

#### 1.2. localhost
##### Определи и запиши в отчёт, можно ли обратиться к приложению, работающему на localhost, со следующими IP: *194.34.23.100*, *127.0.0.2*, *127.1.0.1*, *128.0.0.1*
 
- 194.34.23.100 - нет
- 127.0.0.2 - да
- 127.1.0.1 - да
- 128.0.0.1 - нет

#### 1.3. Диапазоны и сегменты сетей

- 10.0.0.45 192.168.4.2 172.20.250.4 172.16.255.255 10.10.10.10 - частные
- 134.43.0.2 172.0.2.1 192.172.0.1  172.68.0.2 192.169.168.1 - публичные

- Возможны: 10.10.0.2 10.10.10.10 10.10.1.255
- Невозможны: 10.0.0.1 10.10.100.1

## Part 2. Статическая маршрутизация между двумя машинами

- ``ip a`` для машин ws1 и ws2

![Alt Text](pictures/6.jpg)

##### Опиши сетевой интерфейс, соответствующий внутренней сети, на обеих машинах и задать следующие адреса и маски: ws1 - *192.168.100.10*, маска */16*, ws2 - *172.24.116.8*, маска */12*.

![Alt Text](pictures/7.jpg)

##### Выполни команду `netplan apply` для перезапуска сервиса сети.
![Alt Text](pictures/8.jpg)

#### 2.1. Добавление статического маршрута вручную
##### Добавь статический маршрут от одной машины до другой и обратно при помощи команды вида `ip r add`.

- Добавили статический маршрут и пропинговали
![Alt Text](pictures/9.jpg)

#### 2.2. Добавление статического маршрута с сохранением
- перезагружаем ws1 и ws2 с помощью ``sudo reboot``

- Измененный файл *etc/netplan/00-installer-config.yaml*.

![Alt Text](pictures/10.jpg)

- Пингуем соединение между ws1 и ws2
![Alt Text](pictures/11.jpg)

## Part 3. Утилита **iperf3**

- 8 Mbps = 1 MB/s
- 100 MB/s = 819200 Kbps
- 1 Gbps = 1024 Mbps

#### 3.2. Утилита **iperf3**
##### Измерь скорость соединения между ws1 и ws2.

- На ws1 пишем команду ``sudo iperf3 -s`` на ws2 ``sudo iperf3 -c 192.168.100.10``
![Alt Text](pictures/12.jpg)

## Part 4. Сетевой экран

#### 4.1. Утилита **iptables**

- Создаем файл ``/etc/firewall.sh`` и заполняем в соответствии с заданием
![Alt Text](pictures/13.jpg)


- Запускаем файл на ws1 и ws2
![Alt Text](pictures/14.jpg)

- Разница между стратегиями заключается в том, что в первом файле первым подходящим правилом для пакета является запрет, а во втором - разрешение. Применяется только первое подходящее правило, остальные игнорируются.

#### 4.2. Утилита **nmap**

- Команда ``ping`` и ``nmap`` 
![Alt Text](pictures/15.png)

## Part 5. Статическая маршрутизация сети

#### 5.1. Настройка адресов машин

- Скрины с содержанием файла *etc/netplan/00-installer-config.yaml* для каждой машины.
![Alt Text](pictures/16.png)
![Alt Text](pictures/17.png)
![Alt Text](pictures/18.png)
![Alt Text](pictures/19.png)
![Alt Text](pictures/20.png)

- `ip -4 a` проверить, что адрес машины задан верно
![Alt Text](pictures/21.png)
![Alt Text](pictures/22.png)
![Alt Text](pictures/23.png)
![Alt Text](pictures/24.png)

- Пинг ws22 с ws21. Аналогично пингуем r1 с ws11.
![Alt Text](pictures/25.png)
![Alt Text](pictures/26.png)

#### 5.2. Включение переадресации IP-адресов

- Включаем переадресацию IP на роутерах:
`sysctl -w net.ipv4.ip_forward=1`
![Alt Text](pictures/27.png)
![Alt Text](pictures/28.png)

- Скрин с содержанием изменённого файла */etc/sysctl.conf*
![Alt Text](pictures/29.png)
![Alt Text](pictures/30.png)

#### 5.3. Установка маршрута по-умолчанию

- Скрин с содержанием файла *etc/netplan/00-installer-config.yaml*
![Alt Text](pictures/31.jpg)

- Скрин с вызовом `ip r`
![Alt Text](pictures/32.jpg)

- Пингуем с ws11 роутер r2
![Alt Text](pictures/33.jpg)

#### 5.4. Добавление статических маршрутов
##### Добавь в роутеры r1 и r2 статические маршруты в файле конфигураций. Пример для r1 маршрута в сетку 10.20.0.0/26:

- Скрины с содержанием изменённого файла *etc/netplan/00-installer-config.yaml* для каждого роутера.
![Alt Text](pictures/34.jpg)

- Таблицы с маршрутами на обоих роутерах
![Alt Text](pictures/35.jpg)

- Запуск команды на ws11: ``ip r list 10.10.0.0/[маска сети]`` и ``ip r list 0.0.0.0/0``
![Alt Text](pictures/36.png)

- Для адреса 10.10.0.0/[маска сети] был выбран маршрут, отличный от 0.0.0.0/0, потому что маска /18 описывает маршрут к сети точнее, в отличие от маски /0.

#### 5.5. Построение списка маршрутизаторов
- Вывод команд traceroute и tcpdump
![Alt Text](pictures/37.jpg)

- Для определения промежуточных маршрутизаторов traceroute отправляет серию пакетов данных целевому узлу, при этом каждый раз увеличивая на 1 значение поля TTL («время жизни»). Это поле обычно указывает максимальное количество маршрутизаторов, которое может быть пройдено пакетом. Первый пакет отправляется с TTL, равным 1, и поэтому первый же маршрутизатор возвращает обратно сообщение ICMP, указывающее на невозможность доставки данных. Traceroute фиксирует адрес маршрутизатора, а также время между отправкой пакета и получением ответа (эти сведения выводятся на монитор компьютера). Затем traceroute повторяет отправку пакета, но уже с TTL, равным 2, что позволяет первому маршрутизатору пропустить пакет дальше. Процесс повторяется до тех пор, пока при определённом значении TTL пакет не достигнет целевого узла. При получении ответа от этого узла процесс трассировки считается завершённым.

#### 5.6. Использование протокола **ICMP** при маршрутизации
- Запускаем на r1 перехват сетевого трафика, проходящего через eth0 с помощью команды: ``tcpdump -n -i eth0 icmp``
- Пингуем с ws11 несуществующий IP (например, 10.30.0.111) с помощью команды: ``ping -c 1 10.30.0.111``
![Alt Text](pictures/38.jpg)

## Part 6. Динамическая настройка IP с помощью **DHCP**

- Указать адрес маршрутизатора по-умолчанию, DNS-сервер и адрес внутренней сети. Пример файла для r2:
![Alt Text](pictures/39.png)

- В файле resolv.conf прописываем nameserver 8.8.8.8.
![Alt Text](pictures/40.png)

- Рестарт isc-dhcp-server
![Alt Text](pictures/41.png)

- Перезагрузили и смотрим, что получили адрес, ниже пингуем ws22 с ws21
![Alt Text](pictures/42.png)

- Cкрин с содержанием изменённого файла *etc/netplan/00-installer-config.yaml*.
![Alt Text](pictures/43.png)

- Настройка dhcpd.conf для r1
![Alt Text](pictures/44.png)

Для r1 настроить аналогично r2, в файле resolv.conf прописать nameserver 8.8.8.8.
![Alt Text](pictures/45.png)

- Перезагружаем службу DHCP командой ``systemctl restart isc-dhcp-server``. Машину ws11 перезагружаем при помощи ``reboot`` и через ``ip a`` показываем, что она получила адрес.
![Alt Text](pictures/46.jpg)

- Делаем запрос обновления ip
![Alt Text](pictures/47.png)

- ``sudo dhclient -4 eth0``. После выполнения этой команды в выводе будет указан текущий IP-адрес интерфейса eth0.
![Alt Text](pictures/48.png)

## Part 7. **NAT**

- В файле /etc/apache2/ports.conf на ws22 и r1 изменяем строку Listen 80 на Listen 0.0.0.0:80, делаем сервер Apache2 общедоступным
![Alt Text](pictures/49.png)

- Запускаем веб-сервер Apache командой ``service apache2 start`` на ws22 и r1
![Alt Text](pictures/50.png)

- Добавляем на r2 правила из задания
![Alt Text](pictures/51.png)
![Alt Text](pictures/52.png)

- При запуске файла с этими правилами, ws22 не должна «пинговаться» с r1.
![Alt Text](pictures/53.png)

- Добавляем в файл ещё одно правило в соответствии с заданием
![Alt Text](pictures/54.png)
![Alt Text](pictures/55.png)

- При запуске файла с этими правилами, ws22 должна «пинговаться» с r1.
![Alt Text](pictures/56.png)

- Включаем DNAT и SNAT в соответствии с заданием
![Alt Text](pictures/57.png)

Проверяем соединение по TCP для SNAT: для этого с ws22 подключаемся к серверу Apache на r1
![Alt Text](pictures/58.png)

- Проверяем соединение по TCP для DNAT: для этого с r1 подключаемся к серверу Apache на ws22 командой ``telnet`` (обращаться по адресу r2 и порту 8080)
![Alt Text](pictures/59.png)

## Part 8. Дополнительно. Знакомство с **SSH Tunnels**

- Запускаем на r2 фаервол с правилами из Части 7.
![Alt Text](pictures/61.png)

- В файле /etc/apache2/ports.conf на ws22 изменяем строку ``Listen 80`` на ``Listen localhost:80``
![Alt Text](pictures/60.png)

- Пользуемся Local TCP forwarding с ws21 до ws22, чтобы получить доступ к веб-серверу на ws22 с ws21
- Образец команды: ``ssh -L [LOCAL_IP:]LOCAL_PORT:DESTINATION:DESTINATION_PORT [USER@]SSH_SERVER``
- Команда ``ssh -L 8888:10.20.0.20:80 shor@10.20.0.20``
- Проверяем соединение через telnet
![Alt Text](pictures/62.png)
![Alt Text](pictures/63.png)

- Пользуемся Remote TCP forwarding c ws11 до ws22, чтобы получить доступ к веб-серверу на ws22 с ws11 (прописываем именно с ws22)
- Образец команды: ``ssh -R [REMOTE:]REMOTE_PORT:DESTINATION:DESTINATION_PORT [USER@]SSH_SERVER``
- Проверяем соединение через telnet
![Alt Text](pictures/64.png)
![Alt Text](pictures/65.png)


