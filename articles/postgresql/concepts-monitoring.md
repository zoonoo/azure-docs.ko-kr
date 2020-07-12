---
title: 모니터링 및 튜닝 - Azure Database for PostgreSQL - 단일 서버
description: 이 문서에서는 Azure Database for PostgreSQL 단일 서버의 모니터링 및 튜닝 기능을 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: fc41b76fa14d464b2e4ddcca7e98997011a51cd4
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276637"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL의 모니터링 및 튜닝 - Single Server
서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. Azure Database for PostgreSQL은 서버 동작에 대한 인사이트를 제공하기 위해 다양한 모니터링 옵션을 제공합니다.

## <a name="metrics"></a>메트릭
Azure Database for PostgreSQL은 PostgreSQL 서버를 지원하는 리소스의 동작에 대한 통찰력을 제공하는 다양한 메트릭을 제공합니다. 각 메트릭은 1 분 주기로 내보내지고 최대 [93 일의 기록이](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#retention-of-metrics)있습니다. 메트릭에 대한 경고를 구성할 수 있습니다. 단계별 지침은 [How to set up alerts](howto-alert-on-metric.md)(경고를 설정하는 방법)를 참조하세요. 다른 작업에는 자동화된 작업 설정, 고급 분석 수행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
Azure Database for PostgreSQL에서는 다음 메트릭을 사용할 수 있습니다.

|메트릭|메트릭 표시 이름|단위|설명|
|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|사용 중인 CPU의 비율|
|memory_percent|메모리 백분율|백분율|사용 중인 메모리의 비율|
|io_consumption_percent|IO 백분율|백분율|사용 중인 IO의 비율 기본 계층 서버에는 적용 되지 않습니다.|
|storage_percent|스토리지 비율|백분율|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|storage_used|스토리지 사용됨|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|storage_limit|스토리지 제한|바이트|이 서버의 최대 스토리지|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|서버의 최대 서버 로그 스토리지에서 사용된 서버 로그 스토리지의 백분율입니다.|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|서버 로그 스토리지에서 사용된 크기입니다.|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|이 서버에 대한 서버 로그 스토리지의 최대 크기입니다.|
|active_connections|활성 연결 수|개수|서버에 대한 활성 연결 수|
|connections_failed|실패한 연결|개수|실패 한 설정 된 연결 수입니다.|
|network_bytes_egress|네트워크 아웃|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|network_bytes_ingress|네트워크 인|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|
|backup_storage_used|사용된 백업 스토리지|바이트|사용된 백업 스토리지 양.|
|pg_replica_log_delay_in_bytes|복제본 간 최대 지연 시간|바이트|마스터와 가장 지연 복제본 사이의 지연 (바이트)입니다. 이 메트릭은 마스터 서버에서만 사용할 수 있습니다.|
|pg_replica_log_delay_in_seconds|복제본 지연 시간|초|마지막으로 재생 된 트랜잭션 이후 경과 된 시간입니다. 이 메트릭은 복제본 서버에만 사용할 수 있습니다.|

## <a name="server-logs"></a>서버 로그
서버에서 로그를 사용하도록 설정할 수 있습니다. 이러한 리소스 로그는 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md), Event Hubs 및 저장소 계정으로 보낼 수 있습니다. 로깅에 대한 자세한 내용은 [서버 로그](concepts-server-logs.md) 페이지를 참조하세요.

## <a name="query-store"></a>쿼리 저장소
[쿼리 저장소](concepts-query-store.md) 쿼리 런타임 통계 및 대기 이벤트를 포함 하 여 시간에 따른 쿼리 성능을 추적 합니다. 이 기능은 query_store 스키마 아래의 **azure_sys**라는 시스템 데이터베이스에서 쿼리 런타임 성능 정보를 유지합니다. 데이터 수집 및 스토리지은 다양한 구성 노브를 통해 제어할 수 있습니다.

## <a name="query-performance-insight"></a>쿼리
[Query Performance Insight](concepts-query-performance-insight.md)는 쿼리 저장소와 함께 작동하여 Azure Portal에서 액세스할 수 있는 시각화를 제공합니다. 이러한 차트를 사용하면 성능에 영향을 주는 주요 쿼리를 식별할 수 있습니다. Query Performance Insight은 Azure Database for PostgreSQL 서버 포털 페이지의 **지원 + 문제 해결** 섹션에서 액세스할 수 있습니다.

## <a name="performance-recommendations"></a>성능 권장 사항
[성능 권장 사항](concepts-performance-recommendations.md) 기능은 워크로드 성능을 향상시킬 수 있는 기회를 식별합니다. 성능 권장 사항은 워크 로드의 성능을 향상 시킬 수 있는 새 인덱스를 만들기 위한 권장 사항을 제공 합니다. 이 기능은 인덱스 권장 사항을 생성하기 위해 쿼리 저장소에서 보고한 스키마와 워크로드를 포함하여 다양한 데이터베이스 특성을 고려하고 있습니다. 성능 권장 사항이 구현되면 고객이 성능을 테스트하여 변경에 따른 영향을 평가해야 합니다. 

## <a name="planned-maintenance-notification"></a>계획 된 유지 관리 알림

**계획 된 유지 관리 알림을** 사용 하면 Azure Database for PostgreSQL 단일 서버에 예정 된 예정 된 유지 관리에 대 한 경고를 받을 수 있습니다. 이러한 알림은 [Service Health의](../service-health/overview.md) 계획 된 유지 관리와 통합 되어 있으므로 구독에 대해 예약 된 유지 관리 작업을 한 곳에서 모두 볼 수 있습니다. 또한 다양 한 리소스를 담당 하는 여러 연락처가 있을 수 있으므로 다양 한 리소스 그룹에 대 한 올바른 대상으로 알림을 확장 하는 데 도움이 됩니다. 이벤트 전에 예정 된 유지 관리 72 시간에 대 한 알림을 받게 됩니다.

> [!Note]
> 모든 이벤트에 대해 **계획 된 유지 관리 알림** 72 시간 통지를 제공 하려고 합니다. 그러나 중요 또는 보안 패치의 경우 알림이 이벤트에 더 가까이 전송 되거나 생략 될 수 있습니다.

### <a name="to-receive-planned-maintenance-notification"></a>계획 된 유지 관리 알림을 받으려면

1. [포털](https://portal.azure.com)에서 **서비스 상태**를 선택합니다.
2. **경고** 섹션에서 **상태 경고**를 선택합니다.
3. **+ 서비스 상태 경고 추가** 를 선택 하 고 필드를 입력 합니다.
4. 필수 필드를 입력 합니다. 
5. **이벤트 유형을**선택 하 고 **계획 된 유지 관리** 를 선택 하거나 **모두를 선택** 합니다.
6. **작업 그룹** 에서 경고를 수신 하는 방법 (전자 메일 가져오기, 논리 앱 트리거 등)을 정의 합니다.  
7. 규칙을 만들 때 규칙 사용이 예로 설정 되어 있는지 확인 합니다.
8. 경고 **규칙 만들기** 를 선택 하 여 경고를 완료 합니다.

**서비스 상태 경고**를 만드는 방법에 대 한 자세한 단계는 [서비스 알림에서 활동 로그 경고 만들기](../service-health/alerts-activity-log-service-notifications.md)를 참조 하세요.

> [!IMPORTANT]
> 계획 된 유지 관리 알림은 현재 미국 서 부를 **제외한** 모든 지역에서 미리 보기로 제공 됩니다.

## <a name="next-steps"></a>다음 단계
- 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](howto-alert-on-metric.md)을 참조하세요.
- Azure Portal, REST API 또는 CLI를 사용하여 메트릭에 액세스하고 내보내는 방법에 대한 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.
- [서버 모니터링에 대한 모범 사례](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)는 블로그를 참조하세요.
