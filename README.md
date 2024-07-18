Лабораторная работа №2 по курсу Linux Pro.

Задание:
	Обновить ядро в ОС Linux

Решение:

1) С помощью Vagrant развернул виртуальную машина с ОС Ubuntu Jammy. Подключился к ней с помощью команды vagrant ssh.
Выполнил uname -r, ядро было 5.15.0-113-generic.
Выполнил sudo apt install linux-image-6.5.0-41-generic
Перезагрузил ВМ, еще раз выполнил uname -r. Ядро стало 6.5.0-41-generic

С помощью Vagrant развернул виртуальную машина с ОС CentOS 8. Подключился к ней с помощью команды vagrant ssh.
Выполнил uname -r, ядро было 4.18.0-240.1.1.el8_3.x86_64.
Выполнил sudo yum --enablerepo elrepo-kernel install kernel-ml -y
Затем sudo grub2-mkconfig -o /boot/grub2/grub.cfg
Перезагрузил ВМ, еще раз выполнил uname -r. Ядро стало 6.9.9-1.el8.elrepo.x86_64

2) Описание процесса компилирования и установки ядра:
- проверил версию ядра (см скрин "До установки ядра")
- скачал исходники ядра с сайта kernel.org wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.1.99.tar.xz
- распаковал архив tar xvf linux-*
- обновил данные из репозиториев и установил пакеты sudo apt update && sudo apt-get install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison
- скопировал текущий конфиг к исходникам ядра cd linux-6.1.99 && sudo cp -v /boot/config-$(uname -r) .config
- посмотрел конфигурацию (без внесения изменений в конфиг)  make menuconfig
- запустил процесс сборки make
На этом шаге поймал ошибку связанную с указанием сертификатов в конфиге. Для решения этой проблемы очистил значение параметра  CONFIG_SYSTEM_TRUSTED_KEYS="" и CONFIG_SYSTEM_REVOCATION_KEYS=""
После этого перезапустил процесс компиляции, поймал ошибку. Ошибка ссылалась на то что процесс компиляции уперся в размер оперативной памяти виртуальной машины. Увеличил память ВМ до 8Гб. Перезапустил процесс компиляции.
После всех действий процесс компиляции завершился успешно.
- запустил сборку модулей sudo make modules_install
- запустил сборку ядра sudo make install
- обновил initramfs sudo update-initramfs -c -k 6.1.99
- обновил загрузчик sudo update-grub
- перезагрузил ВМ
- проверил версию ядра (см скрин "После установки ядра")

Вывод:
Ядро обновлено двумя способами: из репозитория и собрано из исходных кодов 
