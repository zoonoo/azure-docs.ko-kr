---
title: Azure SQL Database로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항에 대한 문서 | Microsoft Docs
description: Azure SQL Database로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 1f8e3ede4140ab5346285f7c247864f8ef8e2d48
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889156"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Azure SQL DB로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항

SQL Server에서 Azure SQL Database로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항은 아래에 설명되어 있습니다.

- 임시 테이블의 마이그레이션은 지원되지 않음

    **증상**

    원본 데이터베이스가 하나 이상의 임시 테이블로 구성되어 있는 경우 데이터베이스 마이그레이션은 “전체 데이터 로드” 작업 중 실패하고 다음과 같은 메시지가 표시될 수 있습니다.

    { "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
 
   ![임시 테이블 오류 예제](media\known-issues-azure-sql-online\dms-temporal-tables-errors.png)

   **해결 방법**

   1. 아래 쿼리를 사용하여 원본 스키마의 임시 테이블을 찾습니다.
        ``` 
       select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
        ```
   2. 마이그레이션에 대한 테이블을 지정하는 **마이그레이션 설정 구성** 블레이드에서 이러한 테이블을 제외합니다.
   3. 마이그레이션 작업을 다시 실행합니다.

    **리소스**

    자세한 내용은 [임시 테이블](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017) 문서를 참조하세요.
 
- 테이블의 마이그레이션은 hierarchyid 데이터 형식의 열을 하나 이상 포함합니다.

    **증상**

    “전체 데이터 로드” 작업 도중 SQL 예외에서 “ntext가 hierarchyid와 호환되지 않음” 메시지가 표시될 수 있습니다.
     
    ![hierarchyid 오류 예제](media\known-issues-azure-sql-online\dms-hierarchyid-errors.png)

    **해결 방법**

    1. 아래 쿼리를 사용하여 hierarchyid 데이터 형식의 열을 포함하는 사용자 테이블을 찾습니다.

        ``` 
        select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
        ``` 

    2.  마이그레이션에 대한 테이블을 지정하는 **마이그레이션 설정 구성** 블레이드에서 이러한 테이블을 제외합니다.
    3.  마이그레이션 작업을 다시 실행합니다.

- “전체 데이터 로드” 또는 “증분 데이터 동기화”를 수행하는 동안 스키마의 활성 트리거를 사용한 다양한 무결성 위반으로 마이그레이션이 실패합니다.

    **해결 방법**
    1. 아래 쿼리를 사용하여 원본 데이터베이스에서 현재 활성화된 트리거를 찾습니다.
        ```
        select * from sys.triggers where is_disabled =0
        ```
    2.  [트리거 사용 안 함(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) 문서에 나와 있는 단계를 사용하여 원본 데이터베이스에서 트리거를 사용하지 않도록 설정합니다.
    3.  마이그레이션 작업을 다시 실행합니다.

- LOB 데이터 형식에 대한 지원

    **증상**

    LOB(Large Object) 열의 길이가 32KB보다 큰 경우 데이터는 대상에서 잘릴 수 있습니다. 아래 쿼리를 사용하여 LOB 열의 길이를 확인할 수 있습니다. 

    ``` 
    SELECT max(len(ColumnName)) as LEN from TableName
    ```

    **해결 방법**

    32KB보다 큰 LOB 열이 있는 경우 [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)으로 엔지니어링 팀에 문의하세요.

- 타임스탬프 열이 포함된 문제

    **증상**

    DMS는 원본 타임스탬프 값을 마이그레이션하지 않습니다. 대신 DMS는 대상 테이블에 새 타임스탬프 값을 생성합니다.

    **해결 방법**

    원본 테이블에 저장된 정확한 타임스탬프 값을 마이그레이션하기 위해 DMS가 필요한 경우 [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)으로 엔지니어링 팀에 문의하세요.

- 데이터 마이그레이션 오류는 데이터베이스 세부 정보 상태 블레이드에 추가 세부 정보를 제공하지 않습니다.

    **증상**

    데이터베이스 세부 정보 상태 보기에 마이그레이션 오류가 발생한 경우 위쪽 리본에서 **데이터 마이그레이션 오류** 링크를 선택하면 마이그레이션 오류 관련 추가 정보가 제공되지 않을 수도 있습니다.

     ![세부 정보가 없는 데이터 마이그레이션 오류 예제](media\known-issues-azure-sql-online\dms-data-migration-errors-no-details.png)

    **해결 방법**

    특정 오류 세부 정보를 가져오려면 다음 단계를 수행합니다.

    1.  데이터베이스 세부 정보 상태 블레이드를 닫아 마이그레이션 작업 화면을 표시합니다.

     ![마이그레이션 작업 화면](media\known-issues-azure-sql-online\dms-migration-activity-screen.png)

    2. **오류 세부 정보 참조**를 선택하여 마이그레이션 오류를 해결하는 데 도움이 되는 특정 오류 메시지를 확인합니다.
