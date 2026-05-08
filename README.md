## Домашнее задание к занятию «Установка Kubernetes»
## `Михеев Алексей`



### Задание 1. Установить кластер k8s с 1 master node

  1. Подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды.

  2. В качестве CRI — containerd.

  3. Запуск etcd производить на мастере.

  4. Способ установки выбрать самостоятельно.

- - - - -

### Решение:

```

# Инициализация CLI Yandex Cloud
yc init
# Команда запускает процесс авторизации и настройки профиля YC CLI (выбор облака, токена, папки, региона)

# Просмотр текущих настроек Yandex Cloud CLI
yc config list
# Проверяем, какие конфигурации используются: токен, папка, облако, зона

# Просмотр доступных зон для развертывания ВМ
yc compute zone list
# Смотрим, где можно создавать виртуальные машины

# Просмотр списка подсетей в VPC
yc vpc subnet list
# Проверяем созданные подсети и их параметры (CIDR, сеть, зона)

# Просмотр списка виртуальных машин
yc compute instance list
# Проверяем текущие ВМ, их статус, внутренние и внешние IP

```

```

#!/bin/bash
set -e
# Скрипт для автоматического создания сети, подсети и виртуальных машин в Yandex Cloud
# set -e — прекращает выполнение при любой ошибке

# Путь к твоему публичному SSH ключу
SSH_KEY="$HOME/.ssh/id_rsa.pub"

# Названия сети и подсети, зона развертывания
NET_NAME="net"
SUBNET_NAME="my-subnet"
ZONE="ru-central1-a"
SUBNET_CIDR="10.1.2.0/24"

# Создание VPC сети
NET_ID=$(yc vpc network create \
  --name $NET_NAME \
  --labels my-label=netology \
  --description "net yc" \
  --format json | jq -r '.id')
echo "Создана сеть $NET_NAME ($NET_ID)"

# Создание подсети
SUBNET_ID=$(yc vpc subnet create \
  --name $SUBNET_NAME \
  --zone $ZONE \
  --range $SUBNET_CIDR \
  --network-name $NET_NAME \
  --description "subnet yc" \
  --format json | jq -r '.id')
echo "Создана подсеть $SUBNET_NAME ($SUBNET_ID)"

# Функция для создания виртуальной машины
create_vm() {
  local NAME=$1
  yc compute instance create \
    --name $NAME \
    --zone $ZONE \
    --platform standard-v2 \
    --cores 2 \
    --memory 2 \
    --network-interface subnet-id=$SUBNET_ID,nat-ip-version=ipv4 \
    --create-boot-disk image-folder-id=standard-images,image-family=ubuntu-2204-lts,size=20 \
    --metadata "ssh-keys=ubuntu:$(cat $SSH_KEY)" \
    --format json
}

# Создание мастер-ноды
echo "Создаём мастер-ноду k8s-master..."
create_vm k8s-master

# Создание 4 рабочих нод
for i in 1 2 3 4; do
  echo "Создаём рабочую ноду node$i..."
  create_vm node$i
done

echo "Все ноды созданы. Внешние IP можно посмотреть командой:"
echo "yc compute instance list"

```
```

# Клонирование репозитория Kubespray для автоматической установки Kubernetes
git clone https://github.com/kubernetes-sigs/kubespray.git

# Переход в директорию Kubespray
cd kubespray

# Создание виртуального окружения Python
python3 -m venv venv

# Активация виртуального окружения
source venv/bin/activate

# Установка всех зависимостей Python для Kubespray
pip install -r requirements.txt

# Редактирование файла inventory для описания своих нод
nano ./inventory/mycluster/hosts.yaml

# Проверка доступности нод по SSH через Ansible
ansible all -i inventory/mycluster/hosts.yaml -m ping -u ubuntu --private-key ~/.ssh/id_rsa

```

```

# Настройка kubectl на мастер-ноду для работы с кластером
ssh ubuntu@93.77.188.92           # подключение к мастер-ноде
sudo mkdir -p /home/ubuntu/.kube   # создаем директорию для конфигурации kubectl
sudo cp /etc/kubernetes/admin.conf /home/ubuntu/.kube/config
sudo chown -R ubuntu:ubuntu /home/ubuntu/.kube
kubectl get nodes                  # проверка, что ноды отображаются
kubectl get pods -A                # проверка всех pod'ов во всех namespace

```



![1](https://github.com/Alm798/Kuber_3.2/blob/main/img/1.png)

![2](https://github.com/Alm798/Kuber_3.2/blob/main/img/2.png)

![3](https://github.com/Alm798/Kuber_3.2/blob/main/img/3.png)

![4](https://github.com/Alm798/Kuber_3.2/blob/main/img/4.png)

![5](https://github.com/Alm798/Kuber_3.2/blob/main/img/5.png)

![6](https://github.com/Alm798/Kuber_3.2/blob/main/img/6.png)

![7](https://github.com/Alm798/Kuber_3.2/blob/main/img/7.png)

![8](https://github.com/Alm798/Kuber_3.2/blob/main/img/8.png)

![9](https://github.com/Alm798/Kuber_3.2/blob/main/img/9.png)

![10](https://github.com/Alm798/Kuber_3.2/blob/main/img/10.png)

![11](https://github.com/Alm798/Kuber_3.2/blob/main/img/11.png)

![12](https://github.com/Alm798/Kuber_3.2/blob/main/img/12.png)

![13](https://github.com/Alm798/Kuber_3.2/blob/main/img/13.png)

![14](https://github.com/Alm798/Kuber_3.2/blob/main/img/14.png)


- - - - -
