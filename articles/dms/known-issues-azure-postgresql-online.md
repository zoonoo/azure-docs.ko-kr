---
title: '알려진 문제: PostgreSQL에서 Azure Database for PostgreSQL로 온라인 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용 하 여 PostgreSQL에서 Azure Database for PostgreSQL로의 온라인 마이그레이션과의 알려진 문제 및 마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 2d268c5ced0d427216ce4f6a7e9c97c6b5b8b0f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330318"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>PostgreSQL에서 PostgreSQL에 대 한 Azure DB로의 온라인 마이그레이션에 대 한 알려진 문제/마이그레이션 제한 사항

다음 섹션에서는 PostgreSQL에서 Azure Database for PostgreSQL로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항에 대해 설명합니다.

## <a name="online-migration-configuration"></a>온라인 마이그레이션 구성

- 원본 PostgreSQL 서버에서 버전 9.4, 9.5, 9.6, 10 또는 11을 실행 해야 합니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](../postgresql/concepts-supported-versions.md) 문서를 참조하세요.
- 동일한 버전 또는 더 높은 버전으로의 마이그레이션만 지원 됩니다. 예를 들어 Azure Database for PostgreSQL 9.6 또는 10으로 PostgreSQL 9.5를 마이그레이션하는 것은 지원 되지만 PostgreSQL 11에서 PostgreSQL 9.6로의 마이그레이션은 지원 되지 않습니다.
- **원본 PostgreSQL postgresql.conf** 파일에서 논리 복제를 사용하도록 설정하려면 다음 매개 변수를 설정합니다.
  - **wal_level** = 논리적
  - **max_replication_slots** = [마이그레이션에 대 한 최대 데이터베이스 수 이상]; 4 개의 데이터베이스를 마이그레이션하려면 값을 4 이상으로 설정 합니다.
  - **max_wal_senders** = [동시에 실행되는 데이터베이스 수]. 권장 값은 10입니다.
- PostgreSQL pg_hba 원본에 DMS 에이전트 IP를 추가 합니다.
  1. Azure Database Migration Service 인스턴스 프로 비전을 완료 한 후 DMS IP 주소를 기록해 둡니다.
  2. 아래에 나와 있는 것처럼 pg_hba.conf 파일에 IP 주소를 추가합니다.

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- 사용자는 원본 데이터베이스를 호스트 하는 서버에 대 한 복제 역할이 있어야 합니다.
- 원본 및 대상 데이터베이스 스키마가 일치 해야 합니다.
- 대상 Azure Database for PostgreSQL-Single 서버에서 스키마에 외래 키가 없어야 합니다. 외래 키를 삭제하려면 다음 쿼리를 사용합니다.

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    쿼리 결과에서 외래 키 삭제(두 번째 열)를 실행합니다.

- 대상 Azure Database for PostgreSQL-Single 서버에서 스키마는 트리거를 포함 하지 않아야 합니다. 대상 데이터베이스에서 트리거를 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>데이터 형식 제한 사항

  **제한**사항: 테이블에 기본 키가 없는 경우 변경 내용이 대상 데이터베이스와 동기화 되지 않을 수 있습니다.

  **해결 방법**: 마이그레이션을 계속할 수 있도록 일시적으로 테이블에 대한 기본 키를 설정합니다. 데이터 마이그레이션이 완료된 후 기본 키를 제거할 수 있습니다.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>AWS RDS PostgreSQL에서 온라인으로 마이그레이션하는 경우의 제한 사항

AWS RDS PostgreSQL에서 Azure Database for PostgreSQL로 온라인 마이그레이션을 수행 하려고 할 때 다음과 같은 오류가 발생할 수 있습니다.

- **오류**: '{database}' 데이터베이스의 '{table}' 테이블에 있는 '{column}' 열의 기본값이 원본 서버와 대상 서버에서 서로 다릅니다. 원본은 '{value on source}'이고 대상은 '{value on target}'입니다.

  **제한**사항:이 오류는 열 스키마의 기본값이 원본 데이터베이스와 대상 데이터베이스 간에 다를 때 발생 합니다.
  **해결 방법**: 대상의 스키마가 원본의 스키마와 일치 하는지 확인 합니다. 스키마 마이그레이션에 대 한 자세한 내용은 [Azure PostgreSQL 온라인 마이그레이션 설명서](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)를 참조 하세요.

- **오류**: 대상 데이터베이스 '{database}'에는 '{number of tables}'개의 테이블이 있고, 원본 데이터베이스 '{database}'에는 '{number of tables}'개의 테이블이 있습니다. 원본과 대상 데이터베이스의 테이블 수가 같아야 합니다.

  **제한 사항**:이 오류는 원본 데이터베이스와 대상 데이터베이스 간에 테이블 수가 다를 때 발생 합니다.

  **해결 방법**: 대상의 스키마가 원본의 스키마와 일치 하는지 확인 합니다. 스키마 마이그레이션에 대 한 자세한 내용은 [Azure PostgreSQL 온라인 마이그레이션 설명서](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)를 참조 하세요.

- **오류:** 원본 데이터베이스 {database}이 (가) 비어 있습니다.

  **제한 사항**:이 오류는 원본 데이터베이스가 비어 있을 때 발생 합니다. 잘못 된 데이터베이스를 원본으로 선택 했기 때문일 수 있습니다.

  **해결 방법**: 마이그레이션을 위해 선택한 원본 데이터베이스를 다시 한 번 확인 한 후 다시 시도 하십시오.

- **오류:** 대상 데이터베이스 {database}이 (가) 비어 있습니다. 스키마를 마이그레이션하세요.

  **제한**사항:이 오류는 대상 데이터베이스에 스키마가 없을 때 발생 합니다. 대상의 스키마가 원본의 스키마와 일치 하는지 확인 합니다.
  **해결 방법**: 대상의 스키마가 원본의 스키마와 일치 하는지 확인 합니다. 스키마 마이그레이션에 대 한 자세한 내용은 [Azure PostgreSQL 온라인 마이그레이션 설명서](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)를 참조 하세요.

## <a name="other-limitations"></a>기타 제한 사항

- 데이터베이스 이름은 세미콜론(;)을 포함할 수 없습니다.
- 캡처된 테이블에는 기본 키가 있어야 합니다. 테이블에 기본 키가 없는 경우 DELETE 및 UPDATE 레코드 작업의 결과를 예측할 수 없습니다.
- 기본 키 세그먼트 업데이트는 무시됩니다. 이 경우 해당 업데이트를 적용하면 대상이 업데이트 작업을 모든 행을 업데이트하지 않은 업데이트로 식별하므로 예외 테이블에 레코드가 작성됩니다.
- 이름은 같고 대/소문자만 다른 여러 테이블(예: table1, TABLE1, Table1)을 마이그레이션하면 예기치 않은 동작이 발생할 수 있으므로 해당 마이그레이션은 지원되지 않습니다.
- 변경 처리 [만들기 | ALTER | DROP | TRUNCATE] 테이블 DDLs가 지원 되지 않습니다.
- Azure Database Migration Service에서 단일 마이그레이션 작업은 최대 4 개의 데이터베이스를 수용할 수 있습니다.
