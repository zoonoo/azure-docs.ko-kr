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
	ms.date="01/08/2016"
	ms.author="danlep"/>

# Azure CLI 설치

신속하게 Azure CLI(Azure 명령줄 인터페이스)를 설치하여 Microsoft Azure에서 리소스를 만들고 관리하기 위한 오픈 소스 셸 기반 명령 집합을 사용합니다. 제공된 설치 관리자 패키지 중 하나를 사용하여 운영 체제에 Azure CLI를 설치, Node.js 및 **npm**을 사용하여 CLI를 설치 또는 Docker 호스트에서 컨테이너로 Azure CLI를 설치합니다. 더 많은 옵션과 배경은 [GitHub](https://github.com/azure/azure-xplat-cli)의 프로젝트 리포지토리를 참조하세요.


Azure CLI가 설치되었으면 [Azure 구독을 사용하여 연결](xplat-cli-connect.md)하고 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트 등)에서 **azure** 명령을 실행하여 Azure 리소스 작업을 수행할 수 있습니다.




## 설치 관리자 사용

다음과 같은 설치 관리자 패키지를 사용할 수 있습니다.

* [Windows 설치 관리자][windows-installer]

* [OS X 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Linux 설치 관리자][linux-installer]


## Node.js 및 npm 설치 및 사용

또는 Node.js가 시스템에 이미 설치된 경우 다음 명령을 사용하여 Azure CLI를 설치합니다.

	npm install azure-cli -g

> [AZURE.NOTE] __npm__ 명령을 성공적으로 실행하기 위해 `sudo`을(를) 사용해야 할 수도 있습니다.

### [dpkg](http://en.wikipedia.org/wiki/Dpkg) 패키지 관리를 사용하는 Linux 배포에 Node.js 및 npm 설치

이 중 가장 일반적인 배포는 [APT(고급 패키징 도구)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 또는 `.deb` 패키지 형식을 기반으로 한 기타 도구를 사용합니다. Debian 및 Ubuntu가 그 예입니다.

이러한 배포의 가장 최신 배포 대부분이 **nodejs-legacy**를 설치해야 제대로 구성된 **npm** 도구로 Azure CLI를 설치할 수 있습니다. 다음 코드는 Ubuntu 14.04에 **npm**을 제대로 설치하는 명령을 보여줍니다.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Ubuntu 12.04와 같은 기존 배포 중 일부는 Node.js의 현재 이진 배포를 설치해야 합니다. 다음 코드는 **curl**을 설치 및 사용하여 이 작업을 수행하는 방법을 보여줍니다.

>[AZURE.NOTE] 명령은 [여기](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager)에 나와 있는 설치 지침에서 가져온 것입니다. 하지만 **sudo**를 파이프 대상으로 사용하는 경우 항상 설치하는 스크립트를 확인하고 **sudo**를 통해 실행하기 전에 원하는 것을 정확하게 수행하는지 확인해야 합니다. 성능이 좋으면 그만큼 책임도 따릅니다.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager) 패키지 관리를 사용하는 Linux 배포에 Node.js 및 npm 설치

RPM 기반 배포에 Node.js를 설치하려면 EPEL 리포지토리를 사용하도록 설정해야 합니다. 다음 코드는 CentOS 7에 설치에 대한 모범 사례를 보여줍니다. (아래 첫 번째 줄에서 '-'(하이픈 문자)이 중요합니다!)

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Windows 및 Mac OS X에 Node.js 및 npm 설치

[Nodejs.org](https://nodejs.org/en/download/)로부터 설치 관리자를 사용하여 Node.js 및 npm을 Windows 및 OS X에 설치할 수 있습니다. 설치를 완료하려면 컴퓨터를 다시 시작해야 합니다. 명령 또는 터미널 창을 열고 입력하여 노드 및 npm이 제대로 설치되었는지 확인합니다.

	npm -v

설치된 npm 버전을 표시하는 경우 함께 계속해서 Azure CLI를 설치할 수 있습니다.

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

>[AZURE.NOTE] Linux 시스템의 경우 [소스](http://go.microsoft.com/fwlink/?linkid=253472)에서 빌드하여 Azure CLI를 설치할 수도 있습니다. 소스에서 빌드하는 방법에 대한 자세한 내용은 보관 파일에 포함된 INSTALL 파일을 참조하세요.

## Docker 컨테이너 사용

Docker 호스트에서 다음을 실행합니다.

```
docker run -it microsoft/azure-cli
```

## Azure CLI 명령 실행
Azure CLI가 설치되었으면 명령줄 사용자 인터페이스(Bash, 터미널, 명령 프롬프트 등)에서 **azure** 명령을 실행할 수 있습니다. 예를 들어 help 명령을 실행하려면 다음을 입력합니다.

```
azure help
```

설치한 Azure CLI의 버전을 보려면 다음을 입력합니다.

```
azure --version
```

이제 준비가 되었습니다! 사용자 고유의 리소스 작업을 수행하기 위해 모든 CLI 명령에 대한 액세스 권한을 얻으려면 [Azure CLI에서 Azure 구독에 연결](xplat-cli-connect.md)합니다.

## CLI 업데이트

Microsoft는 업데이트된 Azure CLI 버전을 자주 발표합니다. 운영 체제의 설치 관리자를 사용하여 CLI를 다시 설치하거나 Node.js 및 npm이 설치된 경우 다음을 입력하여 업데이트합니다. Linux 배포에서는 **sudo**를 사용해야 할 수 있습니다.

```
npm update -g azure-cli
```

## 추가 리소스

* [Azure 리소스 관리자에서 Azure CLI 사용][cliarm]

* [Azure 서비스 관리에서 Azure CLI 사용][cliasm]

* Azure CLI에 대한 자세한 내용을 보거나, 소스 코드를 다운로드하거나, 문제를 보고하거나, 프로젝트에 기여하려면 [Azure CLI에 대한 GitHub 리포지토리](https://github.com/azure/azure-xplat-cli)를 방문하세요.

* Azure CLI 또는 Azure 사용에 대한 질문이 있는 경우 [Azure 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=azurescripting)을 방문하세요.



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0218_2016-->