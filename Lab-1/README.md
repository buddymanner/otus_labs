# Команды IOS. Базовая конфигурация устройств

###  Задание:
1. Создать указанную ниже топологию с помощью подходящего эмулятора.

#### Топология сети
![alt-текст](/Lab-1/lab-1.png "Топология lab-1")

2. Проверить текущую конфигурацию коммутатора или по умолчанию (заводскую).
3. Настроить с помощью консоли основные параметры устройств, используя заданные параметры.

#### 	Таблица адресации
 |Устройство| Интерфейс| ip-адрес/префикс|
 |----------|----------|-----------------|
 |  S1      | VLAN1    |192.168.1.2  /24 |
 |  PC-A    | NIC      |192.168.1.10 /24 |

4. Проверить работоспособность сетевого подключения, используя эхо-запрос и telnet.  

###  Решение:
1. Воспользуемся для создания заданной топологии соответствующим эмулятором,например, Cisco Packet Tracer v.8.1. На первом этапе подключимся к коммутатору Cisco 2960-24TT с помощью консольного провода, т.к. настраиваемое оборудование может содержать следы предыдующих конфигураций с заданными, но неизвестными настройками, в том числе и IP адресами управления. Для этого компьютер, с помощью которого производится подключение, должен быть оборудован последовательным СОМ портом и/или, в его отсутствие (неисправности), соотвествующим USB адаптером. Для успешного подключения иногда требуется предварительно уточнить некоторые параметры, например, скорость подключения в бодах. Если все будет исправно мы сможем подключиться к коммутатору и получить доступ к CLI для проверки текущих настроек оборудования.

2. Проверка текущих или заводских настроек

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

b) Видно, что в данной модели присутствует всего 26 физических портов: 24 fastethernet порта и 2 gigabitethernet порта. Кроме этого консольный порт и 2 терминальных порта vty 