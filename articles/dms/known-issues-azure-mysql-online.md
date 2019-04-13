---
title: Azure Database for MySQL로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항에 대한 문서 | Microsoft Docs
description: Azure Database for MySQL로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: cf5215ff5acaf08125cf280103ba8ff0123dc116
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523820"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Azure DB for MySQL로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항

MySQL에서 Azure Database for MySQL로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항은 다음 섹션에 설명되어 있습니다. 

## <a name="online-migration-configuration"></a>온라인 마이그레이션 구성
- 원본 MySQL 서버 버전은 5.6.35, 5.7.18 이상이어야 합니다.
- Azure Database for MySQL은 다음을 지원합니다.
    - MySQL 커뮤니티 버전
    - InnoDB 엔진
- 동일한 버전 마이그레이션. Azure Database for MySQL 5.7로의 MySQL 5.6 마이그레이션은 지원되지 않습니다.
- my.ini(Windows) 또는 my.cnf(Unix)로 이진 로깅을 사용
    - Server_id를 1 이상의 숫자로 설정(예: Server_id=1(MySQL 5.6에만 해당))
    - 로그 bin 설정 = \<경로 > (MySQL 5.6)에 해당
    - binlog_format = row로 설정
    - Expire_logs_days = 5(권장됨 - MySQL 5.6에만 해당)
- 사용자에게 ReplicationAdmin 역할이 있어야 함.
- 원본 MySQL 데이터베이스에 대해 정의된 데이터 정렬은 대상 Azure Database for MySQL에 정의된 것과 동일합니다.
- 스키마는 원본 MySQL 데이터베이스와 Azure Database for MySQL의 대상 데이터베이스 간에 일치해야 합니다.
- 대상 Azure Database for MySQL의 스키마에는 외래 키가 없어야 합니다. 외래 키를 삭제하려면 다음 쿼리를 사용합니다.
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    쿼리 결과에서 외래 키 삭제(두 번째 열)를 실행합니다.
- 대상 Azure Database for MySQL의 스키마에는 트리거가 없어야 합니다. 대상 데이터베이스에서 트리거를 삭제하려면:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>데이터 형식 제한 사항
- **제한 사항**: 원본 MySQL 데이터베이스에서 JSON 데이터 형식이 있는 경우 지속적인 동기화를 수행하는 동안 마이그레이션이 실패합니다.

    **해결 방법**: JSON 데이터 형식을 원본 MySQL 데이터베이스의 medium text 또는 Longtext로 수정합니다.

- **제한 사항**: 테이블에 기본 키가 없는 경우 지속적인 동기화가 실패합니다.
 
    **해결 방법**: 마이그레이션을 계속할 수 있도록 일시적으로 테이블에 대한 기본 키를 설정합니다. 데이터 마이그레이션이 완료된 후 기본 키를 제거할 수 있습니다.

## <a name="lob-limitations"></a>LOB 제한 사항
LOB(Large Object) 열은 크기가 커질 수 있는 열입니다. MySQL의 경우 Medium text, Longtext, Blob, Mediumblob, Longblob 등은 LOB의 일부 데이터 형식입니다.

- **제한 사항**: LOB 데이터 형식이 기본 키로 사용되는 경우 마이그레이션이 실패합니다.

    **해결 방법**: 기본 키를 다른 데이터 형식 또는 LOB가 아닌 열로 바꿉니다.

- **제한 사항**: LOB(Large Object) 열의 길이가 32KB보다 큰 경우 데이터는 대상에서 잘릴 수 있습니다. 이 쿼리를 사용하여 LOB 열의 길이를 확인할 수 있습니다.
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **해결 방법**: 32KB 보다 큰 LOB 개체에 있는 경우 엔지니어링 팀에 문의 [Azure 데이터베이스 마이그레이션을 요청](mailto:AskAzureDatabaseMigrations@service.microsoft.com)합니다. 

## <a name="other-limitations"></a>기타 제한 사항
- 암호 문자열의 시작과 끝에 여는 중괄호 및 닫는 중괄호 {  }가 있는 암호 문자열은 지원되지 않습니다. 이 제한 사항은 원본 MySQL 및 대상 Azure Database for MySQL에 모두 적용됩니다.
- 다음 DDL은 지원되지 않습니다.
    - 모든 파티션 DDL
    - 테이블 삭제
    - 테이블 이름 변경
- *alter table <table_name> add column <column_name>* 문을 사용하여 열을 테이블의 시작 또는 중간에 추가하는 것은 지원되지 않습니다. *alter table <table_name> add column <column_name>* 은 열을 테이블의 끝에 추가합니다.
- 열 데이터의 일부에서만 생성된 인덱스는 지원되지 않습니다. 다음 문은 열 데이터의 일부만 사용하여 인덱스를 만드는 예제입니다.
    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- DMS에서 하나의 단일 마이그레이션 작업에서 마이그레이션할 데이터베이스의 한계는 4개입니다.
