# sshrdp
Необходимо было дать доступ к веб-приложению работающему за периметром организации. При этом внаружу открыт только ssh на 22 порту.
Веб-приложение состоит из фронтенда и бэкенда, которые общаются между собой по локальному ip-сервера, поэтому сделать просто 
``` 
sudo ssh -f -L 80:localhost:80 user@ip_server
```
не получается.

Решение было поставить на сервер (Ubuntu 18.04) xfce и xrdp и затем тунелирование по ssh порта 3389.

Устанавливаем окружение рабочего стола и браузер:
```
sudo apt-get install xfce4 xfce4-goodies xorg dbus-x11 x11-xserver-utils
sudo apt-get install firefox
```
Устанавливаем сервер Xrdp:
```
sudo apt-get install xrdp
```
Даем возможность читать сертификаты:
```
sudo adduser xrdp ssl-cert
```
Делаем резервную копию файла скрипта, который исполняется при инициализации пользовательской сессии:
```
sudo mv /etc/xrdp/startwm.sh /etc/xrdp/startwm.copy
```
Создаем новый файл скрипта и прописываем свои настройки:
```
sudo touch /etc/xrdp/startwm.sh
sudo chmod 755 /etc/xrdp/startwm.sh
sudo nano /etc/xrdp/startwm.sh
```

```
#!/bin/sh
if [ -r /etc/default/locale ]; then
. /etc/default/locale
export LANG LANGUAGE
fi
exec /usr/bin/startxfce4
```
Перезапускаем xrdp:
```
sudo systemctl restart xrdp
```
Разрешаем доступ на файрволе:
```
sudo ufw allow 3389
```
Теперь на своем компьютере запускаю:
```
sudo ssh -f -L 3389:localhost:3389 user@ip_server
```
Запускаю Remmina, прописываю localhost:3389 и получаю доступ к X-ам сервера, где запускаю браузер и веб-приложение с локальным ip.


P.S. Возможно установить guacamole, чтобы не морочится с локальными клиентами, а просто получить доступ через браузер

P.S. В планах написать Ansible-плейбук, если это будет не еденичный случай
