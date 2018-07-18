---
title: Azure Portal을 사용하여 Azure 서비스에 대한 클래식 경고 만들기| Microsoft 문서
description: 사용자가 지정한 조건이 충족되면 이메일 또는 알림을 트리거하거나 웹 사이트 URL(webhook) 또는 자동화를 호출합니다.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/23/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: d0e5512eb3f963898ded6d7155f8b75cfb6ef911
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287432"
---
# <a name="use-the-azure-portal-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Azure Portal을 사용하여 Azure Monitor에서 Azure 서비스에 대한 클래식 메트릭 경고 만들기 

> [!div class="op_single_selector"]
> * [포털](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>

> [!NOTE]
> 이 문서에서는 기존 클래식 메트릭 경고를 만드는 방법을 설명합니다. Azure Monitor는 이제 [최신 메트릭 경고](monitoring-near-real-time-metric-alerts.md)를 지원합니다. 


이 문서에서는 Azure Portal을 사용하여 클래식 Azure 메트릭 경고를 설정하는 방법을 보여줍니다. 

Azure 서비스에 대한 메트릭을 기반으로 경고를 받을 수도 있고, Azure에서 발생하는 이벤트에 대한 경고를 받을 수도 있습니다.

* **메트릭 값**: 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과하면 경고가 트리거됩니다. 즉, 조건이 처음으로 충족될 때 그리고 해당 조건이 더 이상 충족되지 않을 때 트리거됩니다.    

* **활동 로그 이벤트**: *모든* 이벤트에 대해 또는 특정 이벤트가 발생할 때 경고를 트리거할 수 있습니다. [활동 로그 경고](monitoring-activity-log-alerts.md)에 대해 자세히 알아봅니다.

트리거되면 다음을 수행하도록 클래식 메트릭 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 메일 알림을 보냅니다.
* 사용자가 지정한 추가 이메일 주소로 이메일을 보냅니다.
* Webhook를 호출합니다.
* Azure Runbook 실행을 시작합니다(현재는 Azure Portal에서만 가능).

다음 위치를 사용하여 클래식 메트릭 경고 규칙을 구성하고 관련 정보를 가져올 수 있습니다. 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure 포털에서 메트릭에 대한 경고 규칙 만들기
1. [포털](https://portal.azure.com/)에서 모니터링할 리소스를 찾아서 선택합니다.

2. **모니터링** 섹션에서 **경고(클래식)** 를 선택합니다. 텍스트와 아이콘은 리소스마다 약간씩 다를 수 있습니다. 여기서 **경고(클래식)** 를 찾을 수 없는 경우 **경고** 또는 **경고 규칙**에서 찾을 수 있습니다.

    ![모니터링](./media/insights-alerts-portal/AlertRulesButton.png)

3. **Add metric alert(클래식)** 명령을 선택하고 필드를 채웁니다.

    ![Add alert](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. 경고 규칙의 **이름을 지정**합니다. 그리고 **설명**을 선택합니다. 설명도 알림 이메일에 표시됩니다.

5. 모니터링할 **메트릭**을 선택합니다. 메트릭의 **조건** 및 **임계값**을 선택합니다. 경고를 트리거하기 전에 메트릭 규칙을 만족해야 하는 **기간**도 선택합니다. 예를 들어 "지난 5분" 기간을 사용하고 경고가 80% 이상인 CPU를 찾는다면 이 경고는 CPU가 5분 동안 계속 80%를 넘으면 트리거됩니다. 첫 번째 트리거가 발생한 후 CPU가 5분 동안 80% 미만을 유지하면 다시 트리거됩니다. CPU 메트릭 측정은 1분마다 발생합니다.

6. 경고가 발생하면 관리자 및 공동 관리자가 이메일 알림을 수신하게 하려면 **소유자에게 이메일 보내기...** 를 선택합니다.

7. 경고가 발생하면 추가 이메일 주소에 알림을 보내려면 **추가 관리자 이메일** 필드에 해당 이메일 주소를 추가합니다. 이메일 주소가 여러 개인 경우 *email@contoso.com; email2@contoso.com*처럼 세미콜론을 사용하여 구분할 수 있습니다.

8. 경고가 발생할 때 URI를 호출하려면 **Webhook** 필드에 유효한 URI를 입력합니다.

9. Azure Automation을 사용하는 경우 경고가 발생할 때 실행할 Runbook을 선택할 수 있습니다.

10. **확인**을 선택하여 경고를 만듭니다.   

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="manage-your-alerts"></a>경고 관리
경고를 만든 후에는 해당 경고를 선택하고 다음 작업 중 하나를 수행할 수 있습니다.

* 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 보기
* 편집 또는 삭제
* 해당 경고에 대한 알림 수신을 일시 중지 또는 재개하려면 **사용 중지** 또는 **사용**하도록 설정합니다.

## <a name="next-steps"></a>다음 단계
* 수집하고 모니터링할 수 있는 정보 종류를 포함하여 [Azure 모니터링 개요를 확인](monitoring-overview.md)합니다.
* [최신 메트릭 경고](monitoring-near-real-time-metric-alerts.md)에 대해 자세히 알아봅니다.
* [경고에서의 webhook 구성](insights-webhooks-alerts.md)에 대해 자세히 알아봅니다.
* [활동 로그 이벤트에 대한 경고 구성](monitoring-activity-log-alerts.md)에 대해 자세히 알아봅니다.
* [Azure Automation Runbook](../automation/automation-starting-a-runbook.md)에 대해 자세히 알아봅니다.
* 서비스의 구체적인 고빈도 메트릭을 수집하기 위한 [진단 로그 개요](monitoring-overview-of-diagnostic-logs.md)를 살펴봅니다.
* 서비스의 가용성과 응답성을 유지하기 위한 [메트릭 수집 개요](insights-how-to-customize-monitoring.md)를 살펴봅니다.
