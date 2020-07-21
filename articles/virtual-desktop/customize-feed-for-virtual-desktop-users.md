---
title: Windows 가상 데스크톱 사용자에 대 한 피드 사용자 지정-Azure
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱 사용자에 대 한 피드를 사용자 지정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7ae4e4916333439956216c11e0c7e738b4e2fff1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527781"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>Windows 가상 데스크톱 사용자의 피드 사용자 지정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 사용하여 2020년 봄 업데이트에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop 2019년 가을 릴리스를 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md)를 참조하세요.
>
> Windows Virtual Desktop 2020 봄 업데이트는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

사용자에 게 RemoteApp 및 원격 데스크톱 리소스가 인식할 수 있는 방식으로 표시 되도록 피드를 사용자 지정할 수 있습니다.

## <a name="prerequisites"></a>사전 준비 사항

이 문서에서는 Windows 가상 데스크톱 PowerShell 모듈을 이미 다운로드 하 여 설치 했다고 가정 합니다. 그렇지 않은 경우 [PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp의 표시 이름 사용자 지정

이름을 설정 하 여 게시 된 RemoteApp의 표시 이름을 변경할 수 있습니다. 기본적으로 이름은 RemoteApp 프로그램의 이름과 동일 합니다.

앱 그룹에 대해 게시 된 Remoteapp 목록을 검색 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

RemoteApp에 이름을 할당 하려면 필수 매개 변수를 사용 하 여 다음 cmdlet을 실행 합니다.

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

예를 들어 다음 예제 cmdlet을 사용 하 여 현재 응용 프로그램을 검색 했다고 가정해 보겠습니다.

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
이름을 업데이트 하려면이 cmdlet을 실행 합니다.

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

이름을 성공적으로 업데이트 했는지 확인 하려면 다음 cmdlet을 실행 합니다.

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

Cmdlet은 다음과 같은 출력을 제공 해야 합니다.

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>원격 데스크톱에 대 한 표시 이름 사용자 지정

친숙 한 이름을 설정 하 여 게시 된 원격 데스크톱의 표시 이름을 변경할 수 있습니다. PowerShell을 통해 호스트 풀과 데스크톱 앱 그룹을 수동으로 만든 경우 기본 이름은 "Session Desktop"입니다. GitHub Azure Resource Manager 템플릿 또는 Azure Marketplace 제공을 통해 호스트 풀 및 데스크톱 앱 그룹을 만든 경우 기본 이름은 호스트 풀 이름과 동일 합니다.

원격 데스크톱 리소스를 검색 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

원격 데스크톱 리소스에 이름을 할당 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Azure Portal에서 표시 이름 사용자 지정

Azure Portal를 사용 하 여 이름을 설정 하 여 게시 된 원격 데스크톱의 표시 이름을 변경할 수 있습니다.

1. <https://portal.azure.com> 에서 Azure Portal에 로그인합니다.

2. **Windows 가상 데스크톱**을 검색 합니다.

3. 서비스에서 **Windows 가상 데스크톱**을 선택 합니다.

4. Windows 가상 데스크톱 페이지의 화면 왼쪽에서 **응용 프로그램 그룹** 을 선택 하 고 편집 하려는 앱 그룹의 이름을 선택 합니다.

5. 화면 왼쪽의 메뉴에서 **응용 프로그램** 을 선택 합니다.

6. 업데이트 하려는 응용 프로그램을 선택 하 고 새 **표시 이름을**입력 합니다.

7. **저장**을 선택합니다. 이제 편집한 응용 프로그램에 업데이트 된 이름이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

사용자에 대 한 피드를 사용자 지정 했으므로 Windows 가상 데스크톱 클라이언트에 로그인 하 여 테스트할 수 있습니다. 이렇게 하려면 Windows 가상 데스크톱 방법에 연결을 계속 진행 합니다.

 * [Windows 10 또는 Windows 7을 사용 하 여 연결](connect-windows-7-10.md)
 * [웹 클라이언트를 사용하여 연결](connect-web.md)
 * [Android 클라이언트와 연결](connect-android.md)
 * [iOS 클라이언트와 연결](connect-ios.md)
 * [macOS 클라이언트와 연결](connect-macos.md)
