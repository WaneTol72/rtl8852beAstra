# rtl8852beAstra
## Для работы rtl8852be на Astra с ядром линукс 5.15 и ниже:
1. Разблокируем репозитории: 
```sudo nano /etc/apt/sources.list```
Убираем ```#``` в начале у четырех последних адресов, которые начинаются с ```deb```.
Добавляем ```#``` там, где в начале ```cdrom```.
2. Обновляем списки репозиториев: 
```sudo apt update```
```sudo apt upgrade```
3. Докачиваем необходимые инструменты для компиляции драйвера с github: 
```sudo apt install build-essential linux-headers-$(uname -r) bc git```
4. Качаем исходники с github: 
```sudo git clone https://github.com/HRex39/rtl8852be.git```
5. Переходим в папку со скачанными исходниками:
```cd rtl8852be```
6. Запускаем компиляцию и установку: 
```sudo make -j8```
```sudo make install```
```sudo modprobe 8852be```

### Теперь у нас работает Wi-Fi, но не работает Bluetooth!

Для работы BT:
1. Качаем ещё одни исходники с github: ```sudo git clone https://github.com/HRex39/rtl8852be_bt.git```
2. Переходим в папку rtl8852be_bt: ```cd rtl8852be_bt```
3. Смотрим какой у нас адаптер через ```lsusb``` 
В моём примере был такой вывод:
```Bus 001 Device 003: ID 0bda:b85c Realtek Semiconductor Corp.```
Запоминаем эти данные: ```ID 0bda:b85c```
4. Модифицируем файл btusb.c: ```sudo nano btusb.c```
Ищем строчки (```Ctrl+w```), где указаны ```(0x0bda, 0x4853)``` и меняем их на то, что выдавала команда ```lsusb```  (в моём случае ```ID 0bda:b85c```), поэтому я поменял эти значения на ```(0x0bda, 0xb85c)```, сохраняем файл ```Ctrl+o```:
>/* Realtek 8852BE Bluetooth devices */
{ USB_DEVICE(0x0bda, 0x4853), .driver_info = BTUSB_REALTEK |
               BTUSB_WIDEBAND_SPEECH }
5. Собираем драйвер ```sudo make```, ```sudo make install```
6. Устанавливаем приложение для блютуза, если не установлено: ```sudo apt install blueman```
7. Перезагружаемся удобным способом.
8. Проверяем.
