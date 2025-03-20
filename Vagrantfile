# Vagrantfile для автоматизации создания и настройки VirtualBox VM
# Используется для запуска Java-приложения (Spring Boot) с endpoint'ом /dogs

Vagrant.configure("2") do |config|
  # Указываем базовый образ для VM
  # Используем Ubuntu 22.04 (Jammy Jellyfish) в 64-битной версии
  config.vm.box = "Ubuntu/jammy64"

  # Настраиваем приватную сеть для VM
  # Приватная сеть позволяет хост-машине обращаться к VM по фиксированному IP
  # Используем IP-адрес 192.168.100.100, как указано в задании
  config.vm.network "private_network", ip: "192.168.100.100"
  
  # Отключаем создание символических ссылок в синхронизированных папках
  # Это устраняет предупреждение о SharedFoldersEnableSymlinksCreate
  config.vm.synced_folder ".", "/vagrant", SharedFoldersEnableSymlinksCreate: false
  
  # Настраиваем провайдер VirtualBox
  # Здесь задаются ресурсы для VM
  config.vm.provider "virtualbox" do |vb|
    # Выделяем 4096 MB оперативной памяти для VM
    # Это обеспечивает достаточные ресурсы для работы Java-приложения
    vb.memory = "4096"
    
    # Выделяем 2 CPU для VM
    # Это улучшает производительность приложения
    vb.cpus = 2

  end
  
  # Определяем inline shell-скрипт для установки зависимостей
  # Этот скрипт будет выполнен во время настройки VM
  install_deps = <<-SHELL
    #!/bin/bash
    # Обновляем список пакетов APT
    # Это необходимо для установки новых пакетов
    sudo apt-get update
    
    # Устанавливаем OpenJDK 17
    # OpenJDK 17 требуется для запуска Java-приложения (Spring Boot)
    sudo apt-get install -y openjdk-17-jdk
    
    # Устанавливаем зависимости для Guest Additions
    sudo apt-get install -y build-essential dkms linux-headers-$(uname -r)
  SHELL
  
  # Используем shell provisioner для выполнения скрипта install_deps
  # Provisioner выполняется во время первого запуска VM или при vagrant provision
  config.vm.provision "shell", inline: install_deps
  
  # Устанавливаем VirtualBox Guest Additions
  config.vbguest.auto_update = true
  config.vbguest.iso_path = "https://download.virtualbox.org/virtualbox/7.0.20/VBoxGuestAdditions_7.0.24.iso"
  config.vbguest.no_remote = false
  
  # Настраиваем Vagrant trigger, который срабатывает после vagrant up
  # Trigger автоматически запускает приложение после настройки VM
  config.trigger.after :up do |trigger|
    # Задаем имя триггера для логов
    trigger.name = "Run Spring Boot Application"
    
    # Информационное сообщение, отображаемое при срабатывании триггера
    trigger.info = "Starting the Java application with Gradle"
    
    # Определяем команду для выполнения
    # Команда подключается к VM через SSH, переходит в директорию /vagrant
    # и запускает приложение с помощью ./gradlew bootRun
    trigger.run = {
      inline: <<-SHELL
        vagrant ssh -c "cd /vagrant && ./gradlew bootRun"
      SHELL
    }
  end
end
