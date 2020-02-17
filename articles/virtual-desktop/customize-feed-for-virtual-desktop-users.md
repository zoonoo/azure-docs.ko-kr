---
title: Windows 가상 데스크톱 사용자에 대 한 피드 사용자 지정-Azure
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱 사용자에 대 한 피드를 사용자 지정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 32abddad600edf0f62efef48aea402b6d4f09c10
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368867"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Windows Virtual Desktop 사용자용 피드를 사용자 지정

사용자에 게 RemoteApp 및 원격 데스크톱 리소스가 인식할 수 있는 방식으로 표시 되도록 피드를 사용자 지정할 수 있습니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/)(아직 다운로드하고 가져오지 않은 경우). 그런 후, 다음 cmdlet을 실행하여 계정에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp의 표시 이름 사용자 지정

이름을 설정 하 여 게시 된 RemoteApp의 표시 이름을 변경할 수 있습니다. 기본적으로 이름은 RemoteApp 프로그램의 이름과 동일 합니다.

앱 그룹에 대해 게시 된 Remoteapp 목록을 검색 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Name 및 FriendlyName이 강조 표시 된 PowerShell cmdlet RDSRemoteApp의 스크린샷](media/get-rdsremoteapp.png)

RemoteApp에 이름을 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![이름 및 새 FriendlyName이 강조 표시 된 PowerShell cmdlet RDSRemoteApp의 스크린샷](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>원격 데스크톱에 대 한 표시 이름 사용자 지정

친숙 한 이름을 설정 하 여 게시 된 원격 데스크톱의 표시 이름을 변경할 수 있습니다. PowerShell을 통해 호스트 풀과 데스크톱 앱 그룹을 수동으로 만든 경우 기본 이름은 "Session Desktop"입니다. GitHub Azure Resource Manager 템플릿 또는 Azure Marketplace 제공을 통해 호스트 풀 및 데스크톱 앱 그룹을 만든 경우 기본 이름은 호스트 풀 이름과 동일 합니다.

원격 데스크톱 리소스를 검색 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Name 및 FriendlyName이 강조 표시 된 PowerShell cmdlet RDSRemoteApp의 스크린샷](media/get-rdsremotedesktop.png)

원격 데스크톱 리소스에 이름을 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![이름 및 새 FriendlyName이 강조 표시 된 PowerShell cmdlet RDSRemoteApp의 스크린샷](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>다음 단계

사용자에 대 한 피드를 사용자 지정 했으므로 Windows 가상 데스크톱 클라이언트에 로그인 하 여 테스트할 수 있습니다. 이렇게 하려면 Windows 가상 데스크톱 방법에 연결을 계속 진행 합니다.
    
 * [Windows 10 또는 Windows 7에서 연결](connect-windows-7-and-10.md)
 * [웹 브라우저에서 연결](connect-web.md) 
