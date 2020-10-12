---
title: 제한 Azure Database for PostgreSQL-유연한 서버
description: 이 문서에서는 연결 수 및 저장소 엔진 옵션과 같은 Azure Database for PostgreSQL 유연한 서버의 제한을 설명 합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30c2da4ac750375c66b92cdca552e1a51a8dbc40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940397"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 유연한 서버 제한

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

다음 섹션에서는 데이터베이스 서비스의 용량 및 기능 제한에 대해 설명합니다. 리소스 (계산, 메모리, 저장소) 계층에 대해 알아보려면 [계산 및 저장소](concepts-compute-storage.md) 문서를 참조 하세요.

## <a name="maximum-connections"></a>최대 연결 수

가격 책정 계층 및 vCores 당 최대 연결 수는 다음과 같습니다. Azure Database for PostgreSQL 유연한 서버를 모니터링 하려면 Azure 시스템에 세 개의 연결이 필요 합니다.

| SKU 이름             | vCore 수 | 메모리 크기 | 최대 연결 수 | 최대 사용자 연결 |
|----------------------|--------|-------------|-----------------|----------------------|
| **버스터 블**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **범용**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192GiB     | 5,000            | 4997                 |
| D64s_v3              | 64     | 256GiB     | 5,000            | 4997                 |
| **메모리 액세스에 최적화** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64GiB      | 5,000            | 4997                 |
| E16s_v3              | 16     | 128GiB     | 5,000            | 4997                 |
| E32s_v3              | 32     | 256GiB     | 5,000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5,000            | 4997                 |
| E64s_v3              | 64     | 432GiB     | 5,000            | 4997                 |

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류: 너무 많은 클라이언트가 이미 연결되어 있습니다.

> [!IMPORTANT]
> 최상의 환경을 위해 PgBouncer와 같은 연결 풀을 사용 하 여 효율적으로 연결을 관리 하는 것이 좋습니다.

유휴 상태 에서도 PostgreSQL 연결은 약 10mb의 메모리를 차지할 수 있습니다. 또한 새 연결을 만드는 데 시간이 걸립니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 연결 풀링을 사용 하 여 유휴 연결을 줄이고 기존 연결을 다시 사용할 수 있습니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)을 참조 하세요.

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="scale-operations"></a>크기 조정 작업

- 서버 저장소 크기를 조정 하려면 서버를 다시 시작 해야 합니다.
- 서버 저장소는 2x 증분 까지만 확장할 수 있습니다. 자세한 내용은 [Compute 및 storage](concepts-compute-storage.md) 를 참조 하세요.
- 서버 스토리지 크기를 줄이는 것은 현재 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드

- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다. 다음의 주 버전으로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 주 버전을 [덤프 및 복원](../howto-migrate-using-dump-and-restore.md)합니다.

### <a name="networking"></a>네트워킹

- 현재 VNET의 이동 및 외부로의 이동은 지원 되지 않습니다.
- VNET 내의 배포와 공용 액세스를 조합 하는 것은 현재 지원 되지 않습니다.
- VNET에서는 방화벽 규칙이 지원 되지 않습니다. 대신 네트워크 보안 그룹을 사용할 수 있습니다.
- 공용 액세스 데이터베이스 서버는와 같은 공용 인터넷에 연결할 수 `postgres_fdw` 있으며,이 액세스는 제한할 수 없습니다. VNET 기반 서버는 네트워크 보안 그룹을 사용 하 여 제한 된 아웃 바운드 액세스를 가질 수 있습니다.

### <a name="high-availability"></a>고가용성

- Zone-Redundant HA는 현재 간에 안정화 된 서버에 대해 지원 되지 않습니다.
- 서버에서 HA 대기로 장애 조치 (failover) 될 때 데이터베이스 서버 IP 주소가 변경 됩니다. 서버 IP 주소 대신 DNS 레코드를 사용 해야 합니다.

### <a name="availability-zones"></a>가용성 영역

- 서버를 다른 가용성 영역으로 수동으로 이동 하는 것은 현재 지원 되지 않습니다.
- HA 대기 서버의 가용성 영역을 수동으로 구성할 수 없습니다.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres 엔진, 확장 및 PgBouncer

- Postgres 10 및 이전 버전은 지원 되지 않습니다. 이전 Postgres 버전이 필요한 경우에는 [단일 서버](../overview-single-server.md) 옵션을 사용 하는 것이 좋습니다.
- 확장 지원은 현재 Postgres 확장으로 제한 됩니다 `contrib` .
- 기본 제공 PgBouncer 연결 풀은 현재 VNET 내의 데이터베이스 서버 또는 간에 안정화 된 서버에서 사용할 수 없습니다.

### <a name="stopstart-operation"></a>작업 중지/시작

- 7 일 넘게 서버를 중지할 수 없습니다.

### <a name="scheduled-maintenance"></a>예약된 유지 관리

- 이미 계획 된 업그레이드 보다 5 일 이내에 유지 관리 기간을 변경 하면 해당 업그레이드에 영향을 주지 않습니다. 변경 내용은 예약 된 다음 유지 관리에만 적용 됩니다.

### <a name="backing-up-a-server"></a>서버 백업

- 백업은 시스템에서 관리 하므로 현재 이러한 백업을 수동으로 실행 하는 방법은 없습니다. 대신를 사용 하는 것이 좋습니다 `pg_dump` .
- 백업은 항상 스냅숏 기반 전체 백업 (차등 백업 아님) 이므로 백업 저장소 사용률을 높일 수 있습니다. 트랜잭션 로그 (미리 쓰기 로그-WAL)는 전체/차등 백업과 별개 이며 지속적으로 보관 됩니다.

### <a name="restoring-a-server"></a>서버 복원

- 지정 시간 복원 기능을 사용 하는 경우 새 서버는 기반 서버와 동일한 계산 및 저장소 구성을 사용 하 여 만들어집니다.
- VNET 기반 데이터베이스 서버는 백업에서 복원할 때 동일한 VNET으로 복원 됩니다.
- 복원 동안 만든 새 서버에는 원래 서버에 존재했던 방화벽 규칙이 없습니다. 새 서버에 대해 별도로 방화벽 규칙을 만들어야 합니다.
- 삭제된 서버 복원은 지원되지 않습니다.
- 지역 간 복원은 지원 되지 않습니다.

### <a name="other-features"></a>기타 기능

* Azure AD 인증은 아직 지원 되지 않습니다. Azure AD 인증이 필요한 경우에는 [단일 서버](../overview-single-server.md) 옵션을 사용 하는 것이 좋습니다.
* 읽기 복제본은 아직 지원 되지 않습니다. 읽기 복제본이 필요한 경우에는 [단일 서버](../overview-single-server.md) 옵션을 사용 하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

- [계산 및 저장소 옵션에 사용할 수 있는 기능](concepts-compute-storage.md) 이해
- [지원되는 PostgreSQL 데이터베이스 버전](concepts-supported-versions.md) 알아보기
- [Azure Portal을 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법](how-to-restore-server-portal.md) 검토
