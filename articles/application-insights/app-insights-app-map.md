---
title: "Application Insights의 응용 프로그램 맵 | Microsoft Docs"
description: "앱 구성 요소 간 종속성의 시각적 프레젠테이션은 KPI 및 경고로 레이블이 지정되었습니다."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: douge
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc2d3c3fee5abbab0eab16c106c88c8753e703cc
ms.openlocfilehash: f7ee8a43c83b1bad53acdb71de7a66d8afaa09b2


---
# <a name="application-map-in-application-insights"></a>Application Insights의 응용 프로그램 맵
[ Application Insights](app-insights-overview.md)에서 응용 프로그램 맵은 응용 프로그램 구성 요소의 종속성 관계를 시각적으로 레이아웃합니다. 각 구성 요소는 부하, 성능, 오류 및 경고와 같은 KPI를 보여 주어 성능 문제 또는 실패를 유발하는 모든 구성 요소를 발견할 수 있도록 합니다. 구성 요소부터 Application Insights 이벤트와 같은 보다 자세한 진단까지 클릭하면서 살펴볼 수 있습니다. 앱에서 Azure 서비스를 사용하는 경우 SQL Database Advisor 권장 사항과 같은 Azure 진단도 살펴볼 수 있습니다.

다른 차트와 같이 Azure 대시보드에 응용 프로그램 맵을 고정할 수 있으며 이 때 완벽하게 작동됩니다. 

## <a name="open-the-application-map"></a>응용 프로그램 맵을 엽니다.
응용 프로그램에 대한 개요 블레이드에서 맵을 엽니다.

![앱 맵 열기](./media/app-insights-app-map/01.png)

![앱 맵](./media/app-insights-app-map/02.png)

지도에서는 다음을 보여 줍니다.

* 가용성 테스트
* 클라이언트 쪽 구성 요소(JavaScript SDK를 사용하여 모니터링됨)
* 서버 쪽 구성 요소
* 클라이언트 및 서버 구성 요소의 종속성

종속성 링크 그룹을 확장하고 축소할 수 있습니다.

![축소](./media/app-insights-app-map/03.png)

SQL, HTTP 등 한 종류의 종속성이 많은 경우 그룹화되어 표시될 수 있습니다. 

![그룹화된 종속성](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>문제 발견
각 노드에는 해당 구성 요소에 대한 부하, 성능 및 실패율과 같은 관련 성능 지표가 있습니다. 

경고 아이콘은 발생 가능한 문제를 강조 표시합니다. 주황색 경고는 요청, 페이지 보기 또는 종속성 호출에서 오류가 발생했음을 의미합니다. 빨간색 경고는 5% 이상의 실패율을 의미합니다.

![오류 아이콘](./media/app-insights-app-map/04.png)

활성 경고도 다음과 같이 표시됩니다. 

![활성 경고](./media/app-insights-app-map/05.png)

SQL Azure를 사용하는 경우 성능을 향상시키는 방법에 대한 권장 사항이 있는 경우 나타나는 아이콘이 있습니다. 

![Azure 권장 사항](./media/app-insights-app-map/06.png)

자세한 내용을 보려면 아이콘을 클릭합니다.

![Azure 권장 사항](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>진단 클릭
맵의 각 노드에서 진단하기 위해 클릭할 대상을 지정합니다. 옵션은 노드 유형에 따라 다릅니다.

![서버 옵션](./media/app-insights-app-map/09.png)

Azure에서 호스팅되는 구성 요소의 경우 옵션은 직접 링크를 포함합니다.

## <a name="filters-and-time-range"></a>필터 및 시간 범위
기본적으로 맵은 선택한 시간 범위에 사용할 수 있는 모든 데이터를 요약합니다. 하지만 특정 작업 이름 또는 종속성을 포함하도록 필터링할 수 있습니다.

* 작업 이름: 페이지 보기 및 서버 쪽 요청 형식을 모두 포함합니다. 이 옵션을 사용하면 맵은 선택한 작업에 대해서만 서버/클라이언트 쪽 노드의 KPI를 보여 줍니다. 특정 작업의 컨텍스트에서 호출될 종속성을 보여 줍니다.
* 종속성 기본 이름: AJAX 브라우저 종속성 및 서버 쪽 종속성을 포함합니다. TrackDependency API를 사용하여 사용자 지정 종속성 원격 분석을 보고하는 경우 여기에도 표시됩니다. 지도에 표시되는 종속성을 선택할 수 있습니다. 현재 이 선택 항목은 서버 쪽 요청 또는 클라이언트 쪽 페이지 보기를 필터링하지 않습니다.

![필터 설정](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>필터 저장
적용한 필터를 저장하려면 필터링된 보기를 [대시보드](app-insights-dashboards.md)에 고정합니다.

![대시보드에 고정](./media/app-insights-app-map/12.png)

## <a name="end-to-end-system-app-maps"></a>종단 간 시스템 앱 맵

응용 프로그램에 여러 구성 요소(예: 백 엔드 서비스 및 웹앱)가 있는 경우 하나의 통합된 앱 맵에 모두 표시할 수 있습니다.

![필터 설정](./media/app-insights-app-map/multi-component-app-map.png)

앱 맵은 현재 리소스 그룹 내의 모든 Application Insights 리소스를 검색하여 서버 노드를 찾습니다. 또한 현재 리소스 그룹에서 Application Insights 리소스에 의해 추적되는 모든 종속성 호출을 따라가면서 서버 노드를 검색합니다.


### <a name="setting-up"></a>설치

> [!NOTE] 
> 종단 간 시스템 앱 맵은 미리 보기 상태입니다. SDK의 특수 버전으로 구성 요소를 계측해야 하며 특수 URL을 사용하여 앱 맵을 확인해야 합니다. [종단 간 시스템 앱 맵을 설정하는 방법을 알아봅니다](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-app-map-preview.md).


## <a name="feedback"></a>사용자 의견
[포털 사용자 의견 옵션을 통해 피드백을 제공](app-insights-get-dev-support.md)해주세요.

![MapLink-1 이미지](./media/app-insights-app-map/13.png)




<!--HONumber=Nov16_HO4-->


