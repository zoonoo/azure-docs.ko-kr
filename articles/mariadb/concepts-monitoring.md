---
title: 모니터링 - MariaDB용 Azure 데이터베이스
description: 이 문서에서는 CPU, 스토리지, 연결 통계를 포함하여 Azure Database for MariaDB에 대해 모니터링 및 경고를 제공하기 위한 메트릭을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20a2066dc0228fe9c2fee09387d96bf6aafeb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531977"
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
|storage_used|스토리지 사용됨|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|서버의 최대 서버 로그 스토리지에서 사용된 서버 로그 스토리지의 백분율입니다.|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|서버 로그 스토리지에서 사용된 크기입니다.|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|이 서버에 대한 서버 로그 스토리지의 최대 크기입니다.|
|storage_limit|스토리지 제한|바이트|이 서버의 최대 스토리지|
|active_connections|활성 연결 수|개수|서버에 대한 활성 연결 수|
|connections_failed|실패한 연결|개수|서버에 대해 실패한 연결 수|
|network_bytes_egress|네트워크 아웃|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|network_bytes_ingress|네트워크 인|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|

## <a name="server-logs"></a>서버 로그

서버에 느린 쿼리 로그를 사용할 수 있습니다. 이러한 로그는 Azure 모니터 로그, 이벤트 허브 및 저장소 계정의 Azure 진단 로그를 통해서도 사용할 수 있습니다. 로깅에 대한 자세한 내용은  [서버 로그](concepts-server-logs.md) 페이지를 참조하세요.

## <a name="query-store"></a>쿼리 저장소

[쿼리 저장소는](concepts-query-store.md) 쿼리 런타임 통계 및 대기 이벤트를 포함하여 시간에 따라 쿼리 성능을 추적합니다. 이 기능은 **mysql** 스키마에서 쿼리 런타임 성능 정보를 유지합니다. 데이터 수집 및 스토리지은 다양한 구성 노브를 통해 제어할 수 있습니다.

## <a name="query-performance-insight"></a>쿼리

[Query Performance Insight](concepts-query-performance-insight.md)는 쿼리 저장소와 함께 작동하여 Azure Portal에서 액세스할 수 있는 시각화를 제공합니다. 이러한 차트를 사용하면 성능에 영향을 주는 주요 쿼리를 식별할 수 있습니다. 쿼리 성능 인사이트는 MariaDB 서버의 포털 페이지에 대한 Azure 데이터베이스의 **지능형 성능** 섹션에서 액세스할 수 있습니다.

## <a name="performance-recommendations"></a>성능 권장 사항

[성능 권장 사항](concepts-performance-recommendations.md) 기능은 워크로드 성능을 향상시킬 수 있는 기회를 식별합니다. 성능 권장 사항은 워크로드의 성능을 향상시킬 수 있는 새 인덱스를 만들기 위한 권장 사항을 제공합니다. 이 기능은 인덱스 권장 사항을 생성하기 위해 쿼리 저장소에서 보고한 스키마와 워크로드를 포함하여 다양한 데이터베이스 특성을 고려하고 있습니다. 성능 권장 사항이 구현되면 고객이 성능을 테스트하여 변경에 따른 영향을 평가해야 합니다.

## <a name="planned-maintenance-notification"></a>계획된 유지 관리 알림

**계획된 유지 관리 알림을** 사용하면 MariaDB에 대한 Azure 데이터베이스에 예정된 계획된 유지 관리에 대한 경고를 받을 수 있습니다. 이러한 알림은 [Service Health의](../service-health/overview.md) 계획된 유지 관리와 통합되며 구독에 대한 예약된 모든 유지 관리를 한 곳에서 볼 수 있습니다. 또한 서로 다른 리소스를 담당하는 연락처가 다를 수 있으므로 다른 리소스 그룹에 대해 적절한 대상그룹에 대한 알림을 확장하는 데도 도움이 됩니다. 이벤트 72시간 전에 예정된 유지 관리에 대한 알림을 받게 됩니다.

> [!Note]
> 우리는 모든 이벤트에 대한 **계획 유지 보수 알림을** 72 시간 통지를 제공하기 위해 모든 시도를 할 것입니다. 그러나 중요 하거나 보안 패치의 경우 알림 이벤트에 가까운 전송 되거나 생략 될 수 있습니다.

### <a name="to-receive-planned-maintenance-notification"></a>계획된 유지 관리 알림을 받으려면

1. [포털](https://portal.azure.com)에서 **서비스 상태**를 선택합니다.
2. **경고** 섹션에서 **상태 경고**를 선택합니다.
3. **+ 서비스 상태 경고를 추가하고** 필드를 채웁니다.
4. 필요한 필드를 채웁니다. 
5. 이벤트 **유형을**선택하거나 **계획된 유지 관리를** 선택하거나 모두 **선택합니다.**
6. **작업 그룹에서** 경고를 수신하는 방법을 정의합니다(이메일 받기, 논리 앱 트리거 등)  
7. 생성 시 규칙 활성화가 예로 설정되어 있는지 확인합니다.
8. **경고 규칙 만들기를** 선택하여 경고를 완료합니다.

서비스 상태 경고를 만드는 방법에 대한 자세한 **단계는** [서비스 알림에서 활동 로그 경고 만들기를](../service-health/alerts-activity-log-service-notifications.md)참조하십시오.

> [!IMPORTANT]
> 계획된 유지 관리 알림이 현재 미리 보기 상태입니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal, REST API 또는 CLI를 사용하여 메트릭에 액세스하고 내보내는 방법에 대한 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.
  - 메트릭에 대한 경고를 만드는 방법에 대한 지침은 [경고를 설정하는 방법을](howto-alert-metric.md) 참조하세요.
