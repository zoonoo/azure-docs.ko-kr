---
title: Windows 용 azure 직렬 콘솔 | Microsoft Docs
description: Azure Virtual Machines 및 Virtual Machine Scale Sets에 대 한 양방향 직렬 콘솔입니다.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 32d385416c83f81553e734d9471d0b502a458b07
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390498"
---
# <a name="azure-serial-console-for-windows"></a>Windows 용 azure 직렬 콘솔

가상 머신 확장 집합 인스턴스 및 Azure portal에서 직렬 콘솔 Windows 가상 머신 (Vm)에 대 한 텍스트 기반 콘솔에 대 한 액세스를 제공 합니다. 이 직렬 연결의 VM 또는 가상 머신 확장 집합 인스턴스, 네트워크 또는 운영 체제 상태와 관계 없이에 대 한 액세스를 제공 COM1 직렬 포트에 연결 합니다. 직렬 콘솔 Azure portal을 사용 하 여 액세스할 수 있습니다 이며 VM 또는 가상 머신 확장 집합에 이상 참가자의 액세스 역할을가지고 있는 사용자만 사용할 수 있습니다.

가상 머신 확장 집합 인스턴스 직렬 콘솔 Vm에서 동일한 방식으로 작동 하며 이 문서에서 Vm에 모든 멘 션 암시적으로 포함 됩니다 가상 머신 확장 집합 인스턴스 달리 언급 하지 않으면.

Linux Vm 및 가상 머신 확장 집합에 대 한 직렬 콘솔 설명서를 참조 하세요 [Azure Linux 용 직렬 콘솔](serial-console-linux.md)합니다.

> [!NOTE]
> 직렬 콘솔 글로벌 Azure 지역에서 일반 공급 됩니다. 아직 Azure Government 또는 Azure 중국 클라우드에서는 지원되지 않습니다.


## <a name="prerequisites"></a>필수 조건

* VM 또는 가상 머신 확장 집합 인스턴스에 리소스 관리 배포 모델을 사용 해야 합니다. 클래식 배포는 지원되지 않습니다.

- 직렬 콘솔을 사용 하 여 해당 계정에 있어야 합니다 [Virtual Machine 참여자 역할](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) VM에 대 한 및 [부트 진단](boot-diagnostics.md) 저장소 계정

- 암호 기반 사용자를 VM 또는 가상 머신 확장 집합 인스턴스에 있어야 합니다. VM 액세스 확장의 [암호 재설정](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) 기능으로 이러한 계정을 만들 수 있습니다. **지원 + 문제 해결** 섹션에서 **암호 재설정**을 선택합니다.

* 직렬 콘솔에 액세스하는 VM에서는 [부트 진단](boot-diagnostics.md)을 사용하도록 설정해야 합니다.

    ![부트 진단 설정](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="get-started-with-the-serial-console"></a>직렬 콘솔 시작하기
Vm 및 가상 머신 확장 집합에 대 한 직렬 콘솔은 Azure 포털을 통해서만 액세스할 수 있습니다.

### <a name="serial-console-for-virtual-machines"></a>가상 머신 용 직렬 콘솔
Vm에 대 한 직렬 콘솔은 클릭으로 간단 **직렬 콘솔** 내 합니다 **지원 + 문제 해결** 섹션에서는 Azure portal에서 합니다.
  1. [Azure Portal](https://portal.azure.com)을 엽니다.

  1. 이동할 **모든 리소스** 가상 머신을 선택 합니다. VM에 대한 개요 페이지가 열립니다.

  1. **지원 + 문제 해결** 섹션까지 아래로 스크롤하여 **직렬 콘솔**을 선택합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에 대 한 직렬 콘솔
직렬 콘솔은 virtual machine scale sets는 인스턴스 단위로 제공 됩니다. 표시 하기 전에 가상 머신 확장 집합의 개별 인스턴스를 이동 해야 합니다 **직렬 콘솔** 단추입니다. 가상 머신 확장 집합에 부팅 진단을 사용 하도록 설정 하는 경우 부팅 진단을 사용 하도록 설정 하 여 직렬 콘솔에 액세스 하기 위해 새 모델에 모든 인스턴스를 업그레이드 한 후에 가상 머신 확장 집합 모델을 업데이트 해야 합니다.
  1. [Azure Portal](https://portal.azure.com)을 엽니다.

  1. 이동할 **모든 리소스** 가상 머신 확장 집합을 선택 합니다. 가상 머신 확장에 대 한 개요 페이지가 열리고를 설정합니다.

  1. 이동할 **인스턴스**

  1. 가상 머신 확장 집합 인스턴스를 선택 합니다.

  1. **지원 + 문제 해결** 섹션에서 **직렬 콘솔**합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

## <a name="enable-serial-console-functionality"></a>직렬 콘솔 기능을 사용 하도록 설정

> [!NOTE]
> 직렬 콘솔에 아무 것도 표시 되지 않는 경우 해당 부팅 진단 VM 또는 가상 머신 확장 집합에 대해 활성화 되어 있는지 확인 합니다.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>사용자 지정 또는 이전 이미지에서 직렬 콘솔 사용
Azure의 최신 Windows Server 이미지에는 기본적으로 SAC([Special Administrative Console](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx))가 사용되도록 설정되어 있습니다. SAC는 서버 버전의 Windows에서 지원되지만, 클라이언트 버전(예: Windows 10, Windows 8 또는 Windows 7)에서는 사용할 수 없습니다.

2018년 2월 전에 만든 이전 Windows Server 이미지의 경우 Azure Portal의 실행 명령 기능을 통해 직렬 콘솔을 자동으로 사용하도록 설정할 수 있습니다. Azure portal에서 선택 **명령을 실행**, 선택한 라는 명령이 **EnableEMS** 목록에서.

![명령 실행 목록](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

또는 수동으로 2018 년 2 월 이전에 만든 Windows Vm/가상 머신 확장 집합에 대 한 직렬 콘솔을 사용 하도록 설정 하려면 다음이 단계를 수행 합니다.

1. 원격 데스크톱을 사용하여 Windows 가상 머신에 연결합니다.
1. 관리자 명령 프롬프트에서 다음 명령을 실행합니다.
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. 시스템을 다시 부팅하여 SAC 콘솔을 사용하도록 설정합니다.

    ![SAC 콘솔](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

필요한 경우 오프라인에서도 SAC를 사용하도록 설정할 수 있습니다.

1. SAC를 데이터 디스크용으로 구성하려는 Windows 디스크를 기존 VM에 연결합니다.

1. 관리자 명령 프롬프트에서 다음 명령을 실행합니다.
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>SAC이 사용되도록 설정되어 있는지를 확인하는 방법

[SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)가 사용되도록 설정되어 있지 않으면 직렬 콘솔에 SAC 프롬프트가 표시되지 않습니다. VM 상태 정보가 표시될 때도 있고 비어 있을 때도 있습니다. 2018년 2월 이전에 만든 Windows Server 이미지를 사용하는 경우 SAC가 사용되지 않을 것입니다.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>직렬 콘솔에서 Windows 부팅 메뉴 사용

Windows 부팅 로더 프롬프트를 사용하도록 설정하여 직렬 콘솔에 표시해야 할 경우 부팅 구성 데이터에 다음과 같은 추가 옵션을 추가할 수 있습니다. 자세한 내용은 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)를 참조하세요.

1. Windows VM에 연결 하거나 원격 데스크톱을 사용 하 여 가상 머신 확장 집합 인스턴스.

1. 관리자 명령 프롬프트에서 다음 명령을 실행합니다.
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. 시스템을 다시 부팅하여 부팅 메뉴를 사용하도록 설정

> [!NOTE]
> 부팅 관리자 메뉴가 표시되도록 설정된 시간 제한은 OS 부팅 시간에 영향을 줍니다. 10초 시간 제한 값이 너무 짧거나 너무 길다고 생각되는 경우 다른 값으로 설정합니다.

## <a name="use-serial-console"></a>직렬 콘솔 사용

### <a name="use-cmd-or-powershell-in-serial-console"></a>직렬 콘솔에 CMD 또는 PowerShell을 사용 합니다.

1. 직렬 콘솔에 연결합니다. 연결에 성공하면 프롬프트가 **SAC>** 입니다.

    ![SAC에 연결](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  `cmd`를 입력하여 CMD 인스턴스가 있는 채널을 만듭니다.

1.  `ch -si 1`을 입력하여 CMD 인스턴스를 실행하는 채널로 전환합니다.

1.  **Enter** 키를 누른 다음, 관리자 권한으로 로그인 자격 증명을 입력합니다.

1.  유효한 자격 증명을 입력하면 CMD 인스턴스가 열립니다.

1.  PowerShell 인스턴스를 시작하려면 CMD 인스턴스에서 `PowerShell`을 입력하고 **Enter** 키를 누릅니다.

    ![PowerShell 인스턴스 열기](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>NMI 호출에 대해 직렬 콘솔 사용
NMI(마스크 불가능 인터럽트)는 가상 머신에 있는 소프트웨어가 무시하는 신호를 만들도록 설계되었습니다. 지금까지 NMI는 특정 응답 시간이 필요한 시스템에서 하드웨어 문제를 모니터링하는 데 사용되었습니다. 현재, 프로그래머 및 시스템 관리자는 종종 디버그 하거나 응답 하지 않는 시스템 문제를 해결 하려면 메커니즘으로 NMI를 사용 합니다.

명령줄에서 키보드 아이콘을 사용하여 NMI를 Azure 가상 머신에 전송하는 데 직렬 콘솔을 사용할 수 있습니다. NMI가 전달되면 가상 머신 구성이 시스템의 응답을 제어하게 됩니다. NMI를 받는 경우 메모리 덤프의 작동을 중단하고 메모리 덤프 파일을 만들도록 Windows를 구성할 수 있습니다.

![NMI 보내기](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

NMI를 받을 때 크래시 덤프 파일을 만들도록 Windows 구성에 대한 정보는 [NMI를 사용하여 크래시 덤프 파일을 생성하는 방법](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)을 참조하세요.

### <a name="use-function-keys-in-serial-console"></a>직렬 콘솔에 기능 키 사용
기능 키는 Windows VM의 직렬 콘솔의 사용량에 대해 사용하도록 설정됩니다. 직렬 콘솔 드롭다운의 F8은 고급 부팅 설정 메뉴를 쉽게 입력하는 편리함을 제공하지만 직렬 콘솔은 다른 모든 기능 키와 호환됩니다. 키를 눌러야 **Fn** + **F1** (또는 F3, F2 등)에서 직렬 콘솔을 사용 중인 컴퓨터에 따라 키보드에 있습니다.

### <a name="use-wsl-in-serial-console"></a>직렬 콘솔에서 WSL 사용
Linux(WSL)용 Windows 하위 시스템은 Windows Server 2019 이상에서 사용하도록 설정됐으므로 Windows Server 2019 이상을 실행하는 경우 직렬 콘솔 내에서 사용을 위해 WSL을 사용하도록 설정할 수 있습니다. 이 기능은 또한 Linux 명령에 익숙한 사용자에게 유용할 수 있습니다. Windows Server용 WSL를 사용하도록 설정하는 지침은 [설치 가이드](https://docs.microsoft.com/windows/wsl/install-on-server)를 참조하세요.

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>직렬 콘솔에 Windows V m/가상 머신 확장 집합 인스턴스를 다시 시작
전원 단추를 탐색 하 고 "VM 다시 시작"을 클릭 하 여 직렬 콘솔 내에서 다시 시작을 시작할 수 있습니다. 그러면 VM 다시 시작을 시작하고, 다시 시작에 대해 Azure Portal 내에 알림이 표시됩니다.

직렬 콘솔 환경을 종료 하지 않고 부팅 메뉴에 액세스 하려는 경우에 유용 합니다.

![Windows 직렬 콘솔 다시 시작](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>직렬 콘솔 사용하지 않도록 설정
기본적으로 모든 구독에는 모든 VM에 대한 직렬 콘솔 액세스가 활성화되어 있습니다. 구독 수준 또는 VM 수준에서 직렬 콘솔을 비활성화할 수 있습니다.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>V m/가상 머신 확장 집합 수준 사용 안 함
특정 VM 또는 가상 머신 확장 집합을 부트 진단 설정을 사용 하지 않도록 설정 하 여 직렬 콘솔을 해제할 수 있습니다. VM 또는 가상 머신 확장 집합에 대 한 직렬 콘솔을 사용 하지 않도록 설정 하려면 Azure portal에서 부트 진단을 해제 합니다. 직렬 콘솔을 사용 하 여 가상 머신 확장 집합에는, 경우에 가상 머신 확장 집합 인스턴스에 최신 모델로 업그레이드를 확인 합니다.

> [!NOTE]
> 직렬 콘솔을 사용하거나 사용하지 않도록 설정하려면 구독에 대한 쓰기 권한이 있어야 합니다. 이러한 권한에는 관리자 또는 소유자 역할이 포함되지만 이에 국한되지 않습니다. 사용자 지정 역할에는 쓰기 권한도 있을 수 있습니다.

### <a name="subscription-level-disable"></a>구독 수준에서 비활성화
[콘솔 REST API 호출 비활성화](/rest/api/serialconsole/console/disableconsole)를 통해 전체 구독의 직렬 콘솔을 비활성화할 수 있습니다. 이 작업에 대 한 참가자 수준 액세스 필요 이상 구독에 있습니다. API 설명서 페이지에서 지원되는 **사용해 보기** 기능을 사용하여 구독의 직렬 콘솔을 비활성화하거나 활성화할 수 있습니다. **subscriptionId**에 대한 구독 ID를 입력하고, **기본값**에 대한 “기본값”을 입력한 다음, **실행**을 선택합니다. Azure CLI 명령은 아직 지원되지 않습니다.

직렬 콘솔에 대 한 구독을 다시 설정 하려면 사용 합니다 [콘솔을 사용 하도록 설정 REST API 호출](/rest/api/serialconsole/console/enableconsole)합니다.

![REST API 사용해 보기](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

또는 Cloud Shell에서 다음 bash 명령 집합을 사용하여 구독에 대한 직렬 콘솔의 비활성화된 상태를 비활성화하고, 활성화하고, 확인할 수 있습니다.

* 구독의 비활성화된 직렬 콘솔 상태를 가져오려면:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* 구독의 직렬 콘솔을 비활성화하려면:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* 구독의 직렬 콘솔을 활성화하려면:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>직렬 콘솔 보안

### <a name="access-security"></a>액세스 보안
직렬 콘솔에 대한 액세스는 가상 머신에 대해 [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 이상의 액세스 역할을 가지고 있는 사용자로 제한됩니다. Azure Active Directory 테넌트에는 MFA(Multi-Factor Authentication)가 필요한 경우 직렬 콘솔이 [Azure Portal](https://portal.azure.com)을 통해 액세스되었기 때문에 직렬 콘솔에 대한 액세스에는 MFA가 필요합니다.

### <a name="channel-security"></a>채널 보안
주고받는 모든 데이터는 전송 중에 암호화됩니다.

### <a name="audit-logs"></a>감사 로그
직렬 콘솔에 대한 모든 액세스는 현재 가상 머신의 [부트 진단](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) 로그에 기록됩니다. 이러한 로그에 대한 액세스 권한은 Azure 가상 머신 관리자가 소유하며 제어합니다.

> [!CAUTION]
> 콘솔에 대한 액세스 암호는 기록되지 않습니다. 그러나 콘솔 내에서 실행되는 명령이 암호, 비밀, 사용자 이름 또는 기타 형태의 PII(개인 식별 정보)를 포함하거나 출력하는 경우 해당 정보는 VM 부트 진단 로그에 작성됩니다. 또한 직렬 콘솔의 스크롤 백 기능을 구현하는 일부로 모든 다른 표시되는 텍스트와 함께 작성됩니다. 이러한 로그는 순환되며 진단 스토리지 계정에 대한 읽기 권한이 있는 사용자만 액세스할 수 있습니다. 그러나 비밀 및/또는 PII가 포함될 수 있는 항목에 대해 원격 데스크톱을 사용하는 모범 사례를 따르는 것이 좋습니다.

### <a name="concurrent-usage"></a>동시 사용
한 사용자가 직렬 콘솔에 연결되어 있을 때 다른 사용자가 같은 가상 머신에 대한 액세스를 성공적으로 요청한 경우 첫 번째 사용자의 연결이 끊기고 두 번째 사용자가 동일한 세션에 연결됩니다.

> [!CAUTION]
> 그렇다고 연결이 끊어진 사용자가 로그아웃되지는 않습니다. SIGHUP 또는 유사한 메커니즘을 사용하여 연결 해제 시 로그아웃을 강제 적용하는 기능은 아직 계획 중입니다. Windows의 경우 SAC에 자동 시간 제한을 사용하도록 설정되어 있고 Linux의 경우에는 터미널 시간 제한 설정을 구성할 수 있습니다.

## <a name="accessibility"></a>내게 필요한 옵션
접근성은 Azure 직렬 콘솔의 핵심 기능입니다. 이를 위해 마우스를 사용하지 못할 수 있는 사람뿐만 아니라 시력 및 청력 장애가 있는 사람이 직렬 콘솔에 액세스할 수 있도록 했습니다.

### <a name="keyboard-navigation"></a>키보드 탐색
키보드의 **탭** 키를 사용하여 Azure Portal 내의 직렬 콘솔 인터페이스를 탐색합니다. 사용자 위치는 화면에서 강조 표시됩니다. 직렬 콘솔 블레이드 외부로 포커스를 이동하려면 키보드에서 **Ctrl**+**F6** 키를 누릅니다.

### <a name="use-the-serial-console-with-a-screen-reader"></a>화면 reader에서 직렬 콘솔 사용
직렬 콘솔은 화면 reader 지원을 기본 제공합니다. 화면 읽기 프로그램을 켠 상태로 탐색하면 화면 읽기 프로그램은 현재 선택한 단추에 대한 alt 텍스트를 큰 소리로 읽을 수 있습니다.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>직렬 콘솔에 액세스하는 일반적인 시나리오

시나리오          | 직렬 콘솔의 작업
:------------------|:-----------------------------------------
잘못된 방화벽 규칙 | 직렬 콘솔에 액세스하여 Windows 방화벽 규칙을 수정합니다.
파일 시스템 손상/검사 | 직렬 콘솔에 액세스하여 파일 시스템을 복구합니다.
RDP 구성 문제 | 직렬 콘솔에 액세스하여 설정을 변경합니다. 자세한 내용은 [RDP 설명서](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)를 참조하세요.
네트워크 시스템 잠금 | 시스템을 관리하려면 Azure Portal에서 직렬 콘솔에 액세스합니다. 일부 네트워크 명령이 [Windows 명령: CMD 및 PowerShell](serial-console-cmd-ps-commands.md)에 나열돼 있습니다.
부팅 로더와 상호 작용 | 직렬 콘솔을 통해 BCD에 액세스합니다. 자세한 내용은 [직렬 콘솔에서 Windows 부팅 메뉴 사용](#enable-the-windows-boot-menu-in-the-serial-console)을 참조하세요.


## <a name="errors"></a>오류
대부분의 오류는 일시적이므로 연결을 다시 시도하면 해결되는 경우가 많습니다. 다음 표에서 오류 및 두 Vm에 대 한 완화 방법의 목록을 표시 하 고 가상 머신 확장 집합 인스턴스.

오류                            |   해결 방법
:---------------------------------|:--------------------------------------------|
*&lt;VMNAME&gt;* 에 대한 부트 진단 설정을 검색할 수 없습니다. 직렬 콘솔을 사용하려면 부트 진단이 VM에 활성화되어 있는지 확인하세요. | VM에 [부트 진단](boot-diagnostics.md)이 사용되도록 설정되어 있는지 확인합니다.
VM이 중지된 할당 취소 상태입니다. VM을 시작하고 직렬 콘솔 연결을 다시 시도합니다. | 직렬 콘솔에 액세스하려면 가상 머신이 시작된 상태여야 합니다.
직렬 콘솔 VM을 사용하는 데 필요한 권한이 없습니다. Virtual Machine Contributor 역할 이상의 권한이 있는지 확인합니다.| 직렬 콘솔 액세스에는 특정 사용 권한이 필요합니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조하세요.
부트 진단 스토리지 계정인 *&lt;STORAGEACCOUNTNAME&gt;* 에 대한 리소스 그룹을 확인할 수 없습니다. 이 VM에 부트 진단이 활성화되어 있고 저장소 계정에 액세스 권한이 있는지 확인합니다. | 직렬 콘솔 액세스에는 특정 사용 권한이 필요합니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조하세요.
이 VM의 부트 진단 저장소 계정에 액세스할 경우 "사용할 수 없음" 응답이 발생했습니다. | 부트 진단에 계정 방화벽이 없는지 확인하세요. 직렬 콘솔이 작동하려면 액세스 가능한 부트 진단 저장소 계정이 필요합니다.
웹 소켓이 닫혀 있거나 열 수 없습니다. | `*.console.azure.com`을 허용 목록에 추가해야 할 수 있습니다. 더 자세하지만 더 오래 걸리는 방법은 매우 정기적으로 변경되는 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 허용 목록에 추가하는 것입니다.
Windows VM에 연결할 때 상태 정보만 표시됩니다.| Windows 이미지에 Special Administrative Console을 사용하도록 설정하지 않은 경우 이 오류가 발생합니다. Windows VM에서 SAC를 수동으로 설정하는 방법에 대한 정보는 [사용자 지정 또는 이전 이미지에서 직렬 콘솔 사용](#enable-the-serial-console-in-custom-or-older-images)을 참조하세요. 자세한 내용은 [Windows 상태 신호](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)를 참조하세요.

## <a name="known-issues"></a>알려진 문제
직렬 콘솔에 관한 몇 가지 문제를 인식하고 있습니다. 이러한 문제 목록 및 완화 단계는 다음과 같습니다. 두 Vm에 적용 하는 이러한 문제 및 완화 방법 및 가상 머신 확장 집합 인스턴스.

문제                             |   해결 방법
:---------------------------------|:--------------------------------------------|
연결 배너에서 로그인 프롬프트가 표시되지 않으면 **Enter** 키를 누릅니다. | 자세한 내용은 [Enter를 누르면 아무 작업도 수행되지 않습니다](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)를 참조하세요. 이 오류는 Windows가 직렬 포트에 제대로 연결하지 못하게 하는 사용자 지정 VM, 강화된 어플라이언스 또는 부팅 구성을 실행하는 경우 발생할 수 있습니다. Windows 10 클라이언트 VM을 실행하는 경우에도 이 오류가 발생합니다. Windows Server VM만 EMS를 사용하도록 구성되었기 때문입니다.
커널 디버깅을 사용할 수 있으면 SAC 프롬프트에 입력할 수 없습니다. | VM에 RDP하고 관리자 권한 명령 프롬프트에서 `bcdedit /debug {current} off`을 실행합니다. RDP할 수 없는 경우 대신 `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`를 실행하여 데이터 디스크로 연결하는 동안 다른 Azure VM에 OS 디스크를 연결하고 수정한 다음, 다시 디스크를 교체합니다.
원래 콘텐츠에 반복 문자가 있는 경우 SAC의 PowerShell에 붙여 넣으면 세 번째 문자가 나타납니다. | 해결 방법은 `Remove-Module PSReadLine`을 실행하여 현재 세션에서 PSReadLine 모듈을 언로드하는 것입니다. 이 작업으로 모듈이 삭제되거나 제거되지 않습니다.
일부 키보드 입력이 이상한 SAC 출력을 생성합니다(예: **[A**, **[3~** ). | [VT100](https://aka.ms/vtsequences) 이스케이프 시퀀스는 SAC 프롬프트에서 지원되지 않습니다.
긴 문자열을 붙여넣는 작업이 작동하지 않습니다. | 직렬 콘솔은 터미널에 붙여넣는 문자열의 길이를 2048자로 제한하여 직렬 포트 대역폭을 오버로드하지 않도록 방지합니다.
직렬 콘솔은 스토리지 계정 방화벽에서 작동하지 않습니다. | 직렬 콘솔은 기본적으로 부트 진단 스토리지 계정에서 사용하도록 설정된 스토리지 계정 방화벽과 함께 작동할 수 없습니다.
직렬 콘솔 계층적 네임 스페이스를 사용 하 여 Azure Data Lake 저장소 Gen2를 사용 하 여 저장소 계정을 사용 하 여 작동 하지 않습니다. | 계층적 네임 스페이스를 사용 하 여 알려진된 문제입니다. 를 완화 하기 위해 VM의 부트 진단 저장소 계정이 생성 되지 않도록 Azure Data Lake 저장소 Gen2를 사용 하 여 확인 합니다. 이 옵션은 저장소 계정 만들 때만 설정할 수 있습니다. 이 문제를 완화 하려면 사용 하도록 설정 하는 Azure Data Lake 저장소 Gen2 없이 별도 부트 진단 저장소 계정을 만들려면 해야 합니다.


## <a name="frequently-asked-questions"></a>질문과 대답

**Q. 피드백을 보내려면 어떻게 해야 하나요?**

1. [https://aka.ms/serialconsolefeedback](https://aka.ms/serialconsolefeedback) 에서 GitHub 문제를 만들어 피드백을 제공해주세요. 또는 (대안으로) azserialhelp@microsoft.com 을 통해 또는 https://feedback.azure.com 의 가상 머신 범주에 피드백을 보낼 수 있습니다.

**Q. 직렬 콘솔이 복사/붙여넣기를 지원하나요?**

a. 예. **Ctrl**+**Shift**+**C** 및 **Ctrl**+**Shift**+**V**를 사용하여 복사하고 터미널에 붙여넣습니다.

**Q. 내 구독에 직렬 콘솔을 사용하거나 사용하지 않도록 설정할 수 있나요?**

1. 전체 구독 수준에서 직렬 콘솔을 사용하거나 사용하지 않도록 설정하려면 구독에 대한 쓰기 권한이 있어야 합니다. 쓰기 권한이 있는 역할에는 관리자 또는 소유자 역할이 포함됩니다. 사용자 지정 역할에는 쓰기 권한도 있을 수 있습니다.

**Q. 내 VM의 직렬 콘솔에 액세스할 수 있는 사용자는 누구인가요?**

a. VM의 직렬 콘솔에 액세스하려면 VM의 Virtual Machine Contributor 역할 이상이 있어야 합니다.

**Q. 내 직렬 콘솔에 아무 것도 표시되지 않으면 어떻게 하나요?**

a. 사용자의 이미지가 직렬 콘솔 액세스에 대해 잘못 구성되었습니다. 직렬 콘솔을 사용하도록 이미지를 구성하는 방법에 대한 자세한 내용은 [사용자 지정 또는 이전 이미지에서 직렬 콘솔 사용](#enable-the-serial-console-in-custom-or-older-images)을 참조하세요.

**Q. 가상 머신 확장 집합에 대해 직렬 콘솔을 사용할 수 있나요?**

a. 현재는 가상 머신 확장 집합 인스턴스의 직렬 콘솔에 대한 액세스가 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
* Windows SAC에서 사용할 수 있는 CMD 및 PowerShell 명령에 대한 심층 가이드는 [Windows 명령: CMD 및 PowerShell](serial-console-cmd-ps-commands.md)을 참조하세요.
* 이 직렬 콘솔을 [Linux](serial-console-linux.md) VM에서도 사용할 수 있습니다.
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아봅니다.
