---
title: Azure Database for MySQL에서 모니터링
description: 이 문서에서는 CPU, 스토리지 및 연결 통계를 포함하여 Azure Database for MySQL을 모니터링하고 경고하는 메트릭을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 0122f952e586d0535fc2e482c7b78266f8809272
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "67062445"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 모니터링
서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. Azure Database for MySQL은 서버의 동작에 대한 통찰력을 제공하는 다양한 메트릭을 제공합니다.

## <a name="metrics"></a>metrics
모든 Azure 메트릭의 빈도는 1분이고 각 메트릭은 30일의 기록을 제공합니다. 메트릭에 대한 경고를 구성할 수 있습니다. 단계별 지침은 [경고를 설정하는 방법](howto-alert-on-metric.md)을 참조하세요. 다른 작업에는 자동화된 작업 설정, 고급 분석 수행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
Azure Database for MySQL에서는 다음 메트릭을 사용할 수 있습니다.

|메트릭|메트릭 표시 이름|단위|Description|
|---|---|---|---|
|cpu_percent|CPU 백분율|Percent|사용 중인 CPU의 비율|
|memory_percent|메모리 백분율|Percent|사용 중인 메모리의 비율|
|io_consumption_percent|IO 백분율|Percent|사용 중인 IO의 비율|
|storage_percent|스토리지 비율|Percent|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|storage_used|사용된 스토리지|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|serverlog_storage_percent|서버 로그 스토리지 비율|Percent|서버의 최대 서버 로그 스토리지에서 사용된 서버 로그 스토리지의 백분율입니다.|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|서버 로그 스토리지에서 사용된 크기입니다.|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|이 서버에 대한 서버 로그 스토리지의 최대 크기입니다.|
|storage_limit|스토리지 제한|바이트|이 서버의 최대 스토리지|
|active_connections|활성 연결 수|개수|서버에 대한 활성 연결 수|
|connections_failed|연결 실패|개수|서버에 대해 실패한 연결 수|
|seconds_behind_master|복제 지연 시간(초)|개수|마스터 서버에 대해 복제본 서버가 지연되는 시간(초)입니다.|
|network_bytes_egress|네트워크 출력|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|network_bytes_ingress|네트워크 입력|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|
|backup_storage_used|사용된 백업 스토리지|바이트|사용된 백업 스토리지 양.|

## <a name="server-logs"></a>서버 로그
서버에서 느리게 쿼리 및 감사 로깅을 사용 하도록 설정할 수 있습니다. 이러한 로그는 Azure Monitor 로그, Event Hubs 및 저장소 계정의 Azure 진단 로그를 통해 사용할 수도 있습니다. 로깅에 대 한 자세한 내용은 [감사 로그](concepts-audit-logs.md) 및 [쿼리 로그 느림](concepts-server-logs.md) 문서를 참조 하세요.

## <a name="query-store"></a>쿼리 저장소
[쿼리 저장소](concepts-query-store.md)는 쿼리 런타임 통계 및 대기 이벤트를 포함하여 시간 경과에 따라 쿼리 성능을 추적하는 공개 미리 보기 기능입니다. 기능은 **mysql** 스키마에서 쿼리 런타임 성능 정보를 유지 합니다. 데이터 수집 및 스토리지은 다양한 구성 노브를 통해 제어할 수 있습니다.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md)는 쿼리 저장소와 함께 작동하여 Azure Portal에서 액세스할 수 있는 시각화를 제공합니다. 이러한 차트를 사용하면 성능에 영향을 주는 주요 쿼리를 식별할 수 있습니다. Query Performance Insight는 공개 미리 보기로 제공 되며 Azure Database for MySQL 서버 포털 페이지의 **지능형 성능** 섹션에서 액세스할 수 있습니다.

## <a name="performance-recommendations"></a>성능 권장 사항
[성능 권장 사항](concepts-performance-recommendations.md) 기능은 워크로드 성능을 향상시킬 수 있는 기회를 식별합니다. 성능 권장 사항의 공개 미리 보기 릴리스에서는 워크로드 성능을 향상시킬 수 있는 새 인덱스를 만드는 방법에 대한 권장 사항을 제공합니다. 이 기능은 인덱스 권장 사항을 생성하기 위해 쿼리 저장소에서 보고한 스키마와 워크로드를 포함하여 다양한 데이터베이스 특성을 고려하고 있습니다. 성능 권장 사항이 구현되면 고객이 성능을 테스트하여 변경에 따른 영향을 평가해야 합니다.

## <a name="next-steps"></a>다음 단계
- 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](howto-alert-on-metric.md)을 참조하세요.
- Azure Portal, REST API 또는 CLI를 사용하여 메트릭에 액세스하고 내보내는 방법에 대한 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.
- [서버 모니터링에 대한 모범 사례](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/)는 블로그를 참조하세요.
