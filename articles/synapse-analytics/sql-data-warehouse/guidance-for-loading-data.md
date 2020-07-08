---
title: Synapse SQL 풀에 대 한 데이터 로드 모범 사례
description: Synapse SQL 풀을 사용 하 여 데이터를 로드 하기 위한 권장 사항 및 성능 최적화
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 10a6c2e4f6f9dcbb29eb16cbfabd8fba31668f06
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201636"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Synapse SQL 풀을 사용 하 여 데이터를 로드 하는 모범 사례

이 문서에서는 SQL 풀을 사용 하 여 데이터를 로드 하기 위한 권장 사항 및 성능 최적화에 대해 알아봅니다.

## <a name="preparing-data-in-azure-storage"></a>Azure Storage에 데이터 준비

대기 시간을 최소화 하려면 저장소 계층과 SQL 풀을 함께 배치 합니다.

ORC 파일 형식으로 데이터를 내보낼 때 큰 텍스트 열이 있으면 Java 메모리 부족 오류가 발생할 수 있습니다. 이러한 제한 사항을 해결하려면 열의 하위 집합만 내보냅니다.

모든 파일 형식에는 서로 다른 성능 특성이 있습니다. 가장 빠르게 로드하려면 압축 구분 텍스트 파일을 사용합니다. UTF-8과 UTF-16의 성능 차이는 미미합니다.

대규모 압축 파일은 더 작은 크기의 압축 파일로 분할합니다.

## <a name="running-loads-with-enough-compute"></a>충분한 컴퓨팅 리소스로 로드 실행

로드 속도를 가장 빠르게 하려면 로드 작업을 한 번에 하나만 실행합니다. 가능 하지 않은 경우 최소 개수의 로드를 동시에 실행 합니다. 대량 로드 작업을 원하는 경우 로드 하기 전에 SQL 풀을 확장 하는 것이 좋습니다.

적절한 컴퓨팅 리소스가 포함된 로드를 실행하려면 부하를 실행하기 위해 지정된 로드 사용자를 만듭니다. 각 로드 사용자를 특정 작업 그룹으로 분류 합니다. 부하를 실행 하려면 로드 하는 사용자 중 하나로 로그인 한 후 로드를 실행 합니다. 사용자의 작업 그룹을 사용 하 여 로드를 실행 합니다.  

### <a name="example-of-creating-a-loading-user"></a>로드 사용자를 만드는 예제

이 예에서는 특정 작업 그룹으로 분류 된 로드 사용자를 만듭니다. 첫 번째 단계는 **마스터에 연결**하고 로그인을 만드는 것입니다.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

SQL 풀에 연결 하 고 사용자를 만듭니다. 다음 코드에서는 mySampleDataWarehouse 라는 데이터베이스에 연결 되어 있다고 가정 합니다. 로더 라는 사용자를 만드는 방법을 보여 주고 [COPY 문을](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)사용 하 여 테이블을 만들고 로드 하는 사용자 권한을 부여 합니다. 그런 다음 사용자를 최대 리소스를 사용 하는 DataLoads 작업 그룹으로 분류 합니다. 

```sql
   -- Connect to the SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
      MIN_PERCENTAGE_RESOURCE = 100
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```

로드 작업 그룹에 대 한 리소스를 사용 하 여 부하를 실행 하려면 로더에 로그인 하 고 부하를 실행 합니다.

## <a name="allowing-multiple-users-to-load-polybase"></a>여러 사용자가 로드 하도록 허용 (PolyBase)

여러 사용자가 SQL 풀에 데이터를 로드 해야 하는 경우가 종종 있습니다. [CREATE TABLE AS SELECT (transact-sql)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (PolyBase)를 사용 하 여 로드 하려면 데이터베이스에 대 한 CONTROL 권한이 필요 합니다.  CONTROL 권한은 모든 스키마에 대한 제어 액세스를 부여합니다.

모든 로드 사용자가 모든 스키마에 대한 제어 액세스 권한을 갖는 것은 좋지 않습니다. 권한을 제한하려면 DENY CONTROL 문을 사용합니다.

예를 들어, 데이터베이스 스키마, 부서 A에 대한 스키마_A 및 부서 B에 대한 스키마_B를 가정합니다. 데이터베이스 사용자, 사용자_A 및 사용자_B가 부서 A와 B 각각에서 PolyBase 로드에 대한 사용자가 되도록 합니다. 둘 모두 데이터베이스 CONTROL 권한을 부여 받습니다. 스키마 A와 B의 작성자는 이제 DENY를 사용하여 해당 스키마를 잠급니다.

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A 및 user_B은 이제 다른 dept의 스키마에서 잠깁니다.

## <a name="loading-to-a-staging-table"></a>준비 테이블에 로드

SQL 풀 테이블로 데이터를 이동 하기 위한 가장 빠른 로드 속도를 얻으려면 준비 테이블에 데이터를 로드 합니다.  준비 테이블을 힙으로 정의하고 배포 옵션에 라운드 로빈을 사용합니다.

일반적으로 로딩은 준비 테이블에 먼저 로드 한 다음 프로덕션 SQL 풀 테이블에 데이터를 삽입 하는 2 단계 프로세스입니다. 프로덕션 테이블이 해시 배포를 사용하는 경우 해시 배포를 사용하여 준비 테이블을 정의하면 로드하고 삽입하는 총 시간이 더 빠를 수 있습니다.

준비 테이블로 로드하는 데 시간이 더 걸리지만 프로덕션 테이블에 행을 삽입하는 두 번째 단계에서는 배포에서 데이터 이동이 발생하지 않습니다.

## <a name="loading-to-a-columnstore-index"></a>columnstore 인덱스에 로드

columnstore 인덱스는 고품질 행 그룹으로 데이터를 압축하기 위해 대량의 메모리가 필요합니다. 최상의 압축 및 인덱스 효율성을 위해 columnstore 인덱스는 최대 1,048,576개의 행을 각 행 그룹으로 압축해야 합니다.

메모리 압박이 있는 경우 columnstore 인덱스는 최대 압축률을 달성하지 못할 수 있습니다. 이 시나리오는 쿼리 성능에 영향을 미칠 것입니다. 심층 분석은 [Columnstore 메모리 최적화](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)를 참조하세요

- 로드 사용자가 메모리를 최대 압축률을 충분히 달성할 수 있도록 하려면 중간 규모 또는 대규모 리소스 클래스의 멤버인 로드 사용자를 사용합니다.
- 새로운 행 그룹을 완전히 채울 수 있는 충분한 행을 로드합니다. 대량 로드 중에는 행 수가 1,048,576이 될 때마다 전체 열 그룹으로 직접 columnstore에 압축됩니다. 로드하는 행 수가 102,400 미만인 경우에는 B-트리 인덱스에 행이 보유되는 deltastore에 행을 보냅니다.

> [!NOTE]
> 너무 적은 행을 로드 하는 경우 모두 deltastore로 라우팅하고 columnstore 형식으로 즉시 압축 되지 않을 수 있습니다.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>SqLBulkCopy API 또는 bcp를 사용 하는 경우 일괄 처리 크기 늘리기

COPY 문으로 로드 하면 SQL 풀에서 가장 높은 처리량이 제공 됩니다. 이 복사본을 사용 하 여 로드할 수 없고 [SQLBULKCOPY API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 또는 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용 해야 하는 경우 처리량을 높이기 위해 일괄 처리 크기를 늘려야 합니다.

> [!TIP]
> 최적의 일괄 처리 크기를 결정 하기 위한 권장 기준은 100 K ~ 1M 행 사이의 일괄 처리 크기입니다.

## <a name="handling-loading-failures"></a>로드 처리 실패

외부 테이블을 사용하는 로드가 *"쿼리가 중단되었습니다. 외부 소스에서 읽는 동안 최대 거부 임계값에 도달했습니다."* 오류로 인해 실패할 수 있습니다. 이 메시지는 외부 데이터에 더티 레코드가 포함되어 있음을 나타냅니다.

데이터 레코드는 다음 조건 중 하나를 충족 하는 경우에는 변경 되지 않은 것으로 간주 됩니다.

- 데이터 형식과 열 개수가 외부 테이블의 열 정의와 일치 하지 않습니다.
- 데이터가 지정 된 외부 파일 형식에 맞지 않습니다.

더티 레코드 문제를 해결하려면 외부 테이블 및 외부 파일 형식 정의가 올바른지와 외부 데이터가 이러한 정의를 준수하는지 확인합니다.

외부 데이터 레코드의 하위 집합이 더티 인 경우 [CREATE EXTERNAL TABLE (transact-sql)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에서 거부 옵션을 사용 하 여 쿼리에 대해 이러한 레코드를 거부 하도록 선택할 수 있습니다.

## <a name="inserting-data-into-a-production-table"></a>프로덕션 테이블에 데이터 삽입

[INSERT 문](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용하는 작은 테이블에 한 번만 로드하거나 정기적으로 조회를 다시 로드하는 경우, `INSERT INTO MyLookup VALUES (1, 'Type 1')`와 같은 명령문으로 충분히 수행할 수 있습니다.  하지만, singleton 삽입은 대량 로드 수행만큼 효율적이지 않습니다.

하루 종일 수천 개 이상의 단일 삽입을 수행하는 경우 대량 로드할 수 있도록 로드를 일괄 처리합니다.  파일에 단일 삽입을 추가하는 프로세스를 개발하고 정기적으로 파일을 로드하는 다른 프로세스를 만듭니다.

## <a name="creating-statistics-after-the-load"></a>로드 후 통계 만들기

쿼리 성능을 개선하려면 데이터를 처음 로드하거나 데이터 내에 상당한 변화가 생긴 후에, 모든 테이블의 모든 열에서 통계를 만드는 것이 중요합니다. 통계 만들기는 수동으로 수행 하거나 [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)를 사용 하도록 설정할 수 있습니다.

통계에 대한 자세한 설명은 [통계](sql-data-warehouse-tables-statistics.md)를 참조하세요. 다음 예에서는 Customer_Speed 테이블의 5 개 열에 대 한 통계를 수동으로 만드는 방법을 보여 줍니다.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys-polybase"></a>저장소 키 회전 (PolyBase)

정기적으로 Blob Storage 액세스 키를 변경하는 것은 좋은 보안 방법입니다. Blob Storage 계정에 두 개의 스토리지 키가 있으므로 키 전환이 가능합니다.

Azure Storage 계정 키를 회전하려면:

키가 변경된 각 스토리지 계정에 대해 [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 실행합니다.

예:

원래 키를 만드는 경우

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

키 1에서 키 2로 키를 회전하는 경우

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

기본 외부 데이터 원본에 대한 다른 변경은 필요하지 않습니다.

## <a name="next-steps"></a>다음 단계

- ELT (추출, 로드 및 변환) 프로세스를 설계할 때 복사 문이나 PolyBase에 대해 자세히 알아보려면 [SQL Data Warehouse에 대 한 Elt 디자인](design-elt-data-loading.md)을 참조 하세요.
- 로드 자습서의 경우 [COPY 문을 사용 하 여 Azure blob storage에서 SYNAPSE SQL로 데이터를 로드](load-data-from-azure-blob-storage-using-polybase.md)합니다.
- 데이터 로드를 모니터링하려면 [DMV를 사용하여 워크로드 모니터링](sql-data-warehouse-manage-monitor.md)을 참조하세요.
