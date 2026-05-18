# knowledge-of-Linux
---
Настройка SambaAD
---
Подготовка имени и сетевых настроек
Имя хоста (hostname): server (до 15 символов)
важный шаг — файл /etc/hosts Убедитесь, что полное имя хоста (FQDN) и короткое имя резолвятся в ваш внешний статический IP, а не в 127.0.0.1

Установка пакета

	apt-get install task-samba-dc

Перед конфигурированием Samba DC необходимо остановить сервисы и удалить файлы которые могут конфликтовать с юнитом Samba DC

	systemctl disable --now bind krb5kdc nmb smb slapd
	rm -f /etc/samba/smb.conf
	rm -rf /var/lib/samba
	rm -rf /var/cache/samba
	mkdir -p /var/lib/samba/sysvol

Команда для инифиализации домена

	samba-tool domain provision
	
после ввода команды инициализации домена выводится "диалог" для ввода данных о домене
после завершения диалога необходимо запустить сервис домена и ввести в автозагрузку

	systemctl enable --now samba
	samba-tool domain info 127.0.0.1
---
Управление зонами DNS
--
Создание прямой зоны

	samba-tool dns zonecreate <DNS-сервер> <имя_зоны> -U <пользователь>

Добавление А записей в прямую зону

	samba-tool dns add <DNS-сервер> <зона> <имя_хоста> A <IP-адрес> -U <пользователь>

Создание обратной зоны

	samba-tool dns zonecreate <DNS-сервер> <обратная_зона> -U <пользователь>

Добавление PTR записей в прямую зону
	
	samba-tool dns add <DNS-сервер> <обратная_зона> <последний_октет> PTR <FQDN> -U <пользователь>
