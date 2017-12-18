---
title: "데이터 로드 지침 - Azure SQL Data Warehouse | Microsoft Docs"
description: "Azure SQL Data Warehouse를 사용하여 데이터를 로드하고 ELT를 수행하기 위한 권장 사항입니다."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 데이터를 로드하기 위한 지침
Azure SQL Data Warehouse를 사용하여 데이터를 로드하기 위한 권장 사항 및 성능 최적화입니다. 

- PolyBase 및 ELT(추출, 로드 및 변환) 프로세스를 디자인하는 방법을 자세히 알아보려면 [SQL Data Warehouse에 대한 ELT 디자인](design-elt-data-loading.md)을 참조하세요.
- 로드 자습서는 [PolyBase를 사용하여 Azure Blob Storage에서 Azure SQL Data Warehouse로 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)를 참조하세요.


## <a name="extract-source-data"></a>원본 데이터 추출

SQL Server 또는 Azure SQL Data Warehouse의 텍스트가 많은 열에서 ORC 파일 형식으로 데이터를 내보낼 때는 Java의 메모리 부족 오류 때문에 50개 열로 제한될 수 있습니다. 이를 해결하려면 열의 하위 집합만 내보냅니다.


## <a name="land-data-to-azure"></a>Azure로 데이터 이동
PolyBase는 1백만 바이트 이상의 데이터를 포함하는 행을 로드할 수 없습니다. Azure Blob storage 또는 Azure Data Lake Store의 텍스트 파일에 데이터를 배치한 경우 데이터가 1백만 바이트 미만이어야 합니다. 정의된 테이블 스키마에 관계없이 항상 적용됩니다.

저장소 계층과 데이터 웨어하우스를 배치하여 대기 시간 최소화

## <a name="data-preparation"></a>데이터 준비

모든 파일 형식에는 서로 다른 성능 특성이 있습니다. 가장 빠르게 로드하려면 압축 구분 텍스트 파일을 사용합니다. UTF-8과 UTF-16의 성능 차이는 미미합니다.

대규모 압축 파일은 더 작은 크기의 압축 파일로 분할합니다.

## <a name="create-designated-loading-users"></a>지정된 로드 사용자 만들기
적절한 계산 리소스가 포함된 로드를 실행하려면 부하를 실행하기 위해 지정된 로드 사용자를 만듭니다. 특정 리소스 클래스에 각 로드 사용자를 할당합니다. 로드를 실행하려면 로드 사용자 중 한 명으로 로그인하고 부하를 실행합니다. 사용자의 리소스 클래스를 사용하여 부하를 실행합니다.  이 메서드는 현재 리소스 클래스 요구 사항에 맞게 사용자의 리소스 클래스를 변경하는 것보다 더 간단합니다.

이 코드는 staticrc20 리소스 클래스에 대한 로드 사용자를 만듭니다. 데이터베이스에서 사용자 정의 컨트롤 사용 권한을 부여하고 staticrc20 데이터베이스 역할의 멤버인 사용자를 추가합니다. staticRC20 리소스 클래스에 대한 리소스를 사용하여 로드를 실행하려면 단순히 LoaderRC20로 로그인하고 부하를 실행합니다. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

동적 리소스 클래스가 아닌 고정 리소스 클래스에서 로드를 실행합니다. 고정 리소스 클래스를 사용하면 [서비스 수준](performance-tiers.md#service-levels)에 관계 없이 동일한 리소스를 사용하도록 보장합니다. 동적 리소스 클래스를 사용하는 경우 리소스는 서비스 수준에 따라 달라집니다. 동적 클래스의 경우 서비스 수준이 낮으면 로드 사용자에 대해 큰 리소스 클래스를 사용해야 합니다.

### <a name="example-for-isolating-loading-users"></a>로드 사용자를 격리하는 예제

SQL DW로 데이터를 로드할 수 있는 여러 명의 사용자가 필요한 경우가 있습니다. [CREATE TABLE AS SELECT(Transact-SQL)][CREATE TABLE AS SELECT(Transact-SQL)]는 데이터베이스의 CONTROL 권한이 필요하므로 모든 스키마에 대한 제어 액세스가 있는 여러 명의 사용자가 필요합니다. 이를 제한하기 위해 DENY CONTROL 문을 사용할 수 있습니다.

예를 들어, 데이터베이스 스키마, 부서 A에 대한 스키마_A 및 부서 B에 대한 스키마_B를 가정합니다. 데이터베이스 사용자, 사용자_A 및 사용자_B가 부서 A와 B 각각에서 PolyBase 로드에 대한 사용자가 되도록 합니다. 둘 모두 데이터베이스 CONTROL 권한을 부여 받습니다. 스키마 A와 B의 작성자는 이제 DENY를 사용하여 해당 스키마를 잠급니다.

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

사용자_A 및 사용자_B는 이제 다른 부서의 스키마에서 차단되었습니다.


## <a name="load-to-a-staging-table"></a>준비 테이블에 로드

가장 빠른 로드를 위해 round_robin 힙 준비 테이블에 로드합니다. 이것이 저장소 계층의 데이터를 Azure Storage 계층에서 SQl Data Warehouse로 이동하는 가장 효율적인 방법입니다.

대용량 로드 작업이 예상된다면 데이터 웨어하우스를 확장합니다.

최적의 로드 성능을 위해 한 번에 하나의 로드 작업만 실행

### <a name="optimize-columnstore-index-loads"></a>columnstore 인덱스 로드 최적화

columnstore 인덱스에는 고품질 행 그룹으로 데이터를 압축하기 위해 많은 메모리가 필요합니다. 최상의 압축 및 인덱스 효율성을 위해 columnstore 인덱스는 1,048,576개의 행을 각 행 그룹으로 압축해야 합니다. 행 그룹당 최대 행 수입니다. 메모리 압박이 있는 경우 columnstore 인덱스는 최대 압축률을 달성하지 못할 수 있습니다. 쿼리 성능에 영향을 줍니다. 심층 분석은 [Columnstore 메모리 최적화](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)를 참조하세요

- 로드 사용자가 메모리를 최대 압축률을 충분히 달성할 수 있도록 하려면 중간 규모 또는 대규모 리소스 클래스의 멤버인 로드 사용자를 사용합니다. 
- 새로운 행 그룹을 완전히 채울 수 있는 충분한 행을 로드합니다. 대량 로드 중에 1,048,576개의 행은 각각 columnstore로 바로 이동합니다. 102,400개 미만의 행을 포함하는 로드는 행을 deltastore로 보냅니다. 여기서는 클러스터형된 인덱스에서 압축에 충분한 만큼의 행을 보유합니다. 너무 적은 행을 로드한 경우 모두 deltastore로 이동하여 columnstore 형식으로 즉시 압축되지 않을 수 있습니다.


### <a name="handling-loading-failures"></a>로드 처리 실패

외부 테이블을 사용하는 로드가 *"쿼리가 중단되었습니다. 외부 소스에서 읽는 동안 최대 거부 임계값에 도달했습니다."*오류로 인해 실패할 수 있습니다. 이는 외부 데이터에 *더티* 레코드가 포함되어 있음을 나타냅니다. 열의 실제 데이터 형식/개수가 외부 테이블의 열 정의와 일치하지 않거나 데이터가 지정된 외부 파일 형식에 맞지 않는 경우 데이터 레코드가 '더티'로 간주됩니다. 

이 문제를 해결하려면 외부 테이블 및 외부 파일 형식 정의가 올바른지, 그리고 외부 데이터가 이러한 정의를 준수하는지 확인합니다. 외부 데이터 레코드의 하위 집합이 더티인 경우 CREATE EXTERNAL TABLE DDL의 거부 옵션을 사용하여 쿼리에 대해 해당 레코드를 거부하도록 선택할 수 있습니다.



## <a name="insert-data-into-production-table"></a>프로덕션 테이블에 데이터 삽입
프로덕션 테이블에 행을 삽입하기 위한 권장 사항은 다음과 같습니다.


### <a name="batch-insert-statements"></a>일괄 처리 INSERT 문
[INSERT 문](/sql/t-sql/statements/insert-transact-sql.md)을 사용하는 작은 테이블에 대한 일회성 로드 또는 조회의 정기적인 다시 로드는 `INSERT INTO MyLookup VALUES (1, 'Type 1')`와 같은 문을 사용하는 사용자 요구를 제대로 수행할 수 있습니다.  단일 톤 삽입은 대량 로드 수행만큼 효율적이지 않습니다. 

하루 종일 수천 개 이상의 단일 삽입을 수행하는 경우 대량 로드할 수 있도록 로드를 일괄 처리합니다.  파일에 단일 삽입을 추가하는 프로세스를 개발하고 정기적으로 파일을 로드하는 다른 프로세스를 만듭니다.

### <a name="create-statistics-after-the-load"></a>로드 후 통계 만들기

쿼리 성능을 개선하려면 데이터를 처음 로드하거나 데이터 내에 상당한 변화가 생긴 후에, 모든 테이블의 모든 열에서 통계를 만드는 것이 중요합니다.  통계에 대한 자세한 설명은 [통계][통계]를 참조하세요. 다음 예제에서는 Customer_Speed 테이블에 있는 5개의 열에서 통계를 만듭니다.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>저장 키 회전
정기적으로 Blob Storage 액세스 키를 변경하는 것은 좋은 보안 방법입니다. Blob Storage 계정에 두 개의 저장소 키가 있습니다. 키를 전환하기 위해서입니다.

Azure Storage 계정 키를 회전하려면:

1. 보조 저장소 액세스 키를 기반으로 두 번째 데이터베이스 범위 자격 증명을 만듭니다.
2. 이 새 자격 증명을 기반으로 하는 두 번째 외부 데이터 원본을 만듭니다.
3. 새 외부 데이터 원본을 가리키도록 새 외부 테이블을 삭제하고 만듭니다. 

외부 테이블을 새 데이터 원본으로 마이그레이션한 후에 이러한 정리 작업을 수행합니다.

1. 첫 번째 외부 데이터 원본을 삭제합니다.
2. 기본 저장소 액세스 키를 기반으로 하는 첫 번째 데이터베이스 범위 자격 증명을 삭제합니다.
3. Azure에 로그인하고 다음 회전에 사용할 준비가 되도록 기본 액세스 키를 다시 생성합니다.


## <a name="next-steps"></a>다음 단계
로드 프로세스를 모니터링하려면 [DMV를 사용하여 작업 모니터링](sql-data-warehouse-manage-monitor.md)을 참조하세요.



