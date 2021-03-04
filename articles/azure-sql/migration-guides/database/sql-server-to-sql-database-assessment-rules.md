---
title: SQL Server SQL Database 마이그레이션에 대 한 평가 규칙
description: Azure SQL Database로 마이그레이션하기 전에 해결 해야 하는 원본 SQL Server 인스턴스의 문제를 식별 하는 평가 규칙입니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: bf825572226bf5d7432fd3ad825f2f3a13355c53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054732"
---
# <a name="assessment-rules-for-sql-server-to-sql-database-migration"></a>SQL Server SQL Database 마이그레이션에 대 한 평가 규칙
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

마이그레이션 도구는 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션하기 전에 해결 해야 하는 문제를 식별 하는 다양 한 평가 규칙을 실행 하 여 원본 SQL Server 인스턴스의 유효성을 검사 합니다. 

이 문서에서는 SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션할 수 있는 가능성을 평가 하는 데 사용 되는 규칙 목록을 제공 합니다. 


## <a name="bulk-insert"></a>대량 삽입<a id="BulkInsert"></a>

**제목: Azure blob 데이터 원본이 아닌 BULK INSERT는 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
파일 공유 또는 Windows 폴더에 액세스할 수 Azure SQL Database. Azure blob을 참조 하지 않는 BULK INSERT 문의 특정 사용에 대해서는 "영향을 받는 개체" 섹션을 참조 하세요. 소스가 Azure blob storage가 아닌 ' BULK INSERT ' 인 개체는 Azure SQL Database으로 마이그레이션한 후에 작동 하지 않습니다. 


**권유**   
Azure SQL Database로 마이그레이션할 때 대신 로컬 파일 또는 파일 공유를 사용 하 여 Azure blob storage의 파일을 사용 하는 BULK INSERT 문을 변환 해야 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

## <a name="compute-clause"></a>Compute 절<a id="ComputeClause"></a>

**제목: COMPUTE 절이 중단 되었으며 제거 되었습니다.**   
**범주**: 경고   

**설명**   
COMPUTE 절은 결과 집합의 끝에 추가 요약 열로 표시 되는 합계를 생성 합니다. 그러나이 절은 Azure SQL Database에서 더 이상 지원 되지 않습니다. 


**권유**   
대신 ROLLUP 연산자를 사용 하 여 T-sql 모듈을 다시 작성 해야 합니다. 아래 코드에서는 계산을 ROLLUP으로 대체 하는 방법을 보여 줍니다. 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="change-data-capture-cdc"></a>CDC (변경 데이터 캡처)<a id="CDC"></a>

**제목: CDC (변경 데이터 캡처)는 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   


**설명**   
CDC (변경 데이터 캡처)는 Azure SQL Database에서 지원 되지 않습니다. 변경 내용 추적를 대신 사용할 수 있는지 평가 합니다.  또는 azure SQL Managed Instance로 마이그레이션하거나 Azure Virtual Machines에서 SQL Server 합니다. 


**권유**   
CDC (변경 데이터 캡처)는 Azure SQL Database에서 지원 되지 않습니다. 변경 내용 추적를 대신 사용할 수 있는지 평가 하거나 Azure SQL Managed Instance로 마이그레이션하는 것이 좋습니다.

추가 정보: [AZURE SQL 변경 내용 추적 사용](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>CLR 어셈블리<a id="ClrAssemblies"></a>

**제목: SQL CLR 어셈블리는 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   


**설명**   
Azure SQL Database는 SQL CLR 어셈블리를 지원 하지 않습니다. 


**권유**   
현재 Azure SQL Database에서이를 달성할 수 있는 방법은 없습니다. 권장 되는 대체 솔루션을 사용 하려면 Azure SQL Database에서 지 원하는 어셈블리만 사용 하도록 응용 프로그램 코드와 데이터베이스를 변경 해야 합니다. 또는 azure 가상 컴퓨터에서 Azure SQL Managed Instance 또는 SQL Server로 마이그레이션

추가 정보: [SQL Database에서 지원 되지 않는 transact-sql 차이점](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>암호화 공급자<a id="CryptographicProvider"></a>

**제목: Azure SQL Database에서 지원 되지 않는 CREATE 암호화 공급자 또는 ALTER 암호화 공급자를 사용 했습니다.**   
**범주**: 문제   

**설명**   
Azure SQL Database는 파일에 액세스할 수 없기 때문에 암호화 공급자 문을 지원 하지 않습니다. 암호화 공급자 문의 특정 용도는 영향을 받는 개체 섹션을 참조 하세요. `CREATE CRYPTOGRAPHIC PROVIDER`또는를 사용 `ALTER CRYPTOGRAPHIC PROVIDER` 하는 개체는 Azure SQL Database로 마이그레이션한 후 제대로 작동 하지 않습니다.  


**권유**   
또는를 사용 하 여 개체를 검토 `CREATE CRYPTOGRAPHIC PROVIDER` `ALTER CRYPTOGRAPHIC PROVIDER` 합니다. 필요한 모든 개체에서 이러한 기능의 사용을 제거 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션

## <a name="cross-database-references"></a>데이터베이스 간 참조<a id="CrossDataseReferences"></a>

**제목: 데이터베이스 간 쿼리는 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
이 서버의 데이터베이스는 Azure SQL Database에서 지원 되지 않는 데이터베이스 간 쿼리를 사용 합니다. 


**권유**   
Azure SQL Database는 데이터베이스 간 쿼리를 지원 하지 않습니다. 다음 작업을 수행 하는 것이 좋습니다. 
- Azure SQL Database에 종속 데이터베이스를 마이그레이션하고 Elastic Database 쿼리 (현재 미리 보기 상태) 기능을 사용 하 여 Azure SQL database에서 쿼리 합니다. 
- 다른 데이터베이스의 종속 데이터 집합을 마이그레이션하는 데이터베이스로 이동 합니다. 
- Azure SQL Managed Instance로 마이그레이션합니다.
- Azure 가상 머신에서 SQL Server로 마이그레이션합니다. 

추가 정보: [탄력적 데이터베이스 쿼리 (미리 보기) Azure SQL Database 확인](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>데이터베이스 호환성<a id="DbCompatLevelLowerThan100"></a>

**제목: Azure SQL Database 100 미만의 호환성 수준을 지원 하지 않습니다.**   
**범주**: 경고   

**설명**   
데이터베이스 호환성 수준은 동일한 업그레이드 전 데이터베이스 호환성 수준을 유지 하 여 응용 프로그램의 기능 상태를 유지 하면서 SQL Server 데이터베이스 엔진를 업그레이드할 수 있도록 하 여 데이터베이스 현대화을 지 원하는 데 유용한 도구입니다. Azure SQL Database 100 미만의 호환성 수준을 지원 하지 않습니다. 


**권유**   
Azure SQL Managed Instance에서 데이터베이스 호환성 수준이 100으로 업그레이드 될 때 응용 프로그램 기능이 그대로 유지 되는지 여부를 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션

## <a name="database-mail"></a>데이터베이스 메일<a id="DatabaseMail"></a>

**제목: 데이터베이스 메일은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
이 서버는 Azure SQL Database에서 지원 되지 않는 데이터베이스 메일 기능을 사용 합니다.


**권유**   
데이터베이스 메일를 지 원하는 Azure SQL Managed Instance로 마이그레이션하는 것이 좋습니다.  또는 Azure 함수 및 Sendgrid를 사용 하 여 Azure SQL Database에서 메일 기능을 수행 하는 것이 좋습니다.

추가 정보: [Azure Functions 스크립트를 사용 하 여 Azure SQL Database에서 전자 메일 보내기](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>데이터베이스 보안 주체 별칭<a id="DatabasePrincipalAlias"></a>

**제목: SYS. DATABASE_PRINCIPAL_ALIASES 중단 되었고 제거 되었습니다.**   
**범주**: 문제   

**설명**   
시스템. DATABASE_PRINCIPAL_ALIASES 중단 되었으며 Azure SQL Database 제거 되었습니다.  


**권유**   
별칭 대신 역할을 사용해야 합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK 옵션<a id="DisableDefCNSTCHK"></a>

**제목: SET 옵션 DISABLE_DEF_CNST_CHK 중단 되었고 제거 되었습니다.**   
**범주**: 문제   

**설명**   
SET 옵션 DISABLE_DEF_CNST_CHK 중단 되었으며 Azure SQL Database에서 제거 되었습니다.  


추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW 힌트<a id="FastFirstRowHint"></a>

**제목: FASTFIRSTROW 쿼리 힌트가 중단 되었으며 제거 되었습니다.**   
**범주**: 경고   

**설명**   
FASTFIRSTROW 쿼리 힌트는 중단 되었으며 Azure SQL Database에서 제거 되었습니다.  


**권유**   
FASTFIRSTROW 쿼리 힌트 대신 OPTION (FAST n)을 사용 합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**제목: Filestream은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
NTFS 파일 시스템에 텍스트 문서, 이미지 및 비디오와 같은 구조화 되지 않은 데이터를 저장 하는 데 사용할 수 있는 Filestream 기능은 Azure SQL Database에서 지원 되지 않습니다. 


**권유**   
구조화 되지 않은 파일을 Azure Blob 저장소에 업로드 하 고 이러한 파일 (이름, 유형, URL 위치, 저장소 키 등)과 관련 된 메타 데이터를 Azure SQL Database에 저장 합니다. Azure SQL Database에서 스트리밍 blob을 사용할 수 있도록 응용 프로그램을 다시 엔지니어링 해야 할 수도 있습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL 블로그에서 Blob 스트리밍](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>연결된 서버<a id="LinkedServer"></a>

**제목: 연결 된 서버 기능은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
연결 된 서버를 사용 하면 SQL Server 데이터베이스 엔진에서 SQL Server 인스턴스 외부의 OLE DB 데이터 원본에 대해 명령을 실행할 수 있습니다. 


**권유**   
Azure SQL Database는 연결 된 서버 기능을 지원 하지 않습니다. 연결 된 서버에 대 한 필요성을 제거 하려면 다음 작업을 수행 하는 것이 좋습니다. 
- 원격 SQL server에서 종속 데이터 집합을 식별 하 고이를 마이그레이션하는 데이터베이스로 이동 하는 것이 좋습니다. 
- 종속 데이터베이스를 Azure로 마이그레이션하고 Elastic Database 쿼리 (미리 보기) 기능을 사용 하 여 Azure SQL Database의 데이터베이스에서 쿼리 합니다. 

추가 정보: [탄력적 쿼리 (미리 보기) Azure SQL Database 확인](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**제목: BEGIN DISTRIBUTED TRANSACTION은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
MS DTC (Microsoft DTC(Distributed Transaction Coordinator))에서 관리 하는 SQL BEGIN DISTRIBUTED TRANSACTION에서 시작한 분산 트랜잭션은 Azure SQL Database에서 지원 되지 않습니다.  


**권유**   
BEGIN 지리적 TRANSACTION을 사용 하 여 모든 개체를 보려면 Azure Migrate의 영향을 받는 개체 섹션을 검토 합니다. 여러 인스턴스에 걸친 분산 트랜잭션이 지원 되는 (현재 미리 보기 상태) 인 Azure SQL Managed Instance에 참가자 데이터베이스를 마이그레이션하는 것이 좋습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance에 대 한 여러 서버 간 트랜잭션 ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (대량)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**제목: 비 Azure blob 저장소 데이터 원본으로 대량 작업에 사용 되는 OpenRowSet은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명** OPENROWSET은 파일의 데이터를 읽고 행 집합으로 반환할 수 있는 기본 제공 대량 공급자를 통한 대량 작업을 지원 합니다. 비 Azure blob 저장소 데이터 원본에 대 한 OPENROWSET은 Azure SQL Database에서 지원 되지 않습니다.


**권유**   
Azure SQL Database 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure blob storage에서 파일을 가져와야 합니다. 따라서 OPENROWSET 함수에서는 blob 유형 DATASOURCE만 지원 됩니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션

추가 정보: [SQL Database로 마이그레이션하는 동안 transact-sql 차이점 해결](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (공급자)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**제목: SQL 또는 SQL 이외의 공급자를 사용한 OpenRowSet은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
SQL 또는 SQL 이외의 공급자를 사용 하는 OpenRowSet은 연결 된 서버에서 테이블에 액세스 하는 대신 OLE DB를 사용 하 여 원격 데이터를 연결 하 고 액세스 하는 일회성 임시 방법입니다. SQL 또는 SQL 이외의 공급자를 사용한 OpenRowSet은 Azure SQL Database에서 지원 되지 않습니다.


**권유**   
Azure SQL Database는 Azure blob 저장소에서 가져오는 경우에만 OPENROWSET을 지원 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션

추가 정보: [SQL Database로 마이그레이션하는 동안 transact-sql 차이점 해결](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>비 ANSI 왼쪽 우선 외부 조인<a id="NonANSILeftOuterJoinSyntax"></a>

**제목: ANSI 스타일이 아닌 왼쪽 우선 외부 조인이 중단 되었으며 제거 되었습니다.**   
**범주**: 경고   

**설명**   
비 ANSI 스타일 왼쪽 우선 외부 조인은 중단 되었으며 Azure SQL Database에서 제거 되었습니다. 


**권유**   
ANSI 조인 구문을 사용합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능


## <a name="non-ansi-right-outer-join"></a>비 ANSI 오른쪽 우선 외부 조인<a id="NonANSIRightOuterJoinSyntax"></a>

**제목: ANSI 스타일이 아닌 오른쪽 우선 외부 조인이 중단 되었으며 제거 되었습니다.**   
**범주**: 경고   

**설명**   
비 ANSI 스타일 오른쪽 우선 외부 조인은 중단 되었으며 Azure SQL Database에서 제거 되었습니다. 


**권유**   
ANSI 조인 구문을 사용합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="next-column"></a>다음 열<a id="NextColumn"></a>

**제목: 다음으로 이름이 지정 된 테이블 및 열은 Azure SQL Database 오류가 발생 합니다.**   
**범주**: 문제   

**설명**   
NEXT 라는 테이블이 나 열이 검색 되었습니다. Microsoft SQL Server에서 도입 된 시퀀스는 ANSI 표준 NEXT VALUE FOR 함수를 사용 합니다. 테이블 또는 열 이름이 NEXT이고 열 별칭이 VALUE이며 ANSI 표준 AS가 생략된 경우 결과 문에서 오류가 발생할 수 있습니다.  


**권유**   
테이블 또는 열 별칭을 지정할 때 ANSI 표준 AS 키워드를 포함하도록 문을 다시 작성합니다. 예를 들어 열 이름이 NEXT이 고 해당 열이 값으로 별칭을 지정 하는 경우 쿼리는 오류를 발생 `SELECT NEXT VALUE FROM TABLE` 시킵니다 .이 경우 테이블의 값으로 다음 SELECT로 다시 작성 해야 합니다. 마찬가지로 테이블 이름이 NEXT이 고 해당 테이블의 별칭이 VALUE 인 경우 쿼리는 오류를 발생 `SELECT Col1 FROM NEXT VALUE` 하므로로 다시 작성 해야 합니다 `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**제목: 레거시 스타일 RAISERROR 호출은 최신 항목으로 바꾸어야 합니다.**   
**범주**: 경고   

**설명**   
아래 예제와 같은 RAISERROR 호출은 쉼표와 괄호를 포함 하지 않기 때문에 레거시 스타일 이라고 합니다. `RAISERROR 50001 'this is a test'`. RAISERROR를 호출 하는이 메서드는 Azure SQL Database에서 중단 되 고 제거 됩니다. 


**권유**   
현재 RAISERROR 구문을 사용 하 여 문을 다시 작성 하거나의 최신 접근 방식이 적합 한 경우를 평가 `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` 합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="server-audits"></a>서버 감사<a id="ServerAudits"></a>

**제목: Azure SQL Database 감사 기능을 사용 하 여 서버 감사 바꾸기**   
**범주**: 경고   

**설명**   
서버 감사는 Azure SQL Database에서 지원 되지 않습니다.


**권유**   
서버 감사를 대체할 감사 기능을 Azure SQL Database 하는 것이 좋습니다.  Azure SQL은 감사를 지원 하 고 기능은 SQL Server 보다 더 다양 합니다. Azure SQL database는 데이터 액세스, 스키마 변경 (DDL), 데이터 변경 내용 (DML), 계정, 역할 및 사용 권한 (DCL, 보안 예외)을 포함 하 여 다양 한 데이터베이스 동작 및 이벤트를 감사할 수 있습니다. Azure SQL Database 감사는 데이터에 대 한 업데이트 및 쿼리를 포함 하 여 데이터베이스 내에서 발생 하는 이벤트 및 변경 내용을 심층 분석 하는 조직의 기능을 향상 시킵니다. 또는 azure 가상 컴퓨터에서 Azure SQL Managed Instance 또는 SQL Server로 마이그레이션합니다.

추가 정보: [Azure SQL Database에 대 한 감사 ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>서버 자격 증명<a id="ServerCredentials"></a>

**제목: 서버 범위 자격 증명은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
자격 증명은 SQL Server 외부의 리소스에 연결 하는 데 필요한 인증 정보 (자격 증명)가 포함 된 레코드입니다. Azure SQL Database는 데이터베이스 자격 증명을 지원 하지만 SQL Server 범위에서 만들어진 자격 증명은 지원 하지 않습니다.   


**권유**   
Azure SQL Database는 데이터베이스 범위 자격 증명을 지원 합니다. 서버 범위 자격 증명을 데이터베이스 범위 자격 증명으로 변환 합니다. 또는 azure 가상 컴퓨터에서 Azure SQL Managed Instance 또는 SQL Server로 마이그레이션

추가 정보: [데이터베이스 범위 자격 증명 만들기](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Service Broker<a id="ServiceBroker"></a>

**제목: Service Broker 기능은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
SQL Server Service Broker은 SQL Server 데이터베이스 엔진에서 메시징 및 큐 응용 프로그램에 대 한 기본 지원을 제공 합니다. Service Broker 기능은 Azure SQL Database에서 지원 되지 않습니다.


**권유**   
Service Broker 기능은 Azure SQL Database에서 지원 되지 않습니다. 동일한 인스턴스 내에서 service broker를 지 원하는 Azure SQL Managed Instance로 마이그레이션하는 것이 좋습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다. 

## <a name="server-scoped-triggers"></a>서버 범위 트리거<a id="ServerScopedTriggers"></a>

**제목: 서버 범위 트리거는 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
트리거는 데이터 삽입, 삭제 또는 업데이트와 같은 테이블의 특정 동작에 대 한 응답으로 실행 되는 특별 한 종류의 저장 프로시저입니다. 서버 범위 트리거는 Azure SQL Database에서 지원 되지 않습니다. Azure SQL Database는 트리거에 대 한 다음 옵션을 지원 하지 않습니다. LOGON, ENCRYPTION, WITH APPEND, NOT FOR REPLICATION, EXTERNAL NAME 옵션 (외부 메서드 지원 없음), 모든 서버 옵션 (DDL 트리거), LOGON 이벤트에 대 한 트리거 (Logon 트리거) Azure SQL Database CLR 트리거를 지원 하지 않습니다.


**권유**   
대신 데이터베이스 수준 트리거를 사용 하십시오. 또는 azure 가상 컴퓨터에서 Azure SQL Managed Instance 또는 SQL Server로 마이그레이션

추가 정보: [SQL Database로 마이그레이션하는 동안 transact-sql 차이점 해결](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL 에이전트 작업<a id="AgentJobs"></a>

**제목: SQL Server 에이전트 작업을 Azure SQL Database에서 사용할 수 없습니다.**   
**범주**: 경고   

**설명**   
SQL Server 에이전트은 SQL Server에서 작업 이라고 하는 예약 된 관리 태스크를 실행 하는 Microsoft Windows 서비스입니다. SQL Server 에이전트 작업은 Azure SQL Database에서 사용할 수 없습니다. 


**권장 사항** Azure SQL Database에서 SQL Server 에이전트 작업에 대 한 대체 인 탄력적 작업 (미리 보기)을 사용 합니다. Azure SQL Database Elastic Database 작업를 사용 하면 자동으로 다시 시도 하는 동시에 여러 데이터베이스에 걸친 T-sql 스크립트를 안정적으로 실행할 수 있으며 최종 완료 보장을 제공할 수 있습니다. 또는 azure SQL Managed Instance로 또는 Azure Virtual Machines에서 SQL Server으로 마이그레이션하는 것이 좋습니다.

추가 정보: [Elastic Database 작업 시작 (미리 보기) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL Database 크기<a id="SQLDBDatabaseSize"></a>

**제목: Azure SQL Database는 100 TB 보다 큰 데이터베이스 크기를 지원 하지 않습니다.**   
**범주**: 문제   

**설명**   
데이터베이스 크기가 지원 되는 최대 크기인 100 TB 보다 큽니다. 


**권유**   
데이터를 여러 데이터베이스에 보관 하거나 압축 하거나 분할 된 수 있는지 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다. 

추가 정보: [Vcore 리소스 제한](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL 메일<a id="SqlMail"></a>

**제목: SQL 메일은 중단 되었습니다.**   
**범주**: 경고   

**설명**   
SQL 메일은 Azure SQL Database에서 중단 되 고 제거 되었습니다.


**권유**   
Azure SQL Managed Instance 또는 Azure Virtual Machines에서 SQL Server으로 마이그레이션하고 데이터베이스 메일를 사용 하는 것이 좋습니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title: Azure SQL Database에서 사용할 수 없는 제거 된 시스템 저장 프로시저를 참조 하는 문을 발견 했습니다.**   
**범주**: 경고   

**설명**   
Azure SQL database-,,,,에서 지원 되지 않는 시스템 및 확장 저장 프로시저를 사용할 수 없습니다 `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` .


**권유**    
Azure SQL Database에서 제거 된 지원 되지 않는 시스템 프로시저에 대 한 참조를 제거 합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="trace-flags"></a>추적 플래그<a id="TraceFlags"></a>

**제목: Azure SQL Database에서 추적 플래그를 지원 하지 않습니다.**   
**범주**: 경고   

**설명**   
추적 플래그는 특정 서버의 특성을 임시로 설정하거나 특정 동작을 중지시키는 데 사용합니다. 추적 플래그는 주로 성능 문제를 진단하거나 저장 프로시저 또는 복잡한 컴퓨터 시스템을 디버깅하는 데 사용됩니다. Azure SQL Database는 추적 플래그를 지원 하지 않습니다. 


**권유**   
Azure Migrate의 영향을 받는 개체 섹션을 검토 하 여 Azure SQL Database에서 지원 되지 않는 추적 플래그를 모두 확인 하 고 제거할 수 있는지 평가 합니다. 또는 Azure 가상 컴퓨터에서 제한 된 수의 전역 추적 플래그 또는 SQL Server를 지 원하는 Azure SQL Managed Instance로 마이그레이션합니다.

추가 정보: [SQL Database로 마이그레이션하는 동안 transact-sql 차이점 해결](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows 인증<a id="WindowsAuthentication"></a>

**제목: Windows 인증 (통합 보안)으로 매핑된 데이터베이스 사용자는 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
Azure SQL Database는 두 가지 인증 유형을 지원 합니다. 
- SQL 인증: 사용자 이름 및 암호를 사용 합니다. 
- Azure Active Directory 인증: Azure Active Directory에서 관리 되는 id를 사용 하며, 관리 되는 및 통합 된 도메인에 대해 지원 됩니다. 

Windows 인증 (통합 보안)으로 매핑된 데이터베이스 사용자는 Azure SQL Database에서 지원 되지 않습니다. 



**권유**   
로컬 Active Directory Azure Active Directory와 페더레이션 합니다. 그런 다음 Windows id를 해당 하는 Azure Active Directory id로 바꿀 수 있습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Database 보안 기능](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**제목: xp_cmdshell은 Azure SQL Database에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
Windows 명령 셸을 생성 하 고 실행을 위해 문자열을 전달 하는 xp_cmdshell는 Azure SQL Database에서 지원 되지 않습니다. 


**권유**   
Azure Migrate의 영향을 받는 개체 섹션을 검토 하 여 xp_cmdshell를 사용 하는 모든 개체를 확인 하 고 xp_cmdshell에 대 한 참조 또는 영향을 받는 개체를 제거할 수 있는지 평가 합니다. 또한 클라우드 기반 자동화 및 구성 서비스를 제공 하는 Azure Automation 탐색 하는 것이 좋습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다. 

## <a name="next-steps"></a>다음 단계

Azure SQL Database로 SQL Server 마이그레이션을 시작 하려면 [SQL Server 마이그레이션 가이드 SQL Database를](sql-server-to-sql-database-guide.md)참조 하세요.

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구의 행렬은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- SQL Database에 대 한 자세한 내용은 다음을 참조 하세요.
   - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 

- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [작업의 비용 및 크기 조정 작업에 대 한 모범 사례 Azure로 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.
