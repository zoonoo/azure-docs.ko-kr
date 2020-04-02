---
title: 제한 사항 - MySQL용 Azure 데이터베이스
description: 이 문서에서는 Azure Database for MySQL에 대한 연결 수 및 스토리지 엔진 옵션과 같은 제한 사항을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 6ca09ab0578fb88e443d6e9e1f920c22457eb042
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548480"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL의 제한 사항
다음 섹션에서는 데이터베이스 서비스의 용량, 스토리지 엔진 지원, 권한 지원, 데이터 조작 명령문 지원 및 기능 제한 사항에 대해 설명합니다. 또한 MySQL 데이터베이스 엔진에 적용할 수 있는 [일반적인 제한 사항](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)도 참조하세요.

## <a name="server-parameters"></a>서버 매개 변수

인기 있는 여러 서버 매개 변수의 최소 값과 최대 값은 가격 책정 계층 및 vCore에 의해 결정됩니다. 제한은 아래 표를 참조하십시오.

### <a name="max_connections"></a>max_connections

|**가격 책정 계층**|**vCore**|**기본값**|**최소 값**|**최대 값**|
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
> 오류 1040(08004): 너무 많은 연결이 있습니다.

> [!IMPORTANT]
> 최상의 환경을 위해 ProxySQL과 같은 연결 풀러를 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

MySQL에 대한 새 클라이언트 연결을 만드는 데는 시간이 걸리고 일단 설정되면 이러한 연결은 유휴 상태일 때에도 데이터베이스 리소스를 차지합니다. 대부분의 응용 프로그램은 이 상황을 복잡하게 하는 많은 단기 연결을 요청합니다. 그 결과 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이를 방지하는 데 도움이 됩니다. ProxySQL 설정에 대해 자세히 알아보려면 [블로그 게시물을](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)방문하십시오.

### <a name="query_cache_size"></a>query_cache_size

쿼리 캐시는 기본적으로 꺼져 있습니다. 쿼리 캐시를 사용하려면 `query_cache_type` 매개 변수를 구성합니다. 

이 매개 변수에 대해 자세히 알아보려면 [MySQL 설명서를](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) 검토하십시오.

> [!NOTE]
> 쿼리 캐시는 MySQL 5.7.20으로 더 이상 사용되지 않으며 MySQL 8.0에서 제거되었습니다.

|**가격 책정 계층**|**vCore**|**기본값**|**최소 값**|**최대 값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
|Basic|2|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
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

이 매개 변수에 대해 자세히 알아보려면 [MySQL 설명서를](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) 검토하십시오.

|**가격 책정 계층**|**vCore**|**기본값**|**최소 값**|**최대 값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
|Basic|2|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
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

이 매개 변수에 대해 자세히 알아보려면 [MySQL 설명서를](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) 검토하십시오.

|**가격 책정 계층**|**vCore**|**기본값**|**최소 값**|**최대 값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
|Basic|2|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
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

이 매개 변수에 대해 자세히 알아보려면 [MySQL 설명서를](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) 검토하십시오.

|**가격 책정 계층**|**vCore**|**기본값**|**최소 값**|**최대 값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
|Basic|2|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
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

이 매개 변수에 대해 자세히 알아보려면 [MySQL 설명서를](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) 검토하십시오.

|**가격 책정 계층**|**vCore**|**기본값**|**최소 값**|**최대 값**|
|---|---|---|---|---|
|Basic|1|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
|Basic|2|기본 계층에서 구성할 수 없습니다.|N/A|N/A|
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

표준 시간대 테이블은 MySQL 명령줄 또는 MySQL 워크벤치와 같은 도구에서 `mysql.az_load_timezone` 저장된 프로시저를 호출하여 채울 수 있습니다. 저장 프로시저를 호출하고 전역 또는 세션 수준 표준 시간대를 설정하는 방법에 대한 [Azure 포털](howto-server-parameters.md#working-with-the-time-zone-parameter) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) 문서를 참조하십시오.

## <a name="storage-engine-support"></a>스토리지 엔진 지원

### <a name="supported"></a>지원됨
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [메모리](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>지원되지 않음
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [아카이브](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>권한 지원

### <a name="unsupported"></a>지원되지 않음
- DBA 역할: 서버 매개 변수 및 설정이 많으면 실수로 서버 성능이 저하되거나 DBMS의 ACID 속성이 무효화될 수 있습니다. 따라서 제품 수준에서 서비스 무결성 및 SLA를 유지하기 위해 이 서비스에서는 DBA 역할이 노출되지 않습니다. 사용자는 새 데이터베이스 인스턴스를 만들 때 생성되는 기본 사용자 계정을 통해 관리되는 데이터베이스 인스턴스에서 대부분의 DDL 및 DML 문을 수행할 수 있습니다. 
- SUPER 권한: 마찬가지로 [SUPER 권한](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)도 제한됩니다.
- 정의자: 만들려면 슈퍼 권한이 필요하며 제한됩니다. 백업을 사용하여 데이터를 가져올 경우 mysqldump를 수행할 때 수동으로 또는 `--skip-definer` 명령을 사용하여 `CREATE DEFINER` 명령을 제거하세요.

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
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다. 다음의 주 버전으로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 주 버전을 [덤프 및 복원](./concepts-migrate-dump-restore.md)합니다.

### <a name="point-in-time-restore"></a>특정 시점 복원
- PITR 기능을 사용하면 새 서버가 기반으로 하는 서버와 동일한 구성으로 새 서버가 만들어집니다.
- 삭제된 서버 복원은 지원되지 않습니다.

### <a name="vnet-service-endpoints"></a>VNet 서비스 엔드포인트
- VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

### <a name="storage-size"></a>스토리지 크기
- 가격 [책정 계층당](concepts-pricing-tiers.md) 저장소 크기 제한에 대한 가격 책정 계층을 참조하십시오.

## <a name="current-known-issues"></a>현재 알려진 문제
- 연결이 설정된 후에 MySQL 서버 인스턴스에서 잘못된 서버 버전을 표시합니다. 올바른 서버 인스턴스 엔진 버전을 설치하려면 `select version();` 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계
- [각 서비스 계층에서 사용할 수 있는 내용](concepts-pricing-tiers.md)
- [지원되는 MySQL 데이터베이스 버전](concepts-supported-versions.md)
