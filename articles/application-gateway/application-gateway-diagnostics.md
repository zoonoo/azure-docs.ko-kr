<properties 
   pageTitle="응용 프로그램 게이트웨이에 대한 액세스 및 성능 로그 모니터링 | Microsoft Azure"
   description="응용 프로그램 게이트웨이에 대한 액세스 및 성능 로그를 사용하고 관리하는 방법을 알아봅니다"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="amitsriva" />

#응용 프로그램 게이트웨이에 대한 진단 로깅

Azure에서 다양한 유형의 로그를 사용하여 응용 프로그램 게이트웨이를 관리하고 문제를 해결할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있으며 Azure Blob 저장소에서 모든 로그를 추출하고 다양한 도구(예: [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel 및 PowerBI)에서 볼 수 있습니다. 아래 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

- **감사 로그:** [Azure 감사 로그](../azure-portal/insights-debugging-with-events.md)(이전의 작업 로그)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 볼 수 있습니다. 감사 로그는 기본적으로 사용하도록 설정되며 Azure Preview 포털에서 볼 수 있습니다.
- **액세스 로그:** 이 로그를 사용하여 응용 프로그램 게이트웨이 액세스 패턴을 보고 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 입력 및 출력 바이트 등 중요한 정보를 분석할 수 있습니다. 액세스 로그는 300초마다 수집됩니다. 이 로그에는 응용 프로그램 게이트웨이 인스턴스당 하나의 레코드가 포함됩니다. 응용 프로그램 게이트웨이 인스턴스는 'InstanceId' 속성으로 식별될 수 있습니다.
- **성능 로그:** 이 로그를 사용하여 응용 프로그램 게이트웨이 인스턴스 성능을 확인할 수 있습니다. 이 로그는 인스턴스 단위로 처리된 총 요청, 처리량(바이트), 실패한 총 요청, 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등의 성능 정보를 캡처합니다. 성능 로그는 60초마다 수집됩니다.

>[AZURE.WARNING] 로그는 리소스 관리자 배포 모델에 배포된 리소스에 대해서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 두 모델의 이해를 돕기 위해 [리소스 관리자 배포 및 클래식 배포 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

##로깅 사용
감사 로깅은 모든 리소스 관리자 리소스에 대해 항상 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 액세스 및 성능 로깅을 사용하도록 설정해야 합니다. 로깅을 사용하려면 다음 단계를 따릅니다.

1. 로그 데이터를 저장할 저장소 계정의 리소스 ID를 적어 둡니다. 형식은 다음과 같습니다. /subscriptions/<구독 ID>/resourceGroups/<리소스 그룹 이름>/providers/Microsoft.Storage/storageAccounts/<저장소 계정 이름>. 구독의 모든 저장소 계정을 사용할 수 있습니다. 미리 보기 포털을 사용하여 이 정보를 찾을 수 있습니다. ![미리 보기 포털 - 응용 프로그램 게이트웨이 진단](./media/application-gateway-diagnostics/diagnostics1.png)
 
2. 로깅을 사용할 응용 프로그램 게이트웨이의 리소스 ID를 적어 둡니다. 형식은 다음과 같습니다. /subscriptions/<구독 ID>/resourceGroups/<리소스 그룹 이름>/providers/Microsoft.Network/applicationGateways/<응용 프로그램 게이트웨이 이름>. 미리 보기 포털을 사용하여 이 정보를 찾을 수 있습니다. ![미리 보기 포털 - 응용 프로그램 게이트웨이 진단](./media/application-gateway-diagnostics/diagnostics2.png)

3. 다음 PowerShell cmdlet을 사용하여 진단 로깅을 사용하도록 설정합니다.

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] 감사 로그에는 별도의 저장소 계정이 필요하지 않습니다. 액세스 및 성능 로깅에 저장소를 사용할 경우 서비스 요금이 부과됩니다.


## 감사 로그
이 로그(이전의 "작업 로그")는 기본적으로 Azure에 의해 생성됩니다. 이 로그는 Azure의 이벤트 로그 저장소에서 90일 동안 유지됩니다. [이벤트 및 감사 로그 보기](../azure-portal/insights-debugging-with-events.md) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

## 액세스 로그
이 로그는 위에서 설명한 대로 응용 프로그램 게이트웨이별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 응용 프로그램 게이트웨이의 각 액세스는 아래와 같이 JSON 형식으로 로깅됩니다.

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resoource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
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


## 성능 로그
이 로그는 위에서 설명한 대로 응용 프로그램 게이트웨이별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 다음 데이터가 로깅됩니다.

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

## 감사 로그 보기 및 분석
다음 방법을 사용하여 감사 로그 데이터를 보고 분석할 수 있습니다.

- **Azure 도구:** Azure PowerShell, Azure 명령줄 인터페이스(CLI), Azure REST API 또는 Azure Preview 포털을 통해 감사 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../resource-group-audit.md) 문서에 자세히 나와 있습니다.
- **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 콘텐츠 팩](https://powerbi.microsoft.com/ko-KR/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 미리 구성된 대시보드를 있는 그대로 사용하거나 사용자 지정하여 데이터를 분석할 수 있습니다.

## 액세스 및 성능 로그 보기 및 분석 
Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)는 Blob 저장소 계정에서 카운터 및 이벤트 로그 파일을 수집할 수 있으며 로그를 분석하기 위한 시각화 및 강력한 검색 기능을 포함합니다.

저장소 계정에 연결하고 액세스 및 성능 로그에 대한 JSON 로그 항목을 검색할 수도 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, PowerBI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

>[AZURE.TIP] Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 Github에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## 다음 단계

- [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)를 사용하여 카운터 및 이벤트 로그 시각화
- [Power BI를 사용하여 Azure 감사 로그 시각화](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 블로그 게시물.
- [Power BI 등에서 Azure 감사 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물.

<!---HONumber=AcomDC_0720_2016-->