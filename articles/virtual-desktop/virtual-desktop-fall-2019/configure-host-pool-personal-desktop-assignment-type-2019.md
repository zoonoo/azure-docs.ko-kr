---
title: Windows 가상 데스크톱 개인 데스크톱 할당 유형-Azure
description: Windows 가상 데스크톱 개인 데스크톱 호스트 풀의 할당 유형을 구성 하는 방법
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2541e9e10103d66c6c2fb6978c3029d61b813eab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614968"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>개인 데스크톱 호스트 풀 할당 유형 구성

>[!IMPORTANT]
>이 콘텐츠는 Windows 가상 데스크톱 개체 Azure Resource Manager를 지원 하지 않는 낙하 2019 릴리스에 적용 됩니다. 스프링 2020 업데이트에 도입 된 Azure Resource Manager Windows 가상 데스크톱 개체를 관리 하려는 경우 [이 문서](../configure-host-pool-personal-desktop-assignment-type.md)를 참조 하세요.

사용자의 요구에 맞게 Windows 가상 데스크톱 환경을 조정 하도록 개인 데스크톱 호스트 풀의 할당 유형을 구성할 수 있습니다. 이 항목에서는 사용자에 대 한 자동 또는 직접 할당을 구성 하는 방법을 보여 줍니다.

>[!NOTE]
> 풀링된 호스트 풀의 사용자는 특정 세션 호스트에 할당 되지 않으므로이 문서의 지침은 풀링된 호스트 풀이 아닌 개인 데스크톱 호스트 풀에만 적용 됩니다.

## <a name="configure-automatic-assignment"></a>자동 할당 구성

자동 할당은 Windows 가상 데스크톱 환경에서 만든 새 개인 데스크톱 호스트 풀의 기본 할당 유형입니다. 사용자를 자동으로 할당 하려면 특정 세션 호스트가 필요 하지 않습니다.

사용자를 자동으로 할당 하려면 먼저 사용자가 피드의 바탕 화면을 볼 수 있도록 개인 데스크톱 호스트 풀에 해당 사용자를 할당 합니다. 할당 된 사용자가 피드에서 바탕 화면을 시작 하면 호스트 풀에 아직 연결 되지 않은 경우 사용 가능한 세션 호스트를 클레임 하 게 됩니다. 그러면 할당 프로세스가 완료 됩니다.

시작 하기 전에 [Windows 가상 데스크톱 PowerShell 모듈을 다운로드 하 고 가져옵니다](/powershell/windows-virtual-desktop/overview/) (아직 없는 경우). 

> [!NOTE]
> 이러한 지침을 수행 하기 전에 Windows 가상 데스크톱 PowerShell 모듈 버전 1.0.1534.2001 이상을 설치 했는지 확인 합니다.

그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Vm에 사용자를 자동으로 할당 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

개인 데스크톱 호스트 풀에 사용자를 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>직접 할당 구성

자동 할당과 달리 직접 할당을 사용 하는 경우 개인 데스크톱에 연결할 수 있으려면 먼저 개인 데스크톱 호스트 풀과 특정 세션 호스트 모두에 사용자를 할당 해야 합니다. 사용자가 세션 호스트를 할당 하지 않고 호스트 풀에만 할당 된 경우 리소스에 액세스할 수 없습니다.

사용자를 세션 호스트에 직접 할당 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

개인 데스크톱 호스트 풀에 사용자를 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

특정 세션 호스트에 사용자를 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>다음 단계

개인 데스크톱 할당 유형을 구성 했으므로 이제 Windows 가상 데스크톱 클라이언트에 로그인 하 여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 두 가지 방법으로 사용자가 선택한 클라이언트를 사용 하 여 세션에 연결 하는 방법을 알 수 있습니다.

- [Windows Desktop 클라이언트와 연결](../connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](connect-web-2019.md)
