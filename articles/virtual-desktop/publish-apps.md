---
title: Azure Virtual Desktop에서 기본 제공 앱 게시 - Azure
description: Azure Virtual Desktop에서 기본 제공 앱을 게시하는 방법.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: fc704b992100430c247260b730929e6bc049b98d
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753074"
---
# <a name="publish-built-in-apps-in-azure-virtual-desktop"></a>Azure Virtual Desktop에서 기본 제공 앱 게시

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/publish-apps-2019.md)를 참조하세요.

이 문서에서는 Azure Virtual Desktop 환경에서 앱을 게시하는 방법을 설명합니다.

## <a name="publish-built-in-apps"></a>기본 제공 앱 게시

기본 제공 앱을 게시하려면 다음을 수행합니다.

1. 호스트 풀의 가상 머신 중 하나에 연결합니다.
2. [이 문서](/powershell/module/appx/get-appxpackage)의 지침에 따라 게시하려는 앱의 **PackageFamilyName** 을 가져옵니다.
3. 마지막으로, `<PackageFamilyName>`을 이전 단계에서 찾은 **PackageFamilyName** 으로 교체하여 다음 cmdlet을 실행합니다.

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Azure Virtual Desktop은 `C:\Program Files\WindowsApps`로 시작하는 설치 위치가 있는 앱 게시만 지원합니다.

## <a name="update-app-icons"></a>앱 아이콘 업데이트

앱을 게시하면 일반 아이콘 그림 대신 기본 Windows 앱 아이콘이 표시됩니다. 아이콘을 일반 아이콘으로 변경하려면 원하는 아이콘 이미지를 네트워크 공유에 배치합니다. 지원되는 이미지 형식은 PNG, BMP, GIF, JPG, JPEG 및 ICO입니다.

## <a name="publish-microsoft-edge"></a>Microsoft Edge 게시

Microsoft Edge를 게시하는 데 사용하는 프로세스는 다른 앱의 게시 프로세스와 약간 다릅니다. 기본 홈 페이지를 사용하여 Microsoft Edge를 게시하려면 다음 cmdlet을 실행합니다.

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>다음 단계

- [Azure Virtual Desktop 사용자를 위한 피드 사용자 지정](customize-feed-for-virtual-desktop-users.md)에서 사용자에 대해 앱이 표시되는 방식을 구성하도록 피드를 구성하는 방법에 대해 알아봅니다.
- [MSIX 앱 연결 설정](app-attach.md)에서 MSIX 앱 연결 기능에 대해 알아봅니다.

