---
title: Windows 가상 데스크톱 사용자-Azure에 대 한 피드를 사용자 지정
description: PowerShell cmdlet 사용 하 여 Windows 가상 데스크톱 사용자에 대 한 피드를 사용자 지정 하는 방법입니다.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 869760a12089ed7453199ad8a3fa18a6cca87511
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157091"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Windows Virtual Desktop 사용자용 피드를 사용자 지정

RemoteApp 및 원격 데스크톱 리소스를 인식할 수 있는 방식으로 사용자에 게 표시 되도록 피드를 사용자 지정할 수 있습니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp에 대 한 표시 이름을 사용자 지정

친숙 한 이름을 설정 하 여 게시 된 RemoteApp에 대 한 표시 이름을 변경할 수 있습니다. 기본적으로 친숙 한 이름을 RemoteApp 프로그램의 이름으로 동일 합니다.

앱 그룹에 대 한 게시 된 Remoteapp 목록을 검색 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![PowerShell cmdlet의 스크린샷 Get-RDSRemoteApp 이름과 FriendlyName를 사용 하 여 강조 표시 합니다.](media/get-rdsremoteapp.png)

RemoteApp에 친숙 한 이름에 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![이름 및 새 FriendlyName 강조 표시를 사용 하 여 PowerShell cmdlet 집합 RDSRemoteApp 스크린샷.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>원격 데스크톱에 대 한 표시 이름을 사용자 지정

친숙 한 이름을 설정 하 여 게시 된 원격 데스크톱에 대 한 표시 이름을 변경할 수 있습니다. 수동으로 만든 경우 호스트 풀 및 PowerShell 통해 데스크톱 앱 그룹을 기본 친숙 한 이름은 "세션 데스크톱." 풀 및 GitHub Azure Resource Manager 템플릿 또는 Azure Marketplace 제품을 통해 데스크톱 응용 프로그램 그룹에 호스트를 만든 경우 기본 친숙 한 이름은 호스트 풀 이름과 동일 합니다.

원격 데스크톱 리소스를 검색 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![PowerShell cmdlet의 스크린샷 Get-RDSRemoteApp 이름과 FriendlyName를 사용 하 여 강조 표시 합니다.](media/get-rdsremotedesktop.png)

이름에 원격 데스크톱 리소스를 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![이름 및 새 FriendlyName 강조 표시를 사용 하 여 PowerShell cmdlet 집합 RDSRemoteApp 스크린샷.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>다음 단계

사용자에 대 한 피드를 사용자 지정 했으므로, 로그인 할 수 있습니다 Windows 가상 데스크톱 클라이언트를 테스트 하겠습니다. 이렇게 하려면 Windows 가상 데스크톱 방법 연결할 계속 합니다.
    
 * [Windows 10 또는 Windows 7에서 연결](connect-windows-7-and-10.md)
 * [웹 브라우저에서 연결](connect-web.md) 
