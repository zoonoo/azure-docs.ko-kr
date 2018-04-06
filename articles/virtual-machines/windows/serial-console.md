---
title: Azure 가상 머신 직렬 콘솔 | Microsoft Docs
description: Azure 가상 머신용 양방향 직렬 콘솔입니다.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>가상 머신 직렬 콘솔(미리 보기) 


Azure의 가상 머신 직렬 콘솔은 Linux 및 Windows 가상 머신용 텍스트 기반 콘솔에 대한 액세스를 제공합니다. 직렬 연결은 가상 머신의 COM1 직렬 포트에 연결되며, 가상 머신에 대한 액세스를 제공하고 가상 머신의 네트워크/운영 체제 상태와 관련이 없습니다. 가상 머신의 직렬 콘솔에 대한 액세스는 현재 Azure Portal을 통해서만 가능하며 가상 머신에 대해 VM 참가자 이상의 액세스 권한이 있는 사용자에게만 허용됩니다. 

> [!Note] 
> 사용 약관에 동의하게 되면 미리 보기를 사용할 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관]을 참조하세요. (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 현재 이 서비스는 **공개 미리 보기** 상태이며 가상 머신의 직렬 콘솔에 대한 액세스는 글로벌 Azure 지역에서 사용할 수 있습니다.) 현재 Azure Government, Azure 독일 및 Azure 중국 클라우드에서는 직렬 콘솔을 사용할 수 없습니다.

 

## <a name="prerequisites"></a>필수 조건 

* 가상 머신에 [부트 진단](boot-diagnostics.md)을 사용하도록 설정되어 있어야 합니다. 
* 직렬 콘솔을 사용하는 계정에는 VM에 대한 [참가자 역할](../../active-directory/role-based-access-built-in-roles.md)과 [부트 진단](boot-diagnostics.md) 저장소 계정이 있어야 합니다. 

## <a name="open-the-serial-console"></a>직렬 콘솔 열기
가상 머신의 직렬 콘솔은 [Azure Portal](https://portal.azure.com)을 통해서만 액세스할 수 있습니다. 다음은 포털을 통해 가상 머신의 직렬 콘솔에 액세스하는 단계입니다. 

  1. Azure 포털 열기
  2. 왼쪽 메뉴에서 가상 머신을 선택합니다.
  3. 목록에서 VM을 클릭합니다. VM에 대한 개요 페이지가 열립니다.
  4. 지원 및 문제 해결 섹션까지 아래로 스크롤하여 직렬 콘솔(미리 보기) 옵션을 클릭합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>기능 사용 중지
특정 VM의 부트 진단 설정을 비활성화하여 VM의 직렬 콘솔 기능을 비활성화할 수 있습니다.

## <a name="serial-console-security"></a>직렬 콘솔 보안 

### <a name="access-security"></a>액세스 보안 
직렬 콘솔에 대한 액세스는 가상 머신에 대해 [VM 참가자](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) 이상의 액세스가 있는 사용자로 제한됩니다. AAD 테넌트에 Multi-Factor Authentication이 필요한 경우에는 직렬 콘솔에 대한 액세스 권한에도 MFA가 필요합니다. 해당 액세스가 [Azure Portal](https://portal.azure.com)을 통해 진행되기 때문입니다.

### <a name="channel-security"></a>채널 보안
주고 받는 모든 데이터는 전송 중에 암호화됩니다.

### <a name="audit-logs"></a>감사 로그
직렬 콘솔에 대한 모든 액세스는 현재 가상 머신의 [부트 진단](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) 로그에 기록됩니다. 이러한 로그에 대한 액세스 권한은 Azure 가상 머신 관리자가 소유하며 제어합니다.  

>[!CAUTION] 
콘솔에 대한 액세스 암호가 기록되지 않은 상태에서 콘솔 내에서 실행되는 명령에 암호, 비밀, 사용자 이름 또는 기타 다른 형태의 PII(개인 식별 정보)가 포함되거나 출력되면, 표시되는 다른 모든 텍스트와 함께 해당 정보가 가상 머신 부트 진단 로그에 기록됩니다. 이것은 직렬 콘솔에 구현된 스크롤백 기능의 일부입니다. 이러한 로그는 순환되며 진단 저장소 계정에 읽기 권한이 있는 사용자만 액세스할 수 있습니다. 하지만, 비밀 및/또는 PII가 포함될 수 있는 모든 작업에 대해서는 원격 데스크톱을 사용하는 것이 가장 좋습니다. 

### <a name="concurrent-usage"></a>동시 사용
한 사용자가 직렬 콘솔에 연결되어 있을 때 다른 사용자가 같은 가상 머신에 대한 액세스를 성공적으로 요청한 경우에는, 첫 번째 사용자의 연결이 끊기고 두 번째 사용자가 연결됩니다. 이것은 첫 번째 사용자가 자리에서 일어나서 물리적 콘솔을 떠나고 새로운 사용자가 자리에 앉는 것과 유사한 방식입니다.

>[!CAUTION] 
즉, 연결이 끊긴 사용자는 로그아웃되지 않습니다. 연결 해제 시 로그아웃을 강제 적용하는 기능은(SIGHUP 또는 유사한 메커니즘을 통해) 아직 계획 중입니다. Windows의 경우 SAC에 자동 시간 제한을 사용하도록 설정되어 있지만 Linux에서는 터미널 시간 제한 설정을 구성할 수 있습니다. 


## <a name="accessing-serial-console-for-windows"></a>Windows의 직렬 콘솔에 액세스 
Azure의 최신 Windows Server 이미지에는 기본적으로 SAC([Special Administrative Console](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx))가 사용되도록 설정되어 있습니다. SAC는 서버 버전의 Windows에서 지원되지만, 클라이언트 버전(예: Windows 10, Windows 8 또는 Windows 7)에서는 사용할 수 없습니다. Feb2018 이하의 이미지를 사용하여 만든 Windows 가상 머신용 직렬 콘솔을 사용하도록 설정하려면 다음 단계를 수행하세요. 

1. 원격 데스크톱을 통해 Windows 가상 머신에 연결
2. 관리자 명령 프롬프트에서 다음 명령 실행 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. 시스템을 다시 부팅하여 SAC 콘솔을 사용하도록 설정

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

필요한 경우 오프라인에서도 SAC를 사용하도록 설정할 수 있습니다. 

1. SAC를 데이터 디스크용으로 구성하려는 Windows 디스크를 기존 VM에 연결 
2. 관리자 명령 프롬프트에서 다음 명령 실행 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>SAC이 사용되도록 설정되어 있는지를 확인하는 방법 

[SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)이 사용되도록 설정되어 있지 않으면 직렬 콘솔에 SAC 프롬프트가 표시되지 않습니다. 경우에 따라 VM 상태 정보가 표시될 수도 있고 비어 있을 수도 있습니다.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>부팅 메뉴를 사용하도록 설정하여 직렬 콘솔에 표시 

Windows 부팅 로더 프롬프트를 사용하도록 설정하여 직렬 콘솔에 표시해야 할 경우 Windows 부팅 로더에 대해 다음과 같은 추가 옵션을 추가할 수 있습니다.

1. 원격 데스크톱을 통해 Windows 가상 머신에 연결
2. 관리자 명령 프롬프트에서 다음 명령 실행 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. 시스템을 다시 부팅하여 부팅 메뉴를 사용하도록 설정

> [!NOTE] 
> 지금은 함수 키에 대한 지원이 사용되도록 설정되어 있지 않습니다. 고급 부팅 옵션이 필요한 경우 bcdedit /set {current} onetimeadvancedoptions on을 사용하세요. 자세한 내용은 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)을 참조하세요.

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>Windows 직렬 콘솔에 액세스하는 일반적인 시나리오 
시나리오          | 직렬 콘솔의 작업                
:------------------|:----------------------------------------
잘못된 방화벽 규칙 | 직렬 콘솔에 액세스하여 iptables 또는 Windows 방화벽 규칙 수정 
파일 시스템 손상/검사 | SAC CMD에 로그인한 후 직렬 콘솔에 액세스하고 파일 시스템 복구
RDP 구성 문제 | 직렬 콘솔에 액세스하고 cmd 채널에 로그인합니다. 터미널 서비스의 상태를 확인하고 필요한 경우 다시 시작합니다.
네트워크 시스템 잠금| 직렬 콘솔에 액세스하고 cmd 채널에 로그인합니다. [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts) 명령줄로 방화벽 상태를 확인합니다. 

## <a name="errors"></a>오류
대부분의 오류는 본래 일시적이며 연결을 다시 시도하면 문제가 해결됩니다. 아래 표에는 오류 및 해결 방법 목록이 있습니다. 

오류                            |   해결 방법 
:---------------------------------|:--------------------------------------------|
'<VMNAME>'에 대한 부트 진단 설정을 검색할 수 없습니다. 직렬 콘솔을 사용하려면 부트 진단이 VM에 활성화되어 있는지 확인하세요. | VM에 [부트 진단](boot-diagnostics.md)이 사용되도록 설정되어 있는지 확인합니다. 
VM이 중지된 할당 취소 상태입니다. VM을 시작하고 직렬 콘솔 연결을 다시 시도합니다. | 직렬 콘솔에 액세스하려면 가상 머신이 시작된 상태여야 합니다.
직렬 콘솔 VM을 사용하는 데 필요한 권한이 없습니다. VM 참가자 역할 이상의 권한이 있는지 확인합니다.| 직렬 콘솔 액세스에는 특정 권한이 필요합니다. 자세한 내용은 [액세스 요구 사항](#prerequisites)을 참조하세요.
부트 진단 저장소 계정인 '<STORAGEACCOUNTNAME>'에 대한 리소스 그룹을 확인할 수 없습니다. 이 VM에 부트 진단이 활성화되어 있고 저장소 계정에 액세스 권한이 있는지 확인합니다. | 직렬 콘솔 액세스에는 특정 권한이 필요합니다. 자세한 내용은 [액세스 요구 사항](#prerequisites)을 참조하세요.

## <a name="known-issues"></a>알려진 문제 
직렬 콘솔 액세스는 아직 미리 보기 단계이며, 알려진 문제를 해결하기 위해 노력하고 있습니다. 아래에는 이러한 문제가 가능한 해결 방법이 나열되어 있습니다. 

문제                           |   해결 방법 
:---------------------------------|:--------------------------------------------|
가상 머신 확장 집합 인스턴스 직렬 콘솔에 옵션이 없습니다. | 미리 보기 단계에서는 가상 시스템 확장 집합 인스턴스의 직렬 콘솔에 대한 액세스가 지원되지 않습니다.
연결 배너가 표시된 후 Enter를 눌러도 로그인 프롬프트가 표시되지 않습니다. | [Enter를 누르면 아무 작업도 수행되지 않습니다.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Windows VM에 연결할 때 상태 정보만 표시됩니다.| [Windows 상태 신호](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>질문과 대답 
**Q. 피드백을 보내려면 어떻게 해야 하나요?**

a. https://aka.ms/serialconsolefeedback으로 이동하여 피드백을 제공해 주세요. 또는(대안으로) azserialhelp@microsoft.com을 통해서 또는 http://feedback.azure.com의 가상 머신 범주에 피드백을 보내주세요. 

**Q. “기존 콘솔의 OS 유형 Windows가 요청된 Linux의 OS 유형과 충돌됩니다.”라는 오류가 발생합니다.**.

a. 이것은 알려진 문제이며, 해결하려면 bash 모드에서 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)을 열고 다시 시도하세요.

**Q. 직렬 콘솔에 액세스할 수 없습니다. 지원 사례를 어디에서 제출할 수 있나요?**

a. 이 미리 보기 기능은 Azure 미리 보기 약관을 통해 제공됩니다. 이 기능에 대한 지원은 위에 언급된 채널을 통해 처리하는 것이 가장 좋습니다. 

## <a name="next-steps"></a>다음 단계
* [Linux](../linux/serial-console.md) VM에서도 직렬 콘솔 사용 가능
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아보기
