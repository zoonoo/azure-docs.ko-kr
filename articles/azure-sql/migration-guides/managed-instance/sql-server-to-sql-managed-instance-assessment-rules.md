---
title: SQL Managed Instance 마이그레이션 SQL Server에 대 한 평가 규칙
description: Azure SQL Managed Instance로 마이그레이션하기 전에 해결 해야 하는 원본 SQL Server 인스턴스의 문제를 식별 하는 평가 규칙.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: 760a6496ff297ae6328810589f780b430d55b18a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054727"
---
# <a name="assessment-rules-for-sql-server-to-sql-managed-instance-migration"></a>SQL Managed Instance 마이그레이션 SQL Server에 대 한 평가 규칙
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

마이그레이션 도구는 SQL Server 데이터베이스를 Azure SQL Managed Instance 마이그레이션하기 전에 해결 해야 하는 문제를 식별 하는 다양 한 평가 규칙을 실행 하 여 원본 SQL Server 인스턴스의 유효성을 검사 합니다. 

이 문서에서는 SQL Server 데이터베이스를 Azure SQL Managed Instance로 마이그레이션할 수 있는 가능성을 평가 하는 데 사용 되는 규칙의 목록을 제공 합니다. 

## <a name="analysiscommand-job"></a>AnalysisCommand 작업<a id="AnalysisCommandJob"></a>

**제목: AnalysisCommand 작업 단계는 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 경고   


**설명**   
Analysis Services 명령을 실행 하는 작업 단계입니다. AnalysisCommand 작업 단계는 Azure SQL Managed Instance에서 지원 되지 않습니다.

**권유**     
Analysis Services 명령 작업 단계를 사용 하 여 모든 작업을 확인 하 고 작업 단계 또는 영향을 받는 개체를 제거할 수 있는지 평가 하는 Azure Migrate의 영향을 받는 개체 섹션을 검토 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance의 SQL Server 에이전트 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery 작업<a id="AnalysisQueryJob"></a>

**제목: AnalysisQuery 작업 단계는 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
Analysis Services 쿼리를 실행 하는 작업 단계입니다. AnalysisQuery 작업 단계는 Azure SQL Managed Instance에서 지원 되지 않습니다.


**권유**   
Analysis Service 쿼리 작업 단계를 사용 하 여 모든 작업을 보려면 Azure Migrate의 영향을 받는 개체 섹션을 검토 하 고 작업 단계 또는 영향을 받는 개체를 제거할 수 있는지 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance의 SQL Server 에이전트 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>파일의 어셈블리<a id="AssemblyFromFile"></a>

**Title: file 매개 변수를 사용 하는 ' CREATE ASSEMBLY ' 및 ' ALTER ASSEMBLY '는 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
Azure SQL Managed Instance 파일 공유 또는 Windows 폴더에 액세스할 수 없습니다. Azure blob을 참조 하지 않는 BULK INSERT 문의 특정 사용에 대해서는 "영향을 받는 개체" 섹션을 참조 하세요. 소스가 Azure blob storage가 아닌 ' BULK INSERT ' 인 개체는 Azure SQL Managed Instance로 마이그레이션한 후 작동 하지 않습니다.


**권유**   
Azure SQL Managed Instance로 마이그레이션할 때 대신 로컬 파일 또는 파일 공유를 사용 하 여 Azure blob storage의 파일을 사용 하는 BULK INSERT 문을 변환 해야 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다. 

추가 정보: [AZURE SQL Managed Instance의 CLR 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>대량 삽입<a id="BulkInsert"></a>

**제목: azure SQL Managed Instance에서 비 Azure blob 데이터 원본에 BULK INSERT 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
Azure SQL Managed Instance 파일 공유 또는 Windows 폴더에 액세스할 수 없습니다. Azure blob을 참조 하지 않는 BULK INSERT 문의 특정 사용에 대해서는 "영향을 받는 개체" 섹션을 참조 하세요. 소스가 Azure blob storage가 아닌 ' BULK INSERT ' 인 개체는 Azure SQL Managed Instance로 마이그레이션한 후 작동 하지 않습니다.


**권유**   
Azure SQL Managed Instance로 마이그레이션할 때 대신 로컬 파일 또는 파일 공유를 사용 하 여 Azure blob storage의 파일을 사용 하는 BULK INSERT 문을 변환 해야 합니다.

추가 정보: [AZURE SQL Managed Instance의 대량 삽입 및 OPENROWSET 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR 보안<a id="ClrStrictSecurity"></a>

**제목: SAFE 또는 EXTERNAL_ACCESS로 표시 된 CLR 어셈블리는 안전 하지 않은 것으로 간주 됩니다.**   
**범주**: 문제   

**설명**   
CLR Strict 보안 모드는 Azure SQL Managed Instance에서 적용 됩니다. 이 모드는 기본적으로 사용 하도록 설정 되어 있으며 SAFE 또는 EXTERNAL_ACCESS로 표시 된 사용자 정의 CLR 어셈블리를 포함 하는 데이터베이스에 대 한 주요 변경 사항을 도입 합니다.


**권유**   
CLR은 더 이상 보안 경계로 지원되지 않는 .NET Framework의 CAS(코드 액세스 보안)를 사용합니다. SQL Server 2017 (14.x) 데이터베이스 엔진부터 clr `sp_configure` 어셈블리의 보안을 강화 하기 위해 clr strict security 라는 옵션이 도입 되었습니다. Clr strict 보안은 기본적으로 사용 하도록 설정 되어 있으며 안전 하 고 EXTERNAL_ACCESS CLR 어셈블리가 안전 하지 않은 것으로 표시 된 것 처럼 처리 합니다. Clr strict security를 사용 하지 않도록 설정 하면 PERMISSION_SET = SAFE를 사용 하 여 만든 CLR 어셈블리에서 외부 시스템 리소스에 액세스 하 고, 비관리 코드를 호출 하 고, sysadmin 권한을 획득할 수 있습니다. strict security(엄격한 보안)를 사용하도록 설정하면 서명되지 않은 어셈블리는 로드되지 않습니다. 또한 데이터베이스에 SAFE 또는 EXTERNAL_ACCESS 어셈블리가 있으면 RESTORE 또는 ATTACH 데이터베이스 문이 완료 될 수 있지만 어셈블리를 로드 하지 못할 수 있습니다. 어셈블리를 로드 하려면 서버에 대 한 UNSAFE ASSEMBLY 권한이 있는 해당 로그인이 있는 인증서 또는 비대칭 키로 서명 되도록 각 어셈블리를 변경 하거나 삭제 한 후 다시 만들어야 합니다.

추가 정보: [CLR strict security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Compute 절<a id="ComputeClause"></a>

**제목: COMPUTE 절이 중단 되었으며 제거 되었습니다.**   
**범주**: 경고   

**설명**   
COMPUTE 절은 결과 집합의 끝에 추가 요약 열로 표시 되는 합계를 생성 합니다. 그러나이 절은 Azure SQL Managed Instance에서 더 이상 지원 되지 않습니다.



**권유**   
대신 ROLLUP 연산자를 사용 하 여 T-sql 모듈을 다시 작성 해야 합니다. 아래 코드에서는 계산을 ROLLUP으로 대체 하는 방법을 보여 줍니다. 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="cryptographic-provider"></a>암호화 공급자<a id="CryptographicProvider"></a>

**제목: Azure SQL Managed Instance에서 지원 되지 않는 CREATE 암호화 공급자 또는 ALTER 암호화 공급자를 사용 했습니다.**   
**범주**: 문제   

**설명**   
Azure SQL Managed Instance는 파일에 액세스할 수 없기 때문에 암호화 공급자 문을 지원 하지 않습니다. 암호화 공급자 문의 특정 용도는 영향을 받는 개체 섹션을 참조 하세요. Azure SQL Managed Instance로 마이그레이션한 후에는 ' 암호화 공급자 만들기 ' 또는 ' ALTER 암호화 공급자 '가 포함 된 개체가 올바르게 작동 하지 않습니다.


**권유**   
' 암호화 공급자 만들기 ' 또는 ' ALTER 암호화 공급자 '를 사용 하 여 개체를 검토 합니다. 필요한 모든 개체에서 이러한 기능의 사용을 제거 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다. 

추가 정보: [AZURE SQL Managed Instance의 암호화 공급자 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>데이터베이스 호환성<a id="DbCompatLevelLowerThan100"></a>

**제목: 100 미만 데이터베이스 호환성 수준은 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
데이터베이스 호환성 수준은 동일한 업그레이드 전 데이터베이스 호환성 수준을 유지 하 여 응용 프로그램의 기능 상태를 유지 하면서 SQL Server 데이터베이스 엔진를 업그레이드할 수 있도록 하 여 데이터베이스 현대화을 지 원하는 데 유용한 도구입니다. Azure SQL Managed Instance는 100 미만의 호환성 수준을 지원 하지 않습니다. 호환성 수준이 100 미만인 데이터베이스가 Azure SQL Managed Instance에서 복원 되 면 호환성 수준이 100로 업그레이드 됩니다. 


**권장 사항**... Azure SQL Managed Instance에서 데이터베이스 호환성 수준이 100으로 업그레이드 될 때 응용 프로그램 기능이 그대로 유지 되는지 여부를 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance에서 지원 되는 호환성 수준 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>데이터베이스 보안 주체 별칭<a id="DatabasePrincipalAlias"></a>

**제목: SYS. DATABASE_PRINCIPAL_ALIASES 중단 되었고 제거 되었습니다.**   
**범주**: 문제   

**설명**   
시스템. DATABASE_PRINCIPAL_ALIASES는 중단 되며 Azure SQL Managed Instance에서 제거 되었습니다.


**권유**   
별칭 대신 역할을 사용해야 합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK 옵션<a id="DisableDefCNSTCHK"></a>

**제목: SET 옵션 DISABLE_DEF_CNST_CHK 중단 되었고 제거 되었습니다.**   
**범주**: 문제   

**설명**   
SET 옵션 DISABLE_DEF_CNST_CHK는 중단 되었으며 Azure SQL Managed Instance에서 제거 되었습니다.


추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW 힌트<a id="FastFirstRowHint"></a>

**제목: FASTFIRSTROW 쿼리 힌트가 중단 되었으며 제거 되었습니다.**   
**범주**: 경고   

**설명**   
FASTFIRSTROW 쿼리 힌트는 중단 되었으며 Azure SQL Managed Instance에서 제거 되었습니다.


**권유**   
FASTFIRSTROW 쿼리 힌트 대신 OPTION (FAST n)을 사용 합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**제목: Filestream 및 Filetable은 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
NTFS 파일 시스템에 텍스트 문서, 이미지 및 비디오와 같은 구조화 되지 않은 데이터를 저장 하는 데 사용할 수 있는 Filestream 기능은 Azure SQL Managed Instance에서 지원 되지 않습니다. **Filestream 파일 그룹을 포함 하는 백업은 Azure SQL Managed Instance에서 복원할 수 없으므로이 데이터베이스를 마이그레이션할 수 없습니다.**


**권유**   
Azure SQL Managed Instance에서 구조화 되지 않은 파일을 Azure Blob 저장소에 업로드 하 고 이러한 파일 (이름, 유형, URL 위치, 저장소 키 등)과 관련 된 메타 데이터를 저장 합니다. 응용 프로그램을 다시 엔지니어링 하 여 Azure SQL Managed Instance 간 blob을 사용할 수 있도록 해야 할 수 있습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Azure 블로그에서 Blob 스트리밍](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>다른 유형의 MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**제목: Azure SQL Managed Instance에서 SQL Server 없는 원격 서버를 사용 하는 BEGIN DISTRIBUTED TRANSACTION은 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
원격 서버가 SQL Server 되지 않은 경우에는 SQL Managed Instance에서 시작 하 고 MS DTC (Microsoft DTC(Distributed Transaction Coordinator))에서 관리 하는 분산 트랜잭션이 Azure SQL에서 지원 되지 않습니다. 


**권유**   
BEGIN 지리적 TRANSACTION을 사용 하 여 모든 개체를 보려면 Azure Migrate의 영향을 받는 개체 섹션을 검토 합니다. 여러 인스턴스에 걸친 분산 트랜잭션이 지원 되는 (현재 미리 보기 상태) 인 Azure SQL Managed Instance에 참가자 데이터베이스를 마이그레이션하는 것이 좋습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance에 대 한 여러 서버 간 트랜잭션 ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>동일한 MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**제목: BEGIN DISTRIBUTED TRANSACTION은 Azure SQL Managed Instance의 여러 서버에서 지원 됩니다.**   
**범주**: 문제   

**설명**   
SQL 시작 및 MS DTC (Microsoft DTC(Distributed Transaction Coordinator))에서 관리 하는 분산 트랜잭션은 Azure SQL Managed Instance에 대 한 여러 서버에서 지원 됩니다.


**권유**   
BEGIN 지리적 TRANSACTION을 사용 하 여 모든 개체를 보려면 Azure Migrate의 영향을 받는 개체 섹션을 검토 합니다. 여러 인스턴스에 걸친 분산 트랜잭션이 지원 되는 (현재 미리 보기 상태) 인 Azure SQL Managed Instance에 참가자 데이터베이스를 마이그레이션하는 것이 좋습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다. 

추가 정보: [AZURE SQL Managed Instance에 대 한 여러 서버 간 트랜잭션](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>연결 된 서버 (SQL 공급자 이외의)<a id="LinkedServerWithNonSQLProvider"></a>

**제목: SQL Server 공급자가 포함 된 연결 된 서버는 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
연결 된 서버를 사용 하면 SQL Server 데이터베이스 엔진에서 SQL Server 인스턴스 외부의 OLE DB 데이터 원본에 대해 명령을 실행할 수 있습니다. SQL Server 공급자가 포함 된 연결 된 서버는 Azure SQL Managed Instance에서 지원 되지 않습니다. 


**권유**   
원격 서버 공급자가 Oracle, Sybase 등의 SQL Server 없는 경우 Azure SQL Managed Instance는 연결 된 서버 기능을 지원 하지 않습니다. 

연결 된 서버에 대 한 필요성을 제거 하려면 다음 작업을 수행 하는 것이 좋습니다. 
- 원격 SQL server 이외 서버에서 종속 데이터베이스를 식별 하 고 마이그레이션하는 데이터베이스로 이동 하는 것이 좋습니다. 
- 종속 데이터베이스를 SQL Managed Instance, SQL Database, Azure Synapse SQL 및 SQL Server 인스턴스와 같은 지원 되는 대상으로 마이그레이션합니다. 
- Azure SQL Managed Instance와 azure 가상 컴퓨터 (SQL VM)에서 SQL Server 간에 연결 된 서버를 만드는 것이 좋습니다.  그런 다음 SQL VM에서 Oracle, Sybase 등의 연결 된 서버를 만듭니다. 이 접근 방식에는 두 홉이 포함 되지만 임시 해결 방법으로 사용할 수 있습니다.  
- 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance의 연결 된 서버 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>병합 작업<a id="MergeJob"></a>

**제목: 병합 작업 단계는 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
복제 병합 에이전트를 활성화 하는 작업 단계입니다. 복제 병합 에이전트는 데이터베이스 테이블에 저장된 초기 스냅샷 파일을 구독자에 적용하는 유틸리티 실행 파일입니다. 또한 이 에이전트는 초기 스냅샷이 만들어진 후 게시자에서 발생한 증분 데이터 변경 내용을 병합하고, 사용자가 구성한 규칙에 따라 또는 사용자가 만든 사용자 지정 해결 프로그램을 사용하여 충돌을 조정합니다. Azure SQL Managed Instance에서는 병합 작업 단계가 지원 되지 않습니다.


**권유**   
Azure Migrate의 영향을 받는 개체 섹션을 검토 하 여 병합 작업 단계를 사용 하는 모든 작업을 확인 하 고 작업 단계 또는 영향을 받는 개체를 제거할 수 있는지 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션

추가 정보: [AZURE SQL Managed Instance의 SQL Server 에이전트 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI 데이터베이스 크기<a id="MIDatabaseSize<"></a>

**제목: Azure SQL Managed Instance는 8TB 보다 큰 데이터베이스 크기를 지원 하지 않습니다.**   
**범주**: 문제   

**설명**   
데이터베이스 크기가 최대 인스턴스 예약 저장소 보다 큽니다. **크기가 허용 한도를 초과 하 여 마이그레이션을 위해이 데이터베이스를 선택할 수 없습니다.**


**권유**   
데이터를 여러 데이터베이스에 압축 하거나 분할 된 보관할 수 있는지 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance의 하드웨어 생성 특성 ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI 인스턴스 크기<a id="MIInstanceSize<"></a>

**제목: Azure SQL Managed Instance의 최대 인스턴스 저장소 크기는 8TB 보다 클 수 없습니다.**   
**범주**: 경고   

**설명**   
모든 데이터베이스의 크기가 최대 인스턴스 예약 저장소 보다 큽니다.  


**권유**   
모든 데이터베이스가 동일한 인스턴스에 있어야 하는 경우 데이터베이스를 다른 Azure SQL 관리 되는 인스턴스로 마이그레이션하거나 Azure 가상 머신에서 SQL Server 하는 것이 좋습니다. 

추가 정보: [AZURE SQL Managed Instance의 하드웨어 생성 특성 ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>여러 로그 파일<a id="MultipleLogFiles<"></a>

**제목: Azure SQL Managed Instance는 여러 로그 파일을 지원 하지 않습니다.**   
**범주**: 문제   

**설명**   
SQL Server를 사용 하 여 데이터베이스에서 여러 파일에 기록할 수 있습니다. 이 데이터베이스에는 Azure SQL Managed Instance에서 지원 되지 않는 여러 로그 파일이 있습니다. * * Azure SQL Managed Instance에서 백업을 복원할 수 없으므로이 데이터베이스를 마이그레이션할 수 없습니다. 
**

**권유**   
Azure SQL Managed Instance는 데이터베이스당 단일 로그만 지원 합니다. 이 데이터베이스를 Azure로 마이그레이션하기 전에 로그 파일 중 하나를 제외한 모든 로그 파일을 삭제 해야 합니다. 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

추가 정보: [AZURE SQL Managed Instance에서 지원 되지 않는 데이터베이스 옵션  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>다음 열<a id="NextColumn"></a>

**제목: 테이블 및 열 이름 다음에는 Azure SQL Managed Instance 오류가 발생 합니다.**   
**범주**: 문제   

**설명**   
NEXT 라는 테이블이 나 열이 검색 되었습니다. Microsoft SQL Server에서 도입 된 시퀀스는 ANSI 표준 NEXT VALUE FOR 함수를 사용 합니다. 테이블 또는 열 이름이 NEXT이고 열 별칭이 VALUE이며 ANSI 표준 AS가 생략된 경우 결과 문에서 오류가 발생할 수 있습니다.


**권유**   
테이블 또는 열 별칭을 지정할 때 ANSI 표준 AS 키워드를 포함하도록 문을 다시 작성합니다. 예를 들어 열 이름이 NEXT이 고 해당 열이 값으로 별칭이 지정 된 경우 테이블에서 다음 값을 선택 하면 오류가 발생 하 고 테이블의 값으로 다음 선택으로 다시 작성 됩니다. 마찬가지로 테이블 이름이 NEXT이 고 해당 테이블의 값이 값으로 지정 된 경우 다음 값에서 Col1을 선택 하면 오류가 발생 하 고 다음 값에서 Col1 선택으로 다시 작성 됩니다.



## <a name="non-ansi-style-left-outer-join"></a>ANSI 스타일이 아닌 왼쪽 우선 외부 조인<a id="NonANSILeftOuterJoinSyntax"></a>

**제목: ANSI 스타일이 아닌 왼쪽 우선 외부 조인이 중단 되었으며 제거 되었습니다.**   
**범주**: 경고   

**설명**   
비 ANSI 스타일 왼쪽 우선 외부 조인은 중단 되 고 Azure SQL Managed Instance에서 제거 되었습니다. 


**권유**   
ANSI 조인 구문을 사용합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="non-ansi-style-right-outer-join"></a>ANSI 스타일이 아닌 오른쪽 우선 외부 조인<a id="NonANSIRightOuterJoinSyntax"></a>

**제목: ANSI 스타일이 아닌 오른쪽 우선 외부 조인이 중단 되었으며 제거 되었습니다.**   
**범주**: 경고   

**설명**   
ANSI 스타일이 아닌 오른쪽 우선 외부 조인은 중단 되며 Azure SQL Managed Instance에서 제거 되었습니다. 



추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

**권유**   
ANSI 조인 구문을 사용합니다.

## <a name="databases-exceed-100"></a>데이터베이스가 100을 초과 합니다. <a id="NumDbExceeds100"></a>

**제목: Azure SQL Managed Instance는 인스턴스당 최대 100 개의 데이터베이스를 지원 합니다.**   
**범주**: 경고   

**설명**   
인스턴스 저장소 크기 제한에 도달 하지 않는 한 Azure SQL Managed Instance에서 지원 되는 최대 데이터베이스 수는 100입니다.



**권유**   
모든 데이터베이스가 동일한 인스턴스에 있어야 하는 경우 데이터베이스를 다른 Azure SQL 관리 되는 인스턴스로 마이그레이션하거나 Azure 가상 머신에서 SQL Server 하는 것이 좋습니다. 

추가 정보: [AZURE SQL Managed Instance 리소스 제한 ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (비 blob 데이터 원본)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**제목: azure SQL Managed Instance에서 비 Azure blob 저장소 데이터 원본으로 대량 작업에 사용 되는 OpenRowSet은 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
OPENROWSET은 파일의 데이터를 읽고 행 집합으로 반환할 수 있는 기본 제공 대량 공급자를 통한 대량 작업을 지원 합니다. Azure SQL Managed Instance에서는 비 Azure blob 저장소 데이터 원본이 포함 된 OPENROWSET을 지원 하지 않습니다. 



**권유**   
OPENROWSET 함수는 SQL Server 인스턴스 (관리 되는 온-프레미스 또는 Virtual Machines) 에서만 쿼리를 실행 하는 데 사용할 수 있습니다. SQLNCLI, SQLNCLI11 및 SQLOLEDB 값만 provider로 지원 됩니다. 따라서 권장 사항은 원격 SQL Server 이외 서버에서 종속 데이터베이스를 식별 하는 것 이며,이를 마이그레이션하는 데이터베이스로 이동 하는 것이 좋습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션

추가 정보: [AZURE SQL Managed Instance의 대량 삽입 및 OPENROWSET 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (SQL 공급자 이외)<a id="OpenRowsetWithNonSQLProvider"></a>

**제목: SQL 공급자가 아닌 OpenRowSet은 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 문제   

**설명**   
이 방법은 OLE DB를 사용하여 원격 데이터에 연결하고 액세스하는 일회성의 임시 방법이며 연결된 서버에서 테이블을 액세스하는 방법의 대안입니다. SQL 공급자가 아닌 OpenRowSet은 Azure SQL Managed Instance에서 지원 되지 않습니다. 



**권유**   
OPENROWSET 함수는 SQL Server 인스턴스 (관리 되는 온-프레미스 또는 Virtual Machines) 에서만 쿼리를 실행 하는 데 사용할 수 있습니다. SQLNCLI, SQLNCLI11 및 SQLOLEDB 값만 provider로 지원 됩니다. 따라서 권장 사항은 원격 SQL Server 이외 서버에서 종속 데이터베이스를 식별 하는 것 이며,이를 마이그레이션하는 데이터베이스로 이동 하는 것이 좋습니다.

추가 정보: [AZURE SQL Managed Instance의 대량 삽입 및 OPENROWSET 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell 작업<a id="PowerShellJob"></a>

**제목: PowerShell 작업 단계는 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
PowerShell 스크립트를 실행 하는 작업 단계입니다. PowerShell 작업 단계는 Azure SQL Managed Instance에서 지원 되지 않습니다. 



**권유**   
PowerShell 작업 단계를 사용 하 여 모든 작업을 확인 하 고 작업 단계 또는 영향을 받는 개체를 제거할 수 있는지 평가 하는 Azure Migrate의 영향을 받는 개체 섹션을 검토 합니다.  Azure Automation를 사용할 수 있는지 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션

추가 정보: [AZURE SQL Managed Instance의 SQL Server 에이전트 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>큐 판독기 작업<a id="QueueReaderJob"></a>

**제목: Azure SQL Managed Instance에서 큐 판독기 작업 단계가 지원 되지 않습니다.**   
**범주**: 경고   

**설명**   
복제 큐 판독기 에이전트를 활성화 하는 작업 단계입니다. 복제 큐 판독기 에이전트는 Microsoft SQL Server 큐 나 Microsoft 메시지 큐에 저장 된 메시지를 읽고 해당 메시지를 게시자에 적용 하는 실행 파일입니다. 큐 판독기 에이전트는 지연 업데이트를 허용하는 스냅샷 및 트랜잭션 게시와 함께 사용됩니다. 큐 판독기 작업 단계는 Azure SQL Managed Instance에서 지원 되지 않습니다.


**권유**   
큐 판독기 작업 단계를 사용 하 여 모든 작업을 확인 하 고 작업 단계 또는 영향을 받는 개체를 제거할 수 있는지 평가 하려면 Azure Migrate의 영향을 받는 개체 섹션을 검토 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance의 SQL Server 에이전트 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**제목: 레거시 스타일 RAISERROR 호출은 최신 항목으로 바꾸어야 합니다.**   
**범주**: 경고   

**설명**   
아래 예제와 같은 RAISERROR 호출은 쉼표와 괄호를 포함 하지 않기 때문에 레거시 스타일 이라고 합니다. RAISERROR 50001 ' 테스트 '입니다. RAISERROR를 호출 하는이 메서드는 Azure SQL Managed Instance에서 중단 되 고 제거 됩니다.



**권유**   
현재 RAISERROR 구문을 사용 하 여 문을 다시 작성 하거나의 최신 접근 방식이 적합 한 경우를 평가 `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` 합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="service-broker"></a>Service broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**제목: Service Broker 기능은 Azure SQL Managed Instance에서 부분적으로 지원 됩니다.**   
**범주**: 문제   

**설명**   
SQL Server Service Broker은 SQL Server 데이터베이스 엔진에서 메시징 및 큐 응용 프로그램에 대 한 기본 지원을 제공 합니다. 이 데이터베이스에는 Azure SQL Managed Instance에서 지원 되지 않는 크로스 인스턴스 Service Broker 사용 하도록 설정 되어 있습니다. 


**권유**   
Azure SQL Managed Instance는 인스턴스 간 service broker를 지원 하지 않습니다. 즉, 주소가 local이 아닙니다. 이 데이터베이스를 Azure로 마이그레이션하기 전에 다음 명령을 사용 하 여 Service Broker를 사용 하지 않도록 설정 해야 합니다. `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; 또한 메시지가 SQL 인스턴스에 도착 하지 않도록 하기 위해 Service Broker 끝점을 제거 하거나 중지 해야 할 수도 있습니다. 데이터베이스가 Azure로 마이그레이션된 후 Azure Service Bus 기능을 확인 하 여 Service Broker 대신 일반적인 클라우드 기반 메시징 시스템을 구현할 수 있습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다. 

추가 정보: [AZURE SQL Managed Instance의 Service Broker 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL 메일<a id="SqlMail"></a>

**제목: SQL 메일은 중단 되었습니다.**   
**범주**: 경고   


**설명**   
SQL 메일은 Azure SQL Managed Instance에서 중단 되 고 제거 되었습니다.



**권유**   
데이터베이스 메일을 사용합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title: Azure SQL Managed Instance에서 사용할 수 없는 제거 된 시스템 저장 프로시저를 참조 하는 문을 발견 했습니다.**   
**범주**: 경고   

**설명**   
Azure SQL Managed Instance,,,, 및에서는 지원 되지 않는 시스템 및 확장 저장 프로시저를 사용할 수 없습니다 `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` . 




**권유**   
Azure SQL Managed Instance에서 제거 된 지원 되지 않는 시스템 프로시저에 대 한 참조를 제거 합니다.

추가 정보: [SQL Server에서](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali) 지원 되지 않는 데이터베이스 엔진 기능

## <a name="transact-sql-job"></a>Transact-sql 작업<a id="TransactSqlJob"></a>

**제목: TSQL 작업 단계에 지원 되지 않는 명령이 Azure SQL Managed Instance 포함 되어 있습니다.**   
**범주**: 경고   


**설명**   
예약 된 시간에 TSQL 스크립트를 실행 하는 작업 단계입니다. TSQL 작업 단계에는 Azure SQL Managed Instance에서 지원 되지 않는 지원 되지 않는 명령이 포함 되어 있습니다.



**권유**   
Azure Migrate의 영향을 받는 개체 섹션을 검토 하 여 Azure SQL Managed Instance에서 지원 되지 않는 명령이 포함 된 모든 작업을 확인 하 고, 작업 단계 또는 영향을 받는 개체를 제거할 수 있는지 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance의 SQL Server 에이전트 차이점 ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>추적 플래그<a id="TraceFlags"></a>

**제목: Azure SQL Managed Instance에서 지원 되지 않는 추적 플래그가 있습니다.**   
**범주**: 경고   


**설명**   
Azure SQL Managed Instance는 제한 된 수의 전역 추적 플래그만 지원 합니다. 세션 추적 플래그는 지원 되지 않습니다.



**권유**   
Azure Migrate의 영향을 받는 개체 섹션을 검토 하 여 Azure SQL Managed Instance에서 지원 되지 않는 모든 추적 플래그를 확인 하 고 제거할 수 있는지 평가 합니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [추적 플래그](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows 인증<a id="WindowsAuthentication"></a>

**제목: Windows 인증 (통합 보안)으로 매핑된 데이터베이스 사용자는 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 경고   


**설명**   
Azure SQL Managed Instance는 두 가지 유형의 인증을 지원 합니다. 
- 사용자 이름 및 암호를 사용 하는 SQL 인증
- Azure Active Directory 인증은 Azure Active Directory에서 관리하는 ID를 사용하고 관리되고 통합된 도메인을 지원합니다. 

Windows 인증 (통합 보안)으로 매핑된 데이터베이스 사용자는 Azure SQL Managed Instance에서 지원 되지 않습니다. 


**권유**   
로컬 Active Directory Azure Active Directory와 페더레이션 합니다. 그런 다음 Windows id를 해당 하는 Azure Active Directory id로 바꿀 수 있습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [SQL Managed Instance 보안 기능](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**제목: xp_cmdshell는 Azure SQL Managed Instance에서 지원 되지 않습니다.**   
**범주**: 문제   


**설명**   
Windows 명령 셸을 생성 하 고 실행을 위해 문자열을 전달 하는 Xp_cmdshell는 Azure SQL Managed Instance에서 지원 되지 않습니다. 



**권유**   
Azure Migrate의 영향을 받는 개체 섹션을 검토 하 여 xp_cmdshell를 사용 하는 모든 개체를 확인 하 고 xp_cmdshell에 대 한 참조 또는 영향을 받는 개체를 제거할 수 있는지 평가 합니다. 클라우드 기반 자동화 및 구성 서비스를 제공 하는 Azure Automation 탐색 하는 것이 좋습니다. 또는 Azure 가상 컴퓨터에서 SQL Server로 마이그레이션합니다.

추가 정보: [AZURE SQL Managed Instance의 저장 프로시저 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>다음 단계

Azure SQL Managed Instance에 대 한 SQL Server 마이그레이션을 시작 하려면 [SQL SERVER sql Managed Instance 마이그레이션 가이드](sql-server-to-managed-instance-guide.md)를 참조 하세요.

- 다양 한 데이터베이스 및 데이터 마이그레이션 시나리오와 전문 작업을 지 원하는 데 사용할 수 있는 Microsoft 및 타사 서비스 및 도구의 행렬은 [데이터 마이그레이션을 위한 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조 하세요.

- Azure SQL Managed Instance에 대 한 자세한 내용은 다음을 참조 하세요.
   - [Azure SQL Managed Instance의 서비스 계층](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server와 Azure SQL Managed Instance의 차이점](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/) 


- 클라우드 마이그레이션의 프레임 워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조 하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [작업의 비용 및 크기 조정 작업에 대 한 모범 사례 Azure로 마이그레이션](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 응용 프로그램 액세스 계층을 평가 하려면 [데이터 액세스 마이그레이션 도구 키트 (미리 보기)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 를 참조 하세요.
- 데이터 액세스 계층 A/B 테스트를 수행 하는 방법에 대 한 자세한 내용은 [데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)를 참조 하세요.