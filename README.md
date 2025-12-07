# LCD Monitor for Raspberry Pi

Проект для отображения системной информации на LCD дисплее 20x4 через I2C интерфейс на Raspberry Pi.

## 📋 Описание

Этот проект предоставляет систему мониторинга для Raspberry Pi, которая отображает на LCD дисплее:
- Загрузку процессора (%)
- Температуру процессора (°C)
- Использование оперативной памяти (GB)

Дополнительно реализованы сервисы для отображения сообщений при перезагрузке и выключении системы.

## 🛠 Требования

### Оборудование
- Raspberry Pi (протестировано на Raspberry Pi 5)
- LCD дисплей 20x4 с I2C интерфейсом
- Соединительные провода

### Подключение LCD дисплея
| LCD модуль | Raspberry Pi 5 |
|------------|----------------|
| VCC        | 5V             |
| GND        | GND            |
| SDA        | GPIO3 (SDA)    |
| SCL        | GPIO5 (SCL)    |

### Программное обеспечение
- Python 3
- Библиотеки: `smbus`, `psutil`

## 📥 Установка

Выполните команду для автоматической установки:

```bash
mkdir -p /opt/lcdmonitor/
cd /opt/lcdmonitor/
curl -sL "https://raw.githubusercontent.com/LevGamer39/LCD-Monitor/refs/heads/main/shutdown_lcd.py" -o shutdown_lcd.py
curl -sL "https://raw.githubusercontent.com/LevGamer39/LCD-Monitor/refs/heads/main/lcd_monitor.py" -o lcd_monitor.py
curl -sL "https://raw.githubusercontent.com/LevGamer39/LCD-Monitor/refs/heads/main/requirements.txt" -o requirements.txt
pip install -r requirements.txt --break-system-packages
cd /etc/systemd/system/
curl -sL "https://raw.githubusercontent.com/LevGamer39/LCD-Monitor/refs/heads/main/lcd-shutdown.service" -o lcd-shutdown.service
curl -sL "https://raw.githubusercontent.com/LevGamer39/LCD-Monitor/refs/heads/main/lcd-reboot.service" -o lcd-reboot.service
curl -sL "https://raw.githubusercontent.com/LevGamer39/LCD-Monitor/refs/heads/main/lcdmonitor.service" -o lcdmonitor.service

systemctl daemon-reload
```

Включите I2C интерфейс в Raspberry Pi:
```bash
sudo raspi-config
# Выберите: Interface Options → I2C → Yes
```

## 🚀 Использование

### Включение автозагрузки сервисов
```bash
systemctl enable lcd-shutdown.service
systemctl enable lcd-reboot.service
systemctl enable lcdmonitor.service
```

### Запуск сервисов
```bash
systemctl start lcdmonitor.service
systemctl start lcd-shutdown.service
systemctl start lcd-reboot.service
```

### Перезапуск сервисов
```bash
systemctl restart lcdmonitor.service
systemctl restart lcd-shutdown.service
systemctl restart lcd-reboot.service
```

### Остановка сервисов
```bash
systemctl stop lcdmonitor.service
systemctl stop lcd-shutdown.service
systemctl stop lcd-reboot.service
```

## 🔧 Функциональность

### Основной мониторинг (lcdmonitor.service)
- Отображает загрузку CPU, температуру и использование памяти
- Автоматическое управление подсветкой:
  - Подсветка включена: с 10:00 до 22:00
  - Подсветка выключена: с 22:00 до 10:00
- Обновление данных каждые 2 секунды

### Сервис выключения (lcd-shutdown.service)
- Отображает сообщение "Shutting down..." при выключении
- Показывает "Offline" перед полным выключением
- Отключает подсветку LCD

### Сервис перезагрузки (lcd-reboot.service)
- Отображает сообщение "Rebooting..." при перезагрузке

## 📁 Структура файлов

```
/opt/lcdmonitor/
├── lcd_monitor.py          # Основной скрипт мониторинга
├── shutdown_lcd.py         # Скрипт для выключения/перезагрузки
├── requirements.txt        # Зависимости Python

/etc/systemd/system/
├── lcdmonitor.service      # Сервис основного мониторинга
├── lcd-shutdown.service    # Сервис сообщения выключения
└── lcd-reboot.service      # Сервис сообщения перезагрузки
```

## ⚙️ Настройка

При необходимости можно изменить настройки в файлах:

- **Адрес I2C**: Измените `ADDRESS = 0x27` в `lcd_monitor.py` при необходимости
- **Время работы подсветки**: Измените условия в функции `main()` в `lcd_monitor.py`
- **Интервал обновления**: Измените `sleep(2)` в основном цикле

## 🔍 Диагностика

Для проверки работы:

1. Проверьте статус сервисов:
```bash
systemctl status lcdmonitor.service
```

2. Проверьте подключение I2C устройства:
```bash
i2cdetect -y 1
```

3. Просмотр логов:
```bash
journalctl -u lcdmonitor.service -f
```

## 📝 Примечания

- Убедитесь, что I2C интерфейс включен в настройках Raspberry Pi
- Адрес I2C по умолчанию: 0x27 (может отличаться в зависимости от производителя LCD)
- Проект автоматически запускается при загрузке системы после включения сервисов
- Установка использует `--break-system-packages` для установки пакетов в системный Python
