---
title: Azure Application Insights의 응용 프로그램 맵 | Microsoft Docs
description: 응용 프로그램 맵을 사용하여 복잡한 응용 프로그램 토폴로지 모니터링
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 9b39eef5accec4764f61ab31dd894d368242ee3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094653"
---
# <a name="application-map-triage-distributed-applications"></a>응용 프로그램 맵: 분산 응용 프로그램 심사

응용 프로그램 맵을 사용하면 분산된 응용 프로그램의 모든 구성 요소에서 성능 병목 상태 또는 실패 핫스폿을 찾는 데 도움이 됩니다. 맵의 각 노드는 응용 프로그램 구성 요소 또는 해당 종속성을 나타내며 상태 KPI 및 경고 상태를 나타냅니다. 구성 요소부터 Application Insights 이벤트와 같은 보다 자세한 진단까지 클릭하면서 살펴볼 수 있습니다. 앱에서 Azure 서비스를 사용하는 경우 SQL Database Advisor 권장 사항과 같은 Azure 진단도 살펴볼 수 있습니다.

## <a name="what-is-a-component"></a>구성 요소란?

구성 요소는 독립적으로 배포할 수 있는 분산/마이크로 서비스 응용 프로그램의 부분입니다. 개발자 및 운영 팀은 이러한 응용 프로그램 구성 요소에서 생성된 원격 분석에 대한 코드 수준의 가시성 또는 액세스 권한을 갖습니다. 

* 구성 요소는 팀/조직에서 액세스할 수 없는 SQL, EventHub 등과 같은 "관찰된" 외부 종속성(코드 또는 원격 분석)과 다릅니다.
* 구성 요소는 서버/역할/컨테이너 인스턴스의 수에 관계없이 실행됩니다.
* 구성 요소는 별도의 Application Insights 계측 키(구독이 다른 경우에도) 또는 단일 Application Insights 계측 키에 보고하는 다른 역할일 수 있습니다. 맵 미리 보기 환경은 구성 요소를 설정 방식에 관계없이 표시합니다.

## <a name="composite-application-map"></a>복합 응용 프로그램 맵

여러 수준의 관련된 응용 프로그램 구성 요소의 전체 응용 프로그램 토폴로지를 볼 수 있습니다. 구성 요소는 다른 Application Insights 리소스이거나 단일 리소스 내의 다른 역할일 수 있습니다. 앱은 Application Insights SDK가 설치된 서버 간에 수행된 HTTP 종속성 호출을 따라 구성 요소를 찾습니다. 

이러한 환경은 구성 요소를 점진적으로 검색으로 시작됩니다. 응용 프로그램 맵을 처음 로드할 때 이 구성 요소와 관련된 구성 요소를 검색하기 위해 쿼리 집합이 트리거됩니다. 왼쪽 위 구석에 있는 단추는 검색된 응용 프로그램의 구성 요소 수로 업데이트됩니다. 

"맵 구성 요소 업데이트"를 클릭하면 맵이 해당 시점까지 검색된 모든 구성 요소로 새로 고쳐집니다.

모든 구성 요소가 단일 Application Insights 리소스 내의 역할인 경우 이 검색 단계가 필요하지 않습니다. 이러한 응용 프로그램에 대한 초기 로드 시에는 모든 구성 요소가 포함됩니다.

![응용 프로그램 맵 스크린샷](media/app-insights-app-map/001.png)

수백 가지 구성 요소가 있는 복잡한 토폴로지를 시각화하는 것이 이 환경의 주요 목표 중 하나입니다.

모든 구성 요소를 클릭하여 관련 정보를 확인하고, 해당 구성 요소에 대한 성능 및 실패 심사 환경으로 이동합니다.

![플라이아웃](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>오류 조사

**오류 조사**를 선택하여 오류 창을 실행합니다.

![오류 조사 단추 스크린샷](media/app-insights-app-map/investigate-failures.png)

![오류 환경 스크린샷](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>성능 조사

성능 문제를 해결하려면 **성능 조사**를 선택합니다.

![성능 조사 단추 스크린샷](media/app-insights-app-map/investigate-performance.png)

![성능 환경 스크린샷](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>세부 정보로 이동

**세부 정보로 이동**을 선택하여 호출 스택 수준으로 수행된 보기를 제공할 수 있는 종단 간 트랜잭션 환경을 탐색합니다.

![세부 정보로 이동 단추 스크린샷](media/app-insights-app-map/go-to-details.png)

![종단 간 트랜잭션 세부 정보 스크린샷](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Analytics에서 보기

응용 프로그램 데이터를 더 쿼리하고 조사하려면 **Analytics에서 보기**를 클릭합니다.

![Analytics에서 보기 단추 스크린샷](media/app-insights-app-map/view-in-analytics.png)

![Analytics 환경 스크린샷](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>경고

경고가 트리거될 수 있는 활성 경고 및 기본 규칙을 보려면 **경고**를 선택합니다.

![경고 단추 스크린샷](media/app-insights-app-map/alerts.png)

![Analytics 환경 스크린샷](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>사용자 의견
포털 사용자 의견 옵션을 통해 피드백을 제공해주세요.

![MapLink-1 이미지](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>다음 단계

* [Azure Portal](https://portal.azure.com)
