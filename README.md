# knowledge-of-Linux
---
Настройка DHCP-сервера
---
Установка пакета

	apt-get install isc-dhcp-server

Добавляем интефейсы в конфигурационный файл

	nano /etc/default/isc-dhcp-server
		INTERFACESv4="ens18 ens19"

Создаём pool dhcp

	nano /etc/dhcp/dhcpd.conf

		subnet 172.16.100.0 netmask 255.255.255.0 {
			range 172.16.100.50 172.16.100.75;
			option routers 172.16.100.1;
			option	domain-name-servers 172.16.20.10;
			
			#Для постоянного адреса
			
			host l-cli-b {
				hardware ethernet "mac-address";
				fixed-address 172.16.200.61
				option host-name "l-cli-b"
			}
		}
		# Если есть пересылка в другую сеть, пишем сеть выходящую из роутера на котором dhcp-server
		subnet 172.16.50.0 netmask 255.255.255.252 {
		}

Для перенаправления dhcp в другую сеть

Устанавливаем пакет на соседний роутер

	apt-get install isc-dhcp-relay

Добавляем интефейсы в конфигурационный файл

	nano /etc/default/isc-dhcp-relay
		INTERFACESv4="ens18 ens19"
---
