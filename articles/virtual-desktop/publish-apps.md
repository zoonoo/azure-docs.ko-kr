---
title: Windows 가상 데스크톱 - Azure에 기본 제공 앱 게시
description: Windows 가상 데스크톱에 기본 제공 앱을 게시하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a697c9a62e52e82a550969e1852abd1489ed59b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127748"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Windows 가상 데스크톱에 기본 제공 앱 게시

이 문서에서는 Windows 가상 데스크톱 환경에서 앱을 게시하는 방법을 설명합니다.

## <a name="publish-built-in-apps"></a>기본 제공 앱 게시

기본 제공 앱을 게시하려면 다음 을 수행합니다.

1. 호스트 풀의 가상 시스템 중 하나에 연결합니다.
2. [이 문서의](/powershell/module/appx/get-appxpackage?view=win10-ps/)지침에 따라 게시할 앱의 **PackageFamilyName을** 가져옵니다.
3. 마지막으로 이전 단계에서 찾은 `<PackageFamilyName>` **PackageFamilyName으로** 대체된 다음 cmdlet을 실행합니다.
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows 가상 데스크톱은 `C:\Program Files\Windows Apps`에서 시작하는 설치 위치가 있는 게시 앱만 지원합니다.

## <a name="update-app-icons"></a>앱 아이콘 업데이트

앱을 게시한 후에는 일반 아이콘 사진 대신 기본 Windows 앱 아이콘이 표시됩니다. 아이콘을 일반 아이콘으로 변경하려면 네트워크 공유에 원하는 아이콘의 이미지를 넣습니다. 지원되는 이미지 형식은 PNG, BMP, GIF, JPG, JPEG 및 ICO입니다.

## <a name="publish-microsoft-edge"></a>마이크로소프트 에지 게시

Microsoft Edge를 게시하는 데 사용하는 프로세스는 다른 앱의 게시 프로세스와 약간 다릅니다. 기본 홈페이지와 마이크로소프트 가장자리를 게시 하려면이 cmdlet를 실행:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 사용자를 위한 [사용자 지정 피드에서](customize-feed-for-virtual-desktop-users.md)사용자에 대 한 앱 표시 방법을 구성 하도록 피드를 구성 하는 방법에 대해 알아봅니다.
- MSIX 앱 연결 [설정에서 MSIX 앱](app-attach.md)연결 기능에 대해 알아봅니다.

