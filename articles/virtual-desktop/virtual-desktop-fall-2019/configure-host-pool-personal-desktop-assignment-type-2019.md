---
title: Windows 가상 데스크톱 (클래식) 개인 데스크톱 할당 유형-Azure
description: Windows 가상 데스크톱 (클래식) 개인 데스크톱 호스트 풀의 할당 유형을 구성 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 669010d2de90498c98fc685fe931b084a11cd104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008511"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-windows-virtual-desktop-classic"></a>Windows 가상 데스크톱 (클래식)에 대 한 개인 데스크톱 호스트 풀 할당 유형 구성

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../configure-host-pool-personal-desktop-assignment-type.md)를 참조하세요.

사용자의 요구에 맞게 Windows Virtual Desktop 환경을 조정하도록 개인 데스크톱 호스트 풀의 할당 유형을 구성할 수 있습니다. 이 항목에서는 사용자에 대한 자동 또는 직접 할당을 구성하는 방법을 보여 줍니다.

>[!NOTE]
> 이 문서의 지침은 풀링된 호스트 풀이 아닌 개인 데스크톱 호스트 풀에만 적용됩니다. 풀링된 호스트 풀의 사용자는 특정 세션 호스트에 할당되지 않기 때문입니다.

## <a name="configure-automatic-assignment"></a>자동 할당 구성

자동 할당은 Windows Virtual Desktop 환경에서 만든 새 개인 데스크톱 호스트 풀의 기본 할당 유형입니다. 사용자를 자동으로 할당하는 데는 특정 세션 호스트가 필요하지 않습니다.

사용자를 자동으로 할당하려면 먼저 사용자가 피드에서 바탕 화면을 볼 수 있도록 개인 데스크톱 호스트 풀에 해당 사용자를 할당합니다. 할당된 사용자가 피드에서 바탕 화면을 시작하면 호스트 풀에 아직 연결되지 않은 경우 사용 가능한 세션 호스트를 클레임하게 됩니다. 그러면 할당 프로세스가 완료됩니다.

시작하기 전에 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 없는 경우).

> [!NOTE]
> 다음 지침을 수행하기 전에 Windows Virtual Desktop PowerShell 모듈 버전 1.0.1534.2001 이상을 설치했는지 확인합니다.

그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

사용자를 VM에 자동으로 할당하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

개인 데스크톱 호스트 풀에 사용자를 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>직접 할당 구성

자동 할당과 달리 직접 할당을 사용하는 경우 먼저 개인 데스크톱 호스트 풀과 특정 세션 호스트 모두에 사용자를 할당해야 사용자가 개인 데스크톱에 연결할 수 있습니다. 사용자가 세션 호스트 할당 없이 호스트 풀에만 할당된 경우 리소스에 액세스할 수 없습니다.

사용자를 세션 호스트에 직접 할당해야 하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

개인 데스크톱 호스트 풀에 사용자를 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

사용자를 특정 세션 호스트에 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>사용자 할당 제거

사용자에게 더 이상 개인 데스크톱이 필요하지 않거나, 사용자가 퇴사했거나, 다른 사용자에게 데스크톱을 다시 사용하려는 경우 사용자 할당을 제거할 수 있습니다.

현재 개인 데스크톱에 대한 사용자 할당을 제거할 수 있는 유일한 방법은 세션 호스트를 완전히 제거하는 것입니다. 세션 호스트를 제거하려면 다음 cmdlet을 실행합니다.

```powershell
Remove-RdsSessionHost
```

세션 호스트를 개인 데스크톱 호스트 풀에 다시 추가해야 하는 경우 해당 머신에서 Windows Virtual Desktop을 제거한 후 [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell-2019.md)의 단계를 수행하여 세션 호스트를 다시 등록합니다.

## <a name="next-steps"></a>다음 단계

개인 데스크톱 할당 유형을 구성했으므로 이제 Windows Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 두 방법 문서는 선택한 클라이언트를 사용하여 세션에 연결하는 방법을 보여 줍니다.

- [Windows Desktop 클라이언트를 사용하여 연결](connect-windows-7-10-2019.md)
- [웹 클라이언트를 사용하여 연결](connect-web-2019.md)
