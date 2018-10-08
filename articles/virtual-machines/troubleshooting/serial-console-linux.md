---
title: Azure 가상 머신 직렬 콘솔 | Microsoft Docs
description: Azure 가상 머신용 양방향 직렬 콘솔입니다.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/11/2018
ms.author: harijay
ms.openlocfilehash: 642bf03ecef7f6db25c51671635d96ef7baed91a
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47412031"
---
# <a name="virtual-machine-serial-console"></a>Virtual Machine 직렬 콘솔


Azure의 가상 머신 직렬 콘솔은 Linux 가상 머신용 텍스트 기반 콘솔에 대한 액세스를 제공합니다. 직렬 연결은 가상 머신의 COM1 직렬 포트에 연결되며, 가상 머신의 네트워크 또는 운영 체제 상태와는 관련이 없는 가상 머신에 대한 액세스를 제공합니다. 가상 머신의 직렬 콘솔에 대한 액세스는 현재 Azure Portal을 통해서만 가능하며 가상 머신에 대해 VM 참가자 이상의 액세스 권한이 있는 사용자에게만 허용됩니다. 

Windows VM에 대한 직렬 콘솔 설명서를 보려면 [여기를 클릭](../windows/serial-console.md)하세요.

> [!Note] 
> 가상 머신용 직렬 콘솔은 일반적으로 글로벌 Azure 지역에서 사용할 수 있습니다. 아직 Azure Government 또는 Azure 중국 클라우드에서는 직렬 콘솔을 사용할 수 없습니다.


## <a name="prerequisites"></a>필수 조건 

* 리소스 관리 배포 모델을 사용해야 합니다. 클래식 배포는 지원되지 않습니다. 
* 가상 머신에 [부트 진단](boot-diagnostics.md)을 사용하도록 설정되어 있어야 합니다. 아래 스크린샷을 참조하세요.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* 직렬 콘솔을 사용하는 Azure 계정에는 VM에 대한 [기여자 역할](../../role-based-access-control/built-in-roles.md)과 [부트 진단](boot-diagnostics.md) 저장소 계정이 있어야 합니다. 
* 직렬 콘솔에 액세스하는 가상 머신에도 암호 기반 계정이 있어야 합니다. VM 액세스 확장의 [암호 재설정](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) 기능으로 이러한 계정을 만들 수 있습니다. 아래 스크린샷을 참조하세요.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Linux 배포판 설정에 대한 자세한 내용은 [Linux용 직렬 콘솔 액세스](#Serial-Console-Linux-distro-availability)를 참조하세요.



## <a name="get-started-with-serial-console"></a>직렬 콘솔 시작
가상 머신의 직렬 콘솔은 [Azure Portal](https://portal.azure.com)을 통해서만 액세스할 수 있습니다. 다음은 포털을 통해 가상 머신의 직렬 콘솔에 액세스하는 단계입니다. 

  1. Azure 포털 열기
  1. (VM에 암호 인증을 사용하는 사용자가 있는 경우 이 단계를 건너뜁니다) "암호 다시 설정" 블레이드를 클릭하여 사용자 이름/암호 인증을 통해 사용자 추가
  1. 왼쪽 메뉴에서 가상 머신을 선택합니다.
  1. 목록에서 VM을 클릭합니다. VM에 대한 개요 페이지가 열립니다.
  1. 지원 및 문제 해결 섹션까지 아래로 스크롤하여 “직렬 콘솔” 옵션을 클릭합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

![](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### 

> [!NOTE] 
> 직렬 콘솔에는 암호가 구성된 로컬 사용자가 필요합니다. 현재 SSH 공개 키로 구성된 VM만 직렬 콘솔에 로그인할 수 없습니다. 암호가 구성된 로컬 사용자를 만들려면 "암호 재설정"을 클릭하여 포털에서 사용 가능한 [VM Access Extension](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)을 포털에서 사용하고 암호가 구성된 로컬 사용자를 만드세요.
> [단일 사용자 모드로 전환하려면GRUB을 사용](./serial-console-grub-single-user-mode.md)하여 계정에서 관리자 암호를 재설정할 수도 있습니다.

## <a name="serial-console-linux-distro-availability"></a>직렬 콘솔 Linux 배포판 가용성
직렬 콘솔이 제대로 작동하려면 게스트 운영 체제가 콘솔 메시지를 읽고 직렬 포트에 쓰도록 구성되어야 합니다. 대부분의 [Azure Linux 보증 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)에는 직렬 콘솔이 기본적으로 구성되어 있습니다. Azure Portal에서 직렬 콘솔 섹션을 클릭하기만 하면 콘솔에 액세스할 수 있습니다. 

배포판      | 직렬 콘솔 액세스
:-----------|:---------------------
Red Hat Enterprise Linux    | Azure에서 사용 가능한 Red Hat Enterprise Linux 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. 
CentOS      | Azure에서 사용 가능한 CentOS 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. 
Ubuntu      | Azure에서 사용 가능한 Ubuntu 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다.
CoreOS      | Azure에서 사용 가능한 CoreOS 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다.
SUSE        | Azure에서 사용 가능한 최신 SLES 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다. Azure에서 이전 버전의 SLES(10 이하)를 사용하는 경우[기술 자료 문서](https://www.novell.com/support/kb/doc.php?id=3456486)를 참고하여 직렬 콘솔을 사용하도록 설정합니다. 
Oracle Linux        | Azure에서 사용 가능한 Oracle Linux 이미지는 콘솔 액세스가 기본적으로 활성화되어 있습니다.
사용자 지정 Linux 이미지     | 사용자 지정 Linux VM 이미지에 대해 직렬 콘솔을 사용하도록 설정하려면 `/etc/inittab`에서 콘솔 액세스를 사용하도록 설정하여 `ttyS0`에서 터미널을 실행합니다. 다음은 inittab 파일에 이를 추가하는 예제입니다. `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. 사용자 지정 이미지를 올바르게 만드는 방법에 대한 자세한 내용은 [Azure에서 Linux VHD 생성 및 업로드](https://aka.ms/createuploadvhd)를 참조하세요. 사용자 지정 커널을 빌드하는 경우 사용하도록 설정을 고려할 일부 커널 플래그는 `CONFIG_SERIAL_8250=y` 및 `CONFIG_MAGIC_SYSRQ_SERIAL=y`입니다. 구성 파일은 종종 추가 탐색을 위해 /boot/ 아래에 있습니다.

## <a name="common-scenarios-for-accessing-serial-console"></a>직렬 콘솔에 액세스하는 일반적인 시나리오 
시나리오          | 직렬 콘솔의 작업                
:------------------|:-----------------------------------------
FSTAB 파일 손상 | `Enter` 키를 입력하여 계속하고 텍스트 편집기를 사용하여 fstab 파일을 수정합니다. 이 작업을 수행하려면 단일 사용자 모드에 있어야 합니다. 시작하려면 [fstab 문제 해결 방법](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) 및 [직렬 콘솔을 사용하여 GRUB 및 단일 사용자 모드 액세스](serial-console-grub-single-user-mode.md)를 참조하세요.
잘못된 방화벽 규칙 | 직렬 콘솔에 액세스하고 iptable을 수정합니다. 
파일 시스템 손상/검사 | 직렬 콘솔에 액세스하여 파일 시스템을 복구합니다. 
SSH/RDP 구성 문제 | 직렬 콘솔에 액세스하고 설정을 변경합니다. 
네트워크 시스템 잠금| 포털을 통해 직렬 콘솔에 액세스하여 시스템을 관리합니다. 
부팅 로더와 상호 작용 | 직렬 콘솔을 통해 GRUB에 액세스합니다. 시작하려면 [직렬 콘솔을 사용하여 GRUB 및 단일 사용자 모드 액세스](serial-console-grub-single-user-mode.md)로 이동하세요. 

## <a name="disable-serial-console"></a>직렬 콘솔 비활성화
기본적으로 모든 구독에는 모든 VM에 대한 직렬 콘솔 액세스가 활성화되어 있습니다. 구독 수준 또는 VM 수준에서 직렬 콘솔을 비활성화할 수 있습니다.

> [!Note] 
> 직렬 콘솔을 사용을 사용하거나 사용하지 않도록 설정하려면 구독에 대한 쓰기 권한이 있어야 합니다. 이 권한에는 관리자 또는 소유자 역할이 포함되지만 이에 국한되지 않습니다. 사용자 지정 역할에는 쓰기 권한도 있을 수 있습니다.

### <a name="subscription-level-disable"></a>구독 수준에서 비활성화
[콘솔 REST API 호출 비활성화](https://aka.ms/disableserialconsoleapi)를 통해 전체 구독의 직렬 콘솔을 비활성화할 수 있습니다. API 설명서 페이지에 있는 "사용해 보기" 기능을 사용하여 구독의 직렬 콘솔을 비활성화하거나 활성화할 수 있습니다. `default` 필드에 `subscriptionId`, "default"를 입력하고 실행을 클릭하세요. Azure CLI 명령은 아직 사용할 수 없으며 나중에 제공될 예정입니다. [여기에서 REST API 호출을 사용해 보세요](https://aka.ms/disableserialconsoleapi).

![](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

또는 Cloud Shell에서 아래의 명령 집합(아래에 표시된 bash 명령)을 사용하여 구독의 직렬 콘솔을 비활성화, 활성화하고 비활성화된 상태를 볼 수 있습니다. 

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

### <a name="vm-level-disable"></a>VM 수준에서 비활성화
특정 VM의 부트 진단 설정을 비활성화하여 VM의 직렬 콘솔을 비활성화할 수 있습니다. Azure Portal에서 부트 진단을 끄기만 하면 VM의 직렬 콘솔이 비활성화됩니다.

## <a name="serial-console-security"></a>직렬 콘솔 보안 

### <a name="access-security"></a>액세스 보안 
직렬 콘솔에 대한 액세스는 가상 머신에 대해 [VM 참가자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 이상의 액세스가 있는 사용자로 제한됩니다. AAD 테넌트에 Multi-Factor Authentication이 필요한 경우에는 직렬 콘솔에 대한 액세스 권한에도 MFA가 필요합니다. 해당 액세스가 [Azure Portal](https://portal.azure.com)을 통해 진행되기 때문입니다.

### <a name="channel-security"></a>채널 보안
주고받는 모든 데이터는 전송 중에 암호화됩니다.

### <a name="audit-logs"></a>감사 로그
직렬 콘솔에 대한 모든 액세스는 현재 가상 머신의 [부트 진단](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) 로그에 기록됩니다. 이러한 로그에 대한 액세스 권한은 Azure 가상 머신 관리자가 소유하며 제어합니다.  

>[!CAUTION] 
콘솔에 대한 액세스 암호가 기록되지 않은 상태에서 콘솔 내에서 실행되는 명령에 암호, 비밀, 사용자 이름 또는 기타 다른 형태의 PII(개인 식별 정보)가 포함되거나 출력되면, 표시되는 다른 모든 텍스트와 함께 해당 정보가 가상 머신 부트 진단 로그에 기록됩니다. 이것은 직렬 콘솔에 구현된 스크롤백 기능의 일부입니다. 이러한 로그는 순환되며 진단 저장소 계정에 읽기 권한이 있는 사용자만 액세스할 수 있습니다. 하지만, 비밀 및/또는 PII가 포함될 수 있는 모든 작업에 대해서는 SSH 콘솔을 사용하는 것이 가장 좋습니다. 

### <a name="concurrent-usage"></a>동시 사용
한 사용자가 직렬 콘솔에 연결되어 있을 때 다른 사용자가 같은 가상 머신에 대한 액세스를 성공적으로 요청한 경우에는, 첫 번째 사용자의 연결이 끊기고 두 번째 사용자가 연결됩니다. 이것은 첫 번째 사용자가 자리에서 일어나서 물리적 콘솔을 떠나고 새로운 사용자가 자리에 앉는 것과 유사한 방식입니다.

>[!CAUTION] 
즉, 연결이 끊긴 사용자는 로그아웃되지 않습니다. 연결 해제 시 로그아웃을 강제 적용하는 기능은(SIGHUP 또는 유사한 메커니즘을 통해) 아직 계획 중입니다. Windows의 경우 SAC에 자동 시간 제한을 사용하도록 설정되어 있지만 Linux에서는 터미널 시간 제한 설정을 구성할 수 있습니다. 이렇게 하려면 콘솔에 로그인할 때 사용한 사용자의 .bash_profile 또는 .profile에 `export TMOUT=600`을 추가하여 10분 후에 세션 시간이 초과되도록 합니다.

## <a name="accessibility"></a>접근성
접근성은 Azure 직렬 콘솔의 핵심 기능입니다. 이를 위해 마우스를 사용하지 못할 수 있는 사용자 뿐만 아니라 시력 및 청력 장애가 있는 사용자가 직렬 콘솔에 액세스할 수 있도록 했습니다.

### <a name="keyboard-navigation"></a>키보드 탐색
키보드의 `tab` 키를 사용하여 Azure Portal 내의 직렬 콘솔 인터페이스를 탐색합니다. 사용자 위치는 화면에서 강조 표시됩니다. 직렬 콘솔 블레이드 외부로 포커스를 이동하려면 키보드에서 `Ctrl + F6`를 누릅니다.

### <a name="use-serial-console-with-a-screen-reader"></a>화면 읽기 프로그램에서 직렬 콘솔 사용
직렬 콘솔은 기본적으로 화면 읽기 프로그램을 지원합니다. 화면 읽기 프로그램을 켠 상태로 탐색하면 화면 읽기 프로그램은 현재 선택한 단추에 대한 alt 텍스트를 큰 소리로 읽을 수 있습니다.

## <a name="errors"></a>오류
대부분의 오류는 본래 일시적이며 직렬 콘솔에 연결을 다시 시도하면 해결되는 경우가 많습니다. 아래 테이블에는 오류 및 해결 방법 목록이 있습니다.

오류                            |   해결 방법 
:---------------------------------|:--------------------------------------------|
'<VMNAME>'에 대한 부트 진단 설정을 검색할 수 없습니다. 직렬 콘솔을 사용하려면 부트 진단이 VM에 활성화되어 있는지 확인하세요. | VM에 [부트 진단](boot-diagnostics.md)이 사용되도록 설정되어 있는지 확인합니다. 
VM이 중지된 할당 취소 상태입니다. VM을 시작하고 직렬 콘솔 연결을 다시 시도합니다. | 직렬 콘솔에 액세스하려면 가상 머신이 시작된 상태여야 합니다.
직렬 콘솔 VM을 사용하는 데 필요한 권한이 없습니다. VM 참가자 역할 이상의 권한이 있는지 확인합니다.| 직렬 콘솔 액세스에는 특정 권한이 필요합니다. 자세한 내용은 [액세스 요구 사항](#prerequisites)을 참조하세요.
부트 진단 저장소 계정인 '<STORAGEACCOUNTNAME>'에 대한 리소스 그룹을 확인할 수 없습니다. 이 VM에 부트 진단이 활성화되어 있고 저장소 계정에 액세스 권한이 있는지 확인합니다. | 직렬 콘솔 액세스에는 특정 권한이 필요합니다. 자세한 내용은 [액세스 요구 사항](#prerequisites)을 참조하세요.
웹 소켓이 닫혀 있거나 열 수 없습니다. | `*.console.azure.com`을 허용 목록에 추가해야 할 수 있습니다. 더 자세하지만 더 오래 걸리는 방법은 매우 정기적으로 변경되는 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/en-us/download/details.aspx?id=41653)를 허용 목록에 추가하는 것입니다.
이 VM의 부트 진단 저장소 계정에 액세스할 때 '사용할 수 없음' 응답이 발생했습니다. | 부트 진단에 계정 방화벽이 없는지 확인하세요. 직렬 콘솔이 작동하려면 액세스 가능한 부트 진단 저장소 계정이 필요합니다.

## <a name="known-issues"></a>알려진 문제 
직렬 콘솔을 통해 몇 가지 문제를 인식하고 있습니다. 이러한 문제 및 해결 방법 단계 목록은 다음과 같습니다.

문제                           |   해결 방법 
:---------------------------------|:--------------------------------------------|
연결 배너가 표시된 후 Enter를 눌러도 로그인 프롬프트가 표시되지 않습니다. | [Enter를 누르면 아무 작업도 수행되지 않습니다.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) 페이지를 참조하세요. 이런 문제는 Linux가 직렬 포트에 제대로 연결하지 못하게 하는 사용자 지정 VM, 강화된 어플라이언스 또는 GRUB 구성을 실행하는 경우 발생할 수 있습니다.
직렬 콘솔 텍스트는 화면 크기의 일부만 차지합니다(종종 텍스트 편집기를 사용한 이후). | 직렬 콘솔은 창 크기([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt))에 대한 협상을 지원하지 않습니다. 즉, 화면 크기를 업데이트하기 위해 전송된 SIGWINCH 신호가 없으며, VM은 사용자 터미널 크기를 모른다는 것입니다. ‘크기 조정’ 명령을 제공하는 xterm 또는 기타 유사한 유틸리티를 설치하는 것이 좋습니다. '크기 조정'을 실행하면 이 문제가 해결됩니다.
매우 긴 문자열은 붙여넣기가 작동하지 않음 | 직렬 콘솔은 터미널에 붙여넣는 문자열의 길이를 2048자로 제한합니다. 이 제한은 직렬 포트 대역폭의 과부하를 방지하기 위함입니다.


## <a name="frequently-asked-questions"></a>질문과 대답 
**Q. 피드백을 보내려면 어떻게 해야 하나요?**

a. 으로 이동하여 피드백을 제공해 주세요 https://aka.ms/serialconsolefeedback. 또는(대안으로) azserialhelp@microsoft.com을 통해 또는 http://feedback.azure.com의 가상 머신 범주에 피드백을 보냄

**Q. 직렬 콘솔이 복사/붙여넣기를 지원하나요?**

a. 예 지원합니다. Ctrl + Shift + C 및 Ctrl + Shift + V를 사용하여 터미널에 복사하고 붙여넣습니다.

**Q. SSH 연결 대신 직렬 콘솔을 사용할 수 있나요?**

a. 기술적으로는 가능해 보일 수 있지만, 직렬 콘솔은 SSH를 통한 연결이 가능한 상황에서 문제 해결 도구로 주로 사용하도록 디자인되었습니다. 다음 두 가지 이유 때문에 직렬 콘솔을 SSH 대신 사용하는 것이 좋습니다.

1. 직렬 콘솔은 SSH 만큼 대역폭을 제공하지 않습니다. 직렬 콘솔은 텍스트 전용 연결이므로 직렬 콘솔에서는 GUI가 더 많이 사용되는 상호 작업이 어렵습니다.
1. 직렬 콘솔 액세스는 현재 사용자 이름 및 암호를 통해서만 진행됩니다. SSH 키는 사용자 이름/암호 조합보다 훨씬 더 안전하므로, 로그인 보안 관점에서는 직렬 콘솔보다 SSH가 더 권장됩니다.

**Q. 내 구독에 대한 직렬 콘솔을 사용하거나 사용하지 않도록 설정할 수 있나요?**

a. 전체 구독 수준에서 직렬 콘솔을 사용을 사용하거나 사용하지 않도록 설정하려면 구독에 대한 쓰기 권한이 있어야 합니다. 쓰기 권한이 있는 역할에는 관리자 또는 소유자 역할이 포함되지만 이에 국한되지 않습니다. 사용자 지정 역할에는 쓰기 권한도 있을 수 있습니다.

**Q. 내 VM의 직렬 콘솔에 액세스할 수 있나요?**

a. VM의 직렬 콘솔에 액세스하려면 VM에 대한 기여자 수준 이상의 액세스 권한이 있어야 합니다. 

**Q. 내 직렬 콘솔에 아무 것도 표시되지 않으면 어떻게 하나요?**

a. 사용자의 이미지가 직렬 콘솔 액세스에 대해 잘못 구성됐습니다. 직렬 콘솔을 사용하도록 설정하기 위해 사용자 이미지를 구성하는 방법에 대한 자세한 내용은 [Linux용 액세스 직렬 콘솔](#Access-Serial-Console-for-Linux)을 참조하세요.

**Q. Virtual Machine Scale Sets에 대해 직렬 콘솔을 사용할 수 있나요?**

a. 현재는 가상 머신 확장 집합 인스턴스의 직렬 콘솔에 대한 액세스가 지원되지 않습니다.

**Q. SSH 키 인증만을 사용하여 VM을 설치한 경우 여전히 직렬 콘솔을 사용하여 VM에 연결할 수 있나요?** A: 예. 직렬 콘솔에 SSH 키가 필요하지 않으므로 사용자 이름/암호 조합을 설정하는 수밖에 없습니다. 이 작업을 수행하려면 포털에서 "암호 재설정" 블레이드를 사용하고 직렬 콘솔에 로그인하기 위해 해당 자격 증명을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 직렬 콘솔을 사용하여 [GRUB로 부팅하고 단일 사용자 모드로 전환](serial-console-grub-single-user-mode.md)
* [NMI 및 SysRq 호출](serial-console-nmi-sysrq.md)에 직렬 콘솔 사용
* [Windows](../windows/serial-console.md) VM에서도 직렬 콘솔 사용 가능
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아보기