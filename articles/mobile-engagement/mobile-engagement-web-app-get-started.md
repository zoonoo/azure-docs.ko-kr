---
title: Web Apps용 Azure Mobile Engagement 시작 | Microsoft Docs
description: Web Apps에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
ms.openlocfilehash: bb7a0e61b6d29c292642fd950aed7617de9b340d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Web Apps용 Azure Mobile Engagement 시작
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 항목에서는 Azure Mobile Engagement를 사용하여 웹앱 사용량을 파악하는 방법을 보여 줍니다.

> [!NOTE]
> Azure Mobile Engagement 서비스는 2018년 3월에 사용 중지되며 현재 기존 고객에게만 제공됩니다. 자세한 내용은 [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)를 참조하세요.

이 자습서를 사용하려면 다음이 필요합니다.

* Visual Studio 2015 또는 원하는 다른 편집기
* [웹 SDK](http://aka.ms/P7b453)

이 웹 SDK는 미리 보기 상태이며 현재 분석을 지원하고 브라우저 또는 앱 내 푸시 알림을 보내도록 아직 지원하지 않습니다. 

> [!NOTE]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started)을 참조하세요.
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>웹앱용 Mobile Engagement 설정
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결
이 자습서에서는 데이터를 수집하는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다.

Visual Studio로 기본 웹앱을 만들어 통합을 보여 주지만 Visual Studio 외부에서 만든 웹 응용 프로그램으로 모든 단계를 수행할 수도 있습니다. 

### <a name="create-a-new-web-app"></a>새 웹앱 만들기
다음 단계에서는 Visual Studio 2015를 사용한다고 가정하지만 이전 버전의 Visual Studio에서도 단계는 유사합니다. 

1. Visual Studio를 시작하고 **홈** 화면에서 **새 프로젝트**를 선택합니다.
2. 팝업에서 **웹** -> **ASP.Net 웹 응용 프로그램**을 선택합니다. 앱 **이름**, **위치** 및 **솔루션 이름**을 입력하고 **확인**을 클릭합니다.
3. **템플릿 선택** 팝업의 **ASP.Net 4.5 템플릿**에서 **비어 있음**을 선택하고 **확인**을 클릭합니다. 

이제 Azure Mobile Engagement 웹 SDK를 통합할 새 비어 있는 웹앱 프로젝트가 만들어졌습니다.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Mobile Engagement 백 엔드에 앱 연결
1. 솔루션에 **javascript**라는 새 폴더를 만들고 **azure-engagement.js**라는 웹 SDK JS 파일을 추가합니다. 
2. 다음 코드를 사용하여 이 javascript 폴더에 **main.js** 라는 새 파일을 추가합니다. 연결 문자열을 업데이트해야 합니다. `azureEngagement` 개체는 웹 SDK 메서드에 액세스하는 데 사용됩니다. 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![js 파일이 포함된 Visual Studio입니다.][1]

## <a name="enable-real-time-monitoring"></a>실시간 모니터링 사용
데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 작업을 하나 이상 보내야 합니다. 웹앱의 컨텍스트에 있는 작업은 웹 페이지입니다. 

1. 솔루션에서 **home.html** 라는 새 페이지를 만들고 웹앱에 대한 페이지의 시작으로 설정합니다. 
2. 본문 태그 안에 다음을 추가하여 이전에 이 페이지에 추가한 두 javascript를 포함합니다. 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. 본문 태그를 업데이트하여 EngagementAgent의 `startActivity` 메서드를 호출합니다.
   
        <body onload="engagement.agent.startActivity('Home')">
4. **home.html**은 다음과 같이 표시됩니다.
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>실시간 모니터링과 앱 연결
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>분석 확장
현재 분석에 사용할 수 있는 웹 SDK와 함께 사용할 수 있는 모든 메서드는 다음과 같습니다.

1. 작업/웹 페이지:
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. 이벤트
   
        engagement.agent.sendEvent(name, extras);
3. 오류
   
        engagement.agent.sendError(name, extras);
4. 교육
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

