---
title: Windows Virtual Desktop Preview의 앱 그룹 관리 - Azure
description: Azure Active Directory에서 Windows Virtual Desktop Preview 테넌트를 설정하는 방법을 설명합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f0cdd28be8c6e7390aa26fdc2dfbf32ec5542c2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233912"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>자습서: Windows Virtual Desktop Preview의 앱 그룹 관리

새 Windows Virtual Desktop Preview 호스트 풀에 대해 생성된 기본 앱 그룹도 전체 데스크톱을 게시합니다. 뿐만 아니라 호스트 풀에 대한 하나 이상의 RemoteApp 애플리케이션 그룹을 만들 수 있습니다. 이 자습서에 따라 RemoteApp 앱 그룹을 만들고 개별 시작 메뉴 엡을 게시해 보세요.

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * RemoteApp 그룹 만들기
> * RemoteApps에 대한 액세스 권한 부여

시작하기 전에, PowerShell 세션에 사용할 Windows Virtual Desktop 모듈이 없는 경우 [Windows Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview).

## <a name="create-a-remoteapp-group"></a>RemoteApp 그룹 만들기

1. 다음 PowerShell cmdlet을 실행하여 빈 RemoteApp 앱 그룹을 새로 만듭니다.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (선택 사항) 앱 그룹이 생성되었는지 확인하려면 다음 cmdlet을 실행하여 호스트 풀에 대한 모든 앱 그룹 목록을 살펴보면 됩니다.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. 다음 cmdlet을 실행하여 호스트 풀의 가상 머신 이미지에 대한 시작 메뉴 앱 목록을 가져옵니다. **FilePath**, **IconPath**, **IconIndex**의 값과 게시하려는 애플리케이션의 기타 중요 정보를 적어 둡니다.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. 다음 cmdlet을 실행하여 appalias를 기반으로 애플리케이션을 설치합니다. 3단계의 출력을 실행하면 appalias를 볼 수 있습니다.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (선택 사항) 새 RemoteApp을 1단계에서 만든 애플리케이션 그룹에 게시하려면 다음 cmdlet을 실행합니다.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. 앱이 게시되었는지 확인하려면 다음 cmdlet을 실행합니다.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. 이 앱 그룹에 대해 게시하려는 애플리케이션마다 1-5단계를 반복합니다.
8. 다음 cmdlet을 실행하여 사용자에게 앱 그룹의 RemoteApps에 대한 액세스 권한을 부여합니다.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 앱 그룹을 만들고, RemoteApp을 사용하여 채우고, 앱 그룹에 사용자를 할당하는 방법을 알아보았습니다. Windows Virtual Desktop에 로그인하는 방법을 자세히 알아보려면 Windows Virtual Desktop에 연결하는 방법에 관한 다음 페이지를 참고하세요.

- [Windows 7 및 Windows 10에서 원격 데스크톱 클라이언트에 연결](connect-windows-7-and-10.md)
- [Windows Virtual Desktop 미리 보기 웹 클라이언트에 연결](connect-web.md)
