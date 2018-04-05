---
title: Azure Mobile Engagement와 기타 유사한 Azure 서비스 비교
description: Azure Mobile Engagement와 기타 유사한 Azure 서비스(HockeyApp, AppInsights, Notification Hubs) 비교
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 57be3b235c5f439741155a70df7ddbe8a80906f2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Azure Mobile Engagement와 기타 유사한 Azure 서비스 비교
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

Microsoft Azure에서 제공하는 서비스 목록은 계속 확장되고 있으며 간혹 Azure Mobile Engagement가 최근에 읽거나 소식을 들은 다른 서비스와 어떻게 다른지 궁금할 수 있습니다. 이 문서는 혼동을 명확히 하고 사용자의 용도에 가장 적합할 때 Azure Mobile Engagement를 선택하도록 안내합니다. 

Azure Mobile Engagement는 특히 **디지털 마케팅 담당자/CMO**를 대상으로 하는 서비스이지만 모바일 앱의 사용, 재방문 주기 및 수익성을 늘리려는 **모바일 앱 소유자 또는 게시자**도 사용할 수 있습니다. 

*데이터 기반 통찰력을 앱 사용, 실시간 사용자 구분에 제공하고 컨텍스트 인식 푸시 알림 및 앱 내 메시징을 가능하게 하는 SaaS(Software-as-a-Service) 사용자 참여 플랫폼입니다.* 

이 정의를 보다 구체적으로 살펴보면 고유한 가치 제한을 강조하는 다음과 같은 주요 특징을 포함합니다.

1. **컨텍스트 인식 푸시 알림 및 앱 내 메시징**
   
   이 제품의 핵심 초점은 대상이 지정되고 개인 설정된 푸시 알림을 수행하는 것입니다. 이를 위해 풍부한 행동 분석 데이터를 수집합니다. 
2. **앱 사용에 대한 데이터 기반 통찰력**
   
   앱 사용자에 대한 행동 분석을 수집하기 위해 플랫폼 간 SDK를 제공합니다. 앱 사용자가 앱을 어떻게 사용하고 있는지에 초점을 두므로 성능 분석이 아닌 행동 분석이라는 용어에 주목합니다. 오류, 충돌 등 기본적인 성능 분석을 수집하지만 이것은 제품의 핵심 초점이 아닙니다. 
3. **실시간 사용자 구분**
   
   앱 사용자의 행동 분석 데이터를 수집했으면 다양한 매개 변수 및 수집된 데이터를 기반으로 대상 그룹을 구분하여 대상에 맞는 푸시 캠페인을 실행할 수 있습니다. 
4. **SaaS(Software-as-a-Service):**
   
   앱 사용자에 대한 풍부한 행동 분석을 상호 작용 및 보고 마케팅 푸시 캠페인을 실행하는 데 최적화된 Azure 관리 포털과 별개의 포털을 보유합니다. 신속하게 제품을 사용할 수 있습니다!   

이러한 일련의 차별성과 함께 여기서는 외형적으로 유사한 다른 Azure 제품과 어떻게 비교할지 다룹니다. 이 문서는 기능 수준 비교는 수행하지 않으며 어떤 제품이 잘 작동하는지 정의하기 위해 시나리오 기반 방법을 사용함에 유의합니다.

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) 은 Microsoft의 모바일 DevOps 솔루션입니다. 이를 통해 빌드를 베타 테스터에게 배포하고, 충돌 데이터를 수집하고, 사용자 피드백을 얻을 수 있습니다. 또한 Visual Studio Team Services와 통합되어 있기 때문에 손쉬운 빌드 배포 및 작업 항목 통합이 가능합니다. 
   
   여기서 초점은 DevOps이며 모바일 앱에 대한 성능 분석 데이터를 수집합니다. 앱에서 HockeyApps 및 Mobile Engagement를 모두 통합할 수 있으며 일부 수집된 데이터가 겹치더라도 제품의 핵심 초점은 다르며 사용자의 다른 목표를 달성하는 데 도움이 됩니다.  
2. [Application Insights](../application-insights/app-insights-overview.md) 모바일 앱이 서버 쪽을 포함하는 경우 Application Insights를 사용하여 앱의 웹 서버 쪽을 모니터링하지만 클라이언트 쪽 모바일 앱의 경우 HockeyApp을 사용해야 합니다. 
3. [Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) 는 모바일 앱에 통합된 SDK가 필요하지 않다는 의미에서 간단한 서비스를 제공하며 사용자는 모바일 앱에 대한 모든 권한을 보유할 수 있으며 기본 태그 지정 기능으로 푸시 알림을 보낼 수 있습니다. 대상 지정보다는 앱 사용자에게 즉각적으로 정보를 전송/통신하는 데 관심이 많은 모든 앱 소유자에게 유용합니다(대규모 인구에 브로드캐스트). 
   
   여기서는 기본적인 구분 기능으로 획기적이고 신속한 알림을 보내는 것이 초점입니다. 

몇 가지 시나리오를 살펴보겠습니다.

1. Tim은 사용자를 위한 모바일 앱을 게시하는 Adventure Works 저장소에서 디지털 마케팅 팀에 소속되어 있습니다. Tim의 목표는 자신의 모바일 앱을 다운로드한 사용자가 지속적으로 자주 앱을 사용하도록 하는 것입니다. 앱 사용자의 브랜드 연결성을 높이고 앱 사용자가 모바일 앱을 사용하여 구매할 때 수익화를 높입니다. 이 경우 Tim은 사용자에게 도움이 되는 대상 지정된 알림을 앱 사용자에게 전송하여 사용자가 앱을 열고 자주 다시 이용하도록 장려해야 합니다. 이 경우 Tim은 Mobile Engagement가 유용하다는 것을 알게 됩니다. 
2. Joann은 Adventure Works에서 모바일 앱 개발 팀에 소속되어 있으며 충돌, 예외에 대한 세부 정보를 로깅하고 앱에서 성능 원격 분석을 가져올 제품을 찾고 있습니다. 이 경우 Joann는 HockeyApp이 유용하다는 것을 알게 됩니다. Joann은 이 둘을 최대한 활용하기 위해 개발자 중심 시나리오를 위한 HockeyApp과 Tim을 위한 Azure Mobile Engagement를 동일한 앱에 통합할 수 있습니다. 
3. Robin은 Contoso News 네트워크에서 모바일 앱 개발 팀에 소속되어 있으며 새 경고가 트리거 되자마자 별다른 구분 없이 모든 사용자에게 뉴스 속보 알림을 전송하기만 하면 됩니다. 이 경우 Robin은 Notification Hubs가 유용하다는 것을 알게 됩니다. 
   하지만 이 시나리오에서는 모바일 앱이 성장함에 따라 보다 세분화된 구분이 필요하며 앱 사용자의 행동에 대한 정보를 얻어야 할 수 있습니다. 이때 Robin은 Azure Mobile Engagement를 평가해야 합니다. 

정리하자면, Mobile Engagement의 목적은 단지 분석을 수집하는 것이 아니며 "Microsoft의 또 다른 분석 제품"이 아닙니다. 대상 지정된 푸시 알림을 전송하는 것을 포함하며 이 대상 지정을 위해 행동 분석 데이터를 수집하지만 앱 사용자가 스팸으로 지나치지 않도록 가장 이해하기 쉬운 푸시 알림을 보내는 데 초점을 둡니다. 

자세한 내용은 Mobile Engagement에 대해 간단하게 설명하는 이 [짧은 비디오](mobile-engagement-overview.md) 를 살펴보세요. 

