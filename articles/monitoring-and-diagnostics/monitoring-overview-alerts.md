---
title: Microsoft Azure 및 Azure Monitor의 경고 개요 | Microsoft Docs
description: 경고를 통해 Azure 리소스 메트릭, 이벤트 또는 로그를 모니터링하고 사용자가 지정한 조건에 부합하면 알림을 받을 수 있습니다.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c64ca224705b7da57846e53bdc28d6d03eb28b06
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure의 경고란?
이 문서에서는 Microsoft Azure의 다양한 경고 출처, 경고의 목적과 장점, 경고 사용을 시작하는 방법에 대해 설명합니다. 경고는 특히 Azure Monitor에 적용되지만 다른 서비스에 대한 포인터를 제공합니다. 경고는 Azure에서 데이터에 대한 조건을 구성하고 최신 모니터링 데이터가 조건에 일치하면 알림을 받을 수 있는 모니터링 방법을 제공합니다.


## <a name="taxonomy-of-azure-alerts"></a>Azure 경고의 분류
Azure는 다음 용어를 사용하여 경고와 그 기능을 설명합니다.
* **경고** - 조건에 부합하면 활성화되는 기준의 정의(하나 이상의 규칙 또는 조건)
* **활성** - 경고로 정의한 기준에 부합하는 상태
* **해결** - 경고에서 정의한 기준에 이전에는 부합했고 이제 더 이상은 부합하지 않는 상태
* **알림** - 활성화된 경고를 기반으로 수행되는 조치
* **작업** - 알림 수신자에게 보내는 특정 호출(예: 주소로 이메일 보내기 또는 웹후크 URL에 게시). 알림은 보통 여러 작업을 트리거할 수 있습니다.


## <a name="alerts-in-different-azure-services"></a>다른 Azure 서비스에서의 경고
경고는 여러 Azure 모니터링 서비스전체 에서 제공됩니다. 이러한 서비스 사용 방법 및 시기에 대한 내용은 [이 문서를 참조하세요](./monitoring-overview.md). Azure 전체에서 사용 가능한 경고 유형을 분석하면 같습니다.


| 서비스 | 경고 유형 | 지원되는 서비스 | 설명 |
|---|---|---|---|
| Azure Monitor | [메트릭 경고](./insights-alerts-portal.md) | [Azure Monitor에서 지원되는 메트릭](./monitoring-supported-metrics.md) | 플랫폼 수준 메트릭이 특정 조건에 부합하면(예: VM의 CPU %가 지난 5분 동안 90%를 넘음) 알림을 받습니다. |
|Azure Monitor | [근 실시간 메트릭 경고](./monitoring-near-real-time-metric-alerts.md)| [Azure Monitor에서 지원되는 리소스](./monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported) | 하나 이상의 플랫폼 수준 메트릭이 지정된 조건을 충족하는 경우 알림을 메트릭 경고보다 먼저 수신합니다.(예를 들어, 지난 5분 동안 VM의 CPU 비율이 90보다 크고 네트워크 입력이 500MB보다 큽니다.) |
| Azure Monitor | [활동 로그 경고](./monitoring-activity-log-alerts.md) | Azure Resource Manager에서 사용 가능한 모든 리소스 유형 | [Azure 활동 로그](./monitoring-overview-activity-logs.md)에서 새 이벤트가 특정 조건(예: myProductionResourceGroup에서 "VM 삭제" 작업이 발생했거나 상태가 "활성"인 새 서비스 상태 이벤트가 나타난 경우)에 부합하는 경우 알림을 받습니다. |
| Application Insights | [메트릭 경고](../application-insights/app-insights-alerts.md) | Application Insights에 데이터를 보내도록 계측되는 모든 응용 프로그램 | 응용 프로그램 수준 메트릭이 특정 조건에 부합하면(예: 서버 응답 시간이 2초를 넘음) 알림을 받습니다. |
| Application Insights | [웹 테스트 경고](../application-insights/app-insights-monitor-web-app-availability.md) | Application Insights에 데이터를 보내도록 계측되는 모든 웹 사이트 | 웹 사이트의 가용성이나 응답성이 기대 이하이면 알림을 받습니다. |
| Log Analytics | [Log Analytics 경고](../log-analytics/log-analytics-alerts.md) | Log Analytics에 데이터를 보내도록 구성된 모든 서비스 | 메트릭 및/또는 이벤트 데이터에 대한 Log Analytics 검색 쿼리가 특정 기준에 부합하면 알림을 받습니다. |

## <a name="alerts-on-azure-monitor-data"></a>Azure Monitor 데이터에 대한 경고
Azure Monitor에서는 메트릭 경고, 근 실시간 메트릭 경고 및 활동 로그 경고와 같은 세 가지 유형의 데이터 경고를 제공합니다.

* **메트릭 경고**: 이 경고는 특정 메트릭의 값이 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 경고가 “활성화”되었을 때(임계값ㅇ르 초과했고 경고 조건에 부합함)와 “해결”되었을 때(임계값을 다시 넘었고 조건에 더 이상 부합하지 않음) 경고가 알림을 생성합니다. 이는 이전 메트릭 경고입니다. 최신 메트릭 경고는 다음을 참조하세요.

* **근 실시간 메트릭 경고** - 이는 이전 메트릭 경고에 비해 기능이 향상된 차세대 메트릭 경고입니다. 이러한 경고는 1분의 주파수로 실행할 수 있습니다. 또한 여러(현재 두 가지) 메트릭 모니터링을 지원합니다.  경고가 "활성화"되었을 때(각 메트릭의 임계값을 동시에 초과하고 경고 조건에 부합하는 경우) 및 "해결"되었을 때(적어도 하나의 메트릭이 임계값을 다시 넘고 조건에 더 이상 부합하지 않은 경우) 경고가 알림을 생성합니다.

* **활동 로그 경고** - 사용자가 할당한 필터 기준에 부합하는 활동 로그 이벤트가 생성되면 트리거되는 스트리밍 로그 알림입니다. 경고 엔진은 단순히 필터 기준을 모든 새 이벤트에 적용하므로 이러한 경고의 상태는 "활성화됨"뿐입니다. 이러한 경고를 통해 새 서비스 상태 사건이 발생하거나 사용자 또는 응용 프로그램이 구독에서 작업을 수행할 때(예: "가상 머신 삭제") 알림을 받을 수 있습니다.

Azure Monitor를 통해 제공되는 진단 로그 데이터의 경우 데이터를 Log Analytics로 전달하고 Log Analytics 경고를 사용하는 것이 좋습니다. 다음 다이어그램에서는 Azure Monitor의 데이터 원본을 요약하고 그러한 데이터에 대해 경고 받는 방법을 개념적으로 설명약합니다.

![경고를 설명합니다.](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>Azure Monitor 경고에서 알림을 받는 방법
지금까지 다양한 서비스의 Azure 경고는 자체 기본 제공 알림 방법을 사용했습니다.  이제부터 Azure Monitor는 작업 그룹이라고 하는 재사용 가능한 알림 그룹화를 제공합니다. 작업 그룹은 알림에 대한 수신자 집합을 명시하며(수 제한 없는 이메일 주소, SMS용 전화번호, 웹후크 URL) 작업 그룹을 참조하는 경고가 활성화될 때마다 모든 수신자가 해당 알림을 수신하게 됩니다. 이를 통해 여러 경고 개체에서 수신자 그룹(예: 엔지니어 전화걸기 목록)을 재사용할 수 있습니다. 현재 활동 로그 경고만 작업 그룹ㅇ르 사용하지만 다른 몇 가지 Azure 경고 유형도 작업 그룹을 사용하기 위해 작업 중에 있습니다.

작업 그룹은 이메일 주소와 SMS 번호 외에도 웹후크 URL 게시를 통한 알림도 지원합니다. 이 때문에 다음 예시 항목을 사용하여 자동화 및 조치를 구현할 수 있습니다.
    - Azure Automation Runbook
    - Azure Function
    - Azure Logic App
    - 타사 서비스

근 실시간 메트릭 경고 및 활동 로그 경고는 작업 그룹을 사용합니다.

이전 메트릭 경고는 알림(클래식)에서 사용할 수 있으며, 작업 그룹을 사용하지 않습니다. 개별 메트릭 경고에서는 다음을 위해 알림을 구성할 수 있습니다.
* 서비스 관리자, 공동 관리자 또는 사용자가 지정한 추가 이메일 주소로 이메일 알림을 보냅니다.
* 추가 자동화 작업을 시작할 수 있게 webhook를 호출합니다.

## <a name="next-steps"></a>다음 단계
다음을 사용하여 경고 규칙에 대한 정보를 확인하고 구성할 수 있습니다.

* [메트릭](monitoring-overview-metrics.md)에 대해 자세히 알아보기
* [Azure Portal을 통해 클래식 메트릭 경고](insights-alerts-portal.md) 구성
* [클래식 메트릭 경고 PowerShell](insights-alerts-powershell.md) 구성
* [클래식 메트릭 경고 CLI(명령줄 인터페이스)](insights-alerts-command-line-interface.md) 구성
* [클래식 메트릭 경고 Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) 구성
* [활동 로그](monitoring-overview-activity-logs.md)에 대해 자세히 알아보기
* [Azure Portal을 통해 활동 로그 경고](monitoring-activity-log-alerts.md) 구성
* [Resource Manager를 통해 활동 로그 경고](monitoring-create-activity-log-alerts-with-resource-manager-template.md) 구성
* [활동 로그 경고 웹후크 스키마](monitoring-activity-log-alerts-webhook.md) 검토
* [최신 메트릭 경고](monitoring-near-real-time-metric-alerts.md)에 대해 자세히 알아보기
* [서비스 알림](monitoring-service-notifications.md)에 대해 자세히 알아보기
* [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기
* [경고](monitor-alerts-unified-usage.md) 구성
