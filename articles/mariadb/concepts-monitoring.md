---
title: Azure Database for MariaDB의 모니터링
description: 이 문서에서는 CPU, 스토리지, 연결 통계를 포함하여 Azure Database for MariaDB에 대해 모니터링 및 경고를 제공하기 위한 메트릭을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3c25798be8af26c1f5e5c1178395cd1688bb132
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382058"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 모니터링
서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. Azure Database for MariaDB는 서버의 동작에 대한 인사이트를 제공하는 다양한 메트릭을 제공합니다.

## <a name="metrics"></a>메트릭
모든 Azure 메트릭의 빈도는 1분이고 각 메트릭은 30일의 기록을 제공합니다. 메트릭에 대한 경고를 구성할 수 있습니다. 다른 작업에는 자동화된 작업 설정, 고급 분석 수행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.

단계별 지침은 [How to set up alerts](howto-alert-metric.md)(경고를 설정하는 방법)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
Azure Database for MariaDB에서는 다음 메트릭을 사용할 수 있습니다.

|메트릭|메트릭 표시 이름|단위|설명|
|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|사용 중인 CPU의 비율|
|memory_percent|메모리 백분율|백분율|사용 중인 메모리의 비율|
|io_consumption_percent|IO 백분율|백분율|사용 중인 IO의 비율|
|storage_percent|스토리지 비율|백분율|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|storage_used|사용된 스토리지|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|서버의 최대 서버 로그 스토리지에서 사용된 서버 로그 스토리지의 백분율입니다.|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|서버 로그 스토리지에서 사용된 크기입니다.|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|이 서버에 대한 서버 로그 스토리지의 최대 크기입니다.|
|storage_limit|스토리지 제한|바이트|이 서버의 최대 스토리지|
|active_connections|활성 연결|카운트|서버에 대한 활성 연결 수|
|connections_failed|실패한 연결|카운트|서버에 대해 실패한 연결 수|
|network_bytes_egress|네트워크 아웃|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|network_bytes_ingress|네트워크 인|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|

## <a name="server-logs"></a>서버 로그

서버에 느린 쿼리 로그를 사용할 수 있습니다. These logs are also available through Azure Diagnostic Logs in Azure Monitor logs, Event Hubs, and Storage Account. 로깅에 대한 자세한 내용은  [서버 로그](concepts-server-logs.md) 페이지를 참조하세요.

## <a name="query-store"></a>쿼리 저장소

[Query Store](concepts-query-store.md) keeps track of query performance over time including query runtime statistics and wait events. The feature persists query runtime performance information in the **mysql** schema. 데이터 수집 및 스토리지은 다양한 구성 노브를 통해 제어할 수 있습니다.

## <a name="query-performance-insight"></a>쿼리

[Query Performance Insight](concepts-query-performance-insight.md)는 쿼리 저장소와 함께 작동하여 Azure Portal에서 액세스할 수 있는 시각화를 제공합니다. 이러한 차트를 사용하면 성능에 영향을 주는 주요 쿼리를 식별할 수 있습니다. Query Performance Insight is accessible in the **Intelligent Performance** section of your Azure Database for MariaDB server's portal page.

## <a name="performance-recommendations"></a>성능 권장 사항

[성능 권장 사항](concepts-performance-recommendations.md) 기능은 워크로드 성능을 향상시킬 수 있는 기회를 식별합니다. Performance Recommendations provides you with recommendations for creating new indexes that have the potential to improve the performance of your workloads. 이 기능은 인덱스 권장 사항을 생성하기 위해 쿼리 저장소에서 보고한 스키마와 워크로드를 포함하여 다양한 데이터베이스 특성을 고려하고 있습니다. 성능 권장 사항이 구현되면 고객이 성능을 테스트하여 변경에 따른 영향을 평가해야 합니다.

## <a name="service-health"></a>서비스 상태
[Azure Service health](../service-health/overview.md) provides a view of all service health notifications in your subscription. You can set up Service Health alerts to notify you via your preferred communication channels when there are issues or changes that may affect the Azure services and regions you use.

You can view scheduled maintenance events for Azure Database for MariaDB by using the **planned maintenance** event type. To learn how to create **service health alerts**, visit the [Create activity log alerts on service notifications](../service-health/alerts-activity-log-service-notifications.md) article.

> [!IMPORTANT]
> The planned maintenance notifications is available in preview for EAST US and UK South only.

## <a name="next-steps"></a>다음 단계

- Azure Portal, REST API 또는 CLI를 사용하여 메트릭에 액세스하고 내보내는 방법에 대한 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.
  - 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](howto-alert-metric.md)을 참조하세요.
