---
title: Azure Application Insights의 스마트 검색 | Microsoft Docs
description: Application Insights는 앱 원격 분석의 자동 심층 분석을 수행하여 잠재적 성능 문제에 대해 경고합니다.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 79fe723a7972d265621a1a52ea589bf7dcf6ec62
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536741"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights의 스마트 검색

>[!NOTE]
>경고에 따라 Application Insights 리소스에 대한 스마트 검색을 마이그레이션할 수 있습니다. 마이그레이션은 다양한 스마트 검색 모듈에 대한 경고 규칙을 만듭니다. 일단 만들어지면 다른 Azure Monitor 경고 규칙과 마찬가지로 이러한 규칙을 관리하고 구성할 수 있습니다. 또한 이러한 규칙에 대한 작업 그룹을 구성할 수 있으므로 여러 가지 방법으로 작업을 수행하거나 새 검색에 대한 알림을 트리거할 수 있습니다.
>
> 자세한 내용은 [스마트 검색 경고 마이그레이션](../alerts/alerts-smart-detections-migration.md)을 참조하세요.

스마트 검색은 웹 애플리케이션의 잠재적인 성능 문제와 오류 이상을 자동으로 경고해 줍니다. 앱에서 [Application Insights](./app-insights-overview.md)로 보내는 원격 분석의 사전 분석을 수행합니다. 실패율이나 클라이언트 또는 서버 성능의 비정상적인 패턴이 갑자기 증가하는 경우 경고가 발생합니다. 이 기능에는 구성이 필요하지 않습니다. 애플리케이션에서 충분한 원격 분석을 보내는 경우 작동합니다.

사용자가 받은 이메일 또는 스마트 검색 블레이드를 통해 스마트 검색이 실행한 검색 항목에 액세스할 수 있습니다.

## <a name="review-your-smart-detections"></a>스마트 검색 검토
두 가지 방법으로 자동 관리 검색을 검색할 수 있습니다.

* **전자 메일을 받습니다** . 일반적인 예는 다음과 같습니다.
  
    ![전자 메일 경고](./media/proactive-diagnostics/03.png)
  
    포털에서 더 자세한 정보를 열려면 크게 단추를 클릭합니다.
* Application Insights의 **스마트 검색 블레이드**. 최근 검색 항목 목록을 보려면 **조사** 메뉴에서 **스마트 검색** 을 선택합니다.

![최근 검색 보기](./media/proactive-diagnostics/04.png)

특정 검색을 선택하여 세부 정보를 봅니다.

## <a name="what-problems-are-detected"></a>어떤 문제가 검색되나요?

스마트 검색은 다음과 같은 다양한 문제를 검색하고 알려 줍니다.

* [스마트 감지 - 실패](./proactive-failure-diagnostics.md). 기계 학습을 사용하여 앱에 대해 실패한 요청의 예상 비율을 설정하고 로드 및 다른 요소와 상관 관계를 지정합니다. 실패율이 예상된 범위를 벗어나는 경우 알려 줍니다.
* [스마트 감지 - 성능 이상](./proactive-performance-diagnostics.md). 기록 기준에 비해 작업 또는 종속성 기간의 응답 시간이 느려지는지 여부를 알립니다. 응답 시간 또는 페이지 로드 시간에서 비정상 패턴을 식별하는 경우에도 알림을 제공합니다.   
* 일반적인 성능 저하 및 문제(예: [추적 성능 저하](./proactive-trace-severity.md), [메모리 누수](./proactive-potential-memory-leak.md), [예외 볼륨의 비정상적인 증가](./proactive-exception-volume.md) 및 [보안 안티 패턴](./proactive-application-security-detection-pack.md))

(각 알림에서 도움말 링크를 통해 관련 문서로 이동할 수 있습니다.)

## <a name="smart-detection-email-notifications"></a>스마트 검색 이메일 알림

_미리 보기_ 로 표시된 규칙을 제외한 모든 스마트 검색 규칙은 기본적으로 검색 항목이 발견되면 이메일 알림을 보내도록 구성됩니다.

특정 스마트 검색 규칙에 대한 이메일 알림 구성은 스마트 검색 **설정** 블레이드를 열고 **규칙 편집** 블레이드를 여는 규칙을 선택하여 수행할 수 있습니다.

또는 Azure Resource Manager 템플릿을 사용하여 구성을 변경할 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Application Insights 스마트 검색 규칙 관리](./proactive-arm-config.md)를 참조하세요.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]



## <a name="next-steps"></a>다음 단계
이러한 진단 도구를 사용하면 앱에서 원격 분석을 검사할 수 있습니다.

* [메트릭 탐색기](../essentials/metrics-charts.md)
* [검색 탐색기](./diagnostic-search.md)
* [분석 - 강력한 쿼리 언어](../logs/log-analytics-tutorial.md)

스마트 검색은 자동입니다. 하지만 보다 많은 경고를 설정하고 싶을 수 있습니다.

* [수동으로 구성된 메트릭 경고](../alerts/alerts-log.md)
* [가용성 웹 테스트](./monitor-web-app-availability.md)
