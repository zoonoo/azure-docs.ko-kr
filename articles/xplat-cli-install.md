<properties
	pageTitle="Azure 명령줄 인터페이스 설치 | Microsoft Azure"
	description="Mac, Linux 및 Windows용 Azure CLI를 설치하여 Azure 서비스 사용 시작"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="danlep"/>

# Azure CLI 설치

이 문서에서는 Azure CLI(Azure 명령줄 인터페이스)를 설치하는 방법을 설명합니다. Azure CLI는 Microsoft Azure에서 리소스를 만들고 관리하기 위한 오픈 소스 셸 기반 명령 집합을 제공합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-include.md)]이 문서에서는 리소스 관리자 배포 모델 또는 클래식 배포 모델을 사용하여 리소스를 만들고 관리하는 방법을 설명합니다.

Azure CLI는 JavaScript로 작성되므로 [Node.js](https://nodejs.org)가 필요합니다. [노드용 Azure SDK](https://github.com/azure/azure-sdk-for-node)를 사용하여 구현되고, Apache 2.0 라이선스 하에 릴리스되었습니다. 프로젝트 리포지토리는 [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli)에 있습니다.

> [AZURE.NOTE]Azure CLI를 이미 설치한 경우 Azure 리소스와 연결합니다. 자세한 내용을 보려면 [Azure 구독에 연결하는 방법](xplat-cli-connect.md#configure)을 참조합니다.

<a id="install"></a>
## Azure CLI를 설치하는 방법

몇 가지 방법으로 Azure CLI를 설치할 수 있습니다.

1. 설치 관리자 사용
2. Node.js 및 npm을 설치하고 **npm 설치** 명령 사용
3. Azure CLI를 Docker 컨테이너로 실행

Azure CLI가 설치되었으면 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트 등)에서 **azure** 명령을 사용하여 Azure CLI 명령에 액세스할 수 있습니다.

## 설치 관리자 사용

다음과 같은 설치 관리자 패키지를 사용할 수 있습니다.

* [Windows 설치 관리자][windows-installer]

* [OS X 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Linux 설치 관리자][linux-installer]


## Node.js 및 npm 설치 및 사용

Node.js가 시스템에 이미 설치된 경우 다음 명령을 사용하여 Azure CLI를 설치합니다.

	npm install azure-cli -g

> [AZURE.NOTE]__npm__ 명령을 성공적으로 실행하기 위해 `sudo`을(를) 사용해야 할 수도 있습니다.

### [dpkg](http://en.wikipedia.org/wiki/Dpkg) 패키지 관리를 사용하는 Linux 배포에 node.js 및 npm 설치
이 중 가장 일반적인 배포는 [APT(고급 패키징 도구)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 또는 `.deb` 패키지 형식을 기반으로 한 기타 도구를 사용합니다. Debian 및 Ubuntu가 그 예입니다.

이러한 배포의 가장 최신 배포 대부분이 **nodejs-legacy**를 설치해야 제대로 구성된 **npm** 도구로 Azure CLI를 설치할 수 있습니다. 다음 코드는 Ubuntu 14.04에 **npm**을 제대로 설치하는 명령을 보여줍니다.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Ubuntu 12.04와 같은 기존 배포 중 일부는 node.js의 현재 이진 배포를 설치해야 합니다. 다음 코드는 **curl**을 설치 및 사용하여 이 작업을 수행하는 방법을 보여줍니다.

>[AZURE.NOTE][여기](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager)에 있는 명령은 여기에 나와 있는 Joyent 설치 지침에서 가져온 것입니다. 하지만 **sudo**를 파이프 대상으로 사용하는 경우 항상 설치하는 스크립트를 확인하고 **sudo**를 통해 실행하기 전에 원하는 것을 정확하게 수행하는지 확인해야 합니다. 성능이 좋으면 그만큼 책임도 따릅니다.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager) 패키지 관리를 사용하는 Linux 배포에 node.js 및 npm 설치

RPM 기반 배포에 node.js를 설치하려면 EPEL 리포지토리를 사용하도록 설정해야 합니다. 다음 코드는 CentOS 7에 설치에 대한 모범 사례를 보여줍니다. (아래 첫 번째 줄에서 '-'(하이픈 문자)이 중요합니다!)

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Windows 및 Mac OS X에 node.js 및 npm 설치

[Nodejs.org](https://nodejs.org/download/)로부터 설치 관리자를 사용하여 node.js 및 npm을 Windows 및 OS X에 설치할 수 있습니다 . 설치를 완료하려면 컴퓨터를 다시 시작해야 합니다. 명령 창을 열고 입력하여 노드 및 npm이 제대로 설치되었는지 확인

	npm -v

설치된 Npm 버전을 표시하는 경우 함께 계속해서 Azure CLI를 설치할 수 있습니다.

	npm install -g azure-cli

설치가 끝나면 다음과 유사하게 표시됩니다.

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

>[AZURE.NOTE]Linux 시스템의 경우 [소스](http://go.microsoft.com/fwlink/?linkid=253472)에서 빌드하여 Azure CLI를 설치할 수도 있습니다. 소스에서 빌드하는 방법에 대한 자세한 내용은 보관 파일에 포함된 INSTALL 파일을 참조하세요.

## Docker 컨테이너 사용

Docker 호스트에서 다음을 실행합니다. ```
	docker run -it microsoft/azure-cli
```

## Azure CLI 명령 실행

Azure CLI가 설치되었으면 명령줄 사용자 인터페이스(Bash, 터미널, 명령 프롬프트 등)에서 **azure** 명령을 사용하여 Azure CLI 명령에 액세스할 수 있습니다. 예를 들어 Windows에서 도움말 명령을 실행하려면 명령 창을 시작하고 다음을 입력합니다.

```
	c:> azure help
```

이제 준비가 되었습니다! 이제 [Azure CLI로부터 Azure 구독에 연결하고](xplat-cli-connect.md) **azure** 명령의 사용을 시작할 수 있습니다.


<a id="additional-resources"></a>
## 추가 리소스

* [리소스 관리 명령으로 Azure CLI 사용][cliarm]

* [서비스 관리(클래식) 명령으로 Azure CLI 사용][cliasm]

* Azure CLI에 대한 자세한 내용을 보거나, 소스 코드를 다운로드하거나, 문제를 보고하거나, 프로젝트에 기여하려면 [Azure CLI에 대한 GitHub 리포지토리](https://github.com/azure/azure-xplat-cli)를 방문하세요.

* Azure CLI 또는 Azure를 사용하는 데 문제가 있는 경우 [Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home)을 방문하세요.



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Sept15_HO4-->