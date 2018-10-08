---
title: 빠른 시작 - Azure Portal에서 Linux VM 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Linux 가상 머신을 만드는 방법을 배웁니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fcc9f338ad69322091199ce9d5d2d1d6f9f2165e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227285"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Linux 가상 머신 만들기

Azure Portal을 통해 Azure VM(가상 머신)을 만들 수 있습니다. 이 방법은 VM 및 관련 리소스를 만드는 브라우저 기반 사용자 인터페이스를 제공합니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Ubuntu를 실행하는 Azure에서 Linux VM(가상 머신)을 배포하는 방법을 보여줍니다. 작업에서 VM을 보려면 VM에 SSH를 수행하고 NGINX 웹 서버를 설치합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-ssh-key-pair"></a>SSH 키 쌍 만들기

이 빠른 시작을 완료하려면 SSH 키 쌍이 필요합니다. 기존 SSH 키 쌍을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

SSH 키 쌍을 만들고 Linux VM에 로그인하려면 Bash 셸에서 다음 명령을 실행하고 화면의 지침을 따릅니다. 예를 들어 [Azure Cloud Shell](../../cloud-shell/overview.md) 또는 [Windows Substem for Linux](/windows/wsl/install-win10)를 사용할 수 있습니다. 명령 출력은 공개 키 파일의 파일 이름을 포함합니다. 공개 키 파일의 콘텐츠(`cat ~/.ssh/id_rsa.pub`)를 클립보드에 복사합니다.

```bash
ssh-keygen -t rsa -b 2048
```

PuTTy 사용을 포함하여 SSH 키 쌍을 만드는 방법에 대한 자세한 내용은 [Windows에 SSH 키를 사용하는 방법](ssh-from-windows.md)을 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

http://portal.azure.com에서 Azure Portal에 로그인

## <a name="create-virtual-machine"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.

1. Azure Marketplace 리소스 목록 위에 있는 검색 상자에서 Canonical의 **Ubuntu Server 16.04 LTS**를 검색하고 선택한 다음, **만들기**를 선택합니다.

1. **기본** 탭의 **프로젝트 세부 정보** 아래에서 올바른 구독이 선택되어 있는지 확인한 다음, **리소스 그룹** 아래에서 **새로 만들기**를 선택합니다. 팝업에서 리소스 그룹의 이름에 *myResourceGroup*을 입력한 다음, **확인*을 선택합니다. 

    ![VM에 대한 새 리소스 그룹 만들기](./media/quick-create-portal/project-details.png)

1. **인스턴스 세부 정보** 아래에서 **가상 머신 이름**에 *myVM*을 입력하고 **지역**에 *미국 동부*를 선택합니다. 다른 기본값을 그대로 둡니다.

    ![인스턴스 세부 정보 섹션](./media/quick-create-portal/instance-details.png)

1. **관리자 계정** 아래에서 **SSH 공개 키**를 선택하고, 사용자 이름을 입력한 다음, 공개 키를 텍스트 상자에 붙여넣습니다. 공개 키에서 선행 또는 후행 공백을 모두 제거합니다.

    ![관리자 계정](./media/quick-create-portal/administrator-account.png)

1. **인바운드 포트 규칙** > **공용 인바운드 포트** 아래에서 **선택한 포트 허용**을 선택한 다음, 드롭다운에서 **SSH(22)** 및 **HTTP(80)** 를 선택합니다. 

    ![RDP 및 HTTP에 대한 포트 열기](./media/quick-create-portal/inbound-port-rules.png)

1. 나머지 기본값을 그대로 둔 다음, 페이지의 아래에서 **검토 + 만들기** 단추를 선택합니다.

    
## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

VM과의 SSH 연결을 만듭니다.

1. VM의 개요 페이지에서 **연결** 단추를 선택합니다. 

    ![포털 9](./media/quick-create-portal/portal-quick-start-9.png)

2. **가상 머신에 연결** 페이지에서, 22 포트를 통해 DNS 이름으로 연결하는 기본 옵션을 유지합니다. **VM 로컬 계정을 사용하여 로그인**에 연결 명령이 표시됩니다. 단추를 클릭하여 명령을 복사합니다. 다음 예제에서는 SSH 연결 명령의 모양을 보여줍니다.

    ```bash
    ssh azureuser@myvm-123abc.eastus.cloudapp.azure.com
    ```

3. Azure Cloud Shell이나 Ubuntu 또는 Windows의 Bash 같은 셸에 SSH 연결 명령을 붙여넣어 연결을 만듭니다. 

## <a name="install-web-server"></a>웹 서버 설치

실제로 작동 중인 VM을 보려면 NGINX 웹 서버를 설치합니다. 패키지 원본을 업데이트하고 최신 NGINX 패키지를 설치하려면 SSH 세션에서 다음 명령을 실행합니다.

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

여기까지 마쳤으면 SSH 세션을 `exit`하고 Azure Portal에서 VM 속성으로 돌아갑니다.


## <a name="view-the-web-server-in-action"></a>실제로 작동 중인 웹 서버 보기

NGINX를 설치하고 VM에 대해 포트 80을 열었으니, 이제 인터넷에서 웹 서버에 액세스할 수 있습니다. 웹 브라우저를 열고 VM의 공용 IP 주소를 입력합니다. 공용 IP 주소는 VM 개요 페이지에서 또는 인바운드 포트 규칙을 추가하는 *네트워킹* 페이지의 위쪽에서 찾을 수 있습니다.

![NGINX 기본 사이트](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 가상 머신 및 모든 관련 리소스가 더 이상 필요 없는 경우 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고 **삭제**를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 가상 머신을 배포하고, 네트워크 보안 그룹 및 규칙을 만들고 기본 웹 서버를 설치했습니다. Azure 가상 머신에 대한 자세한 내용을 알아보려면 Linux VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Linux 가상 머신 자습서](./tutorial-manage-vm.md)
