<properties
	pageTitle="Docker Machine으로 Azure에서 Docker 호스트 만들기 | Microsoft Azure"
	description="Docker Machine을 사용하여 Azure에서 Docker 호스트를 만드는 방법에 대해 설명합니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/22/2016"
	ms.author="rasquill"/>

# Azure 드라이버로 Docker Machine 사용

[Docker](https://www.docker.com/)는 공유 리소스에서 응용 프로그램 데이터와 계산을 격리시키는 한 가지 방법으로, 가상 컴퓨터 대신 Linux 컨테이너를 사용하며 가장 많이 사용되는 가상화 방법 중 하나입니다. 이 항목은 [Docker Machine](https://docs.docker.com/machine/)을 사용하여(`docker-machine` 명령) Linux 컨테이너의 Docker 호스트로 활성화된 Azure에서 새 Linux VM을 만드는 시기와 방법에 대해 설명합니다.


## Docker Machine으로 VM 만들기

드라이버 옵션(`-d`)에 대한 `azure` 드라이버 인수 및 기타 인수를 사용하는 `docker-machine create` 명령으로 Azure에 Docker 호스트 VM을 만듭니다.

다음 예제는 기본 값을 사용하지만 nginx 컨테이너로 테스트하기 위해 인터넷에 대한 VM에서 포트 80을 열고 `ops`를 SSH에 대한 로그온 사용자로 지정하며 새 VM `machine`을 호출합니다.

옵션과 해당 옵션의 기본값을 보려면 `docker-machine create --driver azure`를 입력합니다. [Docker Azure 드라이버 설명서](https://docs.docker.com/machine/drivers/azure/)도 읽을 수 있습니다. (2단계 인증이 활성화된 경우 두 번째 단계를 사용하여 인증하라는 메시지가 표시됩니다.)

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

출력은 계정에 2단계 인증이 구성되어 있는지 여부에 따라 다음과 유사하게 표시됩니다.

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## Docker 셸 구성

이제 `docker-machine env <VM name>`을 입력하여 셸을 구성하는 데 무엇이 필요한지 확인합니다.

```bash
docker-machine env machine
```

그러면 다음과 유사한 환경 정보가 표시됩니다. VM을 테스트하는 데 필요한 IP 주소가 할당되었습니다.

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

제안된 구성 명령을 실행하거나 환경 변수를 직접 설정할 수 있습니다.

## 컨테이너 실행

이제 간단한 웹 서버를 실행하여 모든 기능이 올바르게 작동하는지 테스트할 수 있습니다. 여기서는 표준 nginx 이미지를 사용하고 포트 80에서 수신 대기하도록 지정하며 VM이 다시 시작될 경우 컨테이너도 다시 시작해야 하는지 여부를 지정합니다(`--restart=always`).

```bash
docker run -d -p 80:80 --restart=always nginx
```

다음과 유사하게 출력됩니다.

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## 컨테이너 테스트

`docker ps`를 사용하여 실행 중인 컨테이너 검사

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

그리고 실행 중인 컨테이너를 확인하고 `docker-machine ip <VM name>`을 입력하여 IP 주소를 찾습니다(`env` 명령에서 잊은 경우).

![ngnix 컨테이너 실행](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## 다음 단계

관심이 있을 경우 Azure [Docker VM 확장](virtual-machines-linux-dockerextension.md)에서 Azure CLI 또는 Azure Resource Manager 템플릿을 사용하여 동일한 작업을 시도해 볼 수 있습니다.

Docker를 사용한 더 많은 예는 [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [데모](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)의 [Docker 작업](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker)을 참조하세요. HealthClinic.biz 데모에서 더 빠른 시작은 [Azure 개발자 도구 빠른 시작](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)을 참조하세요.

<!---HONumber=AcomDC_0727_2016-->