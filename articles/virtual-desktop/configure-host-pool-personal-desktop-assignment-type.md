---
title: Windows 가상 데스크톱 개인 데스크톱 할당 유형-Azure
description: Windows 가상 데스크톱 개인 데스크톱 호스트 풀의 할당 유형을 구성 하는 방법
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8451dc14a7ed42aa92f9adbd5ad050936949e302
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612421"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>개인 데스크톱 호스트 풀 할당 유형 구성

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows 가상 데스크톱 개체를 사용 하 여 스프링 2020 업데이트에 적용 됩니다. Azure Resource Manager 개체 없이 Windows 가상 데스크톱 2019 릴리스를 사용 하는 경우 [이 문서](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md)를 참조 하세요.
>
> Windows 가상 데스크톱 스프링 2020 업데이트는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

사용자의 요구에 맞게 Windows 가상 데스크톱 환경을 조정 하도록 개인 데스크톱 호스트 풀의 할당 유형을 구성할 수 있습니다. 이 항목에서는 사용자에 대 한 자동 또는 직접 할당을 구성 하는 방법을 보여 줍니다.

>[!NOTE]
> 풀링된 호스트 풀의 사용자는 특정 세션 호스트에 할당 되지 않으므로이 문서의 지침은 풀링된 호스트 풀이 아닌 개인 데스크톱 호스트 풀에만 적용 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Windows 가상 데스크톱 PowerShell 모듈을 이미 다운로드 하 여 설치 했다고 가정 합니다. 그렇지 않은 경우 [PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

## <a name="configure-automatic-assignment"></a>자동 할당 구성

자동 할당은 Windows 가상 데스크톱 환경에서 만든 새 개인 데스크톱 호스트 풀의 기본 할당 유형입니다. 사용자를 자동으로 할당 하려면 특정 세션 호스트가 필요 하지 않습니다.

사용자를 자동으로 할당 하려면 먼저 사용자가 피드의 바탕 화면을 볼 수 있도록 개인 데스크톱 호스트 풀에 해당 사용자를 할당 합니다. 할당 된 사용자가 피드에서 바탕 화면을 시작 하면 호스트 풀에 아직 연결 되지 않은 경우 사용 가능한 세션 호스트를 클레임 하 게 됩니다. 그러면 할당 프로세스가 완료 됩니다.

Vm에 사용자를 자동으로 할당 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

개인 데스크톱 호스트 풀에 사용자를 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>직접 할당 구성

자동 할당과 달리 직접 할당을 사용 하는 경우 개인 데스크톱에 연결할 수 있으려면 먼저 개인 데스크톱 호스트 풀과 특정 세션 호스트 모두에 사용자를 할당 해야 합니다. 사용자가 세션 호스트를 할당 하지 않고 호스트 풀에만 할당 된 경우 리소스에 액세스할 수 없습니다.

사용자를 세션 호스트에 직접 할당 하도록 호스트 풀을 구성 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

개인 데스크톱 호스트 풀에 사용자를 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

특정 세션 호스트에 사용자를 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>다음 단계

개인 데스크톱 할당 유형을 구성 했으므로 이제 Windows 가상 데스크톱 클라이언트에 로그인 하 여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 두 가지 방법으로 사용자가 선택한 클라이언트를 사용 하 여 세션에 연결 하는 방법을 알 수 있습니다.

- [Windows Desktop 클라이언트와 연결](connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](connect-web.md)
- [Android 클라이언트와 연결](connect-android.md)
- [iOS 클라이언트와 연결](connect-ios.md)
- [macOS 클라이언트와 연결](connect-macos.md)