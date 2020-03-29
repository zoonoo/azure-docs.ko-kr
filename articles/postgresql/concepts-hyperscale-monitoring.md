---
title: 모니터링 및 조정 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: 이 문서에서는 PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스의 기능 모니터링 및 튜닝에 대해 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975519"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스 모니터링 및 조정

서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. 하이퍼스케일(Citus)은 서버 그룹의 노드 동작에 대한 통찰력을 제공하기 위해 다양한 모니터링 옵션을 제공합니다.

## <a name="metrics"></a>메트릭

하이퍼스케일(Citus)은 서버 그룹의 각 노드에 대한 메트릭을 제공합니다. 메트릭은 지원 리소스의 동작에 대한 통찰력을 제공합니다. 각 메트릭은 1분 빈도로 내보내지며, 최대 30일 동안 기록됩니다.

메트릭의 그래프를 보는 것 외에도 경고를 구성할 수 있습니다. 단계별 지침은 [How to set up alerts](howto-hyperscale-alert-on-metric.md)(경고를 설정하는 방법)를 참조하세요.  다른 작업에는 자동화된 작업 설정, 고급 분석 실행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록

이러한 메트릭은 하이퍼스케일(Citus) 노드에서 사용할 수 있습니다.

|메트릭|메트릭 표시 이름|단위|설명|
|---|---|---|---|
|active_connections|활성 연결 수|개수|서버에 대한 활성 연결 수|
|cpu_percent|CPU 백분율|백분율|사용 중인 CPU의 비율|
|Iops|IOPS|개수|[IOPS 정의](../virtual-machines/linux/premium-storage-performance.md#iops) 및 [하이퍼스케일 처리량](concepts-hyperscale-configuration-options.md) 보기|
|memory_percent|메모리 백분율|백분율|사용 중인 메모리의 비율|
|network_bytes_ingress|네트워크 인|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|
|network_bytes_egress|네트워크 아웃|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|storage_percent|스토리지 비율|백분율|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|storage_used|스토리지 사용됨|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|

Azure는 클러스터에 대한 집계 메트릭을 전체적으로 제공하지 않지만 여러 노드에 대한 메트릭을 동일한 그래프에 배치할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 메트릭에 대한 경고 생성에 대한 지침은 [경고를 설정하는 방법](howto-hyperscale-alert-on-metric.md)을 참조하세요.
