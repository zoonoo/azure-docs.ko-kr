---
title: PostgreSQL-단일 서버용 Azure Database의 모니터링 및 튜닝
description: 이 문서에서는 PostgreSQL-단일 서버용 Azure Database의 모니터링 및 튜닝 기능을 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: c69ffb30a37de8e6dc3e15aa1f7dcd6a9311d614
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274293"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL의 모니터링 및 튜닝 - Single Server
서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. Azure Database for PostgreSQL은 서버 동작에 대한 인사이트를 제공하기 위해 다양한 모니터링 옵션을 제공합니다.

## <a name="metrics"></a>메트릭
Azure Database for PostgreSQL은 PostgreSQL 서버를 지원하는 리소스의 동작에 대한 통찰력을 제공하는 다양한 메트릭을 제공합니다. 각 메트릭은 1분 빈도로 내보내지며, 최대 30일 동안 기록됩니다. 메트릭에 대한 경고를 구성할 수 있습니다. 단계별 지침은 [경고를 설정하는 방법](howto-alert-on-metric.md)을 참조하세요. 다른 작업에는 자동화된 작업 설정, 고급 분석 수행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
Azure Database for PostgreSQL에서는 다음 메트릭을 사용할 수 있습니다.

|메트릭|메트릭 표시 이름|단위|설명|
|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|사용 중인 CPU의 비율|
|memory_percent|메모리 백분율|백분율|사용 중인 메모리의 비율|
|io_consumption_percent|IO 백분율|백분율|사용 중인 IO의 비율|
|storage_percent|저장소 비율|백분율|서버의 최대값을 초과하여 사용된 저장소의 비율|
|storage_used|저장소 사용됨|바이트|사용 중인 저장소의 양 서비스에서 사용되는 저장소에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|storage_limit|저장소 제한|바이트|이 서버의 최대 저장소|
|serverlog_storage_percent|서버 로그 저장소 비율|백분율|서버의 최대 서버 로그 저장소에서 사용된 서버 로그 저장소의 백분율입니다.|
|serverlog_storage_percent|사용된 서버 로그 저장소|바이트|서버 로그 저장소에서 사용된 크기입니다.|
|serverlog_storage_limit|서버 로그 저장소 제한|바이트|이 서버에 대한 서버 로그 저장소의 최대 크기입니다.|
|active_connections|활성 연결 수|카운트|서버에 대한 활성 연결 수|
|connections_failed|실패한 연결|카운트|서버에 대해 실패한 연결 수|
|network_bytes_egress|네트워크 아웃|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|network_bytes_ingress|네트워크 인|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|
|backup_storage_used|사용된 백업 스토리지|바이트|사용된 백업 스토리지 양.|
|pg_replica_log_delay_in_bytes|복제본 간 최대 지연 시간|바이트|마스터와 대부분 지연 복제본 간에 바이트를 지연 합니다. 이 메트릭은 마스터 서버에서만 사용할 수 있습니다.|
|pg_replica_log_delay_in_seconds|복제본 지연 시간|초|마지막 트랜잭션 재생 이후 시간입니다. 이 메트릭은 복제본 서버에만 제공 됩니다.|

## <a name="server-logs"></a>서버 로그
서버에서 로그를 사용하도록 설정할 수 있습니다. 이러한 로그는 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md), Event Hubs 및 저장소 계정에서 Azure 진단 로그를 통해 사용할 수도 있습니다. 로깅에 대한 자세한 내용은 [서버 로그](concepts-server-logs.md) 페이지를 참조하세요.

## <a name="query-store"></a>쿼리 저장소
[쿼리 저장소](concepts-query-store.md) 시간 포함 하 여 성능 쿼리 런타임 통계 및 이벤트를 대기 하는 쿼리는 추적 합니다. 이 기능은 query_store 스키마 아래의 **azure_sys**라는 시스템 데이터베이스에서 쿼리 런타임 성능 정보를 유지합니다. 데이터 수집 및 저장은 다양한 구성 노브를 통해 제어할 수 있습니다.

## <a name="query-performance-insight"></a>쿼리
[Query Performance Insight](concepts-query-performance-insight.md)는 쿼리 저장소와 함께 작동하여 Azure Portal에서 액세스할 수 있는 시각화를 제공합니다. 이러한 차트를 사용하면 성능에 영향을 주는 주요 쿼리를 식별할 수 있습니다. 액세스할 수 있는 성능 Insightis를 쿼리 합니다 **지원 + 문제 해결** Azure Database for PostgreSQL 서버의 포털 페이지의 섹션입니다.

## <a name="performance-recommendations"></a>성능 권장 사항
[성능 권장 사항](concepts-performance-recommendations.md) 기능은 워크로드 성능을 향상시킬 수 있는 기회를 식별합니다. 성능 권장 사항 워크 로드의 성능을 향상 시킬 가능성이 있는 새 인덱스를 만들기 위한 권장 사항을 제공 합니다. 이 기능은 인덱스 권장 사항을 생성하기 위해 쿼리 저장소에서 보고한 스키마와 워크로드를 포함하여 다양한 데이터베이스 특성을 고려하고 있습니다. 성능 권장 사항이 구현되면 고객이 성능을 테스트하여 변경에 따른 영향을 평가해야 합니다. 

## <a name="next-steps"></a>다음 단계
- 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](howto-alert-on-metric.md)을 참조하세요.
- Azure Portal, REST API 또는 CLI를 사용하여 메트릭에 액세스하고 내보내는 방법에 대한 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.
- [서버 모니터링에 대한 모범 사례](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)는 블로그를 참조하세요.
