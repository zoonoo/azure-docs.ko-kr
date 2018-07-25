---
title: Microsoft Azure 및 Azure Monitor의 클래식 경고 개요
description: 경고를 통해 Azure 리소스 메트릭, 이벤트 또는 로그를 모니터링하고 사용자가 지정한 조건에 부합하면 알림을 받을 수 있습니다.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: a0abcbdaa7e998413efb717be6e0addc5607ec5c
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114014"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Microsoft Azure의 클래식 경고란?

> [!NOTE]
> 이 문서에서는 기존 클래식 메트릭 경고를 만드는 방법을 설명합니다. 이제 Azure Monitor는 [최신 근 실시간 메트릭 경고 및 새 경고 환경](monitoring-overview-unified-alerts.md)을 지원합니다. 
>

경고를 사용하면 데이터에 대한 조건을 구성하고 최신 모니터링 데이터가 조건에 일치하면 알림을 받을 수 있습니다.


## <a name="alerts-on-azure-monitor-data"></a>Azure Monitor 데이터에 대한 경고
두 가지 클래식 경고인 메트릭 경고 및 활동 로그 경고를 사용할 수 있습니다.

* **클래식 메트릭 경고**: 이 경고는 특정 메트릭의 값이 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 경고는 “활성화됨”인 경우(임계값을 초과하고 경고 조건이 충족된 경우) 알림을 생성합니다. “해결”인 경우(임계값을 다시 초과하고 조건이 더 이상 충족되지 않는 경우)에도 경고를 생성합니다. 

* **클래식 활동 로그 경고**: 사용자가 할당한 필터 기준에 부합하는 활동 로그 이벤트가 생성되면 이 스트리밍 로그 경고가 트리거됩니다. 경고 엔진은 단순히 필터 기준을 모든 새 이벤트에 적용하므로 이러한 경고의 상태는 “활성화됨”뿐입니다. 이러한 경고를 통해 새 Service Health 인시던트가 발생하거나 사용자 또는 응용 프로그램이 구독에서 작업을 수행할 때(예: “가상 머신 삭제”) 알림을 받을 수 있습니다.

Azure Monitor를 통해 제공되는 진단 로그 데이터를 받으려면 데이터를 Log Analytics(이전 Operations Management Suite)로 라우트하고 Log Analytics 쿼리 경고를 사용하는 것이 좋습니다. 이제 Log Analytics는 [새로운 경고 방법](monitoring-overview-unified-alerts.md)을 사용합니다. 

다음 다이어그램에서는 Azure Monitor의 데이터 원본을 요약하고 이 데이터에 대한 경고를 받는 방법을 제안합니다.

![경고를 설명합니다.](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Azure Monitor 경고(클래식)의 분류
Azure는 다음 용어를 사용하여 클래식 경고와 그 기능을 설명합니다.
* **경고**: 조건에 부합하면 활성화되는 기준의 정의(하나 이상의 규칙 또는 조건)
* **활성**: 클래식 경고로 정의한 기준에 부합하는 경우 발생하는 상태
* **해결**: 클래식 경고에서 정의한 기준에 이전에는 부합했고 이제 더 이상은 부합하지 않는 경우 발생하는 상태
* **알림**: 클래식 경고가 활성화되는 시점을 기반으로 수행되는 작업
* **작업**: 알림 받는 사람에게 보내는 특정 호출(예: 메일 보내기 또는 웹후크 URL에 게시) 알림은 보통 여러 작업을 트리거할 수 있습니다.

## <a name="how-do-i-receive-notifications-from-an-azure-monitor-classic-alert"></a>Azure Monitor 클래식 경고에서 알림을 받는 방법
지금까지 다양한 서비스의 Azure 경고는 자체 기본 제공 알림 방법을 사용했습니다.  

이제 Azure Monitor는 ‘작업 그룹’이라고 하는 재사용 가능한 알림 그룹화를 제공합니다. 작업 그룹은 알림에 대한 받는 사람 집합을 지정합니다. 작업 그룹을 참조하는 경고가 활성화되면 모든 받는 사람은 해당 알림을 받습니다. 이 기능을 통해 여러 경고 개체에서 받는 사람 그룹(예: 엔지니어 전화걸기 목록)을 재사용할 수 있습니다. 작업 그룹은 다양한 방법을 통해 알림을 지원합니다. 이러한 방법에는 webhook URL에 게시, 메일 보내기, SMS 메시지 및 다양한 기타 작업이 포함될 수 있습니다. 자세한 내용은 [Azure Portal에서 작업 그룹 만들기 및 관리](monitoring-action-groups.md)를 참조하세요. 

이전 클래식 활동 로그 경고는 작업 그룹을 사용합니다.

그러나 이전 메트릭 경고는 작업 그룹을 사용하지 않습니다. 대신 다음 작업을 구성할 수 있습니다. 
* 서비스 관리자, 공동 관리자 또는 사용자가 지정한 추가 메일 주소로 메일 알림을 보냅니다.
* 추가 자동화 작업을 시작할 수 있게 webhook를 호출합니다.

웹후크는 다음과 같은 서비스를 사용하여 자동화 및 재구성을 구현합니다.
- Azure Automation Runbook
- Azure 기능
- Azure Logic App
- 타사 서비스

## <a name="next-steps"></a>다음 단계
경고 규칙 정보를 알아보고 다음 문서를 사용하여 경고 규칙을 구성하는 방법을 알아봅니다.

* [메트릭](monitoring-overview-metrics.md)에 대한 자세한 정보
* [Azure Portal을 사용하여 클래식 메트릭 경고](insights-alerts-portal.md) 구성
* [PowerShell을 사용하여 클래식 메트릭 경고](insights-alerts-powershell.md) 구성
* [Azure CLI를 사용하여 클래식 메트릭 경고](insights-alerts-command-line-interface.md) 구성
* [Azure Monitor REST API를 사용하여 클래식 메트릭 경고](https://msdn.microsoft.com/library/azure/dn931945.aspx) 구성
* [활동 로그](monitoring-overview-activity-logs.md)에 대한 자세한 정보
* [Azure Portal을 사용하여 활동 로그 경고](monitoring-activity-log-alerts.md) 구성
* [Resource Manager를 사용하여 활동 로그 경고](monitoring-create-activity-log-alerts-with-resource-manager-template.md) 구성
* [활동 로그 경고 웹후크 스키마](monitoring-activity-log-alerts-webhook.md) 검토
* [작업 그룹](monitoring-action-groups.md)에 대해 자세히 알아보기
* [최신 경고](monitor-alerts-unified-usage.md) 구성
