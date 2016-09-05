<properties
	pageTitle="Azure 진단 로그 | Microsoft Azure"
	description="Azure 진단 로그란 무엇이고 Azure 리소스 내에서 발생하는 이벤트를 파악하는 데 어떻게 사용할 수 있는지 알아봅니다."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="johnkem"/>

# Azure 진단 로그 개요
**Azure 진단 로그**는 해당 리소스의 작업에 대한 풍부하고 빈번한 데이터를 제공하는 리소스에서 내보낸 로그입니다. 이러한 로그의 내용은 리소스 형식(예를 들어, Windows 이벤트 시스템 로그는 VM에 대한 진단 로그의 한 범주이며 Blob, 테이블 및 큐 로그는 저장소 계정에 대한 진단 로그의 범주임)에 따라 다르며 구독에 있는 리소스에 대해 수행된 작업에 대한 정보를 제공하는 [활동 로그(이전에 감사 로그 또는 작업 로그로 알려져 있음)](monitoring-overview-activity-logs.md)와 다릅니다. 일부 리소스만 여기서 설명하는 새로운 형식의 진단 로그를 지원합니다. 아래 지원되는 서비스 목록은 새 진단 로그를 지원하는 리소스 형식을 보여 줍니다.

![진단 로그의 논리적 배치](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## 진단 로그로 수행할 수 있는 작업
진단 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

- 감사 또는 수동 검사를 위해 **저장소 계정**에 저장합니다. **진단 설정**을 사용하여 보존 기간(일)을 지정할 수 있습니다.
- 타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)으로 수집을 위해 [**이벤트 허브**로 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)합니다.
- [OMS Log Analytics](../log-analytics/log-analytics-azure-storage-json.md)를 사용하여 분석

## 진단 설정
진단 설정을 사용하여 비-계산 리소스에 대한 진단 로그를 구성합니다. 리소스 제어를 위한 **진단 설정**:

- 진단 로그를 보낼 위치(저장소 계정, 이벤트 허브 및/또는 OMS Log Analytics).
- 보낼 로그 범주.
- 저장소 계정에 각 로그 범주를 보존할 기간 - 0일의 보존 기간은 로그를 영원히 보관하는 것을 의미합니다. 보존 정책이 설정되었지만 저장소 계정에 로그를 저장할 수 없는 경우(예를 들어 이벤트 허브 또는 OMS 옵션만 선택한 경우) 보존 정책은 적용되지 않습니다.

이러한 설정은 Azure 포털에서 리소스에 대한 진단 블레이드를 통해, Azure PowerShell 및 CLI 명령을 통하거나 [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)를 통해 쉽게 구성합니다.

> [AZURE.WARNING] 계산 리소스(예: VM 또는 서비스 패브릭)에 대한 진단 로그 및 메트릭에서는 [출력의 구성 및 선택을 위한 별도의 메커니즘](../azure-diagnostics.md)을 사용합니다.

## 진단 로그의 컬렉션을 사용하도록 설정하는 방법
리소스 만들기의 일부로 또는 포털에서 리소스 블레이드를 통해 리소스를 만든 후 진단 로그의 컬렉션을 사용하도록 설정할 수 있습니다. 또한 Azure PowerShell 또는 CLI 명령을 사용하거나 Insights REST API를 사용하여 언제든지 진단 로그를 사용하도록 설정할 수도 있습니다.

> [AZURE.TIP] 이러한 지침은 모든 리소스에 직접 적용되지 않을 수 있습니다. 특정 리소스 형식에 적용할 수 있는 특수한 단계를 알아보려면 이 페이지 맨 아래에 있는 스키마 링크를 참조하세요.

[이 문서에서는 리소스를 만들 때 리소스 템플릿을 사용하여 진단 설정을 사용하도록 설정하는 방법을 보여 줍니다.](./monitoring-enable-diagnostic-logs-using-template.md)

### 포털에서 진단 사용
다음을 수행하여 일부 리소스 형식을 만들 때 Azure 포털에서 진단 로그를 사용하도록 설정할 수 있습니다.

1.	**새로 만들기**로 이동한 다음 관심 있는 리소스를 선택합니다.
2.	기본 설정을 구성하고 크기를 선택한 후 **모니터링** 아래 **설정** 블레이드에서 **사용**을 선택하고 진단 로그를 저장할 저장소 계정을 선택합니다. 저장소 계정에 진단을 보내는 경우 저장소 및 트랜잭션에 대해 표준 데이터 요금이 부과됩니다. ![리소스 생성 중에 진단 로그 사용](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3.	**확인**을 클릭하여 리소스를 만듭니다.

리소스를 만든 후 Azure 포털에서 진단 로그를 사용하도록 설정하려면 다음을 수행합니다.

1.	리소스에 대한 블레이드로 이동하고 **진단** 블레이드를 엽니다.
2.	**켜기**를 클릭하고 저장소 계정 및/또는 이벤트 허브를 선택합니다. ![리소스 생성 후에 진단 로그 사용](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3.	**로그** 아래에서 수집 또는 스트림할 **로그 범주**를 선택합니다.
4.	**Save**를 클릭합니다.

### 프로그래밍 방식으로 진단 사용
Azure PowerShell Cmdlet을 통해 진단 로그를 사용하도록 설정하려면 다음 명령을 사용합니다.

저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

    Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -StorageAccountId [your storage account id] -Enabled $true

저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

이벤트 허브로 진단 로그의 스트리밍을 사용하도록 설정하려면 다음 명령을 사용합니다.

    Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

서비스 버스 규칙 ID는 `{service bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다.

Azure CLI를 통해 진단 로그를 사용하도록 설정하려면 다음 명령을 사용합니다.

저장소 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

저장소 계정 ID는 로그를 보낼 저장소 계정에 대한 리소스 ID입니다.

이벤트 허브로 진단 로그의 스트리밍을 사용하도록 설정하려면 다음 명령을 사용합니다.

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

서비스 버스 규칙 ID는 `{service bus resource ID}/authorizationrules/{key name}` 형식의 문자열입니다.

Insights REST API를 사용하여 진단 설정을 변경하려면 [이 문서](https://msdn.microsoft.com/library/azure/dn931931.aspx)를 참조하세요.

## 진단 로그에 대한 지원되는 서비스 및 스키마
진단 로그의 스키마는 리소스 및 로그 범주에 따라 달라집니다. 다음은 지원되는 서비스 및 해당 스키마입니다.

| 부여 | 스키마 및 문서 |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------|
| 소프트웨어 부하 분산 장치 | [Azure 부하 분산 장치에 대한 로그 분석(미리보기)](../load-balancer/load-balancer-monitor-log.md) |
| 네트워크 보안 그룹 | [NSG(네트워크 보안 그룹)에 대한 로그 분석](../virtual-network/virtual-network-nsg-manage-log.md) |
| 응용 프로그램 게이트웨이 | [응용 프로그램 게이트웨이에 대한 진단 로깅](../application-gateway/application-gateway-diagnostics.md) |
| 키 자격 증명 모음 | [Azure 키 자격 증명 모음 로깅](../key-vault/key-vault-logging.md) |
| Azure 검색 | [검색 트래픽 분석 설정 및 사용](../search/search-traffic-analytics.md) |
| 데이터 레이크 저장소 | [Azure Data Lake Store에 대한 진단 로그에 액세스](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 데이터 레이크 분석 | [Azure Data Lake Analytics에 대한 진단 로그에 액세스](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| 논리 앱 | 스키마를 사용할 수 없습니다. |

## 다음 단계
- [**이벤트 허브**로 진단 로그 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [Insights REST API를 사용하여 진단 설정 변경](https://msdn.microsoft.com/library/azure/dn931931.aspx)
- [OMS Log Analytics를 사용하여 로그 분석](../log-analytics/log-analytics-azure-storage-json.md)

<!---HONumber=AcomDC_0824_2016-->