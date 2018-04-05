---
title: Unity iOS 배포용 Azure Mobile Engagement 시작
description: iOS 장치에 Unity 앱을 배포하는 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8270a56daf8e69eede5b3d14b4ff88e0911da11a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Unity iOS 배포용 Azure Mobile Engagement 시작
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 항목에서는 Azure Mobile Engagement를 사용하여 앱 사용법을 이해하고 iOS 장치에 배포할 때 Unity 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 클래식 Unity Roll a Ball 자습서를 시작 지점으로 사용합니다. 아래 자습서에서 소개하는 Mobile Engagement 통합을 진행하기 전에 이 [자습서](mobile-engagement-unity-roll-a-ball.md) 의 단계를 따라야 합니다. 

이 자습서를 사용하려면 다음이 필요합니다.

* [Unity 편집기](http://unity3d.com/get-unity)
* [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
* XCode 편집기

> [!NOTE]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started)을 참조하세요.
> 
> 

## <a id="setup-azme"></a>iOS 앱용 Mobile Engagement 설정
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결
### <a name="import-the-unity-package"></a>Unity 패키지 가져오기
1. [Mobile Engagement Unity 패키지](https://aka.ms/azmeunitysdk) 를 다운로드하고 이를 로컬 컴퓨터에 저장합니다. 
2. **자산 -> 패키지 가져오기 -> 사용자 지정 패키지**로 이동하고 위의 단계에서 다운로드한 패키지를 선택합니다. 
   
    ![][70] 
3. 모든 파일을 선택했는지 확인하고 **가져오기** 단추를 클릭합니다. 
   
    ![][71] 
4. 가져오기에 성공하면 가져온 SDK 파일이 프로젝트에 표시됩니다.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>EngagementConfiguration 업데이트
1. SDK 폴더에서 **EngagementConfiguration** 스크립트 파일을 열고 **IOS\_CONNECTION\_STRING**을 Azure Portal에서 이전에 가져온 연결 문자열로 업데이트합니다.  
   
    ![][73]
2. 파일을 저장합니다. 

### <a name="configure-the-app-for-basic-tracking"></a>기본 추적을 위한 앱 구성
1. 편집을 위해 Player 개체에 연결된 **PlayerController** 스크립트를 엽니다. 
2. 다음 using 문을 추가합니다.
   
        using Microsoft.Azure.Engagement.Unity;
3. 다음을 `Start()` 메서드에 추가합니다.
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>앱 배포 및 실행
1. 컴퓨터에 iOS 장치를 연결합니다. 
2. **파일 -> 빌드 설정**을 엽니다. 
   
    ![][40]
3. **iOS**를 선택한 후 **플랫폼 전환**을 클릭합니다.
   
    ![][41]
   
    ![][42]
4. **플레이어 설정** 을 클릭하고 유효한 번들 식별자를 제공합니다. 
   
    ![][53]
5. 마지막으로 **빌드 및 실행**
   
    ![][54]
6. iOS 패키지를 저장할 폴더 이름을 입력하라는 메시지가 나타날 수 있습니다. 
   
    ![][43]
7. 모든 항목이 제대로 진행되면 프로젝트가 컴파일되고 XCode 응용 프로그램에서 열립니다. 
8. **번들 식별자** 가 프로젝트에서 올바른지 확인합니다.  
   
    ![][75]
9. 이제 패키지가 연결된 장치에 배포되도록 XCode에서 앱을 실행하면 Unity 게임이 휴대폰에 표시됩니다. 

## <a id="monitor"></a>실시간 모니터링과 앱 연결
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용
Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자 및 도달률과 상호 작용할 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다.
이미 설정되어 있으므로 알림을 받도록 앱에서 추가 구성을 하지 않아도 됩니다.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
