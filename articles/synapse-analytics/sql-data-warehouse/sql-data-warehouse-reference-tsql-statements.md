---
title: T-SQL 문
description: Synapse SQL 풀에서 지원되는 T-SQL 문에 대한 설명서에 대한 링크입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 011a850a44948bf5eee28a40d59dcf7672d866e8
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586360"
---
# <a name="t-sql-statements-supported-in-synapse-sql-pool"></a>시냅스 SQL 풀에서 지원되는 T-SQL 문

Synapse SQL 풀에서 지원되는 T-SQL 문에 대한 설명서에 대한 링크입니다.

## <a name="data-definition-language-ddl-statements"></a>데이터 정의 언어(DDL) 문

* [데이터베이스 변경](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [구체화된 뷰 변경(미리](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) 보기) 
* [ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTER SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [테이블 변경](https://msdn.microsoft.com/library/ms190273.aspx)
* [CREATE COLUMNSTORE INDEX](https://msdn.microsoft.com/library/gg492153.aspx)
* [데이터베이스 만들기](https://msdn.microsoft.com/library/mt204021.aspx)
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)
* [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
* [외부 파일 형식 만들기](https://msdn.microsoft.com/library/dn935026.aspx)
* [외부 테이블 만들기](https://msdn.microsoft.com/library/dn935021.aspx)
* [함수 만들기](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE  INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [구체화된 뷰를 선택으로 만들기(미리](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) 보기) 
* [CREATE PROCEDURE](https://msdn.microsoft.com/library/ms187926.aspx)
* [CREATE SCHEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [통계 만들기](https://msdn.microsoft.com/library/ms188038.aspx)
* [테이블 만들기](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [뷰 만들기](https://msdn.microsoft.com/library/ms187956.aspx)
* [워크로드 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/mt146367.aspx)
* [DROP EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/mt146379.aspx)
* [DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [DROP PROCEDURE](https://msdn.microsoft.com/library/ms174969.aspx)
* [DROP STATISTICS](https://msdn.microsoft.com/library/ms175075.aspx)
* [드롭 테이블](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [드롭 뷰](https://msdn.microsoft.com/library/ms173492.aspx)
* [워크로드 분류자 삭제](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [이름을 바꿀](https://msdn.microsoft.com/library/mt631611.aspx)
* [RESULT_SET_CACHING 설정](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [트런케이트 테이블](https://msdn.microsoft.com/library/ms177570.aspx)
* [업데이트 통계](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>데이터 조작 언어(DML) 문

* [삭제](https://msdn.microsoft.com/library/ms189835.aspx)
* [삽입](https://msdn.microsoft.com/library/ms174335.aspx)
* [업데이트](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>데이터베이스 콘솔 명령

* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC 드롭결과셋캐시(미리](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) 보기)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC 쇼결과캐시스페이스사용(미리](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) 보기)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>쿼리 문

* [선택](https://msdn.microsoft.com/library/ms189499.aspx)
* [common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT 및 INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [설명](https://msdn.microsoft.com/library/mt631615.aspx)
* [FROM](https://msdn.microsoft.com/library/ms177634.aspx)
* [PIVOT 및 UNPIVOT 사용](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROUP BY](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDER BY](https://msdn.microsoft.com/library/ms188385.aspx)
* [옵션](https://msdn.microsoft.com/library/ms190322.aspx)
* [연합](https://msdn.microsoft.com/library/ms180026.aspx)
* [어디](https://msdn.microsoft.com/library/ms188047.aspx)
* [TOP](https://msdn.microsoft.com/library/ms189463.aspx)
* [별칭 지정](https://msdn.microsoft.com/library/mt631614.aspx)
* [검색 조건](https://msdn.microsoft.com/library/ms173545.aspx)
* [하위 쿼리](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>보안 문

* 사용 권한: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ALTER AUTHORIZATION](https://msdn.microsoft.com/library/ms187359.aspx)
* [ALTER CERTIFICATE](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTER DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTER MASTER KEY](https://msdn.microsoft.com/library/ms186937.aspx)
* [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
* [BACKUP CERTIFICATE](https://msdn.microsoft.com/library/ms178578.aspx)
* [CLOSE MASTER KEY](https://msdn.microsoft.com/library/ms188387.aspx)
* [CREATE CERTIFICATE](https://msdn.microsoft.com/library/ms187798.aspx)
* [CREATE DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb677241.aspx)
* [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx)
* [마스터 키 만들기](https://msdn.microsoft.com/library/ms174382.aspx)
* [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx)
* [사용자 만들기](https://msdn.microsoft.com/library/ms173463.aspx)
* [DROP CERTIFICATE](https://msdn.microsoft.com/library/ms179906.aspx)
* [DROP DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [DROP MASTER KEY](https://msdn.microsoft.com/library/ms180071.aspx)
* [DROP ROLE](https://msdn.microsoft.com/library/ms174988.aspx)
* [DROP USER](https://msdn.microsoft.com/library/ms189438.aspx)
* [OPEN MASTER KEY](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>다음 단계

자세한 참조 정보는 [시냅스 SQL 풀의 T-SQL 언어 요소](sql-data-warehouse-reference-tsql-language-elements.md)및 [시냅스 SQL 풀의 시스템 보기를](sql-data-warehouse-reference-tsql-system-views.md)참조하십시오.
