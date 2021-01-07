---
title: 앱 개발 모범 사례-Azure Database for PostgreSQL
description: Azure Database for PostgreSQL를 사용 하 여 앱을 빌드하기 위한 모범 사례에 대해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97364603"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Azure Database for PostgreSQL를 사용 하 여 응용 프로그램을 빌드하기 위한 모범 사례

Azure Database for PostgreSQL를 사용 하 여 클라우드 지원 응용 프로그램을 빌드하는 데 도움이 되는 몇 가지 모범 사례는 다음과 같습니다. 이러한 모범 사례는 앱에 대 한 개발 시간을 줄일 수 있습니다.

## <a name="configuration-of-application-and-database-resources"></a>응용 프로그램 및 데이터베이스 리소스 구성

### <a name="keep-the-application-and-database-in-the-same-region"></a>응용 프로그램과 데이터베이스를 동일한 지역에 유지
Azure에서 응용 프로그램을 배포할 때 모든 종속성이 동일한 지역에 있는지 확인 합니다. 영역 또는 가용성 영역에 걸쳐 인스턴스를 분산 하는 경우 네트워크 대기 시간이 만들어지므로 응용 프로그램의 전반적인 성능에 영향을 줄 수 있습니다.

### <a name="keep-your-postgresql-server-secure"></a>PostgreSQL 서버를 안전 하 게 유지
PostgreSQL 서버를 [안전](./concepts-security.md) 하 게 구성 하 고 공개적으로 액세스할 수 없도록 구성 합니다. 다음 옵션 중 하나를 사용 하 여 서버를 보호 합니다.
- [방화벽 규칙](./concepts-firewall-rules.md)
- [가상 네트워크](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

보안을 위해 항상 SSL을 통해 PostgreSQL 서버에 연결 하 고 PostgreSQL 서버 및 응용 프로그램에서 TLS 1.2를 사용 하도록 구성 해야 합니다. [SSL/TLS를 구성 하는 방법을](./concepts-ssl-connection-security.md)참조 하세요.

### <a name="tune-your-server-parameters"></a>서버 매개 변수 조정
읽기 작업이 많은 워크 로드의 경우 서버 매개 변수를 조정 `tmp_table_size` 하는 `max_heap_table_size` 데 도움이 되며 성능을 향상 시킬 수 있습니다. 이러한 변수에 필요한 값을 계산 하려면 총 연결당 메모리 값 및 기본 메모리를 확인 합니다. `tmp_table_size`서버의 총 메모리에 대 한 기본 메모리 계정과 결합 된 연결별 메모리 별 매개 변수의 합입니다.

### <a name="use-environment-variables-for-connection-information"></a>연결 정보에 환경 변수 사용
응용 프로그램 코드에 데이터베이스 자격 증명을 저장 하지 마세요. 프런트 엔드 응용 프로그램에 따라 환경 변수 설정 지침을 따릅니다. App service 사용에 대 한 자세한 내용은[앱 설정을 구성 하는 방법](../app-service/configure-common.md#configure-app-settings) 및 Azure Kuberentes 서비스에 대 한 자세한 내용은 [Kubernetes 암호를 사용 하는 방법](https://kubernetes.io/docs/concepts/configuration/secret/)을 참조 하세요.

## <a name="performance-and-resiliency"></a>성능 및 복원 력
응용 프로그램의 성능 문제를 디버그 하는 데 사용할 수 있는 몇 가지 도구와 사례는 다음과 같습니다.

### <a name="use-connection-pooling"></a>연결 풀링 사용
연결 풀링을 사용 하면 시작 시 고정 된 연결 집합이 설정 되 고 유지 관리 됩니다. 이를 통해 데이터베이스 서버에 설정 된 동적 새 연결로 인해 발생 하는 서버의 메모리 조각화를 줄일 수 있습니다. 응용 프로그램 프레임 워크 또는 데이터베이스 드라이버가 지 원하는 경우 연결 풀링을 응용 프로그램 쪽에서 구성할 수 있습니다. 지원 되지 않는 경우 다른 권장 옵션은 응용 프로그램 외부에서 실행 되 고 데이터베이스 서버에 연결 하는 [PgBouncer](https://pgbouncer.github.io/) 또는 [pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) 과 같은 프록시 연결 풀 서비스를 활용 하는 것입니다. PgBouncer 및 Pgpool은 모두 Azure Database for PostgreSQL와 작동 하는 커뮤니티 기반 도구입니다.

### <a name="retry-logic-to-handle-transient-errors"></a>일시적인 오류를 처리 하는 재시도 논리
응용 프로그램에서 데이터베이스에 대 한 연결이 일시적으로 삭제 되거나 손실 되는 일시적인 오류가 발생할 수 있습니다. 이러한 경우 서버는 5 ~ 10 초 내에 1 ~ 2 회 다시 시도 된 후에 실행 됩니다. 첫 번째 재시도 전에 5 초간 대기 하는 것이 좋습니다. 그런 다음 각 다시 시도를 수행 하 고, 최대 60 초까지 대기를 늘립니다. 응용 프로그램에서 작업에 실패 한 것으로 간주 하는 최대 다시 시도 횟수를 제한 하 여 추가 조사를 수행할 수 있습니다. 자세히 알아보려면 [연결 오류 문제를 해결 하는 방법](./concepts-connectivity.md) 을 참조 하세요.

### <a name="enable-read-replication-to-mitigate-failovers"></a>장애 조치 (failover)를 완화 하기 위해 읽기 복제 사용
장애 조치 (failover) 시나리오에 [입력 데이터 복제](./concepts-read-replicas.md) 를 사용할 수 있습니다. 읽기 복제본을 사용 하는 경우 원본 서버와 복제 서버 간에 자동 장애 조치 (failover)가 수행 되지 않습니다. 복제는 비동기 이므로 원본과 복제본 사이에 지연이 발생 합니다. 네트워크 지연은 원본 서버에서 실행 되는 워크 로드의 크기와 데이터 센터 간의 대기 시간 등 여러 가지 요인에 의해 영향을 받을 수 있습니다. 대부분의 경우 복제본 지연 범위는 몇 초에서 몇 분까지입니다.


## <a name="database-deployment"></a>데이터베이스 배포

### <a name="configure-cicd-deployment-pipeline"></a>CI/CD 배포 파이프라인 구성
경우에 따라 데이터베이스에 변경 내용을 배포 해야 합니다. 이러한 경우 PostgreSQL 서버에 대 한 [GitHub 작업](https://github.com/Azure/postgresql/blob/master/README.md) 을 통해 CI (지속적인 통합)를 사용 하 여 사용자 지정 스크립트를 실행 하 여 데이터베이스를 업데이트할 수 있습니다.

### <a name="define-manual-database-deployment-process"></a>수동 데이터베이스 배포 프로세스 정의
수동 데이터베이스 배포 중에 다음 단계를 수행 하 여 가동 중지 시간을 최소화 하거나 배포 실패의 위험을 줄입니다.

- Pg_dump를 사용 하 여 새 데이터베이스에 프로덕션 데이터베이스의 복사본을 만듭니다.
- 새 데이터베이스를 새 스키마 변경 내용으로 업데이트 하거나 데이터베이스에 필요한 업데이트를 업데이트 합니다.
- 프로덕션 데이터베이스를 읽기 전용 상태로 전환 합니다. 배포가 완료 될 때까지 프로덕션 데이터베이스에 대 한 쓰기 작업이 없어야 합니다.
- 1 단계의 새로 업데이트 된 데이터베이스를 사용 하 여 응용 프로그램을 테스트 합니다.
- 응용 프로그램 변경 내용을 배포 하 고 응용 프로그램이 현재 최신 업데이트가 있는 새 데이터베이스를 사용 하 고 있는지 확인 합니다.
- 변경 사항을 롤백할 수 있도록 이전 프로덕션 데이터베이스를 유지 합니다. 그런 다음 이전 프로덕션 데이터베이스를 삭제 하거나 필요한 경우 Azure Storage에서 내보낼 수 있습니다.

>  [!NOTE]
> 응용 프로그램이 전자 상거래 앱과 비슷하며 읽기 전용 상태로 전환할 수 없는 경우 백업을 수행한 후 프로덕션 데이터베이스에 직접 변경 내용을 배포 합니다. 일부 사용자는 실패 한 요청을 경험할 수 있으므로 앱에 대 한 트래픽이 낮은 사용량이 적은 시간에 이러한 변경이 발생 하 여 영향을 최소화 해야 합니다. 응용 프로그램 코드가 실패 한 모든 요청을 처리 하는지 확인 합니다.

## <a name="database-schema-and-queries"></a>데이터베이스 스키마 및 쿼리
데이터베이스 스키마와 쿼리를 빌드할 때 유의 해야 할 몇 가지 팁은 다음과 같습니다.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>기본 키에 대해 BIGINT 또는 UUID 사용
사용자 지정 응용 프로그램 또는 일부 프레임 워크를 빌드할 때 `INT` 기본 키 대신를 사용 하는 것이 좋습니다 `BIGINT` . 를 사용 하는 경우 ```INT``` 데이터베이스의 값이 데이터 형식의 저장소 용량을 초과할 수 있는 위험을 실행 합니다 ```INT``` . 이러한 변경 작업을 기존 프로덕션 응용 프로그램에 적용 하는 데는 더 많은 개발 시간이 소요 될 수 있습니다. 또 다른 옵션은 기본 키에 [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) 를 사용 하는 것입니다. 이 식별자는 자동 생성 된 128 비트 문자열을 사용 합니다 (예:) ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . [PostgreSQL 데이터 형식](https://www.postgresql.org/docs/8.1/datatype.html)에 대해 자세히 알아보세요.

### <a name="use-indexes"></a>인덱스 사용

Postgres에는 다양 한 방식으로 사용할 수 있는 여러 유형의 [인덱스가](https://www.postgresql.org/docs/9.1/indexes.html) 있습니다. 인덱스를 사용 하면 서버에서 인덱스 없이 수행 하는 것 보다 훨씬 더 빠르게 특정 행을 찾고 검색할 수 있습니다. 하지만 인덱스는 데이터베이스 서버에 오버 헤드를 추가 하므로 인덱스 수가 너무 많지 않습니다.

### <a name="use-autovacuum"></a>Autovacuum 사용
Azure Database for PostgreSQL 서버에서 autovacuum을 사용 하 여 서버를 최적화할 수 있습니다. PostgreSQL를 사용 하면 데이터베이스 동시성이 향상 되지만 모든 업데이트 결과가 삽입 및 삭제 됩니다. 삭제의 경우 레코드는 나중에 삭제 되는 소프트로 표시 됩니다. 이러한 작업을 수행하기 위해 PostgreSQL은 진공 작업을 실행합니다. 때때로 진공하지 않으면 누적된 데드 튜플로 인해 다음과 같은 결과가 발생할 수 있습니다.

- 데이터 블로트(예: 큰 데이터베이스 및 테이블)
- 부적절한 인덱스 증가
- I/O 증가

[Autovacuum을 사용 하 여 최적화 하는 방법](howto-optimize-autovacuum.md)에 대해 자세히 알아보세요.

### <a name="use-pg_stats_statements"></a>pg_stats_statements 사용
Pg_stat_statements는 Azure Database for PostgreSQL에서 기본적으로 사용하도록 설정된 PostgreSQL 확장입니다. 이 확장은 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. [Pg_statement 사용 방법을](howto-optimize-query-stats-collection.md)참조 하세요.


### <a name="use-the-query-store"></a>쿼리 저장소 사용
Azure Database for PostgreSQL의 [쿼리 저장소](./concepts-query-store.md) 기능은 쿼리 성능을 추적하는 더 효과적인 방법을 제공합니다. pg_stats_statements를 사용하지 않고 기능을 사용하는 것이 좋습니다.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>대량 삽입 최적화 및 임시 데이터 사용
임시 데이터를 포함하거나 큰 데이터 세트를 대량으로 삽입하는 워크로드 작업이 있는 경우 기록되지 않는 테이블을 사용하는 방안을 고려해 보세요. 기본적으로 원자성 및 내구성을 제공합니다. 원자성, 일관성, 격리 및 내구성이 모여서 ACID 속성을 구성합니다. [대량 삽입을 최적화 하는 방법을](howto-optimize-bulk-inserts.md)참조 하세요.

## <a name="next-steps"></a>다음 단계
[Postgres 가이드](http://postgresguide.com/)
