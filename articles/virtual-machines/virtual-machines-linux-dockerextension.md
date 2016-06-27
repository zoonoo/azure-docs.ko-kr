<properties
   pageTitle="Azure의 Docker VM 확장에 대해 알아보기 | Microsoft Azure"
   description="Docker VM 확장을 사용하여 Azure에 Docker 환경을 빠르고 안전하게 배포하는 방법에 대해 알아보세요."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/04/2016"
   ms.author="iainfou"/>

# Docker VM 확장을 사용하여 환경 배포

Docker는 Linux(및 Windows)에서 컨테이너와 함께 빠르게 사용할 수 있는 인기 있는 컨테이너 관리 및 이미징 플랫폼입니다. Azure에서는 요구 사항에 따라 Docker를 몇 가지 다른 방식으로 유연하게 배포할 수 있습니다.

- 앱의 프로토타입을 신속하게 만들려는 경우 또는 Docker Machine에 대해 이미 알고 사용하고 있는 경우 [Docker Machine Azure 드라이버를 사용](./virtual-machines-linux-docker-machine.md)하여 Azure 내 Docker 호스트를 배포할 수 있습니다.
- 템플릿 기반 배포의 경우, Azure 가상 컴퓨터에 대해 Docker VM 확장이 사용될 수 있습니다. 이 방식은 Azure Resource Manager 템플릿 배포와 통합될 수 있으며, 역할 기반 액세스, 진단, 및 배포 후 구성 같은 관련된 이점을 모두 포함합니다.
- Docker VM 확장은 또한 개발자 모델링된 응용 프로그램을 모든 환경에 가져가고 일관된 배포를 생성하기 위해 선언적 YAML 파일을 사용하는 Docker Compose를 지원합니다.  
- 또한 Swarm에서 제공하는 추가 일정 예약 및 관리 도구를 활용하며 프로덕션에 준비된 확장 가능 배포의 경우 [Azure 컨테이너 서비스에 전체 Docker Swarm 클러스터를 배포](../container-service/container-service-deployment.md)할 수 있습니다.

이 문서는 Resource Manager 템플릿을 사용하여 사용자가 정의하는 사용자 지정, 프로덕션 준비 환경에 Docker VM 확장을 배포하는 방법을 살펴봅니다.

## 템플릿 배포용 Azure Docker VM 확장

Azure Docker VM 확장은 Linux 가상 컴퓨터의 Docker 디먼, Docker 클라이언트, 및 Docker Compose를 설치하고 구성합니다. 확장은 Docker Compose를 사용하여 컨테이너 응용 프로그램을 정의하고 배포하는 데 사용될 수도 있습니다. Resource Manager 템플릿을 사용함으로써 환경을 일관적 방식으로 다시 배포할 수 있습니다. Azure Docker VM 확장은 단순히 Docker Machine을 사용하거나 Docker 호스트를 직접 만드는 경우에 비해 추가적인 제어 기능을 제공하므로 더욱 견고한 개발자 또는 운영 환경에 적합합니다.

Azure Resource Manager를 사용하여 Docker 호스트, 저장소, RBAC(역할 기반 액세스 제어), 진단과 같은 환경의 전체 구조를 정의하는 템플릿을 만들고 배포할 수 있습니다. [Resource Manager와 템플릿에 대해 자세히 읽어보고](../resource-group-overview.md) 몇 가지 이점을 더 잘 이해하십시오. 단순히 Docker Machine을 사용하는 경우에 비해 Resource Manager 템플릿을 사용하는 이점은 추가 Docker 호스트, 저장소, 액세스 제어 등을 정의하고 향우 필요할 때 배포를 재현할 수 있다는 점입니다.

## Docker VM 확장을 사용하여 템플릿 배포:

기존의 빠른 시작 템플릿을 사용하여 Docker VM 확장이 설치된 Ubuntu VM을 배포하는 방법을 보여드리겠습니다. 템플릿은 [Docker를 사용한 간단한 Ubuntu VM 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)에서 볼 수 있습니다.

Azure CLI를 사용하여 템플릿을 배포하면서 새 리소스 그룹(이 경우는 `myDockerResourceGroup`)의 이름과 템플릿 URI를 지정합니다.

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

저장소 계정, DNS 이름, 사용자 이름 등의 이름 지정에 대한 메시지에 답을 입력한 다음 배포가 완료될 때까지 몇 분간 기다립니다. 다음과 비슷한 결과가 나타나야 합니다.

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

## 첫 번째 nginx 컨테이너 배포

배포가 완료되면 배포 중 입력한 DNS 이름을 사용하여 새 Docker 호스트에 SSH를 연결합니다. Docker 도구가 이미 설치되어 있으므로 nginx 컨테이너를 실행해 보겠습니다.

```
docker run -d -p 80:80 nginx
```

다음과 비슷한 결과가 나타나야 합니다.

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

`docker ps`를 사용하여 호스트에서 실행 중인 컨테이너를 검사합니다.

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   nostalgic_murdock
```

웹 브라우저를 열고 배포 중 지정한 DNS 이름을 입력하여 실제 컨테이너를 확인합니다.

![ngnix 컨테이너 실행](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Docker 디먼 TCP 포트 구성, 보안 구성, 및 Docker Compose를 사용한 컨테이너 배포와 같은 Docker VM 확장에 대한 자세한 정보는 [Azure Virtual Machine Extension for Docker GitHub project](https://github.com/Azure/azure-docker-extension/)(Docker GitHub 프로젝트용 Azure 가상 컴퓨터 확장)를 참조하세요.

## Docker VM 확장 JSON 템플릿 참조

이 예제는 빠른 시작 템플릿을 사용했습니다. 사용자의 기존 Resource Manager 템플릿을 사용하면 JSON 정의 파일에 다음을 추가하여 템플릿에 정의된 VM에 Docker VM 확장을 설치할 수 있습니다.

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Resource Manager 템플릿 사용에 대한 단계별 설명은 [Azure Resource Manager 개요](../resource-group-overview.md)를 참조하십시오.

## 다음 단계

다양한 배포 옵션에 대한 자세한 단계를 참조하십시오.

1. [Azure 드라이버로 Docker Machine 사용](./virtual-machines-linux-docker-machine.md)  
2. [Azure 명령줄 인터페이스(Azure CLI)에서 Docker VM 확장 사용](./virtual-machines-linux-classic-cli-use-docker.md)  
3. [Azure 가상 컴퓨터에서 다중 컨테이너 응용 프로그램 정의 및 실행을 위해 Docker 및 Compose 시작](virtual-machines-linux-docker-compose-quickstart.md).
3. [Azure 컨테이너 서비스 클러스터 배포](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0615_2016-->