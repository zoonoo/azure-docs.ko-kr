<properties
   pageTitle="NSG에 대한 작업, 이벤트 및 카운터 모니터링 | Microsoft Azure"
   description="NSG에 대한 카운터, 이벤트 및 작업 로깅을 사용하도록 설정하는 방법에 대해 알아보기"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2016"
   ms.author="telmos" />

#NSG(네트워크 보안 그룹)에 대한 로그 분석

Azure에서 NSG를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있으며 Azure Blob 저장소에서 모든 로그를 추출하고 다양한 도구(예: [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel 및 PowerBI)에서 볼 수 있습니다. 아래 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

- **감사 로그:** [Azure 감사 로그](../azure-portal/insights-debugging-with-events.md)(이전의 작업 로그)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 볼 수 있습니다. 감사 로그는 기본적으로 사용하도록 설정되며 Azure Preview 포털에서 볼 수 있습니다.
- **이벤트 로그:** 이 로그를 사용하여 VM 및 MAC 주소 기반 인스턴스 역할에 적용된 NSG 규칙을 볼 수 있습니다. 이러한 규칙에 대한 상태는 60초마다 수집됩니다.
- **카운터 로그:** 이 로그를 사용하여 트래픽을 허용하거나 거부하기 위해 각 NSG 규칙이 적용된 횟수를 볼 수 있습니다.

>[AZURE.WARNING] 로그는 리소스 관리자 배포 모델에 배포된 리소스에 대해서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 두 모델의 이해를 돕기 위해 [리소스 관리자 배포 및 클래식 배포 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

##로깅 사용
감사 로깅은 모든 리소스 관리자 리소스에 대해 항상 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 이벤트 및 카운터 로깅을 사용하도록 설정해야 합니다. 로깅을 사용하려면 다음 단계를 따릅니다.

1.  [Azure 포털](https://portal.azure.com)에 로그인합니다. 기존 네트워크 보안 그룹이 아직 없는 경우 [NSG를 만든](virtual-networks-create-nsg-arm-ps.md) 후 진행합니다.

2.  Preview 포털에서 **찾아보기>** >> **네트워크 보안 그룹**을 클릭합니다.

	![Preview 포털 - 네트워크 보안 그룹](./media/virtual-network-nsg-manage-log/portal-enable1.png)

3. 기존 네트워크 보안 그룹을 선택합니다.

	![Preview 포털 - 네트워크 보안 그룹 설정](./media/virtual-network-nsg-manage-log/portal-enable2.png)

4. **설정** 블레이드에서 **진단**을 클릭한 후 **진단** 창에서 **상태** 옆의 **켜기**를 클릭합니다.
5. **설정** 블레이드에서 **저장소 계정**을 클릭하고 기존 저장소 계정을 선택 하거나 새로 만듭니다.

>[AZURE.INFORMATION] 감사 로그에는 별도의 저장소 계정이 필요하지 않습니다. 이벤트 및 규칙 로깅에 대한 저장소를 사용할 경우 서비스 요금이 부과됩니다.

6. **저장소 계정** 바로 아래 드롭다운 목록에서 이벤트, 카운터 또는 둘 다를 로깅할지 선택하고 **저장**을 클릭합니다.

	![Preview 포털 - 진단 로그](./media/virtual-network-nsg-manage-log/portal-enable3.png)

## 감사 로그
이 로그(이전의 "작업 로그")는 기본적으로 Azure에 의해 생성됩니다. 이 로그는 Azure의 이벤트 로그 저장소에서 90일 동안 유지됩니다. [이벤트 및 감사 로그 보기](../azure-portal/insights-debugging-with-events.md) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

## 카운터 로그
이 로그는 위에서 설명한 대로 NSG별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 리소스에 적용된 각 규칙은 아래와 같이 JSON 형식으로 기록됩니다.

	{
		"time": "2015-09-11T23:14:22.6940000Z",
		"systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
		"category": "NetworkSecurityGroupRuleCounter",
		"resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
		"operationName": "NetworkSecurityGroupCounters",
		"properties": {
			"vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
			"subnetPrefix":"10.0.0.0/24",
			"macAddress":"001517D9C43C",
			"ruleName":"DenyAllOutBound",
			"direction":"Out",
			"type":"block",
			"matchedConnections":0
			}
	}

## 이벤트 로그
이 로그는 위에서 설명한 대로 NSG별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 다음 데이터가 로깅됩니다.

	{
		"time": "2015-09-11T23:05:22.6860000Z",
		"systemId": "e22a0996-e5a7-4952-8e28-4357a6e8f0c5",
		"category": "NetworkSecurityGroupEvent",
		"resourceId": "/SUBSCRIPTIONS/D763EE4A-9131-455F-8C5E-876035455EC4/RESOURCEGROUPS/INSIGHTOBONRP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/NSGINSIGHTOBONRP",
		"operationName": "NetworkSecurityGroupEvents",
		"properties": {
			"vnetResourceGuid":"{DD0074B1-4CB3-49FA-BF10-8719DFBA3568}",
			"subnetPrefix":"10.0.0.0/24",
			"macAddress":"001517D9C43C",
			"ruleName":"AllowVnetOutBound",
			"direction":"Out",
			"priority":65000,
			"type":"allow",
			"conditions":{
				"destinationPortRange":"0-65535",
				"sourcePortRange":"0-65535",
				"destinationIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32",
				"sourceIP":"10.0.0.0/8,172.16.0.0/12,169.254.0.0/16,192.168.0.0/16,168.63.129.16/32"
			}
		}
	}

## 감사 로그 보기 및 분석
다음 방법을 사용하여 감사 로그 데이터를 보고 분석할 수 있습니다.

- **Azure 도구:** Azure PowerShell, Azure 명령줄 인터페이스(CLI), Azure REST API 또는 Azure Preview 포털을 통해 감사 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../resource-group-audit.md) 문서에 자세히 나와 있습니다.
- **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 콘텐츠 팩](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 미리 구성된 대시보드를 있는 그대로 사용하거나 사용자 지정하여 데이터를 분석할 수 있습니다.

## 카운터 및 이벤트 로그 보기 및 분석

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)는 Blob 저장소 계정에서 카운터 및 이벤트 로그 파일을 수집할 수 있으며 로그를 분석하기 위한 시각화 및 강력한 검색 기능을 포함합니다.

또한 저장소 계정에 연결하고 이벤트 및 카운터 로그에 대한 JSON 로그 항목을 검색할 수 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, PowerBI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

>[AZURE.TIP] Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 Github에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## 다음 단계

- [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)를 사용하여 카운터 및 이벤트 로그 시각화
- [Power BI를 사용하여 Azure 감사 로그 시각화](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 블로그 게시물.
- [Power BI 등에서 Azure 감사 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물.

<!---HONumber=AcomDC_0720_2016-->