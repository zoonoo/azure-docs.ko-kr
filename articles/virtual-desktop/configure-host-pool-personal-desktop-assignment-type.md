---
title: Windows Virtual Desktop 개인 데스크톱 할당 유형 - Azure
description: Windows Virtual Desktop 개인 데스크톱 호스트 풀의 할당 유형을 구성하는 방법입니다.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158ac92a930b53e02ee81570c62711ca27dc4ae8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200395"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>개인 데스크톱 호스트 풀 할당 유형 구성

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 사용하여 2020년 봄 업데이트에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop 2019년 가을 릴리스를 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md)를 참조하세요.
>
> Windows Virtual Desktop 2020 봄 업데이트는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

사용자의 요구에 맞게 Windows Virtual Desktop 환경을 조정하도록 개인 데스크톱 호스트 풀의 할당 유형을 구성할 수 있습니다. 이 항목에서는 사용자에 대한 자동 또는 직접 할당을 구성하는 방법을 보여 줍니다.

>[!NOTE]
> 이 문서의 지침은 풀링된 호스트 풀이 아닌 개인 데스크톱 호스트 풀에만 적용됩니다. 풀링된 호스트 풀의 사용자는 특정 세션 호스트에 할당되지 않기 때문입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Windows 가상 데스크톱 PowerShell 모듈을 이미 다운로드 하 여 설치 했다고 가정 합니다. 그렇지 않은 경우 [PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

## <a name="configure-automatic-assignment"></a>자동 할당 구성

자동 할당은 Windows Virtual Desktop 환경에서 만든 새 개인 데스크톱 호스트 풀의 기본 할당 유형입니다. 사용자를 자동으로 할당하는 데는 특정 세션 호스트가 필요하지 않습니다.

사용자를 자동으로 할당하려면 먼저 사용자가 피드에서 바탕 화면을 볼 수 있도록 개인 데스크톱 호스트 풀에 해당 사용자를 할당합니다. 할당된 사용자가 피드에서 바탕 화면을 시작하면 호스트 풀에 아직 연결되지 않은 경우 사용 가능한 세션 호스트를 클레임하게 됩니다. 그러면 할당 프로세스가 완료됩니다.

사용자를 VM에 자동으로 할당하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

개인 데스크톱 호스트 풀에 사용자를 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>직접 할당 구성

자동 할당과 달리 직접 할당을 사용하는 경우 먼저 개인 데스크톱 호스트 풀과 특정 세션 호스트 모두에 사용자를 할당해야 사용자가 개인 데스크톱에 연결할 수 있습니다. 사용자가 세션 호스트 할당 없이 호스트 풀에만 할당된 경우 리소스에 액세스할 수 없습니다.

사용자를 세션 호스트에 직접 할당해야 하도록 호스트 풀을 구성하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

개인 데스크톱 호스트 풀에 사용자를 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

사용자를 특정 세션 호스트에 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>다음 단계

개인 데스크톱 할당 유형을 구성했으므로 이제 Windows Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 두 방법 문서는 선택한 클라이언트를 사용하여 세션에 연결하는 방법을 보여 줍니다.

- [Windows Desktop 클라이언트를 사용하여 연결](connect-windows-7-and-10.md)
- [웹 클라이언트를 사용하여 연결](connect-web.md)
- [Android 클라이언트와 연결](connect-android.md)
- [iOS 클라이언트와 연결](connect-ios.md)
- [macOS 클라이언트와 연결](connect-macos.md)