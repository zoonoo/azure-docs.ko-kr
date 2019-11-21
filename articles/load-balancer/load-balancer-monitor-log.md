---
title: 공용 기본 Load Balancer에 대한 작업, 이벤트 및 카운터 모니터링
titleSuffix: Azure Load Balancer
description: 공용 기본 Load Balancer에 대한 경고 이벤트 및 상태 프로브 상태 로깅을 사용하도록 설정하는 방법에 대해 알아보기
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 23a3a2629c6f2f89c4b8f6d5af57bcf3b6bb67dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214924"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Azure Monitor logs for public Basic Load Balancer

>[!IMPORTANT] 
>Azure Load Balancer는 기본 및 표준이라는 두 가지 형식을 지원합니다. 이 문서는 기본 Load Balancer에 대해 설명합니다. 표준 Load Balancer에 대한 자세한 내용은 Azure Monitor에서 다차원 메트릭을 통해 원격 분석을 노출하는 [표준 Load Balancer 개요](load-balancer-standard-overview.md)를 참조하세요.

Azure에서 기본 Load Balancer를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있으며, Logs can be streamed to an event hub or a Log Analytics workspace. All logs can be extracted from Azure blob storage, and viewed in different tools, such as Excel and Power BI.  아래 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **Activity logs:** You can use [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) to view all activity being submitted to your Azure subscription(s), and their status. Activity logs are enabled by default, and can be viewed in the Azure portal.
* **경고 이벤트 로그:** 이 로그를 사용하여 부하 분산 장치에서 발생한 경고를 볼 수 있습니다. 부하 분산 장치에 대한 상태는 5분 마다 수집됩니다. 이 로그는 부하 분산 장치 경고 이벤트가 발생하는 경우에만 기록됩니다.
* **상태 프로브 로그:** 상태 프로브 오류 때문에 부하 분산 장치에서 요청을 받지 않는 백 엔드 풀에 있는 인스턴스의 수와 같은 상태 프로브에서 발견한 문제를 보기 위해 이 로그를 사용할 수 있습니다. 상태 프로브 상태가 변경되는 경우에 이 로그가 기록됩니다.

> [!IMPORTANT]
> Azure Monitor logs currently works only for public Basic load balancers. 로그는 Resource Manager 배포 모델에 배포된 리소스에서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/resource-manager-deployment-model.md)를 참조하세요.

## <a name="enable-logging"></a>로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. Enable event and health probe logging to start collecting the data available through those logs. 로깅을 사용하려면 다음 단계를 사용합니다.

[Azure portal](https://portal.azure.com)에 로그인합니다. 부하 분산 장치가 아직 없으면, 계속하기 전에 [부하 분산 장치를 만듭니다](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) .

1. In the portal, click **Resource groups**.
2. Select **\<resource-group-name>** where your load balancer is.
3. Select your load balancer.
4. Select **Monitoring** > **Diagnostic settings**.
5. In the **Diagnostics settings** pane, under **Diagnostics settings**, select **+ Add diagnostic setting**.
6. In the **Diagnostics settings** creation pane, enter **myLBDiagnostics** in the **Name** field.
7. You have three options for the **Diagnostics settings**.  You can choose one, two or all three and configure each for your requirements:
   * **Archive to a storage account**
   * **Stream to an event hub**
   * **Send to Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>스토리지 계정에 보관
    You'll need a storage account already created for this process.  To create a storage account, see [Create a storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Select the checkbox next to **Archive to a storage account**.
    2. Select **Configure** to open the **Select a storage account** pane.
    3. Select the **Subscription** where your storage account was created in the pull-down box.
    4. Select the name of your storage account under **Storage account** in the pull-down box. 
    5. Select OK.

    ### <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
    You'll need an event hub already created for this process.  To create an event hub, see [Quickstart: Create an event hub using Azure portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Select the checkbox next to **Stream to an event hub**
    2. Select **Configure** to open the **Select event hub** pane.
    3. Select the **Subscription** where your event hub was created in the pull-down box.
    4. **Select event hub namespace** in the pull-down box.
    5. **Select event hub policy name** in the pull-down box.
    6. Select OK.

    ### <a name="send-to-log-analytics"></a>Log Analytics에 보내기
    You'll need to already have a log analytics workspace created and configured for this process.  To create a Log Analytics workspace, see [Create a Log Analytics workspace in the Azure portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Select the checkbox next to **Send to Log Analytics**.
    2. Select the **Subscription** where your Log Analytics workspace is in the pull-down box.
    3. Select the **Log Analytics Workspace** in the pull-down box.


8. Beneath the **LOG** section in the **Diagnostics settings** pane, select the check box next to both:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Beneath the **METRIC** section in the **Diagnostics settings** pane, select the check box next to:
   * **AllMetrics**

11. Verify everything looks correct and click **Save** at the top of the create **Diagnostic settings** pane.

## <a name="activity-log"></a>활동 로그

The activity log is generated by default. 이 로그는 Azure의 이벤트 로그 저장소에 90일 동안 유지됩니다. Learn more about these logs by reading the [View activity logs to monitor actions on resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) article.

## <a name="archive-to-storage-account-logs"></a>Archive to storage account logs

### <a name="alert-event-log"></a>경고 이벤트 로그

이 로그는 부하 분산 장치별로 설정한 경우에만 생성됩니다. 이벤트는 JSON 형식으로 로깅되며, 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. The following example is of an event.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

The JSON output shows the *eventname* property, which will describe the reason for the load balancer created an alert. In this case, the alert generated was because of TCP port exhaustion caused by source IP NAT limits (SNAT).

### <a name="health-probe-log"></a>상태 프로브 로그

이 로그는 위에서 설명한 대로 부하 분산 장치별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 스토리지 계정에 저장됩니다. 'insights-logs-loadbalancerprobehealthstatus'라는 컨테이너가 생성되고 다음 데이터가 기록됩니다.

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

속성 필드의 JSON 출력은 상태 프로브 상태에 대한 기본 정보를 보여 줍니다. The *dipDownCount* property shows the total number of instances on the back-end, which are not receiving network traffic because of failed probe responses.

### <a name="view-and-analyze-the-audit-log"></a>감사 로그 보기 및 분석

다음 방법을 사용하여 감사 로그 데이터를 보고 분석할 수 있습니다.

* **Azure tools:** Retrieve information from the audit logs through Azure PowerShell, the Azure Command Line Interface (CLI), the Azure REST API, or the Azure portal. 각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../azure-resource-manager/resource-group-audit.md) 문서에 자세히 나와 있습니다.
* **Power BI:** [Power BI](https:// .microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 콘텐츠 팩](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)을 사용하여 미리 구성된 대시보드에서 데이터를 분석하거나 요구 사항에 맞게 보기를 사용자 지정할 수 있습니다.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>상태 프로브 및 이벤트 로그 보기 및 분석

Connect to your storage account and retrieve the JSON log entries for event and health probe logs. Once you download the JSON files, you can convert them to CSV and view in Excel, Power BI, or any other data visualization tool.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
When diagnostic information is streamed to an event hub, it can be used for centralized log analysis in a third-party SIEM tool with Azure Monitor Integration. For more information, see [Stream Azure monitoring data to an event hub](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Log Analytics에 보내기
Resources in Azure can have their diagnostic information sent directly to a Log Analytics workspace where complex queries can be run against the information for troubleshooting and analysis.  For more information, see [Collect Azure resource logs in Log Analytics workspace in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>다음 단계

[부하 분산 장치 프로브 이해](load-balancer-custom-probe-overview.md)
