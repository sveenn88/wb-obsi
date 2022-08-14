## Step 1
Первым шагом является установка OpenVPN и Easy-RSA. Easy-RSA - это инструмент управления инфраструктурой открытых ключей (PKI), который будут использоваться на сервере OpenVPN для генерации запроса на сертификат, который затем будет проверен и подписан на сервере CA.
Оба пакета доступны в репозиториях Ubuntu по умолчанию, поэтому можно использовать `apt` для установки:
> `sudo apt update`
> `sudo apt install openvpn easy-rsa`

## Step 2
- Далее вам нужно будет создать новый каталог на сервере OpenVPN, который вызывается вашим пользователем, не являющимся пользователем root `~/easy-rsa`:
> `mkdir ~/easy-rsa`
-  Копируем содержимое в созданный каталог:
> `cp /usr/share/easy-rsa/* ~/easy-rsa/`
- Устанавливаем права для данного каталога:
> `sudo chown sammy ~/easy-rsa` (мы указывали root)
> `chmod 700 ~/easy-rsa` (установили права)
- В директории инициализируем каталог PKI
> `cd ~/easy-rsa`
> `./easyrsa init-pki`
- Создать и отредактировать файл переменных `vars`
> `cp vars.example vars`
> `rm vars.example` (удаление файла примера)
> `sudo nano vars`
- Раскомментировать и исправить данные:
>`set_var EASYRSA_REQ_COUNTRY     "RU"`
>`set_var EASYRSA_REQ_PROVINCE    "Chelyaba"`
>`set_var EASYRSA_REQ_CITY        "Chebara"`
>`set_var EASYRSA_REQ_ORG         "SEA"`
>`set_var EASYRSA_REQ_EMAIL       "sveenn88@gmail.com"`
>`set_var EASYRSA_REQ_OU          "SEA Bros."`
- Установить алгоритм шифрования:
>`set_var EASYRSA_ALGO "ec"`
>`set_var EASYRSA_DIGEST "sha512"`
- Создать центр сертификации:
> `./easyrsa build-ca`
> `указать пароль` в ходе создания

## Step 3
Генерация приватного ключа на сервере OpenVPN:
> (Установка и развёртывание центра сертификации, если есть разделение серверов VPN и сертификатов)
>`cd ~/easy-rsa`
>`./easyrsa gen-req server nopass`

```
Output

Common Name (eg: your user, host, or server name) [server]:
 
Keypair and certificate request completed. Your files are:
req: /home/sammy/easy-rsa/pki/reqs/server.req
key: /home/sammy/easy-rsa/pki/private/server.key
```

Создан закрытый ключ для сервера и файл запроса сертификата с именем `server.req`. Скопируйте ключ сервера в `/etc/openvpn/server` каталог:

>`sudo cp ~/easy-rsa/pki/private/server.key /etc/openvpn/server/`

## Step 4
Подпись сертификата:
>`cd ~/easy-rsa`
> `./easyrsa import-req /tmp/server.req server` (импорт ключа с другого сервера)
> `./easyrsa sign-req server server` (подпись сертификата)
> В выходных данных вам будет предложено убедиться, что запрос поступает из надежного источника. Введите `yes`, затем нажмите `ENTER` для подтверждения. При необходимости указать пароль.

Теперь, вернувшись на свой сервер OpenVPN, скопировать файлы в `/etc/openvpn/server`:
>`~/easy-rsa/pki/issude/server.crt`
>`~/easy-rsa/pki/ca.crt`

## Step 5
Настройка конфигурации OpenVPN. 
>`sudo cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz /etc/openvpn/server/`
>`sudo gunzip /etc/openvpn/server/server.conf.gz`
>`sudo nano /etc/openvpn/server/server.conf`

## Step 6
Настройка сетевой конфигурации сервера OpenVPN.
>`sudo nano /etc/sysctl.conf`
> Устанавливаем настройку
>`net.ipv4.ip_forward = 1`

Настройка файрволла
> `sudo nano /etc/default/ufw`
> Устанавливаем настройку
> `DEFAULT_FORWARD_POLICY="ACCEPT"`

Настройка брандмауэра, чтобы разрешить трафик для OpenVPN:
>`sudo ufw allow 1194/tcp`
> `sudo ufw allow OpenSSH`

## Step 7
Добавление OpenVPN в автозапуск
>`sudo systemctl -f enable openvpn-server@server.service`

Запуск службы:
>`sudo systemctl start openvpn-server@server.service`

## Step 8
 Создание клиентского сертификата и пар ключей:
 >`mkdir -p ~/client-configs/keys`
 >`chmod -R 700 ~/client-configs`

Затем вернитесь в каталог EasyRSA и запустите `easyrsa` скрипт с параметрами `gen-req` и `nopass`, а также общим именем для клиента:
>`cd ~/easy-rsa` (переход в папку)
>`./easyrsa gen-req client1 nopass` (имя клиента client1 или иное)
>`cp pki/private/client1.key ~/client-configs/keys/` (копируем ключи в папку client-configs)

Подписываем сертификат ключа:
> `./easyrsa sign-req client client1`

Вернувшись на свой сервер OpenVPN, скопируйте сертификат клиента в `~/client-configs/keys/` каталог:
>`cp /root/easy-rsa/pki/issued/client1.crt ~/client-configs/keys/`
>`cp /etc/openvpn/server/ca.crt ~/client-configs/keys/`

## Step 9
Создание инфраструктуры конфигурации клиента
>`mkdir -p ~/client-configs/files`

Затем скопируйте пример файла конфигурации клиента в `client-configs` каталог для использования в качестве базовой конфигурации:
>`cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf ~/client-configs/base.conf`
>`nano ~/client-configs/base.conf`
```
remote "your_server_ip" 1194

proto tcp

```
Далее мы создадим скрипт, который скомпилирует вашу базовую конфигурацию с соответствующими файлами сертификатов, ключей и шифрования, а затем поместит сгенерированную конфигурацию в `~/client-configs/files` каталог. Откройте новый файл, вызываемый `make_config.sh` в `~/client-configs` каталоге:
>`nano ~/client-configs/make_config.sh`

Внутри добавьте следующее содержимое:
``` bush
#!/bin/bash 
#First argument: Client identifier 
KEY_DIR=~/client-configs/keys
OUTPUT_DIR=~/client-configs/files
BASE_CONFIG=~/client-configs/base.conf 
cat ${BASE_CONFIG} \
    <(echo -e '<ca>') \
    ${KEY_DIR}/ca.crt \
    <(echo -e '</ca>\n<cert>') \
    ${KEY_DIR}/${1}.crt \
    <(echo -e '</cert>\n<key>') \
    ${KEY_DIR}/${1}.key \
    <(echo -e '</key>\n<tls-crypt>') \  
    > ${OUTPUT_DIR}/${1}.ovpn	
```
Установите права:		
>`chmod 700 ~/client-configs/make_config.sh`
