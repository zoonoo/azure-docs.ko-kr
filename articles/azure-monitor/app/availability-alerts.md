---
title: Azure Application Insights를 사용 하 여 가용성 경고 설정 | Microsoft Docs
description: Application Insights에서 웹 테스트를 설정합니다. 웹 사이트가 사용할 수 없게 되거나 느리게 응답하는 경우 알림이 제공됩니다.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305088"
---
# <a name="availability-alerts"></a>가용성 경고

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)는 전세계 지점에서 정기적인 간격으로 애플리케이션에 웹 요청을 보냅니다. 응용 프로그램이 응답 하지 않는 경우 또는 응답 속도가 너무 느린 경우 있습니다 경고할 수 있습니다.

## <a name="enable-alerts"></a>경고 사용

경고는 기본적으로 자동으로 활성화 되어 이제 하지만 완벽 하 게 경고를 구성 하려면 먼저 해야 처음에 가용성 테스트를 만듭니다.

![환경 만들기](./media/availability-alerts/create-test.png)

> [!NOTE]
>  [새로 통합된 경고](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)를 사용하여 경고 규칙 심각도 및 [작업 그룹](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)이 포함된 알림 기본 설정은 경고 환경에서 구성**되어야 합니다**. 다음 단계 없이도 포털 내 알림을 받게 됩니다.

1. 가용성 테스트를 저장한 후 세부 정보 탭에서 방금 만든 테스트 옆에 있는 줄임표를 클릭합니다. "경고 편집"을 클릭합니다.

   ![편집 후 저장](./media/availability-alerts/edit-alert.png)

2. 원하는 심각도 수준, 규칙 설명 및 가장 중요한 것으로 이 경고 규칙에 대해 사용하려는 알림 기본 설정을 보유한 작업 그룹을 설정합니다.

   ![편집 후 저장](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Y 위치에서 오류를 보고하는 X에 대해 경고

Y 위치에서 X 경고 규칙은 새 가용성 테스트를 만들 때 기본적으로 [새로 통합된 경고 환경](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)에서 사용하도록 설정됩니다. "클래식" 옵션을 선택하거나 경고 규칙을 사용하지 않도록 선택하여 옵트아웃할 수 있습니다.

> [!NOTE]
> 위의 단계에 따라 경고가 트리거될 때 알림을 받으려면 작업 그룹을 구성합니다. 이 단계가 없으면 규칙이 트리거될 때 포털 내 알림만 받게 됩니다.
>

### <a name="alert-on-availability-metrics"></a>가용성 메트릭에 대한 경고

[새로 통합된 경고](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)를 통해 분할된 집계 가용성 및 테스트 지속 시간 메트릭에 대해서도 경고할 수 있습니다.

1. 메트릭 환경에서 Application Insights 리소스를 선택하고, 가용성 메트릭:

    ![가용성 메트릭 선택](./media/availability-alerts/select-metric.png)

2. 메뉴에서 경고 구성 옵션을 사용하면 경고 규칙을 설정할 특정 테스트 또는 위치를 선택할 수 있는 새 환경으로 이동합니다. 또한 여기에서 이 경고 규칙에 대한 작업 그룹을 구성할 수도 있습니다.

### <a name="alert-on-custom-analytics-queries"></a>사용자 지정 분석 쿼리에 대한 경고

[새로 통합된 경고](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)를 통해 [사용자 지정 로그 쿼리](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log)에 대해 경고할 수 있습니다. 사용자 지정 쿼리를 통해 임의의 모든 조건에 대해 경고하여 가용성 문제에서 가장 안정적인 신호를 얻을 수 있습니다. TrackAvailability SDK를 사용하여 사용자 지정 가용성 결과를 보내는 경우에도 이 방식을 적용할 수 있습니다. 

> [!Tip]
> 가용성 데이터에 대한 메트릭에는 TrackAvailability SDK를 호출하여 제출할 수 있는 사용자 지정 가용성 결과가 모두 포함됩니다. 사용자 지정 가용성 결과를 경고하려면 메트릭 지원에 대한 경고를 사용할 수 있습니다.
>

## <a name="troubleshooting"></a>문제 해결

Dedicated [문제 해결 문서](troubleshoot-availability.md)합니다.

## <a name="next-steps"></a>다음 단계

* [다중 단계 웹 테스트](availability-multistep.md)
* [Url ping 웹 테스트](monitor-web-app-availability.md)

