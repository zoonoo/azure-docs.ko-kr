---
title: Windows 가상 데스크톱 사용자를 위한 피드 사용자 지정 - Azure
description: PowerShell cmdlet을 사용하여 Windows 가상 데스크톱 사용자를 위한 피드를 사용자 지정하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128091"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Windows Virtual Desktop 사용자용 피드를 사용자 지정

RemoteApp 및 원격 데스크톱 리소스가 사용자에게 인식 가능한 방식으로 표시되도록 피드를 사용자 지정할 수 있습니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 다운로드하고 가져오지 않은 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp의 디스플레이 이름 사용자 지정

친숙한 이름을 설정하여 게시된 RemoteApp의 표시 이름을 변경할 수 있습니다. 기본적으로 친숙한 이름은 RemoteApp 프로그램의 이름과 동일합니다.

앱 그룹에 대해 게시된 RemoteApps 목록을 검색하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![이름과 친근한 이름이 강조 표시된 PowerShell cmdlet Get-RDSRemoteApp의 스크린 샷.](media/get-rdsremoteapp.png)

RemoteApp에 친숙한 이름을 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![이름과 새로운 친근한 이름이 강조 표시된 PowerShell cmdlet Set-RDSRemoteApp의 스크린 샷.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>원격 데스크톱의 표시 이름 사용자 지정

친숙한 이름을 설정하여 게시된 원격 데스크톱의 표시 이름을 변경할 수 있습니다. PowerShell을 통해 호스트 풀 및 데스크톱 앱 그룹을 수동으로 만든 경우 기본 친숙한 이름은 "세션 데스크톱"입니다. GitHub Azure 리소스 관리자 템플릿 또는 Azure Marketplace 오퍼링을 통해 호스트 풀 및 데스크톱 앱 그룹을 만든 경우 기본 친숙한 이름은 호스트 풀 이름과 동일합니다.

원격 데스크톱 리소스를 검색하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![이름과 친근한 이름이 강조 표시된 PowerShell cmdlet Get-RDSRemoteApp의 스크린 샷.](media/get-rdsremotedesktop.png)

원격 데스크톱 리소스에 친숙한 이름을 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![이름과 새로운 친근한 이름이 강조 표시된 PowerShell cmdlet Set-RDSRemoteApp의 스크린 샷.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>다음 단계

사용자를 위해 피드를 사용자 지정한 후 Windows 가상 데스크톱 클라이언트에 로그인하여 테스트할 수 있습니다. 이렇게 하려면 Windows 가상 데스크톱 방법-에 연결 계속 합니다.
    
 * [Windows 10 또는 Windows 7에서 연결](connect-windows-7-and-10.md)
 * [웹 브라우저에서 연결](connect-web.md) 
