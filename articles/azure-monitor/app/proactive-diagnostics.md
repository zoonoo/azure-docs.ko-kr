---
title: Azure Application Insights의 스마트 감지 | Microsoft Docs
description: Application Insights는 앱 원격 분석의 자동 심층 분석을 수행하여 잠재적 성능 문제에 대해 경고합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/07/2019
ms.openlocfilehash: ae9af5721e53277f6c939840721ca50ea67ca51e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818822"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights의 스마트 감지
 스마트 검색은 웹 응용 프로그램에서 잠재적인 성능 문제 및 오류 비정상에 대해 자동으로 경고 합니다. 앱에서 [Application Insights](../../azure-monitor/app/app-insights-overview.md)로 보내는 원격 분석의 사전 분석을 수행합니다. 실패율이나 클라이언트 또는 서버 성능의 비정상적인 패턴이 갑자기 증가하는 경우 경고가 발생합니다. 이 기능에는 구성이 필요하지 않습니다. 애플리케이션에서 충분한 원격 분석을 보내는 경우 작동합니다.

수신 하는 전자 메일 및 스마트 감지 블레이드에서 스마트 검색에서 실행 한 검색에 액세스할 수 있습니다.

## <a name="review-your-smart-detections"></a>스마트 감지 검토
두 가지 방법으로 자동 관리 검색을 검색할 수 있습니다.

* Application Insights에서 **전자 메일을 받습니다**. 일반적인 예는 다음과 같습니다.
  
    ![전자 메일 경고](./media/proactive-diagnostics/03.png)
  
    포털에서 자세한 정보를 열려면 큰 단추를 클릭합니다.
* Application Insights의 **스마트 감지 블레이드** 입니다. **조사** 메뉴 아래에서 **스마트 검색** 을 선택 하 여 최근 검색 목록을 확인 합니다.

![최근 검색 보기](./media/proactive-diagnostics/04.png)

검색을 선택 하 여 세부 정보를 확인 합니다.

## <a name="what-problems-are-detected"></a>어떤 문제가 검색되나요?
스마트 감지는 다음과 같은 다양 한 문제를 검색 하 고 알려줍니다.

* [스마트 감지 - 실패](../../azure-monitor/app/proactive-failure-diagnostics.md). 기계 학습을 사용하여 앱에 대해 실패한 요청의 예상 비율을 설정하고 로드 및 다른 요소와 상관 관계를 지정합니다. 실패율이 예상된 범위를 벗어나는 경우 경고를 보냅니다.
* [스마트 감지 - 성능 이상](../../azure-monitor/app/proactive-performance-diagnostics.md). 작업 또는 종속성 지속 시간의 응답 시간이 기록 기준과 비교하여 느려지거나 응답 시간 또는 페이지 로드 시간에서 비정상적인 패턴을 식별하는 경우 알림을 받습니다.   
* [추적 저하](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [메모리 누수](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak)와 같은 일반적인 저하 및 문제는 예외 볼륨 및 [보안 앤티앨리어싱 패턴](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack) [의 비정상적인 증가](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) 입니다.

(각 알림에서 도움말 링크를 통해 관련 문서로 이동할 수 있습니다.)

## <a name="smart-detection-email-notifications"></a>스마트 검색 이메일 알림

_미리 보기로_표시 된 규칙을 제외한 모든 스마트 검색 규칙은 검색이 발견 될 때 전자 메일 알림을 보내도록 기본적으로 구성 됩니다.

특정 스마트 검색 규칙에 대한 이메일 알림 구성은 스마트 검색 **설정** 블레이드를 열고 **규칙 편집** 블레이드를 여는 규칙을 선택하여 수행할 수 있습니다.

또는 Azure Resource Manager 템플릿을 사용하여 구성을 변경할 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Application Insights 스마트 검색 규칙 관리를 참조](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)합니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>다음 단계
이러한 진단 도구를 사용하면 앱에서 원격 분석을 검사할 수 있습니다.

* [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)
* [검색 탐색기](../../azure-monitor/app/diagnostic-search.md)
* [분석 - 강력한 쿼리 언어](../../azure-monitor/log-query/get-started-portal.md)

스마트 감지는 완전 자동입니다. 하지만 보다 많은 경고를 설정하고 싶을 수 있습니다.

* [수동으로 구성된 메트릭 경고](../../azure-monitor/app/alerts.md)
* [가용성 웹 테스트](../../azure-monitor/app/monitor-web-app-availability.md) 

