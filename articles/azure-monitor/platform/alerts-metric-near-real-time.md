---
title: Azure Monitor의 메트릭 경고에 대해 지원되는 리소스
description: Azure Monitor의 메트릭 경고에 대한 지원 메트릭 및 로그 참조
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 9/30/2020
ms.subservice: alerts
ms.openlocfilehash: d1a1a31d5894338e0deeed27fabcde9fdbf6f588
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566489"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure Monitor의 메트릭 경고에 대해 지원되는 리소스

이제 Azure Monitor는 기존의 [클래식 메트릭 경고](./alerts-classic.overview.md)에 비해 상당한 장점이 있는 [새 메트릭 경고 형식](./alerts-overview.md)을 지원합니다. [다양한 Azure 서비스](./metrics-supported.md)에 대해 메트릭을 사용할 수 있습니다. 최신 경고에서 지원하는 리소스 종류가 점점 증가하고 있습니다. 이 문서에서는 이러한 하위 집합이 나열되어 있습니다.

메트릭으로 추출 된 Log Analytics 작업 영역에 저장 된 인기 있는 로그 데이터에 대 한 최신 메트릭 경고를 사용할 수도 있습니다. 자세한 내용은 [로그에 대한 메트릭 경고](./alerts-metric-logs.md)를 확인하세요.

## <a name="portal-powershell-cli-rest-support"></a>포털, PowerShell, CLI, REST 지원
현재 Azure Portal, [REST API](/rest/api/monitor/metricalerts/)또는 [리소스 관리자 템플릿에만](./alerts-metric-create-templates.md)최신 메트릭 경고를 만들 수 있습니다. PowerShell 및 Azure CLI 버전 2.0 이상을 사용한 최신 경고 구성 지원이 곧 제공될 예정입니다.

## <a name="metrics-and-dimensions-supported"></a>지원되는 메트릭 및 차원
최신 메트릭 경고는 차원을 사용하는 메트릭에 대한 경고를 지원합니다. 차원을 사용하여 메트릭을 적절한 수준으로 필터링할 수 있습니다. 해당 차원과 함께 지원되는 모든 메트릭을 [Azure Monitor - 메트릭 탐색기](./metrics-charts.md)에서 탐색하고 시각화할 수 있습니다.

다음은 최신 경고에서 지원 되는 Azure Monitor 메트릭 원본의 전체 목록입니다.

|리소스 유형  |지원되는 차원 |다중 리소스 경고| 사용 가능한 메트릭|
|---------|---------|-----|----------|
|Aadiam/azureADMetrics | 예 | 예 | |
|Microsoft.ApiManagement/service | 예 | 예 | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |예 | 예 | [App Configuration](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/Spring | 예 | 예 | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | 예| 예 | [자동화 계정](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft AVS/privateClouds | 예 | 예 | |
|Microsoft.Batch/batchAccounts | 예 | 예 | [일괄 처리 계정](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | 예 | 예 | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicCompute/domainNames/slots/roles | 예 | 예 | [클래식 Cloud Services](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | 예 | 예 | [클래식 Virtual Machines](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | 예 | 예 | [저장소 계정 (클래식)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | 예 | 예 | |
|Microsoft.ClassicStorage/storageAccounts/fileServices | 예 | 예 | |
|Microsoft.ClassicStorage/storageAccounts/queueServices | 예 | 예 | |
|Microsoft.ClassicStorage/storageAccounts/tableServices | 예 | 예 | |
|Microsoft.CognitiveServices/accounts | 예 | 예 | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | 예 | 예<sup>1</sup> | [Virtual Machines](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | 예 | 예 |[가상 머신 확장 집합](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | 예| 예 | [컨테이너 그룹](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | 예 | 예 | [컨테이너 레지스트리](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | 예 | 예 | [관리되는 클러스터](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | 예 | 예 | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| 예| 예 | [데이터 팩터리 V1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |예 | 예 | [데이터 팩터리 V2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | 예 | 예 | |
|Microsoft.DBforMariaDB/servers | 예 | 예 | [MariaDB 용 DB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | 예 | 예 |[MySQL용 DB](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/flexibleServers | 예 | 예 | |
|Microsoft.DBforPostgreSQL/servers | 예 | 예 | [PostgreSQL용 DB](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | 예 | 예 | [PostgreSQL V2 용 DB](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|DBforPostgreSQL/singleservers | 예 | 예 | [PostgreSQL 용 DB (단일 서버)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft.Devices/IotHubs | 예 | 예 |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| 예 | 예 | [장치 프로 비전 서비스](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|DigitalTwins/digitalTwinsInstances | 예 | 예 | |
|Microsoft.DocumentDB/databaseAccounts | 예 | 예 | [Cosmos DB](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | 예 | 예 | [Event Grid 도메인](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft EventGrid/systemTopics | 예 | 예 | [Event Grid 시스템 항목](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |예 | 예 | [Event Grid 토픽](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |예| 예 | [Event Hubs 클러스터](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |예| 예 | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | 예 | 예 | [HDInsight 클러스터](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | 예 | 예 | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | 예 |예 |[자격 증명 모음](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | 예 |예 |[데이터 탐색기 클러스터](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | 예 | 예 |[Integration Service 환경](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | 예 | 예 |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | 예 | 예 | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/accounts | 예 | 예 | [Maps 계정](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | 예 | 예 | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | 예 | 예 | [스트리밍 끝점 Media Services](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | 예 | 예 | [Azure NetApp 용량 풀](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | 예 | 예 | [Azure NetApp 볼륨](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | 예 | 예 | [응용 프로그램 게이트웨이](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | 예 | 예 | [방화벽](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | 예 | 예 | [DNS 영역](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | 해당 없음 | 예 |[ExpressRoute 회로](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (표준 SKU 전용)| 예| 예 | [부하 분산 장치](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| 예 | 예 | |
|Microsoft. Network/privateEndpoints| 예 | 예 | |
|Microsoft.Network/privateLinkServices| 예 | 예 |
|Microsoft.Network/publicipaddresses | 예 | 예 |[공용 IP 주소](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | 예 | 예 | [Traffic Manager 프로필](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| 예 | 예 | [Log Analytics 작업 영역](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft 피어 링/피어 링 | 예 | 예 | [피어 링](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft 피어 링/peeringServices | 예 | 예 | [Peering Services](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | 예 | 예 | [용량](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | 예 | 예 | [릴레이](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | 예 | 예 | [Search 서비스](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | 예 | 예 | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | 예 | 예 | [SQL Managed Instances](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | 예 | 예 | [SQL Databases](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | 예 | 예 | [SQL 탄력적 풀](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |예 | 예 | [Storage 계정](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | 예| 예 | [Blob 서비스](./metrics-supported.md#microsoftstoragestorageaccountsblobservices), [파일 서비스](./metrics-supported.md#microsoftstoragestorageaccountsfileservices), [큐 서비스](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) 및 [테이블 서비스](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StorageCache/caches | 예 | 예 | |
|Microsoft.storagesync/storageSyncServices | 예 | 예 | [스토리지 동기화 서비스](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | 예 | 예 | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|VMWareCloudSimple/virtualMachines | 예 | 예 | [CloudSimple Virtual Machines](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | 예 | 예 | [App Service Environment 다중 역할 풀](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | 예 | 예 | [App Service Environment 작업자 풀](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | 예 | 예 | [App Service 계획](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | 예 | 예 | [App Services](./metrics-supported.md#microsoftwebsites-excluding-functions) 및 [Functions](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | 예 | 예 | [App Service 슬롯](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> 가상 컴퓨터 네트워크 메트릭 (총 네트워크, 총 네트워크, 인바운드 흐름, 아웃 바운드 흐름, 인바운드 흐름 최대 생성 비율, 아웃 바운드 흐름 최대 생성 비율) 및 사용자 지정 메트릭은 지원 되지 않습니다.

## <a name="payload-schema"></a>페이로드 스키마

> [!NOTE]
> 웹 후크 통합을 위해 Azure Monitor의 모든 경고 서비스에서 확장 가능 하 고 통합 된 단일 경고 페이로드를 사용 하는 이점을 제공 하는 [일반적인 경고 스키마](./alerts-common-schema.md)를 사용할 수도 있습니다. [일반적인 경고 스키마 정의에 대해 알아봅니다.](./alerts-common-schema-definitions.md)


POST 작업에는 적절히 구성된 [작업 그룹](./action-groups.md)이 사용될 때 모든 최신 메트릭 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다.

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

* 새 [경고 환경](./alerts-overview.md)에 대해 자세히 알아봅니다.
* [Azure의 로그 경고](./alerts-unified-log.md)에 대해 알아봅니다.
* [Azure의 경고](./alerts-overview.md)에 대해 알아봅니다.
