---
title: '알려진 문제: SQL 데이터베이스로의 온라인 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure 데이터베이스 마이그레이션 서비스를 사용하여 Azure SQL Database로의 온라인 마이그레이션을 통해 알려진 문제/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650778"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Azure SQL Database로의 온라인 마이그레이션으로 알려진 문제/마이그레이션 제한

SQL Server에서 Azure SQL Database로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항은 아래에 설명되어 있습니다.

> [!IMPORTANT]
> SQL Server를 Azure SQL Database로 온라인으로 마이그레이션하면 SQL_variant 데이터 형식의 마이그레이션이 지원되지 않습니다.

### <a name="migration-of-temporal-tables-not-supported"></a>임시 테이블의 마이그레이션은 지원되지 않음

**증상**

원본 데이터베이스가 하나 이상의 임시 테이블로 구성되어 있는 경우 데이터베이스 마이그레이션은 “전체 데이터 로드” 작업 중 실패하고 다음과 같은 메시지가 표시될 수 있습니다.

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![임시 테이블 오류 예제](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**해결**

다음 단계를 사용하세요.

1. 아래 쿼리를 사용하여 원본 스키마의 임시 테이블을 찾습니다.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. 마이그레이션에 대한 테이블을 지정하는 **마이그레이션 설정 구성** 블레이드에서 이러한 테이블을 제외합니다.

3. 마이그레이션 작업을 다시 실행합니다.

**리소스**

자세한 내용은 [임시 테이블](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017) 문서를 참조하세요.

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>테이블의 마이그레이션은 hierarchyid 데이터 형식의 열을 하나 이상 포함합니다.

**증상**

“전체 데이터 로드” 작업 도중 SQL 예외에서 “ntext가 hierarchyid와 호환되지 않음” 메시지가 표시될 수 있습니다.

![hierarchyid 오류 예제](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**해결**

다음 단계를 사용하세요.

1. 아래 쿼리를 사용하여 hierarchyid 데이터 형식의 열을 포함하는 사용자 테이블을 찾습니다.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. 마이그레이션에 대한 테이블을 지정하는 **마이그레이션 설정 구성** 블레이드에서 이러한 테이블을 제외합니다.

3. 마이그레이션 작업을 다시 실행합니다.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>“전체 데이터 로드” 또는 “증분 데이터 동기화”를 수행하는 동안 스키마의 활성 트리거를 사용한 다양한 무결성 위반으로 마이그레이션이 실패합니다.

**해결**

다음 단계를 사용하세요.

1. 아래 쿼리를 사용하여 원본 데이터베이스에서 현재 활성화된 트리거를 찾습니다.

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. [트리거 사용 안 함(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) 문서에 나와 있는 단계를 사용하여 원본 데이터베이스에서 트리거를 사용하지 않도록 설정합니다.

3. 마이그레이션 작업을 다시 실행합니다.

### <a name="support-for-lob-data-types"></a>LOB 데이터 형식에 대한 지원

**증상**

LOB(Large Object) 열의 길이가 32KB보다 큰 경우 데이터는 대상에서 잘릴 수 있습니다. 아래 쿼리를 사용하여 LOB 열의 길이를 확인할 수 있습니다.

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**해결**

32KB보다 큰 LOB 열이 있는 경우 Azure 데이터베이스 마이그레이션 요청의 엔지니어링 팀에 [문의하십시오.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)

### <a name="issues-with-timestamp-columns"></a>타임스탬프 열이 포함된 문제

**증상**

Azure 데이터베이스 마이그레이션 서비스는 원본 타임스탬프 값을 마이그레이션하지 않습니다. 대신 Azure 데이터베이스 마이그레이션 서비스는 대상 테이블에서 새 타임스탬프 값을 생성합니다.

**해결**

원본 테이블에 저장된 정확한 타임스탬프 값을 마이그레이션하기 위해 Azure 데이터베이스 마이그레이션 서비스가 필요한 경우 [Azure Database 마이그레이션 요청의](mailto:AskAzureDatabaseMigrations@service.microsoft.com)엔지니어링 팀에 문의하십시오.

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>데이터 마이그레이션 오류 데이터베이스 상세 상태 블레이드에 대 한 추가 세부 정보를 제공 하지 않습니다.

**증상**

데이터베이스 세부 정보 상태 보기에서 마이그레이션 실패를 발견하면 상단 리본에서 **데이터 마이그레이션 오류** 링크를 선택하면 마이그레이션 실패와 관련된 추가 세부 정보가 제공되지 않을 수 있습니다.

![세부 정보가 없는 데이터 마이그레이션 오류 예제](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**해결**

특정 오류 세부 정보를 얻으려면 다음 단계를 사용합니다.

1. 데이터베이스 세부 정보 상태 블레이드를 닫아 마이그레이션 작업 화면을 표시합니다.

     ![마이그레이션 작업 화면](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. **오류 세부 정보 참조**를 선택하여 마이그레이션 오류를 해결하는 데 도움이 되는 특정 오류 메시지를 확인합니다.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>SQLDB 온라인 마이그레이션에서 지원되지 않는 지리 데이터 형식

**증상**

다음 텍스트를 포함하는 오류 메시지와 함께 마이그레이션이 실패합니다.

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**해결**

Azure 데이터베이스 마이그레이션 서비스는 Azure SQL Database로의 오프라인 마이그레이션을 위한 지리 데이터 형식을 지원하지만 온라인 마이그레이션의 경우 지리 데이터 형식이 지원되지 않습니다. 이 데이터베이스의 온라인 마이그레이션에 Azure Database 마이그레이션 서비스를 사용하기 전에 원본의 데이터 형식을 지원되는 유형으로 변경하는 다른 방법을 시도해 보십시오.

### <a name="supported-editions"></a>지원되는 버전

**증상**

다음 텍스트를 포함하는 오류 메시지와 함께 마이그레이션이 실패합니다.

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**해결**

Azure 데이터베이스 마이그레이션 서비스를 사용하여 Azure SQL Database에 대한 온라인 마이그레이션에 대한 지원은 엔터프라이즈, 표준 및 개발자 버전으로만 확장됩니다. 마이그레이션 프로세스를 시작하기 전에 지원되는 버전을 사용하고 있는지 확인합니다.
