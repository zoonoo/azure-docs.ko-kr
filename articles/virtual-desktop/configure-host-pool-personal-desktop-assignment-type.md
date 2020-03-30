---
title: 윈도우 가상 데스크톱 개인 데스크톱 할당 유형 - Azure
description: Windows 가상 데스크톱 개인 데스크톱 호스트 풀에 대한 할당 유형을 구성하는 방법.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128296"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>개인 데스크톱 호스트 풀 할당 유형 구성

개인 데스크톱 호스트 풀의 할당 유형을 구성하여 필요에 맞게 Windows 가상 데스크톱 환경을 조정할 수 있습니다. 이 항목에서는 사용자에 대한 자동 또는 직접 할당을 구성하는 방법을 보여 줍니다.

>[!NOTE]
> 이 문서의 지침은 풀린 호스트 풀의 사용자가 특정 세션 호스트에 할당되지 않으므로 풀린 호스트 풀이 아닌 개인 데스크톱 호스트 풀에만 적용됩니다.

## <a name="configure-automatic-assignment"></a>자동 할당 구성

자동 할당은 Windows 가상 데스크톱 환경에서 만든 새 개인 데스크톱 호스트 풀의 기본 할당 유형입니다. 사용자를 자동으로 할당하려면 특정 세션 호스트가 필요하지 않습니다.

사용자를 자동으로 할당하려면 먼저 피드에서 데스크톱을 볼 수 있도록 개인 데스크톱 호스트 풀에 사용자를 할당합니다. 할당된 사용자가 피드에서 데스크톱을 시작하면 할당 프로세스를 완료하는 호스트 풀에 아직 연결되지 않은 경우 사용 가능한 세션 호스트를 클레임합니다.

시작하기 전에 아직 Windows [가상 데스크톱 PowerShell 모듈을 다운로드하여 가져오지](/powershell/windows-virtual-desktop/overview/) 않은 경우. 

> [!NOTE]
> 이 지침을 따르기 전에 Windows 가상 데스크톱 PowerShell 모듈 버전 1.0.1534.2001 이상에 설치했는지 확인하십시오.

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

자동 할당과 달리 직접 할당을 사용하는 경우 개인 데스크톱 호스트 풀과 특정 세션 호스트에 사용자를 할당해야 개인 데스크톱에 연결할 수 있습니다. 사용자가 세션 호스트 할당 없이 호스트 풀에만 할당된 경우 리소스에 액세스할 수 없습니다.

세션 호스트에 사용자를 직접 할당해야 하는 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

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

## <a name="next-steps"></a>다음 단계

개인 데스크톱 할당 유형을 구성한 후 Windows Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 두 가지 방법 설명은 선택한 클라이언트를 사용하여 세션에 연결하는 방법을 알려줍니다.

- [Windows Desktop 클라이언트와 연결](connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](connect-web.md)
