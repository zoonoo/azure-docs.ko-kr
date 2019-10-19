---
title: Azure 리소스 로그 개요 | Microsoft Docs
description: Azure 리소스 로그에 대해 지원 되는 서비스 및 이벤트 스키마를 이해 합니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2019
ms.openlocfilehash: a418e3d1a59379284422d1d24c1457ab61d84a4c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551763"
---
# <a name="azure-resource-logs-overview"></a>Azure 리소스 로그 개요
Azure 리소스 로그는 내부 작업을 설명 하는 Azure 리소스에서 내보낸 [플랫폼 로그](platform-logs-overview.md) 입니다. 모든 리소스 로그는 일반적인 최상위 스키마를 각 서비스의 유연성과 공유 하 여 고유한 이벤트의 고유한 속성을 내보냅니다.

> [!NOTE]
> 이전에는 리소스 로그를 진단 로그 라고 했습니다.

## <a name="collecting-resource-logs"></a>리소스 로그 수집
리소스 로그는 지원 되는 Azure 리소스에 의해 자동으로 생성 되지만 [진단 설정을](diagnostic-settings.md)사용 하 여 구성 하지 않는 한 수집 되지 않습니다. 각 Azure 리소스에 대 한 진단 설정을 만들어 로그를 다음 대상으로 전달 합니다.

| 대상 | 시나리오 |
|:---|:---|:---|
| [Log Analytics 작업 영역](resource-logs-collect-storage.md) | 다른 모니터링 데이터를 사용 하 여 로그를 분석 하 고 로그 쿼리 및 로그 경고와 같은 Azure Monitor 기능을 활용 합니다. |
| [Azure Storage](archive-diagnostic-logs.md) | 감사 또는 백업용 로그를 보관 합니다. |
| [이벤트 허브](resource-logs-stream-event-hubs.md) | 로그를 타사 로깅 및 원격 분석 시스템으로 스트리밍합니다.  |

## <a name="compute-resources"></a>Compute 리소스
리소스 로그는 Azure compute 리소스의 게스트 OS 수준 로그와 다릅니다. 계산 리소스에는 이벤트 로그, syslog 및 성능 카운터와 같은 데이터를 포함 하 여 게스트 OS에서 로그 및 메트릭을 수집 하는 에이전트가 필요 합니다. [진단 확장](agents-overview.md#azure-diagnostic-extension) 을 사용 하 여 azure 가상 머신에서 로그 데이터를 라우팅하고 [Log Analytics 에이전트](agents-overview.md#log-analytics-agent) 를 사용 하 여 azure의 가상 머신, 다른 클라우드 및 온-프레미스에서 Log Analytics 작업 영역으로 로그 및 메트릭을 수집 합니다. 자세한 내용은 [Azure Monitor의 모니터링 데이터 원본](data-sources.md) 을 참조 하세요.

## <a name="resource-logs-schema"></a>리소스 로그 스키마
각 Azure 서비스는 리소스 로그가 대상 중 하나에 기록 될 때 자체 스키마를 포함 하지만 모두 다음 테이블의 최상위 스키마를 공유 합니다. 각 서비스의 스키마에 대 한 링크는 아래 [서비스별 스키마](#service-specific-schemas) 를 참조 하세요. 

| name | 필수/선택 | 설명 |
|---|---|---|
| time | 필수 | 이벤트의 타임스탬프(UTC)입니다. |
| resourceId | 필수 | 이벤트를 내보낸 리소스의 리소스 ID입니다. 테넌트 서비스의 경우 /tenants/tenant-id/providers/provider-name의 형태입니다. |
| tenantId | 테넌트 로그에 필요 | 이 이벤트가 연결된 Active Directory 테넌트의 테넌트 ID입니다. 이 속성은 테넌트 수준 로그에만 사용되며 리소스 수준 로그에는 나타나지 않습니다. |
| operationName | 필수 | 이 이벤트가 나타내는 작업의 이름입니다. 이벤트가 RBAC 작업을 나타내는 경우, RBAC 작업 이름입니다(예: Microsoft.Storage/storageAccounts/blobServices/blobs/Read). 실제로 문서화된 리소스 관리자 작업은 아니지만, 일반적으로 리소스 관리자 작업 형태로 모델링됩니다(`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | 선택 사항 | operationName이 API를 사용하여 수행된 경우, 작업과 연결된 api-version입니다(예: `http://myservice.windowsazure.net/object?api-version=2016-06-01`). 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다. |
| category | 필수 | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. 일반적인 로그 범주는 “감사”, “작동”, “실행” 및 “요청”입니다. |
| resultType | 선택 사항 | 이벤트의 상태입니다. 일반적인 값으로 시작됨, 진행 중, 성공, 실패, 활성 및 확인됨이 있습니다. |
| resultSignature | 선택 사항 | 이벤트의 하위 상태입니다. 이 작업이 REST API 호출에 해당하는 경우, 해당 REST 호출의 HTTP 상태 코드입니다. |
| resultDescription | 선택 사항 | 이 작업에 대한 정적 텍스트 설명입니다(예: “스토리지 파일 가져오기”). |
| durationMS | 선택 사항 | 밀리초 단위의 작업 기간입니다. |
| callerIpAddress | 선택 사항 | 작업이 공개적으로 사용 가능한 IP 주소가 있는 엔터티에서 시작된 API 호출에 해당하는 경우, 호출자 IP 주소입니다. |
| correlationId | 선택 사항 | 관련 이벤트 집합을 그룹화하는 데 사용되는 GUID입니다. 일반적으로, 두 이벤트의 operationName이 같고 상태가 다른(예: “시작됨” 및 “성공”) 경우, 동일한 상관 관계 ID를 공유합니다. 이벤트 간의 다른 관계를 나타낼 수도 있습니다. |
| ID | 선택 사항 | 작업을 수행한 사용자 또는 애플리케이션의 ID를 설명하는 JSON Blob입니다. 일반적으로 활성 디렉터리의 클레임/JWT 토큰 및 권한 부여가 포함됩니다. |
| Level | 선택 사항 | 이벤트의 심각도 수준입니다. 정보, 경고, 오류 또는 위험 중 하나여야 합니다. |
| location | 선택 사항 | 이벤트를 내보내는 리소스의 지역입니다(예: “미국 동부” 또는 “프랑스 남부”). |
| properties | 선택 사항 | 이 특정 범주의 이벤트와 관련된 확장 속성입니다. 모든 사용자 지정/고유 속성은 스키마의 “파트 B”에 넣어야 합니다. |

## <a name="service-specific-schemas"></a>서비스별 스키마
리소스 진단 로그의 스키마는 `resourceId` 속성으로 정의 된 리소스 유형과 `category` 속성에 따라 달라 집니다. 다음 목록에서는 서비스 및 범주별 스키마 (사용 가능한 경우)에 대 한 링크를 사용 하 여 리소스 로그를 지 원하는 모든 Azure 서비스를 보여 줍니다.

| 서비스 | 스키마 및 문서 |
| --- | --- |
| Azure Active Directory | [개요](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [감사 로그 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) 및 [로그인 스키마](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API 관리 진단 로그](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway |[Application Gateway에 대한 진단 로깅](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation에 대한 Log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 진단 로깅](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL 진단 로그](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL 진단 로그](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Azure Cognitive Services에 대 한 진단 로깅](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [CDN에 대한 Azure 진단 로그](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 로깅](../../cosmos-db/logging.md) |
| Data Factory | [Azure Monitor를 사용하여 데이터 팩터리 모니터링](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Azure Data Lake Analytics에 대한 진단 로그에 액세스](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Azure Data Lake Store에 대한 진단 로그에 액세스](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs(영문) |[Azure Event Hubs 진단 로그](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express 경로 | 스키마를 사용할 수 없음 |
| Azure Firewall | 스키마를 사용할 수 없음 |
| IoT Hub | [IoT Hub 작업](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault 로깅](../../key-vault/key-vault-logging.md) |
| 부하 분산 장치 |[Azure Load Balancer에 대한 Log analytics](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B 사용자 지정 추적 스키마](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 네트워크 보안 그룹 |[NSG(네트워크 보안 그룹)에 대한 로그 분석](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Azure DDoS Protection 표준 관리](../../virtual-network/manage-ddos-protection.md) |
| Power BI 전용 | [Azure의 Power BI Embedded에 대 한 진단 로깅](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure Backup용 데이터 모델](../../backup/backup-azure-reports-data-model.md)|
| Search |[검색 트래픽 분석 설정 및 사용](../../search/search-traffic-analytics.md) |
| Service Bus |[Azure Service Bus 진단 로그](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database 진단 로깅](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[작업 진단 로그](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager 로그 스키마](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtual Network | 스키마를 사용할 수 없음 |
| Virtual Network 게이트웨이 | 스키마를 사용할 수 없음 |

## <a name="next-steps"></a>다음 단계

* Azure를 모니터링 하는 데 사용할 수 있는 [다른 azure platform 로그에 대해 자세히 알아보세요](platform-logs-overview.md) .
