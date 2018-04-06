---
title: Azure CLI 1.0 설치 | Microsoft Docs
description: Mac, Linux 및 Windows용 Azure CLI 1.0을 설치하여 Azure 서비스 사용 시작
editor: ''
manager: timlt
documentationcenter: ''
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: rasquill
ms.openlocfilehash: a1ca71a81f4fa6dd36b9ed3a2b16fcca0e29601c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="install-the-azure-cli-10"></a>Azure CLI 1.0 설치
> [!div class="op_single_selector"]
> * [PowerShell](/powershell/azure/overview)
> * [Azure CLI 1.0](cli-install-nodejs.md)
> * [Azure CLI 2.0](/cli/azure/install-azure-cli)

> [!IMPORTANT]
> 이 항목에서는 Azure CLI 1.0을 설치하는 방법을 설명합니다. 이 CLI는 사용되지 않으므로 "클래식" 리소스를 포함한 ASM(Azure 서비스 관리) 모델의 지원에서만 사용해야 합니다.
> Azure Resource Manager 배포의 경우 [Azure CLI 2.0](/cli/azure)을 사용합니다.

신속하게 Azure CLI 1.0(Azure 명령줄 인터페이스)을 설치하여 Microsoft Azure에서 리소스를 만들고 관리하기 위한 오픈 소스 셸 기반 명령 집합을 사용합니다. 컴퓨터에 크로스 플랫폼 도구를 설치하는 몇 가지 옵션이 있습니다.

* **npm 패키지** - npm(JavaScript용 패키지 관리자)을 실행하여 Linux 배포 또는 OS에 최신 Azure CLI 1.0 패키지를 설치합니다. node.js 및 npm이 컴퓨터에 필요합니다.
* **설치 관리자** - 설치 관리자를 다운로드하여 Mac 또는 Windows에서 손쉽게 설치합니다.
* **Docker 컨테이너** - 즉시 실행 가능한 Docker 컨테이너에서 최신 CLI를 사용하기 시작합니다. Docker 호스트가 컴퓨터에 필요합니다.

추가 옵션 및 배경 정보는 [GitHub](https://github.com/azure/azure-xplat-cli)에서 프로젝트 리포지토리를 참조하세요.

Azure CLI 1.0이 설치되면 [Azure 구독을 사용하여 연결](/cli/azure/authenticate-azure-cli)하고 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트 등)에서 **azure** 명령을 실행하여 Azure 리소스 작업을 수행할 수 있습니다.

## <a name="option-1-install-an-npm-package"></a>옵션 1: npm 패키지 설치
npm 패키지에서 CLI를 설치하려면 [최신 Node.js 및 npm](https://nodejs.org/en/download/package-manager/)을 다운로드하고 설치했는지 확인합니다. 그런 다음 **npm install** azure-cli 패키지를 설치합니다.

```bash
npm install -g azure-cli
```

Linux 배포에서는 **npm** 명령을 정상적으로 실행하기 위해 다음과 같이 **sudo**를 사용해야 할 수도 있습니다.

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Linux 배포 또는 OS에 Node.js 및 npm을 설치하거나 업데이트해야 하는 경우 최신 Node.js LTS 버전(4.x)을 설치하는 것이 좋습니다. 이전 버전을 사용하는 경우 설치 오류가 발생할 수 있습니다.

원한다면 npm 패키지용 최신 Linux [tar 파일][linux-installer]을 로컬에 다운로드하세요. 그런 다음, 다운로드한 npm 패키지를 다음과 같이 설치합니다.(Linux 배포에서는 **sudo**를 사용해야 할 수 있습니다.)

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>옵션 2: 설치 관리자 사용
Mac 또는 Windows 컴퓨터를 사용한다면, 다음과 같은 CLI 설치 관리자를 다운로드할 수 있습니다.

* [Mac OS X 설치 관리자][mac-installer]
* [Windows MSI][windows-installer]

> [!TIP]
> Windows에서 [웹 플랫폼 설치 관리자](https://go.microsoft.com/?linkid=9828653) 를 다운로드하여 CLI를 설치할 수도 있습니다. 이 설치 관리자를 사용하면 CLI를 설치한 후에 추가적인 Azure SDK 및 명령줄 도구를 설치하는 옵션이 제공됩니다.

## <a name="option-3-use-a-docker-container"></a>옵션 3: Docker 컨테이너 사용
컴퓨터를 [Docker](https://docs.docker.com/engine/understanding-docker/) 호스트로 설정한 경우 Docker 컨테이너에서 최신 Azure CLI 1.0을 실행할 수 있습니다. 다음 명령을 실행합니다. Linux 배포에서는 **sudo**를 사용해야 할 수도 있습니다.

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-10-commands"></a>Azure CLI 1.0 명령 실행
Azure CLI 1.0이 설치되면 명령줄 사용자 인터페이스(Bash, 터미널, 명령 프롬프트 등)에서 **azure** 명령을 실행합니다. 예를 들어 help 명령을 실행하려면 다음을 입력합니다.

```azurecli
azure help
```

> [!NOTE]
> 일부 Linux 배포에서 `/usr/bin/env: ‘node’: No such file or directory`과 비슷한 오류가 나타날 수 있습니다. 이 오류는 최근에 Node.js를 /usr/bin/nodejs에 설치할 때 비롯되었습니다. 해결하려면 이 명령을 실행하여 /Usr/bin/node에 바로 가기 링크를 만듭니다.

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

설치한 Azure CLI 1.0의 버전을 보려면 다음을 입력합니다.

```azurecli
azure --version
```

이제 준비가 되었습니다! 사용자 고유의 리소스 작업을 수행하기 위해 모든 CLI 명령 액세스 권한을 얻으려면 [Azure CLI에서 Azure 구독에 연결](/cli/azure/authenticate-azure-cli)합니다.

> [!NOTE]
> Azure CLI를 처음 사용할 때, 사용 정보를 Microsoft가 수집하도록 허용할 것인지 묻는 메시지가 표시됩니다. 참여는 자발적입니다. 참여하기로 선택한 경우, `azure telemetry --disable`을 실행하여 언제든지 중지할 수 있습니다. 언제라도 참여를 활성화하려면, `azure telemetry --enable`을 실행합니다.

## <a name="update-the-cli"></a>CLI 업데이트
Microsoft는 업데이트된 Azure CLI 버전을 자주 발표합니다. 사용 중인 운영 체제에 대 한 설치 관리자를 사용하여 CLI를 다시 설치하거나 최신 Docker 컨테이너를 실행합니다. 또는 최신 Node.js 및 npm이 설치된 경우 다음을 입력하여 업데이트합니다.(Linux 배포에서는 **sudo**를 사용해야 할 수 있습니다)

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>탭 완성 기능 사용
CLI 명령의 탭 완성 기능이 Mac 및 Linux에서 지원됩니다.

zsh에서 사용하도록 설정하려면 다음을 실행합니다.

```bash
echo '. <(azure --completion)' >> .zshrc
```

bash에서 사용하도록 설정하려면 다음을 실행합니다.

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>다음 단계
* [CLI에서 Azure 구독에 연결](/cli/azure/authenticate-azure-cli) 하여 Azure 리소스를 만들고 관리합니다.
* Azure CLI에 대한 자세한 내용을 보거나, 소스 코드를 다운로드하거나, 문제를 보고하거나, 프로젝트에 기여하려면 [Azure CLI에 대한 GitHub 리포지토리](https://github.com/azure/azure-xplat-cli)를 방문하세요.
* Azure CLI 또는 Azure 사용에 대한 질문이 있는 경우 [Azure 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting)을 방문하세요.


[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
