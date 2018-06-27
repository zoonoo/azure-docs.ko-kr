---
title: Azure SQL 데이터 동기화에서 스키마 변경 복제 자동화 | Microsoft Docs
description: Azure SQL 데이터 동기화에서 스키마 변경 복제를 자동화하는 방법을 알아봅니다.
services: sql-database
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: Xiaochen.Wu
ms.reviewer: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 6dbbd5c76953b23225951ffb655ac4f5b71fc1e0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232127"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Azure SQL 데이터 동기화에서 스키마 변경 복제 자동화

SQL 데이터 동기화를 사용하면 Azure SQL Database와 온-프레미스 SQL Server 간에 단방향 또는 양방향으로 데이터를 동기화할 수 있습니다. SQL 데이터 동기화의 현재 제한 사항 중 하나는 스키마 변경 복제에 대한 지원이 부족하다는 것입니다. 테이블 스키마를 변경할 때마다 허브 및 모든 구성원을 포함하여 모든 엔드포인트에서 수동으로 변경 내용을 적용한 다음, 동기화 스키마를 업데이트해야 합니다.

이 문서에서는 스키마 변경을 모든 SQL 데이터 동기화 엔드포인트에 자동으로 복제하는 솔루션을 소개합니다.
1. 이 솔루션은 DDL 트리거를 사용하여 스키마 변경 내용을 추적합니다.
2. 트리거는 스키마 변경 명령을 추적 테이블에 삽입합니다.
3. 이 추적 테이블 테이블은 데이터 동기화 서비스를 사용하여 모든 엔드포인트에 동기화됩니다.
4. 삽입 후 DML 트리거는 다른 엔드포인트에서 스키마 변경 내용을 적용하는 데 사용됩니다.

이 문서에서는 스키마 변경의 예로 ALTER TABLE을 사용하지만 이 솔루션은 다른 유형의 스키마 변경에도 사용할 수 있습니다.

> [!IMPORTANT]
> 동기화 환경에서 자동화된 스키마 변경 복제를 구현하기 전에 특히 [문제 해결](#troubleshooting) 및 [기타 고려 사항](#other)에 대한 섹션을 주의 깊게 참조하는 것이 좋습니다. 또한 [SQL 데이터 동기화를 사용하여 여러 클라우드와 온-프레미스 데이터베이스의 데이터 동기화](sql-database-sync-data.md)도 참조하는 것이 좋습니다. 일부 데이터베이스 작업은 이 문서에서 설명하는 솔루션을 손상시킬 수 있습니다. 이러한 문제를 해결하려면 SQL Server 및 Transact-SQL에 대한 추가적인 도메인 지식이 필요할 수 있습니다.

![스키마 변경 복제 자동화](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>자동화된 스키마 변경 복제 설정

### <a name="create-a-table-to-track-schema-changes"></a>스키마 변경을 추적하는 테이블 만들기

동기화 그룹의 모든 데이터베이스에서 스키마 변경을 추적하는 테이블을 만듭니다.

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

이 테이블에는 스키마 변경 순서를 추적하는 ID 열이 있습니다. 필요한 경우 더 많은 정보를 기록하기 위해 더 많은 필드를 추가할 수 있습니다.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>스키마 변경 기록을 추적하는 테이블 만들기

모든 엔드포인트에서 가장 최근에 적용된 스키마 변경 명령의 ID를 추적하는 테이블을 만듭니다.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>스키마가 변경된 데이터베이스에 ALTER TABLE DDL 트리거 테이블 만들기

ALTER TABLE 작업에 대한 DDL 트리거를 만듭니다. 스키마가 변경된 데이터베이스에 이 트리거를 만들기만 하면 됩니다. 충돌을 방지하려면 동기화 그룹의 한 데이터베이스에서만 스키마 변경을 허용합니다.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

트리거는 각 ALTER TABLE 명령에 대한 스키마 변경 내용 추적 테이블에 레코드를 삽입합니다. 스키마 변경은 데이터 동기화 서비스에서 발생할 가능성이 가장 높으므로 이 예제에서는 **DataSync** 스키마에서 수행된 스키마 변경을 복제하지 않도록 방지하는 필터를 추가합니다. 특정 유형의 스키마 변경만 복제하려면 고급 필터를 추가합니다.

또한 다른 유형의 스키마 변경을 복제하는 트리거를 더 많이 추가할 수도 있습니다. 예를 들어 CREATE_PROCEDURE, ALTER_PROCEDURE 및 DROP_PROCEDURE 트리거를 만들어 변경을 저장 프로시저에 복제합니다.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>다른 엔드포인트에 삽입하는 동안 스키마 변경 내용을 적용하는 트리거 만들기

이 트리거는 다른 엔드포인트에 동기화될 때 스키마 변경 명령을 실행합니다. 스키마가 변경된 위치(즉, 이전 단계에서 `AlterTableDDLTrigger` DDL 트리거를 만든 데이터베이스)를 제외한 모든 엔드포인트에서 이 트리거를 만들어야 합니다.

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

이 트리거는 삽입 후에 실행되며 현재 명령이 다음에 실행되어야 하는지 여부를 확인합니다. 코드 논리를 사용하면 스키마 변경 명령문을 건너뛰지 않으며 삽입 순서가 잘못된 경우에도 모든 변경 내용을 적용할 수 있습니다.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>모든 엔드포인트에 스키마 변경 내용 추적 테이블 동기화

기존 동기화 그룹 또는 새 동기화 그룹을 사용하여 스키마 변경 내용 추적 테이블을 모든 엔드포인트에 동기화할 수 있습니다. 특히 단방향 동기화를 사용하는 경우 추적 테이블의 변경 내용을 모든 엔드포인트에 동기화할 수 있는지 확인합니다.

스키마 변경 기록 테이블은 다른 엔드포인트에서 서로 다른 상태를 유지하므로 해당 테이블은 동기화하지 마세요.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>동기화 그룹에 스키마 변경 내용 적용

DDL 트리거를 만든 데이터베이스에서 수행된 스키마 변경만 복제됩니다. 다른 데이터베이스에서 수행된 스키마 변경은 복제되지 않습니다.

스키마 변경이 모든 엔드포인트에 복제된 후에는 새 열 동기화를 시작하거나 중지하도록 동기화 스키마를 업데이트하는 추가 단계도 수행해야 합니다.

#### <a name="add-new-columns"></a>새 열 추가

1.  스키마를 변경합니다.

2.  트리거를 만드는 단계를 완료할 때까지 새 열이 관련된 모든 데이터를 변경하지 않도록 방지합니다.

3.  스키마 변경 내용이 모든 엔드포인트에 적용될 때까지 기다립니다.

4.  데이터베이스 스키마를 새로 고치고 동기화 스키마에 새 열을 추가합니다.

5.  새 열의 데이터는 다음 동기화 작업 중에 동기화됩니다.

#### <a name="remove-columns"></a>열 제거

1.  동기화 스키마에서 열을 제거합니다. 데이터 동기화는 이러한 열의 데이터 동기화를 중지합니다.

2.  스키마를 변경합니다.

3.  데이터베이스 스키마를 새로 고칩니다.

#### <a name="update-data-types"></a>데이터 형식 업데이트

1.  스키마를 변경합니다.

2.  스키마 변경 내용이 모든 엔드포인트에 적용될 때까지 기다립니다.

3.  데이터베이스 스키마를 새로 고칩니다.

4.  새 데이터 형식과 이전 데이터 형식이 완전히 호환되지 않으면(예: `int`에서 `bigint`로 변경하는 경우) 트리거를 만드는 단계가 완료되기 전에 동기화가 실패할 수 있습니다. 이 경우 다시 시도하면 동기화가 성공합니다.

#### <a name="rename-columns-or-tables"></a>열 또는 테이블 이름 바꾸기

열 또는 테이블의 이름을 변경하면 데이터 동기화가 중지됩니다. 새 테이블 또는 열을 만들고, 데이터를 다시 채운 다음, 이름을 바꾸는 대신 이전 테이블 또는 열을 삭제합니다.

#### <a name="other-types-of-schema-changes"></a>다른 유형의 스키마 변경

다른 유형의 스키마 변경(예: 저장 프로시저 만들기 또는 인덱스 삭제)에서는 동기화 스키마를 업데이트할 필요가 없습니다.

## <a name="troubleshoot"></a> 자동화된 스키마 변경 복제 문제 해결

이 문서에서 설명하는 복제 논리는 일부 상황에서 작업을 중지합니다. 예를 들어 Azure SQL Database에서 지원되지 않는 온-프레미스 데이터베이스에서 스키마를 변경한 경우가 있습니다. 이 경우 스키마 변경 내용 추적 테이블의 동기화가 실패합니다. 이 문제는 다음과 같이 수동으로 해결해야 합니다.

1.  DDL 트리거를 비활성화하고, 문제가 해결될 때까지 스키마를 변경하지 않습니다.

2.  문제가 발생한 엔드포인트 데이터베이스에서 스키마를 변경할 수 없는 엔드포인트의 AFTER INSERT 트리거를 비활성화합니다. 이 작업을 수행하면 스키마 변경 명령을 동기화할 수 있습니다.

3.  동기화를 트리거하여 스키마 변경 내용 추적 테이블을 동기화합니다.

4.  문제가 발생한 엔드포인트 데이터베이스에서 스키마 변경 기록 테이블을 쿼리하여 마지막으로 적용된 스키마 변경 명령의 ID를 가져옵니다.

5.  스키마 변경 내용 추적 테이블을 쿼리하여 이전 단계에서 검색한 ID 값보다 큰 ID가 있는 모든 명령을 나열합니다.

    a.  엔드포인트 데이터베이스에서 실행할 수 없는 명령은 무시합니다. 스키마 불일치를 처리해야 합니다. 불일치가 응용 프로그램에 영향을 주는 경우 원래 스키마 변경 내용을 되돌립니다.

    나.  적용해야 하는 명령을 수동으로 적용합니다.

6.  스키마 변경 기록 테이블을 업데이트하고, 마지막으로 적용된 ID를 올바른 값으로 설정합니다.

7.  스키마가 최신인지 다시 확인합니다.

8.  두 번째 단계에서 비활성화된 AFTER INSERT 트리거를 다시 활성화합니다.

9.  첫 번째 단계에서 비활성화된 DDL 트리거를 다시 활성화합니다.

스키마 변경 내용 추적 테이블에서 레코드를 정리하려면 TRUNCATE 대신 DELETE를 사용합니다. DBCC CHECKIDENT를 사용하여 스키마 변경 내용 추적 테이블에서 ID 열을 다시 시드하지 않습니다. 새 스키마 변경 내용 추적 테이블을 만들고, 다시 시드해야 하는 경우 DDL 트리거에서 테이블 이름을 업데이트할 수 있습니다.

## <a name="other"></a> 기타 고려 사항

-   허브 및 구성원 데이터베이스를 구성하는 데이터베이스 사용자에게는 스키마 변경 명령을 실행할 수 있는 충분한 권한이 있어야 합니다.

-   선택한 테이블 또는 작업에서 스키마 변경만 복제하도록 DDL 트리거에 고급 필터를 추가할 수 있습니다.

-   DDL 트리거를 만든 데이터베이스에서만 스키마를 변경할 수 있습니다.

-   온-프레미스 SQL Server 데이터베이스를 변경하는 경우 Azure SQL Database에서 스키마 변경이 지원되는지 확인합니다.

-   DDL 트리거를 만든 데이터베이스 이외의 데이터베이스에서 스키마 변경이 수행되는 경우 변경 내용이 복제되지 않습니다. 이 문제를 방지하기 위해 DDL 트리거를 만들어 다른 엔드포인트에서 변경하지 못하도록 차단할 수 있습니다.

-   스키마 변경 내용 추적 테이블의 스키마를 변경해야 하는 경우, 변경하기 전에 DDL 트리거를 비활성화한 다음, 모든 엔드포인트에 변경 내용을 수동으로 적용합니다. 동일한 테이블의 AFTER INSERT 트리거에서 스키마를 업데이트하는 작업은 작동하지 않습니다.

-   DBCC CHECKIDENT를 사용하여 ID 열을 다시 시드하지 않습니다.

-   TRUNCATE를 사용하여 스키마 변경 내용 추적 테이블의 데이터를 정리하지 않습니다.
