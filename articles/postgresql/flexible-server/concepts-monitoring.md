---
title: 모니터링 및 메트릭-Azure Database for PostgreSQL 유연한 서버
description: 이 문서에서는 Azure Database for PostgreSQL 유연한 서버에서 모니터링 및 메트릭 기능을 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b0957219308dfaab2d375fb7c23926a13c745344
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314677"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 유연한 서버에서 메트릭 모니터링

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. Azure Database for PostgreSQL은 서버 동작에 대한 인사이트를 제공하기 위해 다양한 모니터링 옵션을 제공합니다.

## <a name="metrics"></a>메트릭
Azure Database for PostgreSQL은 PostgreSQL 서버를 지원하는 리소스의 동작에 대한 통찰력을 제공하는 다양한 메트릭을 제공합니다. 각 메트릭은 1 분 주기로 내보내지고 최대 [93 일의 기록이](../../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics)있습니다. 메트릭에 대한 경고를 구성할 수 있습니다. 기타 옵션으로는 자동화 된 작업 설정, 고급 분석 수행 및 기록 보관이 있습니다. 자세한 내용은 [Azure 메트릭 개요](../../azure-monitor/platform/data-platform-metrics.md)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
PostgreSQL 유연한 서버에 사용할 수 있는 메트릭은 다음과 같습니다.


|메트릭|메트릭 표시 이름|단위|Description|
|---|---|---|---|
| active_connections | 활성 연결 수 | 개수 | 서버에 대 한 연결 수입니다. | 
| backup_storage_used | 사용된 백업 스토리지 | 바이트 | 사용 된 백업 저장소의 양입니다. 이 메트릭은 서버에 대해 설정 된 백업 보존 기간에 따라 유지 되는 모든 전체 데이터베이스 백업, 차등 백업 및 로그 백업에서 사용 하는 저장소의 합계를 나타냅니다. 백업 빈도는 서비스에서 관리 됩니다. 지역 중복 저장소의 경우 백업 저장소 사용량이 로컬 중복 저장소의 두 배가 됩니다. |
| connections_failed | 실패한 연결 | 개수 | 실패 한 연결입니다. |
| connections_succeeded | 성공한 연결 | 개수 | 성공한 연결입니다. |
| cpu_credits_consumed | 사용된 CPU 크레딧 | 개수 | 유연한 서버에서 사용 하는 크레딧 수입니다. 삼 계층의 안정화 계층에 적용할 수 있습니다. |
| cpu_credits_remaining | 남은 CPU 크레딧 | 개수 | 버스트에 사용할 수 있는 크레딧 수입니다. 삼 계층의 안정화 계층에 적용할 수 있습니다. |
| cpu_percent | CPU 백분율 | 백분율 | 사용 중인 CPU의 백분율입니다. | 
| disk_queue_depth | 디스크 큐 크기 | 개수 | 데이터 디스크에 대 한 미해결 i/o 작업 수입니다. |
| IOPS | IOPS | 개수 | 초당 디스크에 대 한 i/o 작업 수입니다. |
| maximum_used_transactionIDs | 사용 되는 최대 트랜잭션 Id | 개수 | 사용 중인 최대 트랜잭션 ID입니다. |
| memory_percent | 메모리 백분율 | 백분율 | 사용 중인 메모리의 비율입니다. |
| network_bytes_egress | 네트워크 아웃 | 바이트 | 나가는 네트워크 트래픽 양입니다. |
| network_bytes_ingress | 네트워크 인 | 바이트 | 들어오는 네트워크 트래픽 양입니다. |
| read_iops | 읽기 IOPS | 개수 | 초당 데이터 디스크 i/o 읽기 작업 수입니다. |
| read_throughput | 읽기 처리량 | 바이트 | 디스크에서 초당 읽은 바이트 수입니다. |
| storage_free | 저장소 여유 공간 | 바이트 | 사용 가능한 저장소 공간의 양입니다. |
| storage_percent | 스토리지 비율 | 백분율 | 사용 된 저장소 공간의 비율입니다. 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
| storage_used | 저장소 사용 됨 | 바이트 | 사용 된 저장소 공간의 비율입니다. 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다. |
| txlogs_storage_used | 사용 되는 트랜잭션 로그 저장소 | 바이트 | 트랜잭션 로그에 사용 되는 저장소 공간의 양입니다. | 
| write_throughput | 쓰기 처리량 | 바이트 | 디스크에 초당 쓴 바이트 수입니다. |
| write_iops | 쓰기 IOPS | 개수 | 초당 데이터 디스크 i/o 쓰기 작업 수입니다. |

## <a name="server-logs"></a>서버 로그
Azure Database for PostgreSQL를 사용 하 여 Postgres의 표준 로그를 구성 하 고 액세스할 수 있습니다. 로그에 대 한 자세한 내용은 [로깅 개념 문서](concepts-logging.md)를 참조 하세요.
