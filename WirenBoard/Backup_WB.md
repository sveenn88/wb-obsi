- Подключаем флешку к WB.
- Узнаем физический адрес флешки командой: 
> `fdisk -l` 
>> Пример: `/dev/sda1`

- Монтируем флешку к папке линукса командой:
> `mount /dev/sda1 /media/usb`
>  - `/dev/sda1` - путь до флешки
>  - `/media/usb` - куда подключаем содежимое флешки (дирректория должна быть создана)
>  Для создания папки `mkdir /media/usb`

- Создание резервной копии
> `tar -c -f /media/usb/backup.tar /mnt /usr`
>  - `-c` - будет создан новый архив
>  - `-f` - флаг "куда сохранять". Путь куда сохранять архив + имя архива
>  - Далее указываем каталоги для копирования (`/mnt` `/usr`)

- Переходим в папку с содержимым флешки
> `cd /media/usb` - переходим в каталог
> `ls` - смотрим содержимое
	>> Пример ответа `backup.tar`