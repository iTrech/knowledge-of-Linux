# knowledge-of-Linux
---
НАСТРОЙКА ДИНАМИЧЕСКОЙ МАРШРУТИЗАЦИИ ПО ПРОТОКОЛУ OSPF
---
Устанвка пакета frr:

	apt-get install frr

Добавления необходимых параметров в конфигурационный файл:

	nano /etc/frr/daemons

Параметры, которые необходимо добавить

	ospfd=yes
	zebra=yes

После изменения параметров слудует перезапустить службу:

	systemctl restart frr.service

Натсройка протокола через внутренний интерфейс: 

	vtysh
	conf t
	router ospf
	network 192.168.200.0/24 area 0
	network 10.10.10.0/30 area 0
	do wr
	exit
