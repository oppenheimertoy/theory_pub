# ЛР4 - Access-List

> Данная лаба построена вокруг использования Access-List

## Оглавление

## Условие

Дана следующая тополология:

![](./topology.jpg)

В ней соотв нужно настроить\

## Теория

## Настройка 

Первым делом соберём топологию.

![](./topology.jpg)

Далее, согласно рисунку натсроим IP на конечных устройствах.

### Настройка VLAN

#### Настройка коммутатора

Теперь настроим VLAN на коммутаторах. Пример настройки VLAN на левом коммутаторе, не забываем сообщить о VLAN с правого коммутатора.

```
switch(config)#int fa0/1
switch(config-if)#switchport mode access
switch(config-if)#switchport access vlan 2
switch(config)#int fa0/2
switch(config-if)#switchport mode access
switch(config-if)#switchport access vlan 3
switch(config)#int fa0/3
switch(config-if)#switchport mode trunk
switch(config-if)#ex
switch(config)#vlan 4
switch(config)#vlan 5
switch(config)#vlan 6
```

#### Настройка роутера

Теперь настроим роутеры. Пример настройки VLAN левого роутера.

```
router1(config)#int fa0/0 
router1(config-if)#no sh
router1(config-if)#int fa0/0.2 

router1(config-subif)#encapsulation dot1q 2
router1(config-subif)#ip address 192.168.2.1 255.255.255.0
router1(config-subif)#int fa0/0.3

router1(config-subif)#encapsulation dot1q 3
router1(config-subif)#ip address 192.168.3.1 255.255.255.0
router1(config-subif)#int fa1/0

router1(config-if)#ip address 192.168.12.1 255.255.255.0
router1(config-if)#no sh

// Настройка связи с другими роутерами. Напишем вначале для правых VLAN

router1(config-if)#int fa0/1
router1(config-if)#ip address 10.10.10.1 255.255.255.252
router1(config-if)#no sh
router1(config-if)#int fa0/1.4

router1(config-subif)#encapsulation dot1q 4
router1(config-subif)#ip address 10.10.14.1 255.255.255.252
router1(config-subif)#int fa0/1.5

router1(config-subif)#encapsulation dot1q 5
router1(config-subif)#ip address 10.10.15.1 255.255.255.252
router1(config-subif)#int fa0/1.6

router1(config-subif)#encapsulation dot1q 6
router1(config-subif)#ip address 10.10.16.1 255.255.255.252

// Настройка связи с другими роутерами. Напишем левые VLAN. Это нужна для приёма своих VLAN.

router1(config-subif)#int fa0/1.2
router1(config-subif)#encapsulation dot1q 2
router1(config-subif)#ip address 10.10.12.1 255.255.255.252
router1(config-subif)#int fa0/1.3

router1(config-subif)#encapsulation dot1q 3
router1(config-subif)#ip address 10.10.13.1 255.255.255.252
```

Пример настройки VLAN роутера посередине.

```
router2(config)#int fa1/0
router2(config-if)#ip address 192.168.1.1 255 255.255.255.0
router2(config-if)#no sh

// Настройка связи с левым роутером

router2(config-if)#int fa0/0
router2(config-if)#ip address 10.10.10.2 255 255.255.255.252
router2(config-if)#no sh
router2(config-if)#int fa0/0.2

router2(config-subif)#encapsulation dot1q 2
router2(config-subif)#ip address 10.10.12.2 255 255.255.255.252
router2(config-subif)#int fa0/0.3

router2(config-subif)#encapsulation dot1q 3
router2(config-subif)#ip address 10.10.13.2 255 255.255.255.252
router2(config-if)#int fa0/0.4

router2(config-subif)#encapsulation dot1q 4
router2(config-subif)#ip address 10.10.14.2 255.255.255.252
router2(config-subif)#int fa0/0.5

router2(config-subif)#encapsulation dot1q 5
router2(config-subif)#ip address 10.10.15.2 255.255.255.252
router2(config-subif)#int fa0/0.6

router2(config-subif)#encapsulation dot1q 6
router2(config-subif)#ip address 10.10.16.2 255.255.255.252

//Настройка связи с правым роутером

router2(config-if)#int fa0/1
router2(config-if)#ip address 10.11.10.1 255 255.255.255.252
router2(config-if)#no sh
router2(config-if)#int fa0/1.2

router2(config-subif)#encapsulation dot1q 2
router2(config-subif)#ip address 10.11.12.1 255 255.255.255.252
router2(config-subif)#int fa0/1.3

router2(config-subif)#encapsulation dot1q 3
router2(config-subif)#ip address 10.11.13.1 255 255.255.255.252
router2(config-if)#int fa0/1.4

router2(config-subif)#encapsulation dot1q 4
router2(config-subif)#ip address 10.11.14.1 255.255.255.252
router2(config-subif)#int fa0/1.5

router2(config-subif)#encapsulation dot1q 5
router2(config-subif)#ip address 10.11.15.1 255.255.255.252
router2(config-subif)#int fa0/1.6

router2(config-subif)#encapsulation dot1q 6
router2(config-subif)#ip address 10.11.16.1 255.255.255.252
```

Аналогично левому настраивается правый роутер.

### Настройка OSPF

Начнём настройку с роутера посередине

```
router2(config)#interface loopback 0
router2(config-if)#ip address 192.168.100.1 255.255.255.255
router2(config-if)#no sh

router2(config-if)#router ospf 1
router2(config-if)#network 10.10.10.0 0.0.0.3 area 0
router2(config-if)#network 10.10.12.0 0.0.0.3 area 0
router2(config-if)#network 10.10.13.0 0.0.0.3 area 0
router2(config-if)#network 10.10.14.0 0.0.0.3 area 0
router2(config-if)#network 10.10.15.0 0.0.0.3 area 0
router2(config-if)#network 10.10.16.0 0.0.0.3 area 0
router2(config-if)#network 10.11.10.0 0.0.0.3 area 0
router2(config-if)#network 10.11.12.0 0.0.0.3 area 0
router2(config-if)#network 10.11.13.0 0.0.0.3 area 0
router2(config-if)#network 10.11.14.0 0.0.0.3 area 0
router2(config-if)#network 10.11.15.0 0.0.0.3 area 0
router2(config-if)#network 10.11.16.0 0.0.0.3 area 0
router2(config-if)#network 192.168.1.0 0.0.0.255 area 0
```

Настройка с левого роутера

```
router1(config)#interface loopback 0
router1(config-if)#ip address 192.168.100.2 255.255.255.255
router1(config-if)#no sh

router1(config-if)#router ospf 1
router1(config-if)#network 10.10.10.0 0.0.0.3 area 0
router1(config-if)#network 10.10.12.0 0.0.0.3 area 0
router1(config-if)#network 10.10.13.0 0.0.0.3 area 0
router1(config-if)#network 10.10.14.0 0.0.0.3 area 0
router1(config-if)#network 10.10.15.0 0.0.0.3 area 0
router1(config-if)#network 10.10.16.0 0.0.0.3 area 0

router1(config-if)#network 192.168.2.0 0.0.0.255 area 0
router1(config-if)#network 192.168.3.0 0.0.0.255 area 0
router1(config-if)#network 192.168.12.0 0.0.0.255 area 0
```

Настройка с правого роутера

```
router1(config)#interface loopback 0
router1(config-if)#ip address 192.168.100.3 255.255.255.255
router1(config-if)#no sh

router1(config-if)#router ospf 1
router1(config-if)#network 10.11.10.0 0.0.0.3 area 0
router1(config-if)#network 10.11.12.0 0.0.0.3 area 0
router1(config-if)#network 10.11.13.0 0.0.0.3 area 0
router1(config-if)#network 10.11.14.0 0.0.0.3 area 0
router1(config-if)#network 10.11.15.0 0.0.0.3 area 0
router1(config-if)#network 10.11.16.0 0.0.0.3 area 0

router1(config-if)#network 192.168.4.0 0.0.0.255 area 0
router1(config-if)#network 192.168.5.0 0.0.0.255 area 0
router1(config-if)#network 192.168.6.0 0.0.0.255 area 0
```

### Настройка Access-List
