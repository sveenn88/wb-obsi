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

## Step 3
Генерация приватного ключа:
>`cd ~/easy-rsa`
>`./easyrsa gen-req server nopass`

```
Output

Common Name (eg: your user, host, or server name) [server]:
 
Keypair and certificate request completed. Your files are:
req: /home/sammy/easy-rsa/pki/reqs/server.req
key: /home/sammy/easy-rsa/pki/private/server.key
```

Создан закрытый ключ для сервера и файл запроса сертификата с именем `server.req`. Скопируйте ключ сервера в `/etc/openvpn/server`каталог:

>`sudo cp ~/easy-rsa/pki/private/server.key /etc/openvpn/server/`

## Step 4
Подпись сертификата:
>`cd ~/easy-rsa`
> `./easyrsa import-req /tmp/server.req server`


