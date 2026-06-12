# Домашнее задание к занятию «Система мониторинга Zabbix»
# - Ушаков Игорь Юрьевич

### Задание 1

**Установите Zabbix Server с веб-интерфейсом.**

#### Процесс выполнения:

apt update && apt upgrade -y

apt install -y postgresql postgresql-client systemctl enable --now postgresql

wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-1+ubuntu24.04_all.deb dpkg -i zabbix-release_7.0-1+ubuntu24.04_all.deb apt update

apt install -y zabbix-server-pgsql zabbix-frontend-php php-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agent

sudo -u postgres createuser zabbix sudo -u postgres createdb -O zabbix zabbix sudo -u postgres psql -c "ALTER USER zabbix WITH PASSWORD 'zabbix';"

zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix

sed -i 's/# DBPassword=/DBPassword=zabbix/' /etc/zabbix/zabbix_server.conf

sed -i 's/;date.timezone =/date.timezone = Europe/Moscow/' /etc/php/8.3/apache2/php.ini systemctl restart apache2

systemctl enable --now zabbix-server zabbix-agent apache2

![админка](https://github.com/poproshe/zabbix-1/blob/main/img/screen1.png)

### Задание 2

### Хост 1 – Zabbix Server (Ubuntu 24.04)

Агент уже установлен вместе с сервером и я настраиваю его:

sed -i 's/^Server=127.0.0.1/Server=127.0.0.1,10.10.10.2,212.8.227.85/' /etc/zabbix/zabbix_agentd.conf

sed -i 's/^ServerActive=127.0.0.1/ServerActive=127.0.0.1/' /etc/zabbix/zabbix_agentd.conf

systemctl restart zabbix-agent

Пояснение: на сервере агент может работать с localhost как активный сервер, и принимать пассивные проверки от самого себя.

### Хост 2 – Windows (вторая ВМ)

1. Скачал установщик агента с официального сайта и сохранил в C:\Users\igore\Downloads\zabbix_agent-7.0.27-windows-amd64.msi.

2. Установил агент из командной строки:

msiexec /i "C:\\Users\\igore\\Downloads\\zabbix_agent-7.0.27-windows-amd64.msi" /qn ^
  SERVER=212.8.227.85 ^
  SERVERACTIVE=212.8.227.85 ^
  HOSTNAME=Windows-PC ^
  LOGFILE="C:\\Program Files\\Zabbix Agent\\zabbix_agentd.log" ^
  INSTALLDIR="C:\\Program Files\\Zabbix Agent"

3. ![Агент сервера и агент ноута на windows](https://github.com/poproshe/zabbix-1/blob/main/img/screen2.png)

4. ![Latest data](https://github.com/poproshe/zabbix-1/blob/main/img/screen3.png)

5. ![Configuration > Hosts](https://github.com/poproshe/zabbix-1/blob/main/img/screen4.png)

6. ![log zabbix agent ноута win11](https://github.com/poproshe/zabbix-1/blob/main/img/screen5.png)

### Задание 3

![log zabbix agent ноута win11](https://github.com/poproshe/zabbix-1/blob/main/img/screen6.png)
