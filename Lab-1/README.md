# Команды IOS. Базовая конфигурация устройств

###  Задание:
1. Создать указанную ниже топологию с помощью подходящего эмулятора и проверить конфигурацию коммутатора по умолчанию.

#### Топология сети
![alt-текст](/Lab-1/lab-1.png "Топология lab-1")

2. Настроить с помощью консоли основные параметры устройств, используя заданные параметры.

#### 	Таблица адресации
 |Устройство| Интерфейс| ip-адрес/префикс|
 |----------|----------|-----------------|
 |  S1      | VLAN1    |192.168.1.2  /24 |
 |  PC-A    | NIC      |192.168.1.10 /24 |

3. Проверить работоспособность сетевого подключения, используя эхо-запрос и telnet.  

## Часть 1. Создание заданной топологии с помощью подходящего эмулятора и проверка конфигурации коммутатора по умолчанию. 

###  Решение:

1. Воспользуемся для создания заданной топологии соответствующим эмулятором,например, Cisco Packet Tracer v.8.1. На первом этапе подключимся к коммутатору Cisco 2960-24TT с помощью консольного провода. 

#### Первичная настройка свитча через консоль

a) схема консольного соединения

![alt-текст](/Lab-1/init_switch_console.png "Консольное подключение")

b) использовать консоль на первоначальном этапе необходимо, т.к. настраиваемое оборудование может содержать следы предыдующих конфигураций с заданными, но неизвестными настройками, в том числе и IP адресами управления. Для этого компьютер, с помощью которого производится подключение, должен быть оборудован последовательным СОМ портом и/или, в его отсутствие (неисправности), соотвествующим USB-COM адаптером. Для успешного подключения иногда требуется предварительно уточнить некоторые параметры, например, скорость подключения в бодах, а также номер COM порта . Если все будет исправно мы сможем подключиться к коммутатору и получить доступ к CLI для проверки текущих настроек оборудования. Для подключения к коммутатору будем использовать встроенную в эмулятор CPT 8.1 компьтера PC-A программу "Terminal", расположенную во вкладке "Desktop"   

2. Проверка текущих или заводских настроек:

<details>
  <summary> Вход в привилегированный режим и просмотр текущей конфигурации </summary>

```
enable
show running-config
```
</details>
   
  <details>
  <summary> Результат отображения текущей конфигурации </summary>

```
Building configuration...

Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
!
!
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
!
!
!
end
```
</details>

a) Видим, что данный коммутатор не настроен и имеет заводские базовые установки, например, название коммутатора -> Switch, отсутствие каких либо Vlan, кроме Vlan1 и настроек на каждом интерфейсе.

b) Видно также, что в данной модели коммутатора присутствует всего 26 физических портов: 24 fastethernet порта и 2 gigabitethernet порта. Кроме этого есть интерфейс Vlan1, консольный порт line con и терминальный порт line vty, диапазон значений которго может устанавливаться от 0-15, т.е можно установить 16 удаленных сессий максимум, используя такие службы как telnet или ssh.  

с) Вывод содержимого файла загрузочной конфигурации:

<details>
  <summary> Просмотр файла Startup-config </summary>

```
enable
Switch#show startup-config
startup-config is not present
```
</details>

В ответ коомутатор сообщил, что файл конфигурации отсутствует, т.к. запись в NVRAM еще не осуществлялась. Содержимое файла startup-config хранится в так называемой долговременной памяти NVRAM коммутатора, и не пропадает при отключении питании, ее можно условно сравнить с жестким диском компьютера. Содержимое running-config копируется из NVRAM и хранится в RAM памяти, до тех пор пока включено питание. Изменения в running-config непосредственно влияют на работу коммутатора и могут пропасть после аварийного отключения или внезапного скачка напряжения в электрической сети, поэтому текущие настройки или важные изменения в конфигурации необходимо сохранять путем их записи в startup-config файл командой -> _Switch# copy running-config startup-config_      

d) Для удаленного подключения и управления коммутатором используют IP адресацию. Для этого используют, так называемй SVI, т.е. IP адрес назначенный на VLAN интерфейс для удаленного управления.   В базовой конфигурации свитча он отсутствует и к тому же выключен. Об этом нам говорят строчки из выше приведенного running-config:

<details>
  <summary> SVI info (часть running-config)</summary>

```
!
interface Vlan1
 no ip address <--- отсутствует IP
 shutdown <--- интерфейс выключен 
!
```
</details>

e) Изучение информации интерфейса Vlan 1

Введем соответствующую команду

___Switch#show interface vlan 1___

<details>
  <summary> Vlan 1 статус без подключения Ethernet кабеля </summary>

```
Vlan1 is administratively down, line protocol is down
  Hardware is CPU Interface, address is 0030.a348.c494 (bia 0002.4a97.730c)
  MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 21:40:21, output never, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     1682 packets input, 530955 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicast)
     0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     563859 packets output, 0 bytes, 0 underruns
     0 output errors, 23 interface resets
     0 output buffer failures, 0 output buffers swapped out
```
</details>

Также наблюдаем в первых строчках информацию о том, что интерфейс Vlan 1 выключен, интернет протокол выключен, МАС адрес данного интерфейса 00:30:a3:48:c4:94, максимальный размер пакетов 1500 бит, пропускная способность 100 Мбит и другая статистическая информация.

f) Добавим к нашей схеме соединение с помощью Ethernet кабеля, используя со стороны свича интерфейс Fa0/6, а со стороны PC-A интерфейс сетевого адаптера NIC:

![alt-текст](/Lab-1/adding_eth_cable.png "Ethernet подключение с помощью кабеля")

Произведем повторный вывод информации о состоянии интерфейса Vlan 1 

<details>
  <summary> статус Vlan 1  после подключения Ethernet кабеля   </summary>

```
Switch# sh int vlan 1
Vlan1 is up, line protocol is up
  Hardware is CPU Interface, address is 0030.a348.c494 (bia 0030.a348.c494)
  MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 21:40:21, output never, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     1682 packets input, 530955 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicast)
     0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     563859 packets output, 0 bytes, 0 underruns
     0 output errors, 23 interface resets
     0 output buffer failures, 0 output buffers swapped out
```
</details>



Наблюдаем в результате, что интерфейсы Vlan 1 со стороны свитча, а также интерфейс NIC на стороне PC-A стали активными, протоколы линии тоже. 


g) Получение информации о версии прошивки ПО свитча

для получения данной информации нужн ввести соответствующую команду:

___Switch>show version___

<details>
   <summary> Информация о текущем ПО коммутатора Cisco 2960-24TT </summary>

```Switch>sh version
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

ROM: Bootstrap program is C2960 boot loader
BOOTLDR: C2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)

Switch uptime is 39 minutes
System returned to ROM by power-on
System image file is "flash:c2960-lanbasek9-mz.150-2.SE4.bin"


This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

cisco WS-C2960-24TT-L (PowerPC405) processor (revision B0) with 65536K bytes of memory.
Processor board ID FOC1010X104
Last reset from power-on
1 Virtual Ethernet interface
24 FastEthernet interfaces
2 Gigabit Ethernet interfaces
The password-recovery mechanism is enabled.

64K bytes of flash-simulated non-volatile configuration memory.
Base ethernet MAC Address       : 00:30:A3:48:C4:94
Motherboard assembly number     : 73-10390-03
Power supply part number        : 341-0097-02
Motherboard serial number       : FOC10093R12
Power supply serial number      : AZS1007032H
Model revision number           : B0
Motherboard revision number     : B0
Model number                    : WS-C2960-24TT-L
System serial number            : FOC1010X104
Top Assembly Part Number        : 800-27221-02
Top Assembly Revision Number    : A0
Version ID                      : V02
CLEI Code Number                : COM3L00BRA
Hardware Board Revision Number  : 0x01


Switch   Ports      Model              SW Version            SW Image

*  1      26    WS-C2960-24TT-L       15.0(2)SE4            C2960-LANBASEK9-M


Configuration register is 0xF
```
</details>

Здесь находим следующую информацию - Cisco IOS 15.0.(2)SE4, название образа прошивки свитча - C2960-LANBASEK9-M, МAC адрес коммутатора - 00:30:A3:48:C4:94

h) Изучим выходную информацию и состояние интерфейса Fa0/6. Для получения данной информации нужн ввести соответствующую команду:

___Switch# show inteface Fa0/6___ 

<details>
  <summary> Статус порта fa0/6  после подключения Ethernet кабеля   </summary>
  
```
Switch#sh int fa0/6
FastEthernet0/6 is up, line protocol is up (connected)
  Hardware is Lance, address is 0001.648c.3906 (bia 0001.648c.3906)
 BW 100000 Kbit, DLY 1000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full-duplex, 100Mb/s
  input flow-control is off, output flow-control is off
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:08, output 00:00:05, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue :0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     956 packets input, 193351 bytes, 0 no buffer
     Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     0 watchdog, 0 multicast, 0 pause input
     0 input packets with dribble condition detected
     2357 packets output, 263570 bytes, 0 underruns
     0 output errors, 0 collisions, 10 interface resets
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
     
```
</details>     

   
* первой строке видим что интерфейс активен (FastEthernet0/6 is up)
* для того что бы интерфейс стал активным достаточно подключить его к любому активному сетевому оборудованию, соответствующим кабелем
* МАС адрес данного интерфейса -  0001.648c.3906 
* Cкорость на данном интерфейсе - Full-duplex, 100Mb/s

i)  Изучим параметры сети VLAN по умолчанию на коммутаторе для чего введем команду:

___Switch# show vlan___

<details>
  <summary> Информация о состояние настроек vlan   </summary>
  
  ```
  Switch#sh vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------

```
</details>

Результирующая информация:

* Какое имя присвоено сети VLAN 1 по умолчанию? -  _default_
* Какие порты расположены в сети VLAN 1?  -  _Fa0/1-24, Gig0/1, Gig0/2_
* Активна ли сеть VLAN 1? - да, Status -> _active_
* К какому типу сетей VLAN принадлежит VLAN по умолчанию? - Type -> _enet (Ethernet)_

j) Изучим flash-память. Введем в CLI соответствующую команду:

___Switch# show flash___


Switch#sh flash
Directory of flash:/

    1  -rw-     4670455          <no date>  2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)

аналогичного результата можно добиться, если ввести другую команду:

___Switch# dir flash:___

Switch#dir flash:
Directory of flash:/

    1  -rw-     4670455          <no date>  2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)

Видим, что прошивка свитча хранится именно в виде файла. Так в конце результирующей строки в обоих случаях выводится название файла текущей прошивки:
 
 * _2960-lanbasek9-mz.150-2.SE4.bin_
 
 расширение (тип файла) _*.bin_ указывает на бинарный (двоичный характер) содержимого файла.
 
 ## Часть 2. Настройка с помощью консоли основных параметров устройств, используя заданные параметры. 

###  Решение:

#### Шаг 1. Настроим базовые параметры коммутатора.

a.	В режиме глобальной конфигурации скопируем следующие базовые параметры конфигурации и вставим их в файл на коммутаторе S1.
```
 no ip domain-lookup
 hostname S1
 service password-encryption
 enable secret class
 banner motd #
 Unauthorized access is strictly prohibited. #
```
b.	Назначим IP-адрес интерфейсу SVI на коммутаторе. Благодаря этому мы получим возможность удаленного управления коммутатором.Прежде чем мы сможем управлять коммутатором S1 удаленно с компьютера PC-A, коммутатору нужно назначить IP-адрес. Согласно конфигурации по умолчанию коммутатором можно управлять через VLAN 1. Однако в базовой конфигурации коммутатора не рекомендуется назначать VLAN 1 в качестве административной VLAN, поэтому создадим отдельный vlan для управления - vlan99

<details>
<summary> конфигурирование SVI свитч </summary>

```
S1# configure terminal
S1(config)# vlan 99
S1(config-vlan)# exit
S1(config)# interface vlan99
S1(config-if)# ip address 192.168.1.2 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# exit
S1(config)#

```
</details>



c.	Ограничим доступ через порт консоли с помощью пароля. Используем cisco в качестве пароля для входа в консоль в этом задании. Конфигурация по умолчанию разрешает все консольные подключения без пароля. Чтобы консольные сообщения не прерывали выполнение команд, используем параметр logging synchronous.

<details>
<summary> Установка пароля на консольный порт </summary>

```
S1(config)# line con 0
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# logging synchronous
S1(config-line)# exit
S1(config)#
```
</details>

d. Настроим каналы виртуального соединения для удаленного управления (vty), чтобы коммутатор разрешил доступ через Telnet. Если не настроить пароль VTY, будет невозможно подключиться к коммутатору по протоколу Telnet.

<details>
<summary> Настройка каналов для удаленного управления </summary>

```
S1(config)# line vty 0 15
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# end
S1#

```
</details>

Вопрос:
Для чего нужна команда login?

Введите ваш ответ здесь:

- Команда login настраивает коммутатор для аутентификации при входе в систему. Если включена процедура входа и настроен пароль, пользователь консоли должен будет ввести пароль, чтобы получить доступ к интерфейсу командной строки (CLI)

#### Шаг 2. Настройка IP-адреса на компьютере PC-A.

Назначим компьютеру IP-адрес и маску подсети в соответствии с таблицей адресации.

1)	Перейдем в Панель управления. (Control Panel)
2)	В представлении «Категория» выберем « Просмотр состояния сети и задач».
3)	Щелкнем Изменение параметров адаптера на левой панели.
4)	Щелкнем правой кнопкой мыши интерфейс Ethernet и выберите «Свойства» .
5)	Выберем Протокол Интернета версии 4 (TCP/IPv4) > Свойства.
6)	Выберем Использовать следующий IP-адрес и введем IP-адрес и маску подсети   и нажмите ОК.

![alt-текст](/Lab-1/pc-a-nic-ip.png "Настройка IP адресов NIC адаптера на PC-A"

## Часть 3. Проверка сетевых подключений

Проверим и задокументируем конфигурацию коммутатора, протестируем сквозное соединение между компьютером PC-A и коммутатором S1, а также протестируем возможность удаленного управления коммутатором.

Выведем текущие настройки коммутатора командой:

___S1# show runnig-config___


<details>
<summary> Текущие настройки свитча </summary>

```
Building configuration...

Current configuration : 2206 bytes
!
version 15.2
no service pad
service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption
!
hostname S1
!
boot-start-marker
boot-end-marker
!
enable secret 5 $1$mtvC$6NC.1VKr3p6bj7YGE.jNg0
!
no aaa new-model
system mtu routing 1500 
!
!
no ip domain-lookup
!
<output omitted>
!
interface FastEthernet0/24
 switchport access vlan 99
!
interface GigabitEthernet0/1
 switchport access vlan 99
!
interface GigabitEthernet0/2
 switchport access vlan 99
!
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
!
ip default-gateway 192.168.1.1
ip http server
ip http secure-server
!
banner motd ^C
Unauthorized access is strictly prohibited. ^C
!
line con 0
 password 7 00071A150754
 logging synchronous
 login
line vty 0 4
 password 7 121A0C041104
 login
line vty 5 15
 password 7 121A0C041104
 login
!
end


```
</details>