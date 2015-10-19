<properties
	pageTitle="Ubuntu-Docker VM 이미지를 사용하여 Docker를 신속하게 사용하는 방법"
	description="Azure 이미지 갤러리에서 직접 Ubuntu Server의 Docker를 몇 분 이내에 사용하는 방법에 대해 설명합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure"
	ms.date="10/04/2015"
	ms.author="rasquill"/>

# Azure Marketplace에서 Docker를 신속하게 시작하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.

[Docker] 사용을 가장 빠르게 시작하려면 Azure Marketplace로 이동해 [MSOpenTech]와 함께 [Canonical]에서 만든 **Ubuntu Server의 Docker** 이미지 템플릿을 사용하여 VM을 만듭니다. 그러면 Ubuntu Server VM이 생성되고 Azure에 미리 설치되어 실행 중인 **최신** Docker 엔진과 함께 [Docker VM 확장](virtual-machines-docker-vm-extension.md)이 자동으로 설치됩니다.

SSH를 사용하여 VM에 즉시 연결할 수 있으며, 아무 작업도 수행하지 않고 Docker 작업을 직접 수행할 수 있습니다.

> [AZURE.NOTE]Azure Marketplace 템플릿을 통해 만든 VM은 원격 Docker 클라이언트에서 관리하는 데 필요한 Docker 원격 API를 호스트하지 않습니다. 이 VM에서 원격으로 Docker Host를 제어하도록 하려면 [HTTPS를 사용하여 Docker 실행](https://docs.docker.com/articles/https/)을 참조하거나 [Azure 포털에서 Docker VM 확장 사용](virtual-machines-docker-with-portal.md) 또는 [Azure CLI에서 Docker VM 확장 사용](virtual-machines-docker-with-xplat-cli-install.md)의 단계를 따르세요. <!-- --> Windows에서 Azure Docker VM을 자동화하려는 경우 [Docker Toolbox를 설치](https://docs.docker.com/installation/windows/)하거나 [Chocolatey에서](https://chocolatey.org/packages/docker) Docker.exe를 받을 수 있습니다.

## 포털 로그온

Azure 계정이 있으면 간단합니다. [Azure 계정을 무료로 받는 것도 간단합니다](http://azure.microsoft.com/pricing/free-trial/)!

## Canonical 및 MSOpenTech의 Docker 이미지를 사용하여 VM 만들기

1. 이제 로그온했으므로 왼쪽 아래에서 **새로 만들기**를 클릭하여 새 VM 이미지를 만듭니다. 배너에 적절한 이미지가 즉시 표시될 수도 있습니다.

> ![배너에서 Docker Ubuntu 이미지 선택](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. 이미지가 표시되지 않으면 이미지 갤러리에서 검색합니다.

> ![이미지 갤러리에서 이미지 찾기](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. 인스턴스의 사용자 이름 및 암호 또는 **.pub** 파일의 내용(ssh-rsa 형식)을 제공해 인증서를 사용하여 SSH를 사용하도록 설정합니다. 아래 그림에서는 사용자 이름과 암호 조합을 지정합니다. 그런 다음 아래쪽에 있는 **만들기**를 누릅니다.

> ![vm 인스턴스 구성](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. 실행될 때까지 기다립니다. 그리 오래 걸리지 않습니다.

> ![포털에서 실행되는 Docker 이미지](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## SSH를 사용하여 연결

이제 시작해 보세요. SSH를 사용하여 VM에 즉시 연결할 수 있습니다.

> ![SSH와 함께 연결](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

Docker 명령을 실행해 보세요. 이 Azure VM의 기본 구성에는 **`sudo`**가 필요합니다.

> ![이미지 풀링](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

[Docker] 사용을 시작합니다!

<!--Anchors-->
[Log on to the Portal]: #logon
[Create a VM with the Docker Image from Canonical and MSOpenTech]: #createvm
[Connect with SSH and Have Fun]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/
 

<!---HONumber=Oct15_HO2-->