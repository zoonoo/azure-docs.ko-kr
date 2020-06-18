---
title: 제한 사항 - Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB에 대한 연결 수 및 스토리지 엔진 옵션과 같은 제한 사항을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: d4450689f6865c19436e437e09a3aa9f286c6e21
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653126"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 제한 사항
다음 섹션에서는 데이터베이스 서비스의 용량, 스토리지 엔진 지원, 권한 지원, 데이터 조작 명령문 지원 및 기능 제한 사항에 대해 설명합니다.

## <a name="server-parameters"></a>서버 매개 변수

널리 사용되는 여러 서버 매개 변수의 최솟값과 최댓값은 가격 책정 계층 및 vCores에 의해 결정됩니다. 제한 사항은 아래 표를 참조하세요.

### <a name="max_connections"></a>max_connections

|**가격 책정 계층**|**vCore**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|범용|2|300|10|600|
|범용|4|625|10|1250|
|범용|8|1250|10|2500|
|범용|16|2500|10|5,000|
|범용|32|5,000|10|10000|
|범용|64|10000|10|20000|
|메모리 최적화|2|600|10|800|
|메모리 최적화|4|1250|10|2500|
|메모리 최적화|8|2500|10|5,000|
|메모리 최적화|16|5,000|10|10000|
|메모리 최적화|32|10000|10|20000|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류 1040(08004): 연결이 너무 많음

> [!IMPORTANT]
> 최상의 환경을 위해 ProxySQL과 같은 연결 풀러를 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

MariaDB에 대한 새 클라이언트 연결을 만들려면 시간이 오래 걸리고 일단 설정되면 이러한 연결은 유휴 상태일 때에도 데이터베이스 리소스를 차지합니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이러한 문제를 방지하는 데 도움이 됩니다. ProxySQL 설정에 대해 알아보려면 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)을 방문하세요.

### <a name="query_cache_size"></a>query_cache_size

쿼리 캐시는 기본적으로 해제되어 있습니다. 쿼리 캐시를 사용하도록 설정하려면 `query_cache_type` 매개 변수를 구성합니다. 

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|0|0|16777216|
|범용|4|0|0|33554432|
|범용|8|0|0|67108864|
|범용|16|0|0|134217728|
|범용|32|0|0|134217728|
|범용|64|0|0|134217728|
|메모리 최적화|2|0|0|33554432|
|메모리 최적화|4|0|0|67108864|
|메모리 최적화|8|0|0|134217728|
|메모리 최적화|16|0|0|134217728|
|메모리 최적화|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|524288|32768|4194304|
|범용|4|524288|32768|8388608|
|범용|8|524288|32768|16777216|
|범용|16|524288|32768|33554432|
|범용|32|524288|32768|33554432|
|범용|64|524288|32768|33554432|
|메모리 최적화|2|524288|32768|8388608|
|메모리 최적화|4|524288|32768|16777216|
|메모리 최적화|8|524288|32768|33554432|
|메모리 최적화|16|524288|32768|33554432|
|메모리 최적화|32|524288|32768|33554432|

### <a name="join_buffer_size"></a>join_buffer_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|262144|128|268435455|
|범용|4|262144|128|536870912|
|범용|8|262144|128|1073741824|
|범용|16|262144|128|2147483648|
|범용|32|262144|128|4294967295|
|범용|64|262144|128|4294967295|
|메모리 최적화|2|262144|128|536870912|
|메모리 최적화|4|262144|128|1073741824|
|메모리 최적화|8|262144|128|2147483648|
|메모리 최적화|16|262144|128|4294967295|
|메모리 최적화|32|262144|128|4294967295|

### <a name="max_heap_table_size"></a>max_heap_table_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|16777216|16384|268435455|
|범용|4|16777216|16384|536870912|
|범용|8|16777216|16384|1073741824|
|범용|16|16777216|16384|2147483648|
|범용|32|16777216|16384|4294967295|
|범용|64|16777216|16384|4294967295|
|메모리 최적화|2|16777216|16384|536870912|
|메모리 최적화|4|16777216|16384|1073741824|
|메모리 최적화|8|16777216|16384|2147483648|
|메모리 최적화|16|16777216|16384|4294967295|
|메모리 최적화|32|16777216|16384|4294967295|

### <a name="tmp_table_size"></a>tmp_table_size

이 매개 변수에 대해 자세히 알아보려면 [MariaDB 설명서](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)를 검토하세요.

|**가격 책정 계층**|**vCore**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|Basic|2|기본 계층에서 구성할 수 없음|해당 없음|해당 없음|
|범용|2|16777216|1024|67108864|
|범용|4|16777216|1024|134217728|
|범용|8|16777216|1024|268435456|
|범용|16|16777216|1024|536870912|
|범용|32|16777216|1024|1073741824|
|범용|64|16777216|1024|1073741824|
|메모리 최적화|2|16777216|1024|134217728|
|메모리 최적화|4|16777216|1024|268435456|
|메모리 최적화|8|16777216|1024|536870912|
|메모리 최적화|16|16777216|1024|1073741824|
|메모리 최적화|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하여 표준 시간대 테이블을 채울 수 있습니다. 저장 프로시저를 호출하고 글로벌 또는 세션 수준 표준 시간대를 설정하는 방법은 [Azure Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) 또는 [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 문서를 참조하세요.

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MariaDB는 테이블 생성 중에 제공한 구성에 따라 InnoDB 테이블을 다른 테이블스페이스에 저장합니다. [시스템 테이블스페이스](https://mariadb.com/kb/en/innodb-system-tablespaces/)는 InnoDB 데이터 사전의 스토리지 영역입니다. [file-per-table 테이블스페이스](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/)에는 단일 InnoDB 테이블에 대한 데이터 및 인덱스를 포함하며 파일 시스템에 자체 데이터 파일로 저장됩니다. 이 동작은 `innodb_file_per_table` 서버 매개 변수에 의해 제어됩니다. `innodb_file_per_table`을 `OFF`로 설정하면 InnoDB가 시스템 테이블스페이스에 테이블을 만듭니다. 그렇지 않으면 InnoDB는 file-per-table 테이블스페이스에 테이블을 만듭니다.

Azure Database for MariaDB는 단일 데이터 파일에서 가장 큰 **1TB**를 지원합니다. 데이터베이스 크기가 1TB보다 큰 경우 [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) 테이블스페이스에 테이블을 만들어야 합니다. 단일 테이블 크기가 1TB보다 큰 경우에는 파티션 테이블을 사용해야 합니다.

## <a name="storage-engine-support"></a>스토리지 엔진 지원

### <a name="supported"></a>지원됨
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>지원되지 않음
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>권한 지원

### <a name="unsupported"></a>지원되지 않음
- DBA 역할: 서버 매개 변수 및 설정이 많으면 실수로 서버 성능이 저하되거나 DBMS의 ACID 속성이 무효화될 수 있습니다. 따라서 제품 수준에서 서비스 무결성 및 SLA를 유지하기 위해 이 서비스에서는 DBA 역할이 노출되지 않습니다. 사용자는 새 데이터베이스 인스턴스를 만들 때 생성되는 기본 사용자 계정을 통해 관리되는 데이터베이스 인스턴스에서 대부분의 DDL 및 DML 문을 수행할 수 있습니다.
- SUPER 권한: 마찬가지로 [SUPER 권한](https://mariadb.com/kb/en/library/grant/#global-privileges)도 제한됩니다.
- DEFINER: 생성하려면 SUPER 권한이 필요하며, 제한됩니다. 백업을 사용하여 데이터를 가져올 경우 mysqldump를 수행할 때 수동으로 또는 `--skip-definer` 명령을 사용하여 `CREATE DEFINER` 명령을 제거하세요.

## <a name="data-manipulation-statement-support"></a>데이터 조작 명령문 지원

### <a name="supported"></a>지원됨
- `LOAD DATA INFILE`은 지원되지만 `[LOCAL]` 매개 변수를 지정하고 UNC 경로(SMB를 통해 탑재된 Azure Storage)로 전달해야 합니다.

### <a name="unsupported"></a>지원되지 않음
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="scale-operations"></a>크기 조정 작업
- 기본 가격 책정 계층 간의 동적 크기 조정은 현재 지원되지 않습니다.
- 서버 스토리지 크기를 줄이는 것은 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다.

### <a name="point-in-time-restore"></a>특정 시점 복원
- PITR 기능을 사용하면 새 서버가 기반으로 하는 서버와 동일한 구성으로 새 서버가 만들어집니다.
- 삭제된 서버 복원은 지원되지 않습니다.

### <a name="subscription-management"></a>구독 관리
- 구독 및 리소스 그룹에서 미리 생성된 서버를 동적으로 이동하는 것은 현재 지원되지 않습니다.

### <a name="vnet-service-endpoints"></a>VNet 서비스 엔드포인트
- VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

### <a name="storage-size"></a>스토리지 크기
- 가격 책정 계층당 스토리지 크기 제한에 대한 [가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.

## <a name="current-known-issues"></a>현재 알려진 문제
- 연결이 설정된 후에 MariaDB 서버 인스턴스가 잘못된 서버 버전을 표시합니다. 올바른 서버 인스턴스 엔진 버전을 설치하려면 `select version();` 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계
- [각 서비스 계층에서 사용할 수 있는 기능](concepts-pricing-tiers.md)
- [지원되는 MariaDB 데이터베이스 버전](concepts-supported-versions.md)
