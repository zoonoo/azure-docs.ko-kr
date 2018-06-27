---
title: 플랫폼 간 Azure CLI를 사용하여 Azure 서비스에 대한 클래식 경고 만들기 | Microsoft 문서
description: 사용자가 지정한 조건이 충족되면 이메일 또는 알림을 트리거하거나 웹 사이트 URL(webhook) 또는 자동화를 호출합니다.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287110"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>플랫폼 간 Azure CLI를 사용하여 Azure 서비스에 대한 Azure Monitor에서 클래식 메트릭 경고 만들기 

> [!div class="op_single_selector"]
> * [포털](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> 이 문서에서는 기존 클래식 메트릭 경고를 만드는 방법을 설명합니다. Azure Monitor는 이제 [더 새롭고 더 나은 메트릭 경고](monitoring-near-real-time-metric-alerts.md)를 지원합니다. 이러한 경고는 여러 메트릭을 모니터링하고 차원 메트릭에 대한 경고를 허용할 수 있습니다. 새로운 메트릭 경고를 위한 Azure CLI 지원이 곧 제공됩니다.
>
>

이 문서에서는 플랫폼 간 Azure CLI(명령줄 인터페이스)를 사용하여 Azure 클래식 메트릭 경고를 설정하는 방법을 보여줍니다.

> [!NOTE]
> Azure Monitor는 2016년 9월 25일까지 "Azure Insights"로 지칭했던 제품의 새로운 이름입니다. 하지만 이 문서에서 설명하는 네임스페이스 및 명령에는 여전히 "insights"라는 단어가 계속 사용됩니다.

Azure 서비스에 대한 메트릭 또는 Azure에서 발생하는 이벤트에 따라 경고를 받을 수 있습니다.

* **메트릭 값**: 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과하면 경고가 트리거됩니다. 즉, 조건이 처음으로 충족될 때 그리고 해당 조건이 더 이상 충족되지 않을 때 트리거됩니다.    

* **활동 로그 이벤트**: *모든* 이벤트에 대해 또는 특정 이벤트가 발생할 때 경고를 트리거할 수 있습니다. 활동 로그에 대한 자세한 내용은 [활동 로그 경고 만들기(클래식)](monitoring-activity-log-alerts.md)를 참조하세요. 

트리거되면 다음을 수행하도록 클래식 메트릭 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 메일 알림을 보냅니다. 
* 지정하는 이메일 주소로 이메일을 보냅니다.
* Webhook를 호출합니다.
* Azure Runbook 실행을 시작합니다(현재는 Azure Portal에서만 가능).

다음을 사용하여 클래식 메트릭 경고 규칙을 구성하고 관련 정보를 가져올 수 있습니다. 

* [Azure 포털](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

또한 명령을 입력하고 끝에 **-help**를 붙이면 도움말을 확인할 수 있습니다. 다음은 예제입니다. 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Azure CLI를 사용하여 경고 규칙 만들기
1. 필수 구성 요소를 설치한 후 Azure에 로그인합니다. 시작하는 데 필요한 명령은 [Azure Monitor CLI 샘플](insights-cli-samples.md)을 참조하세요. 이러한 명령은 시작을 도와주고, 현재 사용 중인 구독을 보여주고, Azure Monitor 명령 실행 준비를 도와줍니다.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. 리소스 그룹에 대한 기존 규칙을 나열하려면 다음 형식을 사용합니다. 

   **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. 규칙을 만들려면 먼저 몇 가지 중요한 정보가 필요합니다.
    * 경고를 설정할 리소스의 **리소스 ID**입니다.
    * 해당 리소스에 사용 가능한 **메트릭 정의**입니다.

     리소스 ID를 가져오는 한 가지 방법은 Azure Portal을 사용하는 것입니다. 리소스를 이미 만들었다고 가정하고, 포털에서 해당 리소스를 선택합니다. 그 다음에 표시되는 블레이드의 **설정** 섹션에서 **속성**을 선택합니다. **리소스 ID**는 다음 블레이드의 필드입니다. 
     
     [Azure Resource Explorer](https://resources.azure.com/)를 사용하여 리소스 ID를 가져올 수도 있습니다.

     다음은 웹앱의 리소스 ID 예제입니다.

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     이전 리소스 예제의 메트릭에 사용할 수 있는 메트릭 및 단위 목록을 가져오려면 다음 Azure CLI 명령을 사용합니다.  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M*은 사용 가능한 측정의 세분성입니다(1분 간격). 다른 세분성을 사용하면 다른 메트릭 옵션이 제공됩니다.
     
4. 메트릭 기반 경고 규칙을 만들려면 다음 형식의 명령을 사용합니다.

    **azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    다음 예제는 웹 사이트 리소스에 대한 경고를 설정합니다. 이 경고는 어느 트래픽이나 5분 동안 계속 수신되면 항상 트리거되며, 트래픽이 5분 동안 수신되지 않으면 다시 트리거됩니다.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. 클래식 메트릭 경고가 발생할 때 webhook를 만들거나 이메일을 보내려면 먼저 이메일 또는 webhook를 만듭니다. 그런 다음 바로 규칙을 만듭니다. webhook 또는 이메일을 이미 생성된 규칙과 연결할 수 없습니다.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. 개별 규칙을 살펴서 경고가 제대로 만들어졌는지 확인할 수 있습니다.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. 규칙을 삭제하려면 다음 형식의 명령을 사용합니다.

    **insights alerts rule delete** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

    이 명령은 이 문서의 앞부분에서 만든 규칙을 삭제합니다.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>다음 단계
* 수집하고 모니터링할 수 있는 정보 종류를 포함하여 [Azure 모니터링 개요를 확인](monitoring-overview.md)합니다.
* [경고에서의 webhook 구성](insights-webhooks-alerts.md)에 대해 자세히 알아봅니다.
* [활동 로그 이벤트에 대한 경고 구성](monitoring-activity-log-alerts.md)에 대해 자세히 알아봅니다.
* [Azure Automation Runbook](../automation/automation-starting-a-runbook.md)에 대해 자세히 알아봅니다.
* 서비스의 구체적인 고빈도 메트릭을 수집하기 위한 [진단 로그 수집 개요](monitoring-overview-of-diagnostic-logs.md)를 살펴봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.
