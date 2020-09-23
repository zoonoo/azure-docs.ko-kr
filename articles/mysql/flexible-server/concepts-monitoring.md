---
title: 모니터링-Azure Database for MySQL-유연한 서버
description: 이 문서에서는 CPU, 저장소 및 연결 통계를 포함 하 여 Azure Database for MySQL 유연한 서버를 모니터링 하 고 경고 하는 메트릭을 설명 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: f3a58a6766c42385dd6611c2014ba36fd1078710
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939293"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>기본 제공 메트릭을 사용 하 여 유연한 서버 Azure Database for MySQL 모니터링

> [!IMPORTANT] 
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Database for MySQL 유연한 서버는 Azure Monitor를 통해 서버 모니터링을 제공 합니다. 메트릭은 특정 시간에 서버 리소스의 일부 측면을 설명 하는 숫자 값입니다. 서버 리소스를 모니터링 하면 사용자에 게 가장 중요 한 것을 모니터링할 수 있도록 하 여 워크 로드 문제를 해결 하 고 최적화 하는 데 도움이 됩니다. 올바른 메트릭을 모니터링 하면 서버 및 응용 프로그램의 성능, 안정성 및 가용성을 유지 하는 데 도움이 됩니다.

이 문서에서는 서버의 동작에 대 한 통찰력을 제공 하는 유연한 서버에 사용할 수 있는 다양 한 메트릭에 대해 알아봅니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

유연한 서버는 다양 한 메트릭을 제공 하 여 워크 로드의 성능을 이해 하 고이 데이터를 기반으로 하 여 서버 및 응용 프로그램에 미치는 영향을 이해할 수 있습니다. Azure Database for MySQL 예를 들어 유연한 서버에서 **호스트 CPU 백분율**, **활성 연결**, **IO 백분율**및 **호스트 메모리 비율** 을 모니터링 하 여 성능에 영향을 줄 수 있는 경우를 식별할 수 있습니다. 여기에서 작업을 최적화 하거나, 계산 계층을 변경 하 여 수직으로 확장 하거나, 읽기 복제본을 사용 하 여 수평으로 크기를 조정 해야 할 수 있습니다.

모든 Azure 메트릭의 빈도는 1분이고 각 메트릭은 30일의 기록을 제공합니다. 메트릭에 대한 경고를 구성할 수 있습니다. 단계별 지침은 [경고를 설정 하는 방법](./how-to-alert-on-metric.md)을 참조 하세요. 다른 작업에는 자동화된 작업 설정, 고급 분석 수행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
Azure Database for MySQL에서는 다음 메트릭을 사용할 수 있습니다.

|메트릭 표시 이름|메트릭|단위|설명|
|---|---|---|---|
|호스트 CPU 백분율|cpu_percent|백분율|고객 작업 및 Azure MySQL 프로세스의 CPU 사용률을 포함 하 여 서버의 CPU 사용률 비율|
|호스트 네트워크 |network_bytes_ingress|바이트|고객 데이터베이스의 트래픽과 복제, 모니터링, 로그 등과 같은 Azure MySQL 기능을 포함 하 여 서버에서 들어오는 네트워크 트래픽|
|호스트 네트워크 출력|network_bytes_egress|바이트|고객 데이터베이스의 트래픽과 복제, 모니터링, 로그 등과 같은 Azure MySQL 기능을 비롯 한 서버의 나가는 네트워크 트래픽|
|복제 지연|replication_lag|초|마지막으로 재생 된 트랜잭션 이후 경과 된 시간입니다. 이 메트릭은 복제본 서버에만 사용할 수 있습니다.|
|활성 연결 수|active_connection|개수|서버에 대한 활성 연결 수|
|사용된 백업 스토리지|backup_storage_used|바이트|사용된 백업 스토리지 양.|
|IO 백분율|io_consumption_percent|백분율|사용 중인 IO의 비율|
|호스트 메모리 백분율|memory_percent|백분율|서버에서 사용 중인 메모리의 백분율 (고객 작업 및 Azure MySQL 프로세스의 메모리 사용률 포함)|
|저장소 제한|storage_limit|바이트|이 서버의 최대 스토리지|
|저장소 백분율|storage_percent|백분율|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|저장소 사용 됨|storage_used|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|총 연결|total_connections|개수|서버에 대 한 총 연결 수|
|중단된 연결|aborted_connections|개수|잘못 된 자격 증명으로 인해 연결 실패와 같은 MySQL에 대 한 연결 실패 횟수입니다.|
|쿼리|쿼리|개수|초당 쿼리 수입니다.|

## <a name="next-steps"></a>다음 단계
- 메트릭에 대 한 경고를 만드는 방법에 대 한 지침은 [경고를 설정 하는 방법을](./how-to-alert-on-metric.md) 참조 하세요.
- 성능 향상을 위해 [IOPS 크기 조정](./concepts/../concepts-compute-storage.md#iops) 에 대해 자세히 알아보세요.
