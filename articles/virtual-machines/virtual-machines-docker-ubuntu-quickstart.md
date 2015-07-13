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
	ms.date="05/20/2015"
	ms.author="rasquill"/>

# Azure Marketplace에서 Docker를 신속하게 시작하는 방법

[Docker] 사용을 가장 빠르게 시작하려면 Azure Marketplace로 이동해 [MSOpenTech]와 함께 [Canonical]에서 만든 **Ubuntu Server의 Docker** 이미지 템플릿을 사용하여 VM을 만듭니다. 그러면 Ubuntu Server VM이 생성되고 Azure에 미리 설치되어 실행 중인 **최신** Docker 엔진과 함께 [Docker VM 확장](virtual-machines-docker-vm-extension.md)이 자동으로 설치됩니다.

SSH를 사용하여 VM에 즉시 연결할 수 있으며, 아무 작업도 수행하지 않고 Docker 작업을 직접 수행할 수 있습니다.

> [AZURE.NOTE]Azure Marketplace 템플릿을 통해 만든 VM은 원격 Docker 클라이언트에서 관리하는 데 필요한 Docker 원격 API를 호스트하지 않습니다. 이 VM에서 원격으로 Docker Host를 제어하도록 하려면 [HTTPS를 사용하여 Docker 실행](https://docs.docker.com/articles/https/)을 참조하거나 [Azure 포털에서 Docker VM 확장 사용](virtual-machines-docker-with-portal.md) 또는 [Azure CLI에서 Docker VM 확장 사용](virtual-machines-docker-with-xplat-cli.md)의 단계를 따르세요. 전문적인 지식이 있는 경우 Github에서 [Windows Docker Client](https://github.com/ahmetalpbalkan/Docker.DotNet)를 빌드하여 시도해 볼 수 있습니다(또는 [nuget](https://www.nuget.org/packages/Docker.DotNet/)에서 작업).

항목 내용

- [포털에 로그온]
- [Canonical 및 MSOpenTech의 Docker 이미지를 사용하여 VM 만들기]
- [SSH를 사용하여 연결]

## <a id='logon'>포털에 로그온</a>

Azure 계정이 있으면 간단합니다. [Azure 계정을 무료로 받는 것도 간단합니다](http://azure.microsoft.com/pricing/free-trial/)!

## <a id='createvm'>Canonical 및 MSOpenTech의 Docker 이미지를 사용하여 VM 만들기</a>

1. 이제 로그온했으므로 왼쪽 아래에서 **새로 만들기**를 클릭하여 새 VM 이미지를 만듭니다. 배너에 적절한 이미지가 즉시 표시될 수도 있습니다.

> ![배너에서 Docker Ubuntu 이미지 선택](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. 이미지가 표시되지 않으면 이미지 갤러리에서 검색합니다.

> ![이미지 갤러리에서 이미지 찾기](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. 인스턴스의 사용자 이름 및 암호 또는 **.pem** 파일을 제공해 인증서를 사용하여 SSH를 사용하도록 설정합니다. 아래 그림에서는 사용자 이름과 암호 조합을 지정합니다. 그런 다음 아래쪽에 있는 **만들기**를 누릅니다.

> ![vm 인스턴스 구성](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. 실행될 때까지 기다립니다. 그리 오래 걸리지 않습니다.

> ![포털에서 실행되는 Docker 이미지](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## <a id='havingfun'>SSH를 사용하여 연결</a>

이제 시작해 보세요. SSH를 사용하여 VM에 즉시 연결할 수 있습니다.

> ![SSH와 함께 연결](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

Docker 명령을 실행해 보세요. 이 Azure VM의 기본 구성에는 **`sudo`**가 필요합니다.

> ![이미지 풀링](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계

[Docker] 사용을 시작합니다!

<!--Anchors-->
[포털에 로그온]: #logon
[Canonical 및 MSOpenTech의 Docker 이미지를 사용하여 VM 만들기]: #createvm
[SSH를 사용하여 연결]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/
 

<!---HONumber=July15_HO1-->