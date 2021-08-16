---
title: Azure Virtual Desktop 사용자용 피드 사용자 지정 - Azure
description: PowerShell cmdlet을 사용하여 Azure Virtual Desktop 사용자에 대한 피드를 사용자 지정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 24174d7ac9fa33cb1fb3ca6e325537dde346b3c3
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111752012"
---
# <a name="customize-the-feed-for-azure-virtual-desktop-users"></a>Azure Virtual Desktop 사용자용 피드 사용자 지정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md)를 참조하세요.

사용자가 인식 가능한 방식으로 RemoteApp 및 원격 데스크톱 리소스를 표시하도록 피드를 사용자 지정할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 Azure Virtual Desktop PowerShell 모듈을 이미 다운로드하여 설치했다고 가정합니다. 그렇지 않은 경우 [PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp의 표시 이름 사용자 지정

이름을 설정하여 게시된 RemoteApp의 표시 이름을 변경할 수 있습니다. 기본적으로 친숙한 이름은 RemoteApp 프로그램의 이름과 동일합니다.

앱 그룹에 대해 게시된 RemoteApps 목록을 검색하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

RemoteApp에 친숙한 이름을 할당하려면 필수 매개 변수를 사용하여 다음 cmdlet을 실행합니다.

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

예를 들어 다음 예제 cmdlet을 사용하여 현재 애플리케이션을 검색했다고 가정해 보겠습니다.

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

출력은 다음과 같습니다.

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
이름을 업데이트하려면 다음 cmdlet을 실행합니다.

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

이름을 성공적으로 업데이트했는지 확인하려면 다음 cmdlet을 실행합니다.

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

이 cmdlet은 다음과 같은 결과를 출력합니다.

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>원격 데스크톱의 표시 이름 사용자 지정

이름을 설정하여 게시된 원격 데스크톱의 표시 이름을 변경할 수 있습니다. PowerShell을 통해 호스트 풀과 데스크톱 앱 그룹을 수동으로 만든 경우 기본 이름은 "Session Desktop"입니다. GitHub Azure Resource Manager 템플릿 또는 Azure Marketplace 제품을 통해 호스트 풀과 데스크톱 앱 그룹을 만든 경우 기본 이름은 호스트 풀 이름과 동일합니다.

원격 데스크톱 리소스를 검색하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

원격 데스크톱 리소스에 친숙한 이름을 할당하려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Azure Portal에서 표시 이름 사용자 지정

Azure Portal에서 이름을 설정하여 게시된 원격 데스크톱의 표시 이름을 변경할 수 있습니다.

1. <https://portal.azure.com> 에서 Azure Portal에 로그인합니다.

2. **Azure Virtual Desktop** 을 검색합니다.

3. 서비스에서 **Azure Virtual Desktop** 을 선택합니다.

4. Azure Virtual Desktop 페이지의 화면 왼쪽에서 **애플리케이션 그룹** 을 선택하고 편집하려는 앱 그룹의 이름을 선택합니다. 예를 들어 데스크톱 애플리케이션 그룹의 표시 이름을 편집하려면 **데스크톱** 이라는 앱 그룹을 선택합니다.

5. 화면 왼쪽의 메뉴에서 **애플리케이션** 을 선택합니다.

6. 업데이트하려는 애플리케이션을 선택하고 새 **표시 이름** 을 입력합니다.

7. **저장** 을 선택합니다. 이제 편집한 애플리케이션에 업데이트된 이름이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이제 사용자에 대한 피드를 사용자 지정했으므로 Azure Virtual Desktop 클라이언트에 로그인하여 테스트할 수 있습니다. 이렇게 하려면 Azure Virtual Desktop에 연결 방법을 계속 진행합니다.

 * [Windows 10 또는 Windows 7을 사용하여 연결](connect-windows-7-10.md)
 * [웹 클라이언트를 사용하여 연결](connect-web.md)
 * [Android 클라이언트와 연결](connect-android.md)
 * [iOS 클라이언트와 연결](connect-ios.md)
 * [macOS 클라이언트와 연결](connect-macos.md)
