---
title: 미디어 앱에 대한 Azure Mobile Engagement 구현
description: Azure Mobile Engagement 구현을 위한 미디어 앱 시나리오
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8e5d4585b47a4ca26b81b7168aa499f25722415a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-media-app"></a>미디어 앱에 Mobile Engagement 구현
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

## <a name="overview"></a>개요
John은 큰 미디어 회사의 모바일 프로젝트 관리자입니다. 최근에 새로운 앱을 출시했는데 다운로드 수가 매우 높습니다. 다운로드 목표는 달성했지만 사용자당 투자 수익률(ROI)은 필요 조건을 충족하지 못하고 있습니다. 

John은 이미 ROI가 너무 낮은 이유를 확인했습니다. 사용자들이 앱 사용을 2주 후에 중단하는 경우가 빈번하고 이들 중 대부분이 다시 돌아오지 않고 있습니다. 그는 앱에 대한 재방문 주기를 높이고 싶어합니다.

초기 테스트를 통해 사용자에게 푸시 알림을 사용하면 계속해서 앱을 사용하는 경향이 있다는 것을 알게 되었습니다. 또한 사용자에게 보내는 알림에 따라서 비활성 사용자가 앱으로 돌아오는 경우가 자주 있습니다. John은 앱을 위해서 푸시 알림을 통한 고급 타기팅을 사용하는 일종의 Engagement  프로그램에 투자하기로 결심합니다.

John은 최근에 [Azure Mobile Engagement - 모범 사례 시작 가이드](mobile-engagement-getting-started-best-practices.md) 를 읽고 난 후 가이드에 포함된 권장 사항을 구현하기로 결심합니다.

## <a name="objectives-and-kpis"></a>목표 및 KPI
John의 앱에 대한 주요 관련자 사용자가 미디어를 소비하면 광고를 통해 수입이 발생합니다. 사용자당 소비되는 콘텐츠를 증가시키면 John의 수익도 증가됩니다. 광고를 통해 매출을 25%까지 높인다는 한 가지 주요 목표에 모두 동의합니다.  이들은 목표를 평가하고 진행하기 위해서 비즈니스 KPI(핵심 성과 지표)를 만듭니다.

* 사용자당 클릭한 광고 수
* 방문한 페이지 수(사용자별/세션별/주별/월별)
* 즐겨찾는 범주는 무엇인가요?

John은 주요 관련자들과의 회의에 근거하여 비즈니스 KPI를 정의합니다. 그는 [Azure Mobile Engagement - 모범 사례 시작 가이드](mobile-engagement-getting-started-best-practices.md)의 1 단계에 따릅니다. 

그 후, 목표가 확실히 달성될 수 있도록 다음과 같은 Engagement KPI를 만듭니다.

* 하루, 일주일, 2주일, 한달 간격으로 재방문 주기 모니터링
* 활성 사용자 수
* 앱 스토어의 앱 순위 평가

IT 팀의 권고에 따라 다음 질문에 답하기 위해 다음과 같은 기술 KPI가 추가되었습니다.

* 사용자 경로(방문한 페이지, 사용자가 이곳에서 보낸 시간)
* 세션당 발생한 충돌 수 또는 버그 수
* 사용자가 실행하는 OS 버전
* 사용자의 평균 화면 크기
* 사용자가 사용하는 인터넷 연결 종류

그는 각 KPI에 대해 필요한 데이터를 분류하고 플레이 북의 적절한 위치에 기록합니다.

## <a name="engagement-program-and-integration"></a>Engagement 프로그램 및 통합
John은 KPI 정의를 완료한 후에 4개의 Engagement 프로그램과 목표를 정의하여 Engagement 전략 단계를 시작합니다.![][1]

John은 각 프로그램에 대한 푸시 알림을 자세히 열거하면서 깊이 들어갑니다. 푸시 알림은 다섯 가지 요소로 정의됩니다.

1. 목표: 알림의 목표
2. 목표를 달성하는 방법
3. 대상: 알림을 수신하는 사람
4. 콘텐츠: 알림 단어 선택 및 서식(앱 내부/앱 외부)
5. 시기: 푸시 알림을 보내기에 가장 적합한 순간
   
    ![][2]

자세한 내용은 [플레이 북](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)을 참조하세요.

백서의 2 단계에 따라서 John은 대상 섹션을 사용하여 수집해야 하는 데이터가 무엇인지를 정의하고 솔루션을 구현하기 위하여 IT 팀과 공동으로 태그 계획을 작성합니다. 구현 1주일 후, 사용자 승인 테스트 후에 John은 드디어 프로그램을 출시할 수 있게 됩니다.

## <a name="program-results"></a>프로그램 결과
4개월 후 John은 프로그램의 성과를 검토합니다. Welcome Program과 Weekly Program은 목표를 달성하고 있습니다. 한 세션만 사용하는 사용자 수는 감소되었고, 앱의 기능이 더 많이 사용되고 있으며 주별 연결 수는 두 배가 되었습니다.

**Inactive Program** 은 John이 사용자 성향을 이해하는 데 도움이 됩니다. 비활성 사용자의 15%는 앱에 다시 돌아오는 것으로 보입니다. 하지만 이들 대부분이 1달 이상 활성 상태를 유지하지 않습니다. John은 알림을 추가하고 콘텐츠 선택 범위를 확장하여 이렇게 반복되는 상황을 잠재적으로 최적화할 수 있다고 예상합니다.

**Discover Program** 은 성과가 좋지 않습니다. 교차 판매는 증가시켰지만 목표를 달성하기에는 부족합니다. John은 적절한 타기팅을 진행하기에는 데이터가 부족하다는 점을 파악하고 적절한 콘텐츠를 제안합니다. 이 프로그램을 중지하고 Azure Mobile Engagement를 사용하여 “사설 푸시 알림”을 전송하는 것에 중점을 둡니다. 작가는 푸시 알림을 보내기 위한 CMS 솔루션을 이미 가지고 있으며 이것을 바꾸려고 하지 않습니다.

John은 AZME 웹 인터페이스를 사용하지 않고도 도달률 캠페인을 관리할 수 있는 HTTP REST API인 도달률 API를 사용하기로 결심합니다. 이 방식을 사용하면 John은 필요한 데이터를 수집할 수 있고 작가는 CMS 솔루션을 계속 사용할 수 있습니다.

기능이 제대로 작동하도록 하기 위해서 John은 IT 팀에게 다음과 같은 점에 계속 주목하도록 요청했습니다.

1. **운영 체제** : 각자 푸시 알림을 관리하는 자신만의 규칙이 있기 때문에 John은 모든 케이스를 목록으로 모아서 API가 해당 케이스를 처리하는지 확인하기로 합니다.
   예를 들어, Android 푸시 시스템은 전체적인 그림이 가능하지만 iOS의 경우는 그렇지 않습니다.
2. **시간 프레임**: John은 캠페인에 시간 프레임을 설정하고 끝을 설정하는 API를 원합니다. 사용자에게 지장을 줄 정도로 알림을 많이 보내지 않기를 바랍니다.
3. **범주**: 마케팅 팀은 각각의 경고 유형에 대한 템플릿을 준비합니다. John은 IT 팀에게 API 내부에 범주를 설정하도록 요청합니다.

테스트를 거친 후에 John은 만족하게 됩니다. API 덕분에 작가들은 자신의 CMS를 사용하여 푸시 알림을 보낼 수 있고 Azure Mobile Engagement는 이들의 동작 데이터를 모두 수집할 수 있게 됩니다.

초반 4개월이 지난 후에, 전반적으로 만족할만한 성과가 결과에 반영되었고, John과 이사회는 확신을 갖게 되었으며, 사용자당 ROI가 15% 증가하고 모바일 매출이 총 매출의 17.5 %를 차지하면서 단 4개월 만에 7.5%가 증가되었습니다.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
