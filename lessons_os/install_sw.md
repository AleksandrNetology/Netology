### Установка пакета:\
sudo apt-get install <название пакета>

### Переустановка пакета:\
sudo apt-get install --reinstall <название пакета>

### Удаление пакета:\
sudo apt-get purge --auto-remove <название пакета>\
или\
apt-get remove <название пакета>

### Обновление ОС:
sudo apt-get update\
sudo apt-get install -y linux-headers-5.4.0-99-generic  # Сначала ставим это. Иначе следующая команда вылетает с просьбой установить этот пакет\
sudo apt-get upgrade\


sudo apt-get install manpages-dev  # Ставим полную версию man с разделом 2 system calls

### Установка сетевых пакетов:
sudo apt-get install whois
sudo apt-get install traceroute
sudo apt-get install mtr
sudo apt-get install dnsutils
