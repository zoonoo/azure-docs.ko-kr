---
title: Windows 가상 데스크톱 PowerShell에 대 한 앱 그룹 관리-Azure
description: PowerShell을 사용 하 여 Windows 가상 데스크톱 앱 그룹을 관리 하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c286a3795cc7cb4c1925ff06b3da19952e7f0b43
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85209337"
---
# <a name="manage-app-groups-using-powershell"></a>PowerShell을 사용 하 여 앱 그룹 관리

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 사용하여 2020년 봄 업데이트에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop 2019년 가을 릴리스를 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/manage-app-groups-2019.md)를 참조하세요.
>
> Windows Virtual Desktop 2020 봄 업데이트는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

새 Windows Virtual Desktop 호스트 풀에 대해 생성된 기본 앱 그룹도 전체 데스크톱을 게시합니다. 뿐만 아니라 호스트 풀에 대한 하나 이상의 RemoteApp 애플리케이션 그룹을 만들 수 있습니다. 이 자습서에 따라 RemoteApp 앱 그룹을 만들고 개별 **시작** 메뉴 앱을 게시합니다.

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * RemoteApp 그룹 만들기
> * RemoteApp 프로그램에 액세스 권한을 부여합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 powershell 모듈 [설정](powershell-module.md) 의 지침에 따라 powershell 모듈을 설정 하 고 Azure 계정에 로그인 하는 것으로 가정 합니다.

## <a name="create-a-remoteapp-group"></a>RemoteApp 그룹 만들기

PowerShell을 사용 하 여 RemoteApp 그룹을 만들려면 다음을 수행 합니다.

1. 다음 PowerShell cmdlet을 실행하여 빈 RemoteApp 앱 그룹을 새로 만듭니다.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (선택 사항) 앱 그룹이 생성되었는지 확인하려면 다음 cmdlet을 실행하여 호스트 풀에 대한 모든 앱 그룹 목록을 살펴보면 됩니다.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. 다음 cmdlet을 실행하여 호스트 풀의 가상 머신 이미지에 대한 **시작** 메뉴 앱 목록을 가져옵니다. **FilePath**, **IconPath**, **IconIndex**의 값과 게시하려는 애플리케이션의 기타 중요 정보를 적어 둡니다.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   출력은 다음과 같은 형식으로 모든 시작 메뉴 항목을 표시 해야 합니다.

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. 다음 cmdlet을 실행하여 `AppAlias`를 기반으로 애플리케이션을 설치합니다. 3단계의 출력을 실행하면 `AppAlias`를 볼 수 있습니다.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (선택 사항) 새 RemoteApp 프로그램을 1단계에서 만든 애플리케이션 그룹에 게시하려면 다음 cmdlet을 실행합니다.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. 앱이 게시되었는지 확인하려면 다음 cmdlet을 실행합니다.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. 이 앱 그룹에 대해 게시하려는 애플리케이션마다 1-5단계를 반복합니다.
8. 다음 cmdlet을 실행하여 사용자에게 앱 그룹의 RemoteApp 프로그램에 대한 액세스 권한을 부여합니다.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>다음 단계

이 방법 가이드의 자습서에서 제공 하는 경우 [호스트 풀 만들기를 확인 하 여 서비스 업데이트의 유효성을 검사](create-validation-host-pool.md)합니다. 프로덕션 환경에 배포하기 전에 유효성 검사 호스트 풀을 사용하여 서비스 업데이트를 모니터링할 수 있습니다.