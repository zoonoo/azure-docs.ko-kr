---
title: PowerShell 모듈 Windows 가상 데스크톱-Azure
description: Windows 가상 데스크톱용 PowerShell 모듈을 설치 하 고 설정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 54501e7e00ba8a28dd7cb421232b9a9587604338
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653125"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 PowerShell 모듈 설정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows 가상 데스크톱 개체를 사용 하 여 스프링 2020 업데이트에 적용 됩니다.
>
> Windows 가상 데스크톱 스프링 2020 업데이트는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows 가상 데스크톱 PowerShell 모듈은 Azure PowerShell 모듈에 통합 되어 있습니다. 이 문서에서는 Windows 가상 데스크톱에 대 한 cmdlet을 실행할 수 있도록 PowerShell 모듈을 설정 하는 방법을 설명 합니다.

## <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정

모듈 사용을 시작 하려면 먼저 [최신 버전의 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)를 설치 합니다. Windows 가상 데스크톱 cmdlet은 현재 PowerShell Core 에서만 작동 합니다.

다음에는 PowerShell 세션에서 사용할 DesktopVirtualization 모듈을 설치 해야 합니다.

관리자 모드에서 다음 PowerShell cmdlet을 실행 하 여 모듈을 설치 합니다.

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> 이 cmdlet이 작동 하지 않는 경우 상승 된 권한으로 다시 실행 해 보세요.

그런 다음, 다음 cmdlet을 실행 하 여 Azure에 연결 합니다.

```powershell
Connect-AzAccount
```

Azure 계정에 로그인 하려면 Connect cmdlet을 실행할 때 생성 되는 코드가 필요 합니다. 로그인 하려면로 <https://microsoft.com/devicelogin>이동 하 여 코드를 입력 한 다음 Azure 관리자 자격 증명을 사용 하 여 로그인 합니다.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

그러면 관리자 자격 증명에 대 한 기본값 인 구독에 직접 로그인 됩니다.

## <a name="change-the-default-subscription"></a>기본 구독 변경

로그인 한 후에 기본 구독을 변경 하려면이 cmdlet을 실행 합니다.

```powershell
Select-AzureSubscription -SubscriptionName <preferredsubscriptionname>
```

사용할 새 구독을 선택 하는 경우 나중에 실행 하는 cmdlet에 해당 구독의 ID를 지정할 필요가 없습니다. 예를 들어 다음 cmdlet은 구독 ID를 필요로 하지 않고 특정 세션 호스트를 검색 합니다.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

원하는 구독 이름을 매개 변수로 추가 하 여 cmdlet 별로 구독을 변경할 수도 있습니다. 다음 cmdlet은 cmdlet에서 사용 하는 구독을 변경 하는 매개 변수로 추가 된 구독 ID를 제외 하 고 이전 예제와 동일 합니다.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>위치 가져오기

Location 매개 변수는 새 개체를 만드는 모든 **AzWVD** cmdlet에 대해 필수입니다.

다음 cmdlet을 실행 하 여 구독에서 지 원하는 위치 목록을 가져옵니다.

```powershell
Get-AzLocation
```

**AzLocation** 에 대 한 출력은 다음과 같습니다.

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

계정 위치를 확인 한 후에는 cmdlet에서 사용할 수 있습니다. 예를 들어 "southeastasia" 위치에 호스트 풀을 만드는 cmdlet은 다음과 같습니다.

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>다음 단계

PowerShell 모듈을 설정 했으므로 이제 Windows 가상 데스크톱에서 모든 종류의 작업을 수행 하는 cmdlet을 실행할 수 있습니다. 모듈을 사용할 수 있는 몇 가지 위치는 다음과 같습니다.

- [Windows 가상 데스크톱 자습서]() 를 실행 하 여 자체 Windows 가상 데스크톱 환경을 설정 합니다.
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)
- [Windows Virtual Desktop 부하 분산 방법 구성](configure-host-pool-load-balancing.md)
- [개인 데스크톱 호스트 풀 할당 유형 구성](configure-host-pool-personal-desktop-assignment-type.md)
- 이외에 많은 기능이 있습니다!

문제가 발생 하는 경우 도움이 필요 하면 [PowerShell 문제 해결 문서](troubleshoot-powershell.md) 를 확인 하세요.

