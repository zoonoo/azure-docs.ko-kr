---
title: Azure Monitor의 메트릭 경고에 대해 지원되는 리소스
description: Azure Monitor의 메트릭 경고에 대한 지원 메트릭 및 로그 참조
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: e4dfc33756a287b6d2f6e41edded3332ca85241d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274932"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure Monitor의 메트릭 경고에 대해 지원되는 리소스

이제 Azure Monitor는 기존의 [클래식 메트릭 경고](../../azure-monitor/platform/alerts-overview.md)에 비해 상당한 장점이 있는 [새 메트릭 경고 형식](../../azure-monitor/platform/alerts-classic.overview.md)을 지원합니다. [다양한 Azure 서비스](../../azure-monitor/platform/metrics-supported.md)에 대해 메트릭을 사용할 수 있습니다. 최신 경고에서 지원하는 리소스 종류가 점점 증가하고 있습니다. 이 문서에서는 이러한 하위 집합이 나열되어 있습니다.

메트릭으로 추출 된 Log Analytics 작업 영역에 저장 된 인기 있는 로그 데이터에 대 한 최신 메트릭 경고를 사용할 수도 있습니다. 자세한 내용은 [로그에 대한 메트릭 경고](../../azure-monitor/platform/alerts-metric-logs.md)를 확인하세요.

## <a name="portal-powershell-cli-rest-support"></a>포털, PowerShell, CLI, REST 지원
현재는 Azure Portal, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/) 또는 [Resource Manager 템플릿](../../azure-monitor/platform/alerts-metric-create-templates.md)에서만 최신 메트릭 경고를 만들 수 있습니다. PowerShell 및 Azure CLI 버전 2.0 이상을 사용한 최신 경고 구성 지원이 곧 제공될 예정입니다.

## <a name="metrics-and-dimensions-supported"></a>지원되는 메트릭 및 차원
최신 메트릭 경고는 차원을 사용하는 메트릭에 대한 경고를 지원합니다. 차원을 사용하여 메트릭을 적절한 수준으로 필터링할 수 있습니다. 해당 차원과 함께 지원되는 모든 메트릭을 [Azure Monitor - 메트릭 탐색기](../../azure-monitor/platform/metrics-charts.md)에서 탐색하고 시각화할 수 있습니다.

최신 경고에서 지원하는 Azure Monitor 메트릭 원본의 전체 목록은 다음과 같습니다.

|리소스 유형  |지원되는 차원 |다중 리소스 경고| 사용 가능한 메트릭|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | yes| 예 | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft AppPlatform/스프링 |예| yes|
|Microsoft.Automation/automationAccounts | yes| 예 | [Automation 계정](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 해당 없음| 예 | [Batch 계정](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|yes| 예 |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|ClassicStorage/storageAccounts/mmxclassic|예|yes|
|ClassicStorage/storageAccounts/mmxclassic/blobServices|예|yes|
|ClassicStorage/storageAccounts/mmxclassic/fileServices|예|yes|
|ClassicStorage/storageAccounts/mmxclassic/queueServices|예|yes|
|ClassicStorage/storageAccounts/mmxclassic/tableServices|예|yes| |
|Microsoft.CognitiveServices/accounts| 해당 없음 | 예 | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |yes | yes | [Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |해당 없음 | yes |[가상 머신 확장 집합](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | yes| 예 | [컨테이너 그룹](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | yes | 예 | [관리되는 클러스터](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | yes | yes | |
|Microsoft.DataFactory/datafactories| yes| 예 | [데이터 팩터리 V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |yes | 예 |[데이터 팩터리 V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|DataShare/계정 |예| yes|
|Microsoft.DBforMySQL/servers |해당 없음| 예 |[MySQL용 DB](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |해당 없음 | 예 | [PostgreSQL용 DB](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | 해당 없음 | 예 |[IoT Hub 메트릭](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| yes | 예 |[DPS 메트릭](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domains|예|yes| |
|Microsoft.EventGrid/topics |yes | 예 |[Event Grid 항목](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |yes| 예 |[Event Hubs 클러스터](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |yes| 예 |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| 예 |예 |[자격 증명 모음](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |해당 없음 | 예 |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|yes| 예 | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |yes| 예 | [Azure NetApp 용량 풀](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft NetApp/netAppAccounts/capacityPools/볼륨 |yes| 예 | [Azure NetApp 볼륨](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|해당 없음| 예 |  |
|Microsoft.Network/dnsZones | 해당 없음| 예 | [DNS 영역](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | 해당 없음 | 예 |[ExpressRoute 회로](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (표준 SKU 전용)| yes| 예 | [부하 분산 장치](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways|예|yes|
|Microsoft. Network/privateEndpoints|예|yes|
|Microsoft. Network/privateLinkServices|예|yes|
|Microsoft.Network/publicipaddresses |해당 없음 | 예 |[공용 IP 주소](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | yes | 예 | [Traffic Manager 프로필](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| yes | 예 | [Log Analytics 작업 영역](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | yes | 예 | [중계](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft 피어 링/peeringServices|예|yes|
|Microsoft.PowerBIDedicated/capacities | 해당 없음 | 예 | [Capacities](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |해당 없음|예 | [Search 서비스](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |yes| 예 |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   예 | yes |
|Microsoft.Sql/servers/databases    | 예 | yes |
|Microsoft.Storage/storageAccounts |yes | 예 | [Storage 계정](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | yes| 예 | [Blob 서비스](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [파일 서비스](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [큐 서비스](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) 및 [테이블 서비스](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |해당 없음| 예 | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|VMWareCloudSimple/virtualMachines |yes|예 |[CloudSimple Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | yes | 예 | [App Service Environment 다중 역할 풀](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | yes | 예 | [App Service Environment 작업자 풀](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | yes | 예 | [App Service 계획](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | yes | 예 | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) 및 [Functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | yes | 예 | [App Service 슬롯](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>페이로드 스키마

> [!NOTE]
> 웹 후크 통합을 위해 Azure Monitor의 모든 경고 서비스에서 확장 가능 하 고 통합 된 단일 경고 페이로드를 사용 하는 이점을 제공 하는 [일반적인 경고 스키마](https://aka.ms/commonAlertSchemaDocs)를 사용할 수도 있습니다. [일반적인 경고 스키마 정의에 대해 알아봅니다.](https://aka.ms/commonAlertSchemaDefinitions)


POST 작업에는 적절히 구성된 [작업 그룹](../../azure-monitor/platform/action-groups.md)이 사용될 때 모든 최신 메트릭 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다.

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* 새 [경고 환경](../../azure-monitor/platform/alerts-overview.md)에 대해 자세히 알아봅니다.
* [Azure의 로그 경고](../../azure-monitor/platform/alerts-unified-log.md)에 대해 알아봅니다.
* [Azure의 경고](../../azure-monitor/platform/alerts-overview.md)에 대해 알아봅니다.
