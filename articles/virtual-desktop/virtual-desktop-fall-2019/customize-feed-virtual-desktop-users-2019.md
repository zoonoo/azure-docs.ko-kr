---
title: Windows Virtual Desktop(클래식) 사용자용 피드 사용자 지정 - Azure
description: PowerShell cmdlet을 사용하여 Windows Virtual Desktop(클래식) 사용자에 대한 피드를 사용자 지정하는 방법.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5653ba4b1bcaab9e7b82dae3fe0842cbc7fb86b9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445111"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>Windows Virtual Desktop(클래식) 사용자용 피드 사용자 지정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Windows Virtual Desktop 개체를 관리하려는 경우 [이 문서](../customize-feed-for-virtual-desktop-users.md)를 참조하세요.

사용자가 인식 가능한 방식으로 RemoteApp 및 원격 데스크톱 리소스를 표시하도록 피드를 사용자 지정할 수 있습니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 다운로드하고 가져오지 않은 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp의 표시 이름 사용자 지정

이름을 설정하여 게시된 RemoteApp의 표시 이름을 변경할 수 있습니다. 기본적으로 친숙한 이름은 RemoteApp 프로그램의 이름과 동일합니다.

앱 그룹에 대해 게시된 RemoteApps 목록을 검색하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![표시 이름을 사용자 지정하기 위해 Name 및 FriendlyName이 강조 표시된 PowerShell cmdlet Get-RDSRemoteApp의 스크린샷.](../media/get-rdsremoteapp.png)

RemoteApp에 친숙한 이름을 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![표시 이름을 사용자 지정하기 위해 Name 및 New FriendlyName이 강조 표시된 PowerShell cmdlet Set-RDSRemoteApp의 스크린샷.](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>원격 데스크톱의 표시 이름 사용자 지정

이름을 설정하여 게시된 원격 데스크톱의 표시 이름을 변경할 수 있습니다. PowerShell을 통해 호스트 풀과 데스크톱 앱 그룹을 수동으로 만든 경우 기본 이름은 "Session Desktop"입니다. GitHub Azure Resource Manager 템플릿 또는 Azure Marketplace 제품을 통해 호스트 풀과 데스크톱 앱 그룹을 만든 경우 기본 이름은 호스트 풀 이름과 동일합니다.

원격 데스크톱 리소스를 검색하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![Name 및 FriendlyName이 강조 표시된 PowerShell cmdlet Get-RDSRemoteApp의 스크린샷.](../media/get-rdsremotedesktop.png)

원격 데스크톱 리소스에 친숙한 이름을 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![Name 및 New FriendlyName이 강조 표시된 PowerShell cmdlet Set-RDSRemoteApp의 스크린샷.](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>다음 단계

이제 사용자에 대한 피드를 사용자 지정했으므로 Windows Virtual Desktop 클라이언트에 로그인하여 테스트할 수 있습니다. 이렇게 하려면 Windows Virtual Desktop에 연결 방법을 계속 진행합니다.

 * [Windows 10 또는 Windows 7에서 연결](connect-windows-7-10-2019.md)
 * [웹 브라우저에서 연결](connect-web-2019.md)
