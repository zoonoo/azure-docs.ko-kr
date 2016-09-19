<properties
	pageTitle="Azure 진단 로그 보관 | Microsoft Azure"
	description="저장소 계정에 장기 보존을 위해 Azure 진단 로그를 보관하는 방법에 대해 알아봅니다."
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
	ms.date="08/26/2016"
	ms.author="johnkem"/>

# Azure 진단 로그 보관
이 문서에서는 Azure 포털, PowerShell Cmdlet, CLI 또는 REST API를 사용하여 저장소 계정에서 [Azure 진단 로그](monitoring-overview-of-diagnostic-logs.md)를 보관하는 방법을 보여 줍니다. 이 옵션은 감사, 정적 분석 또는 백업을 위해 옵션 보존 정책으로 진단 로그를 유지하려는 경우에 유용합니다.

## 필수 조건
시작하기 전에 진단 로그를 보관하려면 [저장소 계정을 만들](../storage/storage-create-storage-account.md#create-a-storage-account)어야 합니다. 모니터링 데이터에 대한 액세스를 보다 잘 제어할 수 있도록 다른 비 모니터링 데이터가 저장된 기존 저장소 계정을 사용하지 않는 것이 좋습니다. 그러나 저장소 계정에 대한 활동 로그 및 진단 메트릭을 보관하는 경우 중앙 위치에서 모든 모니터링 데이터를 유지하도록 진단 로그에 대해 해당 저장소 계정을 사용하는 것이 합리적일 수 있습니다. 사용하는 저장소 계정은 Blob 저장소 계정이 아닌 범용 저장소 계정이어야 합니다.

## 진단 설정
다음 방법 중 하나를 사용하여 진단 로그를 보관하려면 특정 리소스에 대한 **진단 설정**을 설정합니다. 리소스에 대한 진단 설정은 저장되거나 스트리밍되는 로그 및 출력(저장소 계정 및/또는 이벤트 허브)의 범주를 정의합니다. 또한 저장소 계정에 저장되는 각 로그 범주의 이벤트에 대한 보존 정책(보존할 일 수)을 정의합니다. 보존 정책이 0으로 설정된 경우 해당 로그 범주에 대한 이벤트는 무기한으로(즉, 영원히) 저장됩니다. 그렇지 않은 경우 보존 정책은 1에서 2147483647 사이의 숫자일 수 있습니다. [진단 설정에 대한 자세한 내용은 여기에서 확인할 수 있습니다](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).

## 포털을 사용하여 진단 로그 보관

1. 포털에서 진단 로그의 보관을 활성화하려는 리소스에 대한 리소스 블레이드를 클릭합니다.
2. 리소스 설정 메뉴의 **모니터링** 섹션에서 **진단**을 선택합니다.

    ![리소스 메뉴의 모니터링 섹션](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-sec.png)
3. **저장소 계정에 내보내기**에 대한 상자를 선택한 다음 저장소 계정을 선택합니다. 필요에 따라 **보존(일)** 슬라이더를 사용하여 이러한 로그를 유지할 일 수를 설정합니다. 0일의 보존은 로그를 무기한 저장합니다.

	![진단 로그 블레이드](media/monitoring-archive-diagnostic-logs/diag-log-monitoring-blade.png)
4. **Save**를 클릭합니다.

진단 로그는 새 이벤트 데이터가 생성되는 즉시 해당 저장소 계정에 보관됩니다.

## PowerShell Cmdlet을 통해 진단 로그 보관

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| 속성 | 필수 | 설명 |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| ResourceId | 예 | 진단 설정을 설정하려는 리소스의 리소스 ID입니다. |
| StorageAccountId | 아니요 | 진단 로그를 저장할 저장소 계정의 리소스 ID입니다. |
| 범주 | 아니요 | 활성화할 로그 범주의 쉼표로 구분된 목록입니다. |
| 사용 | 예 | 이 리소스에 대한 진단 활성화 여부를 나타내는 부울입니다. |
| RetentionEnabled | 아니요 | 이 리소스에 대한 보존 정책 활성화 여부를 나타내는 부울입니다. |
| RetentionInDays | 아니요 | 이벤트를 유지해야 하는 일 수는 1에서 2147483647 사이입니다. 0 값은 로그를 무기한 저장합니다. |

## 플랫폼 간 CLI를 통해 작업 로그를 보관합니다.

```
azure insights diagnostic set --resourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg --storageId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage –categories networksecuritygroupevent,networksecuritygrouprulecounter --enabled true
```

| 속성 | 필수 | 설명 |
|------------------|----------|-------------------------------------------------------------------------------------------------------|
| resourceId | 예 | 진단 설정을 설정하려는 리소스의 리소스 ID입니다. |
| storageId | 아니요 | 진단 로그를 저장할 저장소 계정의 리소스 ID입니다. |
| categories | 아니요 | 활성화할 로그 범주의 쉼표로 구분된 목록입니다. |
| 사용 | 예 | 이 리소스에 대한 진단 활성화 여부를 나타내는 부울입니다. |

## REST API를 통해 진단 로그 보관
Insights REST API를 사용하여 진단 설정을 설정할 수는 방법에 대한 내용은 [이 문서를 참조](https://msdn.microsoft.com/library/azure/dn931931.aspx)하세요.

## 저장소 계정의 진단 로그 스키마
보관을 설정한 후 활성화한 로그 범주 중 하나에서 이벤트가 발생하는 즉시 저장소 계정에 저장소 컨테이너가 만들어집니다. 컨테이너 내의 Blob은 진단 로그 및 활동 로그와 동일한 형식을 따릅니다. 해당 Blob의 구조는 다음과 같습니다.

> insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

또는 더 간단하게

> insights-logs-{log category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

예를 들어 Blob 이름은 다음과 같습니다.

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

각 PT1H.json Blob은 Blob URL에 지정된 시간 내에서 발생한 이벤트의 JSON Blob을 포함합니다(예: h=12). 현재 시간 동안 이벤트는 발생하는 순서대로 PT1H.json 파일에 추가됩니다. 진단 로그 이벤트는 시간당 개별 Blob으로 나뉘므로 분 값(m=00)은 항상 00입니다.

PT1H.json 파일 내에서 각 이벤트는 이 형식에 따라 "레코드" 배열에 저장됩니다.

```
{
	"records": [
		{
			"time": "2016-07-01T00:00:37.2040000Z",
			"systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
			"category": "NetworkSecurityGroupRuleCounter",
			"resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
			"operationName": "NetworkSecurityGroupCounters",
			"properties": {
				"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
				"subnetPrefix": "10.3.0.0/24",
				"macAddress": "000123456789",
				"ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
				"direction": "In",
				"type": "allow",
				"matchedConnections": 1988
			}
		}
	]
}
```

| 요소 이름 | 설명 |
|---------------|-------------------------------------------------------------------------------------------------------------|
| 실시간 | 이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| resourceId | 영향을 받는 리소스의 리소스 ID입니다. |
| operationName | 작업의 이름입니다. |
| 카테고리 | 이벤트의 로그 범주입니다. |
| properties | 이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |

> [AZURE.NOTE] 해당 속성의 속성 및 사용은 리소스에 따라 달라질 수 있습니다.

## 다음 단계
- [분석을 위한 Blob 다운로드](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs)
- [이벤트 허브로 진단 로그 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)
- [진단 로그에 대해 자세히 알아보기](monitoring-overview-of-diagnostic-logs.md)

<!---HONumber=AcomDC_0907_2016-->