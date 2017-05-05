---
title: "Microsoft Azure 및 Azure Monitor의 경고 개요 | Microsoft Docs"
description: "경고를 통해 Azure 리소스 메트릭, 이벤트 또는 로그를 모니터링하고 사용자가 지정한 조건에 부합하면 알림을 받을 수 있습니다."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 76c8feb077cca27dc96f43e708cdef4fbb0f824c
ms.lasthandoff: 03/31/2017


---
# <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고란?
이 문서에서는 경고에 대해 설명하고 그 이점과 경고 사용을 시작하는 방법을 소개합니다. 특히 Azure Monitor에 적용되지만 다른 서비스에 포인터를 제공합니다.  

경고는 Azure 리소스 메트릭, 이벤트 또는 로그를 모니터링하고 사용자가 지정한 조건에 부합하면 알림을 보내는 방법입니다.  

## <a name="alerts-in-different-azure-services"></a>다른 Azure 서비스에서의 경고
경고는 다음과 같은 다양한 서비스에서 사용할 수 있습니다.

* **Application Insights**: 웹 테스트 및 메트릭 경고가 가능합니다. [Application Insights에서 경고 설정](../application-insights/app-insights-alerts.md) 및 [모든 웹 사이트의 가용성 및 응답성 모니터링](../application-insights/app-insights-monitor-web-app-availability.md)을 참조하세요.
* **Log Analytics(Operations Management Suite)**: Log Analytics에 대한 작업 및 진단 로그를 라우팅할 수 있습니다. Operations Management Suite에서는 메트릭, 로그 및 기타 경고 유형을 지원합니다. 자세한 내용은 [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md)를 참조하세요.  
* **Azure Monitor**: Azure Monitor에서는 메트릭 값과 활동 로그 이벤트 모두를 기반으로 한 경고가 가능합니다. [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx)를 사용하여 경고를 관리할 수 있습니다.  자세한 내용은 [Azure 포털, PowerShell 또는 명령줄 인터페이스를 사용하여 경고 만들기](insights-alerts-portal.md)를 참조하세요.

## <a name="visual-summary"></a>시각적 요약
다음 다이어그램에서는 "Azure Monitor"에서 경고 및 수행할 수 있는 기능을 구체적으로 요약합니다. 다른 작업은 이전에 나열된 서비스에 사용할 수 있습니다. 예를 들어 현재 진단 로그에 대한 경고는 Log Analytics에서만 사용할 수 있습니다.

![경고를 설명합니다.](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="what-can-trigger-alerts-in-azure-monitor"></a>Azure Monitor에서 경고를 트리거하려면 어떻게 해야 하나요?

다음을 기준으로 경고를 받을 수 있습니다.

* **메트릭 값**: 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉, 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다. Azure Monitor에서 지원하는 사용 가능한 메트릭 목록은 [Azure Monitor에서 지원되는 메트릭 목록](monitoring-supported-metrics.md)을 참조하세요.
* **활동 로그 이벤트**: 이 경고는 리소스에 대한 특정 이벤트가 발생할 때 또는 서비스 알림이 구독에 게시될 때 트리거될 수 있습니다.

## <a name="what-can-metric-alerts-do"></a>메트릭 경고로 수행할 수 있는 작업
다음 작업을 수행하도록 경고를 구성할 수 있습니다.

* 서비스 관리자, 공동 관리자 또는 사용자가 지정한 추가 이메일 주소로 이메일 알림을 보냅니다.
* 추가 자동화 작업을 시작할 수 있게 webhook를 호출합니다. 예를 들면 다음 항목을 호출할 수 있습니다.
    - Azure 자동화 Runbook
    - Azure Function
    - Azure 논리 앱
    - 타사 서비스

## <a name="what-can-activity-log-alerts-do"></a>활동 로그 경고로 수행할 수 있는 작업
다음 작업을 수행하도록 경고를 구성할 수 있습니다.
* 특정 이벤트가 구독의 리소스 중 하나를 발생할 때마다 트리거
* 서비스 알림이 구독에 게시될 때마다 트리거
* 작업 그룹 멤버에게 경고를 보내는 방법
    * sms
    * Email
    * 웹후크

## <a name="next-steps"></a>다음 단계
다음을 사용하여 경고 규칙에 대한 정보를 확인하고 구성할 수 있습니다.

* [메트릭](monitoring-overview-metrics.md)에 대해 자세히 알아보기
* [Azure Portal을 통해 메트릭 경고](insights-alerts-portal.md) 구성
* [메트릭 경고 PowerShell](insights-alerts-powershell.md) 구성
* [메트릭 경고 CLI(명령줄 인터페이스)](insights-alerts-command-line-interface.md) 구성
* [메트릭 경고 Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) 구성
* [활동 로그](monitoring-overview-activity-logs.md)에 대해 자세히 알아보기
* [Azure Portal을 통해 활동 로그 경고](monitoring-activity-log-alerts.md) 구성
* [Resource Manager를 통해 활동 로그 경고](monitoring-create-activity-log-alerts-with-resource-manager-template.md) 구성
* [활동 로그 경고 웹후크 스키마](monitoring-activity-log-alerts-webhook.md) 검토
* [서비스 알림](monitoring-service-notifications.md)에 대해 자세히 알아보기
* [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기

