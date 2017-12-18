---
title: "OMS(Operations Management Suite)의 Log Analytics란? | Microsoft Docs"
description: "Log Analytics는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성된 운영 데이터를 수집 및 분석하도록 도와주는 OMS(Operations Management Suite)의 서비스입니다.  이 문서에서는 Log Analytics의 여러 구성 요소에 대한 간략한 개요 및 자세한 내용에 대한 링크를 제공합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2017
ms.author: bwren
ms.openlocfilehash: 7f12958550738ff465c06d0e5d774d8bffa0b90b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
---
# <a name="what-is-log-analytics"></a>Log Analytics란?
Log Analytics는 클라우드 및 온-프레미스 환경을 모니터링하여 해당 가용성 및 성능을 유지하는[\(OMS\)Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md)의 서비스입니다.  이 서비스는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성되고 여러 원본에 대한 분석을 제공하는 다른 모니터링 도구에서 생성된 데이터를 수집합니다.  이 문서에서는 Log Analytics에서 제공하는 값, 작동하는 방법의 개요 및 더 알아볼 수 있도록 더 자세한 내용에 대한 링크를 간략하게 설명합니다.

## <a name="is-log-analytics-for-you"></a>Log Analytics를 사용하시겠습니까?
현재 사용자의 Azure 환경에 모니터링을 설정하지 않은 경우 Azure 리소스에 대한 모니터링 데이터를 수집하고 분석하는 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)를 시작해야 합니다.  Log Analytics는 [Azure Monitor의 데이터를 수집](log-analytics-azure-storage.md)하여 다른 데이터와 상호 연결하고 추가 분석을 제공할 수 있습니다.

온-프레미스 환경을 모니터링하거나 Azure Monitor 또는 System Center Operations Manager와 같은 서비스를 사용하는 기존 모니터링이 있는 경우 Log Analytics를 유용하게 사용할 수 있습니다.  에이전트 및 이러한 다른 도구에서 단일 리포지토리로 직접 데이터를 수집할 수 있습니다.  로그 검색, 뷰 및 솔루션과 같은 Log Analytics의 분석 도구는 전체 사용자 환경의 중앙 집중식 분석을 제공하는 모든 수집된 데이터에 대해 작동합니다.


## <a name="using-log-analytics"></a>Log Analytics 사용
수집된 데이터를 분석 및 조작하기 위해 모든 브라우저에서 실행되고 구성 설정 및 여러 도구에 대한 액세스를 제공하는 OMS 포털 또는 Azure Portal을 통해 Log Analytics에 액세스할 수 있습니다.  포털에서 수집된 데이터를 분석할 쿼리를 작성하는 [로그 검색](log-analytics-log-searches.md), 가장 중요한 검색에 대한 그래픽 보기로 사용자 지정할 수 있는 [대시보드](log-analytics-dashboards.md) 및 추가 기능과 분석 도구를 제공하는 [솔루션](log-analytics-add-solutions.md)을 활용할 수 있습니다.

아래 이미지는 작업 영역에 설치되어 있는 [솔루션](#add-functionality-with-management-solutions)에 대한 요약 정보를 표시하는 대시보드를 보여 주는 OMS 포털을 보여 줍니다.  해당 솔루션에 대한 데이터를 자세히 알아보려면 타일을 클릭하면 됩니다.

![OMS 포털](media/log-analytics-overview/portal.png)

Log Analytics는 리포지토리의 데이터를 신속하게 검색하고 통합할 수 있는 쿼리 언어를 포함합니다.  [로그 검색](log-analytics-log-searches.md)을 만들고 저장하여 포털에서 데이터를 직접 분석하거나, 로그 검색을 자동으로 실행하여 쿼리 결과가 중요한 조건을 나타내는 경우 경고를 만들 수 있습니다.

![로그 검색](media/log-analytics-overview/log-search.png)

전체 환경의 상태에 대한 빠른 그래픽 보기를 가져오려면 저장된 로그 검색에 대한 시각화를 [대시보드](log-analytics-dashboards.md)에 추가할 수 있습니다.   

![대시보드](media/log-analytics-overview/dashboard.png)

Log Analytics 외부 데이터를 분석하기 위해 OMS 리포지토리에서 [Power BI](log-analytics-powerbi.md) 또는 Excel과 같은 도구로 데이터를 내보낼 수 있습니다.  또한 [Log Search API](log-analytics-log-search-api.md)를 통해 Log Analytics 데이터를 활용하는 사용자 지정 솔루션을 빌드하거나 다른 시스템에 통합할 수 있습니다.

## <a name="add-functionality-with-management-solutions"></a>관리 솔루션에서 기능 추가
[관리 솔루션](log-analytics-add-solutions.md)은 OMS에 기능을 추가하여 Log Analytics에 추가 데이터 및 분석 도구를 제공합니다.  또한 대시보드의 솔루션에서 제공하는 로그 검색으로 분석할 수 있거나 추가 사용자 인터페이스에서 분석할 수 있는 수집할 새 레코드 유형을 정의할 수 있습니다.  아래 예제 이미지에서는 [변경 내용 추적 솔루션](log-analytics-change-tracking.md)을 보여 줍니다.

![변경 내용 추적 솔루션](media/log-analytics-overview/change-tracking.png)

다양한 기능에 솔루션을 사용할 수 있으며 추가 솔루션을 지속적으로 추가합니다.  사용 가능한 솔루션을 쉽게 찾아서 솔루션 갤러리 또는 Azure Marketplace의 [OMS 작업 영역에 추가](log-analytics-add-solutions.md)할 수 있습니다.  보통 구성이 필요할 수 있는 다른 프로그램과 달리, 대부분 자동으로 배포되며 즉시 작업을 시작합니다.

![솔루션 갤러리](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Log Analytics 구성 요소
Log Analytics의 핵심은 Azure 클라우드에서 호스트되는 OMS 리포지토리입니다.  데이터 원본을 구성하고 구독에 솔루션을 추가하면 연결된 원본에서 리포지토리로 데이터가 수집됩니다.  데이터 원본 및 솔루션은 각각 고유한 속성 집합을 가진 서로 다른 레코드 유형을 만들지만 리포지토리에 대한 쿼리에서 여전히 함께 분석할 수 있습니다.  따라서 동일한 도구 및 메서드를 사용하여 다양한 원본에서 수집된 여러 종류의 데이터로 작업할 수 있습니다.

![OMS 리포지토리](media/log-analytics-overview/overview.png)

연결된 원본은 Log Analytics에서 수집된 데이터를 생성하는 컴퓨터 및 기타 리소스입니다.  여기에는 직접 연결된 [Windows](log-analytics-windows-agent.md) 및 [Linux](log-analytics-linux-agents.md) 컴퓨터에 설치된 에이전트 또는 [연결된 System Center Operations Manager 관리 그룹](log-analytics-om-agents.md)의 에이전트가 포함될 수 있습니다.  Azure 리소스의 경우 Log Analytics는 [Azure Monitor 및 Azure 진단](log-analytics-azure-storage.md)에서 데이터를 수집합니다.

[데이터 원본](log-analytics-data-sources.md) 은 각각의 연결된 원본에서 수집되는 여러 종류의 데이터입니다.  여기에는 [Windows](log-analytics-data-sources-windows-events.md) 및 Linux 에이전트의 [이벤트](log-analytics-data-sources-windows-events.md) 및 [성능 데이터](log-analytics-data-sources-performance-counters.md)뿐만 아니라 [IIS 로그](log-analytics-data-sources-iis-logs.md) 및 [사용자 지정 텍스트 로그](log-analytics-data-sources-custom-logs.md)와 같은 원본이 포함됩니다.  수집할 각 데이터 원본을 구성하면 각 연결된 원본에 구성이 자동으로 전달됩니다.

사용자 지정 요구 사항이 있는 경우 [HTTP 데이터 수집기 API](log-analytics-data-collector-api.md)를 사용하여 REST API 클라이언트에서 리포지토리로 데이터를 작성할 수 있습니다.

## <a name="log-analytics-architecture"></a>Log Analytics 아키텍처
중앙 구성 요소가 Azure 클라우드에서 호스트되기 때문에 Log Analytics의 배포에는 최소한의 요구 사항만 제공됩니다.  여기에는 리포지토리와 함께 수집된 데이터를 상호 연결하고 분석할 수 있는 서비스가 포함됩니다.  모든 브라우저에서 포털에 액세스할 수 있으므로 클라이언트 소프트웨어에 대한 요구 사항이 없습니다.

[Windows](log-analytics-windows-agent.md) 및 [Linux](log-analytics-linux-agents.md) 컴퓨터에 에이전트를 설치해야 하지만 이미 [연결된 SCOM 관리 그룹](log-analytics-om-agents.md)의 멤버인 컴퓨터에 필요한 추가 에이전트는 없습니다.  SCOM 에이전트는 Log Analytics로 데이터를 전달할 관리 서버와 계속 통신합니다.  하지만 일부 솔루션의 경우 에이전트가 Log Analytics와 직접 통신해야 합니다.  해당 통신 요구 사항은 각 솔루션에 대한 설명서에 지정됩니다.

[Log Analytics에 등록](log-analytics-get-started.md)하면 OMS 작업 영역이 만들어집니다.  작업 영역이 고유한 데이터 리포지토리, 데이터 원본 및 솔루션을 포함한 고유한 Log Analytics 환경이라고 생각하면 됩니다. 프로덕션 및 테스트와 같은 여러 환경을 지원하기 위해 구독에 여러 작업 영역을 만들 수 있습니다.

![Log Analytics 아키텍처](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>다음 단계
* [무료 Log Analytics 계정에 등록](log-analytics-get-started.md) 하여 자신의 환경을 테스트합니다.
* OMS 리포지토리로 수집하는 데 사용할 수 있는 여러 [데이터 원본](log-analytics-data-sources.md) 을 확인합니다.
* [솔루션 갤러리에서 사용 가능한 솔루션을 찾아서](log-analytics-add-solutions.md) Log Analytics에 기능을 추가합니다.

