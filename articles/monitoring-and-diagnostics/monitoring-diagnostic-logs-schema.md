---
title: "Azure 진단 로그 지원 서비스 및 스키마 | Microsoft Docs"
description: "Azure 진단 로그에 대해 지원되는 서비스 및 이벤트 스키마에 대해 알아봅니다."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: johnkem
ms.openlocfilehash: 2e99b7afa4e458e9ef62314e65d9e386657a747b
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Azure 진단 로그에 대해 지원되는 서비스, 스키마 및 범주

[Azure 리소스 진단 로그](monitoring-overview-of-diagnostic-logs.md)는 해당 리소스의 작업을 설명하는 Azure 리소스에서 내보낸 로그입니다. 이러한 로그는 리소스 종류별로 다릅니다. 이 문서에서는 각 서비스에서 내보내는 이벤트에 대해 지원되는 서비스 및 이벤트 스키마 집합에 대해 간략히 설명합니다. 또한 이 문서에는 리소스 종류당 사용 가능한 로그 범주의 전체 목록이 포함되어 있습니다.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>리소스 진단 로그에 대해 지원되는 서비스 및 스키마
리소스 진단 로그의 스키마는 리소스 및 로그 범주에 따라 달라집니다.   

| 부여 | 스키마 및 문서 |
| --- | --- |
| API 관리 | [API 관리 진단 로그](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| 응용 프로그램 게이트웨이 |[Application Gateway에 대한 진단 로깅](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation에 대한 Log Analytics](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 진단 로깅](../batch/batch-diagnostics.md) |
| Customer Insights | 스키마를 사용할 수 없음 |
| Content Delivery Network | 스키마를 사용할 수 없음 |
| CosmosDB | [Azure Cosmos DB 로깅](../cosmos-db/logging.md) |
| 데이터 레이크 분석 |[Azure Data Lake Analytics에 대한 진단 로그에 액세스](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| 데이터 레이크 저장소 |[Azure Data Lake Store에 대한 진단 로그에 액세스](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs 진단 로그](../event-hubs/event-hubs-diagnostic-logs.md) |
| IoT 허브 | [IoT Hub 작업](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| 키 자격 증명 모음 |[Azure Key Vault 로깅](../key-vault/key-vault-logging.md) |
| 부하 분산 장치 |[Azure Load Balancer에 대한 Log analytics](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)에 대한 로그 분석](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS 보호 | 스키마를 사용할 수 없음 |
| Recovery Services | 스키마를 사용할 수 없음|
| 검색 |[검색 트래픽 분석 설정 및 사용](../search/search-traffic-analytics.md) |
| 서버 관리 | 스키마를 사용할 수 없음 |
| Service Bus |[Azure Service Bus 진단 로그](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL 데이터베이스 | [Azure SQL Database 진단 로깅](../sql-database/sql-database-metrics-diag-logging.md) |
| 스트림 분석 |[작업 진단 로그](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 가상 네트워크 | 스키마를 사용할 수 없음 |

## <a name="supported-log-categories-per-resource-type"></a>각 리소스 유형별 지원되는 로그 범주
|리소스 종류|Category|범주 표시 이름|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 게이트웨이 관련 로그|
|Microsoft.Automation/automationAccounts|JobLogs|작업 로그|
|Microsoft.Automation/automationAccounts|JobStreams|작업 스트림|
|Microsoft.Automation/automationAccounts|DscNodeStatus|디스크 노드 상태|
|Microsoft.Batch/batchAccounts|ServiceLog|서비스 로그|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|끝점의 메트릭(예: 대역폭, 송신 등)을 가져옵니다.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataLakeAnalytics/accounts|감사|감사 로그|
|Microsoft.DataLakeAnalytics/accounts|요청|요청 로그|
|Microsoft.DataLakeStore/accounts|감사|감사 로그|
|Microsoft.DataLakeStore/accounts|요청|요청 로그|
|Microsoft.Devices/provisioningServices|DeviceOperations|장치 작업|
|Microsoft.Devices/provisioningServices|ServiceOperations|서비스 작동|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|보관 로그|
|Microsoft.EventHub/namespaces|OperationalLogs|작업 로그|
|Microsoft.EventHub/namespaces|AutoScaleLogs|자동 크기 조정 로그|
|Microsoft.KeyVault/vaults|AuditEvent|감사 로그|
|Microsoft.Logic/workflows|WorkflowRuntime|워크플로 런타임 진단 이벤트|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|통합 계정 이벤트 추적|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|네트워크 보안 그룹 이벤트|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|네트워크 보안 그룹 규칙 카운터|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|부하 분산 장치 경고 이벤트|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|부하 분산 장치 프로브 상태|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 보호 알림|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 보호 경고|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Application Gateway 액세스 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway 성능 로그|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway 방화벽 로그|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup 보고 데이터|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 작업|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 이벤트|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 복제된 항목|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery 복제 통계|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 복구 지점|
|Microsoft.Search/searchServices|OperationLogs|작업 로그|
|Microsoft.ServiceBus/namespaces|OperationalLogs|작업 로그|
|Microsoft.Sql/servers/databases|QueryStore|쿼리 저장소|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|쿼리 저장소 런타임 통계|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|쿼리 저장소 대기 통계|
|Microsoft.Sql/servers/databases|오류|오류|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|데이터베이스 대기 통계|
|Microsoft.Sql/servers/databases|시간 제한|시간 제한|
|Microsoft.Sql/servers/databases|블록|블록|
|Microsoft.Sql/servers/databases|SQLInsights|SQL 정보|
|Microsoft.StreamAnalytics/streamingjobs|실행|실행|
|Microsoft.StreamAnalytics/streamingjobs|작성|작성|

## <a name="next-steps"></a>다음 단계

* [진단 로그에 대해 자세히 알아보기](monitoring-overview-of-diagnostic-logs.md)
* [**Event Hubs**로 리소스 진단 로그 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Azure Monitor REST API를 사용하여 리소스 진단 설정 변경](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../log-analytics/log-analytics-azure-storage.md)
