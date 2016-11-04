---
title: Application Gateway에 대한 액세스 및 성능 로그와 메트릭 모니터링 | Microsoft Docs
description: 응용 프로그램 게이트웨이에 대한 액세스 및 성능 로그를 사용하고 관리하는 방법을 알아봅니다
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2016
ms.author: amitsriva

---
# <a name="diagnostics-logging-and-metrics-for-application-gateway"></a>Application Gateway에 대한 진단 로깅 및 메트릭
Azure는 로깅 및 메트릭을 사용하여 리소스를 모니터링할 수 있는 기능을 제공합니다.

[**로깅**](#enable-logging-with-powershell) - 모니터링을 목적으로 리소스의 성능, 액세스 및 기타 로그를 저장하거나 사용할 수 있습니다.

[**메트릭**](#metrics) - 현재는 Application Gateway에 메트릭이 하나 있습니다. 이 메트릭은 Application Gateway의 처리량을 초당 바이트 단위로 측정합니다.

Azure에서 다양한 유형의 로그를 사용하여 Application Gateway를 관리하고 문제를 해결할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있으며 Azure Blob Storage에서 모든 로그를 추출하고 다양한 도구(예: [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel 및 PowerBI)에서 볼 수 있습니다. 다음 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **감사 로그:**[Azure Audit Logs](../azure-portal/insights-debugging-with-events.md) (이전의 작업 로그)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 볼 수 있습니다. 감사 로그는 기본적으로 사용하도록 설정되며 Azure Preview 포털에서 볼 수 있습니다.
* **액세스 로그:** 이 로그를 사용하여 Application Gateway 액세스 패턴을 보고 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 입력 및 출력 바이트 등 중요한 정보를 분석할 수 있습니다. 액세스 로그는 300초마다 수집됩니다. 이 로그에는 Application Gateway 인스턴스당 하나의 레코드가 포함됩니다. Application Gateway 인스턴스는 'instanceId' 속성으로 식별할 수 있습니다.
* **성능 로그:** 이 로그를 사용하여 Application Gateway 인스턴스 성능을 확인할 수 있습니다. 이 로그는 인스턴스 단위로 처리된 총 요청, 처리량(바이트), 실패한 총 요청, 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등의 성능 정보를 캡처합니다. 성능 로그는 60초마다 수집됩니다.
* **방화벽 로그:** 이 로그를 사용하면 웹 응용 프로그램 방화벽으로 구성된 Application Gateway의 검색 모드 또는 방지 모드를 통해 로깅된 요청을 볼 수 있습니다.

> [!WARNING]
> 로그는 리소스 관리자 배포 모델에 배포된 리소스에 대해서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 두 모델의 이해를 돕기 위해 [리소스 관리자 배포 및 클래식 배포 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.
> 
> 

## <a name="enable-logging-with-powershell"></a>PowerShell을 통해 로깅을 사용하도록 설정
감사 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 액세스 및 성능 로깅을 사용하도록 설정해야 합니다. 로깅을 사용하려면 다음 단계를 참조하세요. 

1. 로그 데이터를 저장할 저장소 계정의 리소스 ID를 적어 둡니다. 형식은 /subscriptions/\<subscriptionId\>/resourceGroups/\<리소스 그룹 이름\>/providers/Microsoft.Storage/storageAccounts/\<저장소 계정 이름\>과 같습니다. 구독의 모든 저장소 계정을 사용할 수 있습니다. 미리 보기 포털을 사용하여 이 정보를 찾을 수 있습니다.
   
    ![미리 보기 포털 - 응용 프로그램 게이트웨이 진단](./media/application-gateway-diagnostics/diagnostics1.png)
2. 로깅을 사용할 Application Gateway의 리소스 ID를 적어 둡니다. 형식은 /subscriptions/\<구독 ID\>/resourceGroups/\<리소스 그룹 이름\>/providers/Microsoft.Network/applicationGateways/\<응용 프로그램 게이트웨이 이름\>과 같습니다. 미리 보기 포털을 사용하여 이 정보를 찾을 수 있습니다.
   
    ![미리 보기 포털 - 응용 프로그램 게이트웨이 진단](./media/application-gateway-diagnostics/diagnostics2.png)
3. 다음 powershell cmdlet을 사용하여 진단 로깅을 사용하도록 설정합니다.
   
        Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true  

> [AZURE.INFORMATION] 감사 로그에는 별도의 저장소 계정이 필요하지 않습니다. 액세스 및 성능 로깅에 저장소를 사용할 경우 서비스 요금이 부과됩니다.
> 
> 

## <a name="enable-logging-with-azure-portal"></a>Azure Portal에서 로깅을 사용하도록 설정
### <a name="step-1"></a>1단계
Azure Portal에서 리소스로 이동합니다. **진단 로그**를 클릭합니다. 처음으로 진단을 구성하는 경우 블레이드가 다음 이미지처럼 보입니다.

Application Gateway의 경우 3개 로그를 사용할 수 있습니다.

* 액세스 로그
* 성능 로그
* 방화벽 로그

**진단 켜기** 를 클릭하여 데이터 수집을 시작합니다.

![진단 설정 블레이드][1]

### <a name="step-2"></a>2단계
**진단 설정** 블레이드에서는 진단 로그가 설정되는 방식을 설정합니다. 이 예에서 Log Analytics는 로그를 저장하는 데 사용됩니다. **Log Analytics**에서 **구성**을 클릭하여 작업 영역을 구성합니다. Event Hubs 및 저장소 계정을 사용하여 진단 로그를 저장할 수도 있습니다.

![진단 블레이드][2]

### <a name="step-3"></a>3단계
기존 OMS 작업 영역을 선택하거나 새 작업 영역을 만듭니다. 이 예에서는 기존 작업 영역을 사용합니다.

![oms 작업 영역][3]

### <a name="step-4"></a>4단계
완료되면 설정을 확인하고 **저장** 을 클릭하여 설정을 저장합니다.

![선택 확인][4]

## <a name="audit-log"></a>감사 로그
이 로그(이전의 "작업 로그")는 기본적으로 Azure에 의해 생성됩니다.  이 로그는 Azure의 이벤트 로그 저장소에서 90일 동안 유지됩니다. [이벤트 및 감사 로그 보기](../azure-portal/insights-debugging-with-events.md) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

## <a name="access-log"></a>액세스 로그
이 로그는 이전 단계에서 설명한 대로 Application Gateway별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. Application Gateway의 액세스는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayAccess",
        "time": "2016-04-11T04:24:37Z",
        "category": "ApplicationGatewayAccessLog",
        "properties": {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIP":"37.186.113.170",
            "clientPort":"12345",
            "httpMethod":"HEAD",
            "requestUri":"/xyz/portal",
            "requestQuery":"",
            "userAgent":"-",
            "httpStatus":"200",
            "httpVersion":"HTTP/1.0",
            "receivedBytes":"27",
            "sentBytes":"202",
            "timeTaken":"359",
            "sslEnabled":"off"
        }
    }

## <a name="performance-log"></a>성능 로그
이 로그는 이전 단계에서 설명한 대로 Application Gateway별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 다음 데이터가 로깅됩니다.

    {
        "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
        "operationName": "ApplicationGatewayPerformance",
        "time": "2016-04-09T00:00:00Z",
        "category": "ApplicationGatewayPerformanceLog",
        "properties": 
        {
            "instanceId":"ApplicationGatewayRole_IN_1",
            "healthyHostCount":"4",
            "unHealthyHostCount":"0",
            "requestCount":"185",
            "latency":"0",
            "failedRequestCount":"0",
            "throughput":"119427"
        }
    }


## <a name="firewall-log"></a>방화벽 로그
이 로그는 이전 단계에서 설명한 대로 Application Gateway별로 설정한 경우에만 생성됩니다. 또한 이 로그를 사용하려면 Application Gateway에서 웹 응용 프로그램 방화벽을 구성해야 합니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 다음 데이터가 로깅됩니다.

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="view-and-analyze-the-audit-log"></a>감사 로그 보기 및 분석
다음 방법을 사용하여 감사 로그 데이터를 보고 분석할 수 있습니다.

* **Azure 도구:** Azure PowerShell, Azure 명령줄 인터페이스(CLI), Azure REST API 또는 Azure Preview 포털을 통해 감사 로그에서 정보를 검색합니다.  각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../resource-group-audit.md) 문서에 자세히 나와 있습니다.
* **Power BI:**[Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 콘텐츠 팩](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 미리 구성된 대시보드를 있는 그대로 사용하거나 사용자 지정하여 데이터를 분석할 수 있습니다.

## <a name="view-and-analyze-the-access,-performance-and-firewall-log"></a>액세스, 성능 및 방화벽 로그 보기 및 분석
Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 는 Blob 저장소 계정에서 카운터 및 이벤트 로그 파일을 수집할 수 있으며 로그를 분석하기 위한 시각화 및 강력한 검색 기능을 포함합니다.

저장소 계정에 연결하고 액세스 및 성능 로그에 대한 JSON 로그 항목을 검색할 수도 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, PowerBI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 Github에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter) 를 사용할 수 있습니다.
> 
> 

## <a name="metrics"></a>메트릭
메트릭은 포털에서 성능 카운터를 볼 수 있는 특정 Azure 리소스에 대한 기능입니다. 이 문서가 작성된 시점을 기준으로 Application Gateway에는 메트릭 하나가 제공됩니다. 이 메트릭은 처리량이며 포털에서 볼 수 있습니다. Application Gateway를 찾아서 **메트릭**을 클릭합니다.  **사용 가능한 메트릭** 섹션에서 처리량을 선택하면 값을 볼 수 있습니다. 다음 이미지에서는 여러 시간 범위의 데이터를 표시할 수 있는 필터가 적용된 예를 볼 수 있습니다.

현재 지원되는 메트릭 목록을 보려면 [Azure Monitor에 지원되는 메트릭](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![메트릭 보기][5]

## <a name="alert-rules"></a>경고 규칙
리소스의 메트릭을 기반으로 경고 규칙을 시작할 수 있습니다. 즉, Application Gateway의 경우 Application Gateway의 처리량이 지정된 시간의 임계값보다 높거나 낮거나 같을 때 경고에서 웹후크를 호출하거나 관리자에게 전자 메일을 보낼 수 있습니다.

다음 예에서는 처리량 임계값을 위반하면 관리자에게 전자 메일을 보내는 경고 규칙을 만드는 방법을 안내합니다.

### <a name="step-1"></a>1단계
**메트릭 경고 추가** 를 클릭하여 시작합니다. 메트릭 블레이드에서도 이 블레이드로 이동할 수 있습니다.

![경고 규칙 블레이드][6]

### <a name="step-2"></a>2단계
**규칙 추가** 블레이드에서 이름, 조건을 입력하고, 섹션을 알리고, 모두 완료되면 **확인**을 클릭합니다.

**조건** 선택기에 허용되는 값은 **보다 큼**, **보다 크거나 같음**, **보다 작음**, **보다 작거나 같음**의 4가지입니다.

**기간** 선택기에서는 5분부터 6시간까지 선택할 수 있습니다.

**전자 메일 소유자, 참가자 및 독자** 를 선택하면 해당 리소스에 액세스할 수 있는 사용자에 따라 전자 메일을 동적으로 보낼 수 있습니다. 또는 **추가 관리자 전자 메일** 텍스트 상자에서 쉼표로 구분된 사용자 목록을 제공하는 방법도 있습니다.

![규칙 추가 블레이드][7]

임계값을 초과할 경우 다음 이미지와 비슷한 전자 메일이 도착합니다.

![임계값 위반 전자 메일][8]

메트릭 경고가 만들어지면 경고 목록이 표시되고 모든 경고 규칙에 대한 개요가 제공됩니다.

![경고 규칙 보기][9]

경고 알림에 대한 자세한 내용은 [경고 알림 받기](../azure-portal/insights-receive-alert-notifications.md)

웹후크에 대한 자세한 내용 및 웹후크를 경고와 함께 사용하는 방법을 알아보려면 [Azure 활동 메트릭 경고에 대한 웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>다음 단계
* [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) 
* [Power BI를 사용하여 Azure 감사 로그 시각화](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 블로그 게시물.
* [Power BI 등에서 Azure 감사 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png


<!--HONumber=Oct16_HO2-->


