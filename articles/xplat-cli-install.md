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
	ms.date="04/07/2016"
	ms.author="danlep"/>

# Azure CLI 설치

신속하게 Azure CLI(Azure 명령줄 인터페이스)를 설치하여 Microsoft Azure에서 리소스를 만들고 관리하기 위한 오픈 소스 셸 기반 명령 집합을 사용합니다. 서로 다른 운영 체제에 제공된 설치 관리자 패키지 중 하나를 사용하거나, npm 패키지에서 설치하거나, Azure CLI를 Docker 호스트의 컨테이너로 설치하는 등 몇 가지 설치 옵션이 있습니다. 추가 옵션 및 배경 정보는 [GitHub](https://github.com/azure/azure-xplat-cli)에서 프로젝트 리포지토리를 참조하세요.


Azure CLI가 설치되었으면 [Azure 구독을 사용하여 연결](xplat-cli-connect.md)하고 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트 등)에서 **azure** 명령을 실행하여 Azure 리소스 작업을 수행할 수 있습니다.


## 설치 관리자 사용

다음과 같은 설치 관리자 패키지를 사용할 수 있습니다.

* [Windows 설치 관리자][windows-installer]

* [OS X 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Linux 설치 관리자][linux-installer]


## npm 패키지 설치

또는 최신 Node.js 및 npm이 시스템에 이미 설치된 경우 다음 명령을 실행하여 Azure CLI 패키지를 설치합니다. Linux 배포에서는 __npm__ 명령을 성공적으로 실행하기 위해 **sudo**를 사용해야 할 수도 있습니다.

	npm install azure-cli -g

> [AZURE.NOTE]운영 체제의 Node.js 및 npm을 설치하거나 업데이트해야 하는 경우 [Nodejs.org](https://nodejs.org/en/download/package-manager/)의 설명서를 참조하세요. 최신 Node.js LTS 버전(4.x)을 설치하는 것이 좋습니다. 이전 버전을 사용하는 경우 설치 오류가 발생할 수 있습니다.


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

Microsoft는 업데이트된 Azure CLI 버전을 자주 발표합니다. 운영 체제의 설치 관리자를 사용하여 CLI를 다시 설치하거나 최신 Node.js 및 npm이 설치된 경우 다음을 입력하여 업데이트합니다. Linux 배포에서는 **sudo**를 사용해야 할 수 있습니다.

```
npm update -g azure-cli
```

## 다음 단계 

* [CLI에서 Azure 구독에 연결](xplat-cli-connect.md)하여 Azure 리소스를 만들고 관리합니다.

* Azure CLI에 대한 자세한 내용을 보거나, 소스 코드를 다운로드하거나, 문제를 보고하거나, 프로젝트에 기여하려면 [Azure CLI에 대한 GitHub 리포지토리](https://github.com/azure/azure-xplat-cli)를 방문하세요.

* Azure CLI 또는 Azure 사용에 대한 질문이 있는 경우 [Azure 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=azurescripting)을 방문하세요.

* Linux 시스템의 경우 [소스](http://aka.ms/linux-azure-cli)에서 빌드하여 Azure CLI를 설치할 수도 있습니다. 원본에서 빌드하는 방법에 대한 자세한 내용은 원본 보관 파일에 포함된 INSTALL 파일을 참조하세요.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0420_2016-->