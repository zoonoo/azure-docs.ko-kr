---
title: Azure Mobile Engagement - 주요 기능
description: Azure Mobile Engagement의 주요 기능 설명
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d98bafb6-4fd0-4cc3-8c2f-962af70c416c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ce160fbc78c7069b0114599455403e5e20771c6c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---key-features"></a>Azure Mobile Engagement - 주요 기능
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서에서는 Mobile Engagement 플랫폼의 주요 기능에 대한 간략한 개요를 제공합니다. 

## <a name="general"></a>**일반**
* **모든 주요 플랫폼에 대한 SDK 찾기** iOS, Android, 유니버설 Windows, Windows Phone Silverlight, Kindle, Cordova 등 모든 주요 플랫폼에 대해 사용 가능한 SDK. 
  선택한 플랫폼에서 시작하는 데 유용한 설명서와 간편한 SDK 통합을 제공합니다. 
* **별도의 SaaS 포털** Azure 관리 포털을 통하지 않고 마케팅 팀에 쉽게 액세스할 수 있습니다. 
* **오픈 REST API의 가용성** 오픈 플랫폼 API를 사용하여 CRM/CMS/IT 시스템으로 통합 및 자동화하기 위해 Mobile Engagement와 쉽게 통합 및 자동화할 수 있도록 해주는 API를 사용하는 .NET SDK와 오픈 REST API를 제공합니다. 자세한 내용은 [여기](mobile-engagement-api-authentication.md) 를 참조하세요. 
* **Power BI 커넥터 사용 가능** 또한 키 분석 차트를 Power BI 대시보드로 끌어올 수 있습니다. 이 [가이드](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-mobile/)를 참조하세요.
* **보안 및 개인 정보 보증** Azure 제품군의 일부인 Azure Mobile Engagement는 클라우드 서비스에 대해 예상되는 보안 및 개인 정보에 관한 모든 표준 모범 사례를 따릅니다.

## <a name="actionable-analytics"></a>**실행 가능한 분석**
* **실시간으로 데이터 모니터링** 세션, 이벤트, 오류 및 충돌을 모두 실시간으로 보여 주는 Monitor 모듈을 사용하여 실시간 분석을 추적할 수 있습니다. 기본 개념을 이해하기 위해 이 [문서](mobile-engagement-concepts.md) 를 살펴보겠습니다. 
  
    ![][1]
  
    ![][2]        
* **집계된 데이터 보기** 앱 버전 및 기간을 기준으로 데이터를 쉽게 필터링할 수 있는 Analytics 모듈을 사용하여 집계된 분석 데이터를 다양하게 볼 수 있습니다.
  
    ![][3]        
* **사용자 및 재방문 주기 패턴에 대한 정보 얻기**
  
    ![][4]        
* **사용자의 출처와 화면에서 시간을 얼마나 소요하는지에 대한 정보 얻기**
  
    ![][5]        
  
    ![][6]        
* **앱 사용자가 방문하고 있는 화면이 무엇이고 사용자 경로를 어떻게 최적화할 수 있는지 알아보기** 사용자가 원하는 화면 및 원하는 기능을 검색할 수 있도록 합니다.
  
    ![][7]        
  
    ![][8]        
* **앱에서 가장 자주 발생하는 이벤트에 대한 정보 얻기 및 이러한 이벤트를 기반으로 비즈니스 프로세스 이해** 
  
    ![][9]    
* **일반적인 오류와 충돌 추적 및 개발자 팀에 대한 정보 얻기**
  
    ![][10]        
  
    ![][11]    
* **앱 사용자가 앱을 최적화하기 위해 어떤 장치 및 네트워크에서 앱에 액세스하고 있는지 이해** 
  
    ![][12]    

## <a name="targeted--personalized-push-notifications"></a>**대상 지정 및 개인 설정된 푸시 알림**
* **수집된 데이터를 기반으로 세그먼트 만들기** 이를 위해 이벤트/세션/활동/작업/충돌/오류/태그 데이터를 사용할 수 있습니다.
  
    ![][13]
  
    ![][14]        
* **일별로 만든 세그먼트 기록 추적**
  
    ![][15]    
* **대상 지정된 알림 보내기** 기존/신규 사용자 등과 같이 일반적으로 사용되거나 사용자 지정하여 생성된 세그먼트를 대상으로 함
  
    ![][16]    
* **시나리오에 적절한 앱/시스템 알림 및 풍부한 HTML 기반 앱 내 푸시 알림 전송**
  
    ![][17]    
  
    ![][18]    
* **앱에서 특정 화면/활동에 표시할 앱 내 알림 대상 지정**
  
    ![][19]    
* **사용자가 알림을 클릭할 때 "작업" 지정** 웹 페이지를 열거나 앱 내에서 알림을 클릭하여 특정 화면으로 이동하는 것처럼 간단할 수 있습니다. 
  
    ![][20]
* **지역화된 알림 보내기** 로 앱 사용자에게 가장 편안한 언어로 주의를 끕니다. 
  
    ![][21]    
* **캠페인의 시작 및 종료 시간 지정** 
  
    ![][22]    
* **알림을 쉽게 테스트** 테스트 장치를 등록하고 테스트 알림을 이 장치에만 전송하여 테스트합니다.
  
    ![][23]    
* **간단한 설문 조사로 표시하도록 앱 내 알림 쉽게 설정**  
  
    ![][24]
* **알림에 대한 캠페인 통계 가져오기** 알림이 얼마나 성공적인지에 대한 정보를 얻을 수 있습니다.
  
    ![][25]    
* **앱 정보/태그 및 이모지를 사용하여 쉽게 개인 설정 및 알림에 문자 지정** 
  
    ![][26]    
  
    ![][27]    
* **사용자 스팸 방지를 위해 푸시 제한 설정** 앱 사용자에게 많은 양의 푸시를 보내 스팸으로 분류되도록 하고 싶지 않습니다. 이 경우 세그먼트의 세분성에서 푸시 제한을 구성할 수 있는 푸시 제한 기능이 유용합니다. 
  
    ![][28]            

<!-- Images -->
[1]: ./media/mobile-engagement-key-features/monitor1.png
[2]: ./media/mobile-engagement-key-features/monitor2.png
[3]: ./media/mobile-engagement-key-features/analytics-filter.png
[4]: ./media/mobile-engagement-key-features/retention.png
[5]: ./media/mobile-engagement-key-features/analytics-geomap.png
[6]: ./media/mobile-engagement-key-features/analytics-session-length.png
[7]: ./media/mobile-engagement-key-features/analytics-activities.png
[8]: ./media/mobile-engagement-key-features/analytics-userpath.png
[9]: ./media/mobile-engagement-key-features/analytics-events.png
[10]: ./media/mobile-engagement-key-features/analyics-errors.png
[11]: ./media/mobile-engagement-key-features/analyics-errors-details.png
[12]: ./media/mobile-engagement-key-features/technicals.png
[13]: ./media/mobile-engagement-key-features/segment.png
[14]: ./media/mobile-engagement-key-features/segment-creation.png
[15]: ./media/mobile-engagement-key-features/segment-history.png
[16]: ./media/mobile-engagement-key-features/segment-push.png
[17]: ./media/mobile-engagement-key-features/out-of-app.png
[18]: ./media/mobile-engagement-key-features/in-app-push.png
[19]: ./media/mobile-engagement-key-features/push-in-activity.png
[20]: ./media/mobile-engagement-key-features/push-action.png
[21]: ./media/mobile-engagement-key-features/push-languages.png
[22]: ./media/mobile-engagement-key-features/push-timeframe.png
[23]: ./media/mobile-engagement-key-features/push-test.png
[24]: ./media/mobile-engagement-key-features/push-poll.png
[25]: ./media/mobile-engagement-key-features/push-stats.png
[26]: ./media/mobile-engagement-key-features/push_personalized.png
[27]: ./media/mobile-engagement-key-features/push_emoji.png
[28]: ./media/mobile-engagement-key-features/push_limits.png









