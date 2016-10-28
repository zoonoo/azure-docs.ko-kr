<properties
	pageTitle="스트레치 데이터베이스 관리 및 문제 해결 | Microsoft Azure"
	description="스트레치 데이터베이스를 관리하고 문제를 해결하는 방법을 알아봅니다."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="douglasl"/>

# 스트레치 데이터베이스 관리 및 문제 해결

스트레치 데이터베이스를 관리하고 문제를 해결하려면 이 항목에 설명된 도구 및 메서드를 사용합니다.

## 로컬 데이터 관리

### <a name="LocalInfo"></a>스트레치 데이터베이스를 사용하는 로컬 데이터베이스 및 테이블에 대한 정보 가져오기
스트레치 지원 SQL Server 데이터베이스 및 테이블에 대한 정보를 보려면 카탈로그 뷰 **sys.databases** 및 **sys.tables**를 엽니다. 자세한 내용은 [sys.databases(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) 및 [sys.tables(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms187406.aspx)를 참조하세요.

SQL Server에서 스트레치 지원 테이블이 사용하는 공간을 확인하려면 다음 문을 실행합니다.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## 데이터 마이그레이션 관리

### 테이블에 적용된 필터 함수 확인
카탈로그 뷰 **sys.remote\_data\_archive\_tables**를 열고 **filter\_predicate** 열 값을 확인하여 스트레치 데이터베이스가 마이그레이션할 행을 선택하는 데 사용하는 함수를 식별합니다. 값이 null이면 전체 테이블을 마이그레이션할 수 있습니다. 자세한 내용은 [sys.remote\_data\_archive\_tables(Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) 및 [필터 함수를 사용하여 마이그레이션할 행 선택](sql-server-stretch-database-predicate-function.md)을 참조하세요.

### <a name="Migration"></a>데이터 마이그레이션 상태 확인
스트레치 데이터베이스 모니터에서 데이터 마이그레이션을 모니터링하려면 SQL Server Management Studio에서 데이터베이스에 대한 **작업 | 스트레치 | 모니터**를 선택합니다. 자세한 내용은 [데이터 마이그레이션 모니터링 및 문제 해결(스트레치 데이터베이스)](sql-server-stretch-database-monitor.md)을 참조하세요.

또는 동적 관리 뷰 **sys.dm\_db\_rda\_migration\_status**를 열고 마이그레이션된 배치 수 및 데이터 행 수를 확인합니다.

### <a name="Firewall"></a>데이터 마이그레이션 문제 해결
문제 해결 제안 사항은 [데이터 마이그레이션 모니터링 및 문제 해결(스트레치 데이터베이스)](sql-server-stretch-database-monitor.md)을 참조하세요.

## 원격 데이터 관리

### <a name="RemoteInfo"></a>스트레치 데이터베이스에서 사용하는 원격 데이터베이스 및 테이블에 대한 정보 가져오기
마이그레이션된 데이터가 저장된 원격 데이터베이스 및 테이블에 대한 정보를 보려면 카탈로그 뷰 **sys.remote\_data\_archive\_databases** 및 **sys.remote\_data\_archive\_tables**를 엽니다. 자세한 내용은 [sys.remote\_data\_archive\_databases(Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) 및 [sys.remote\_data\_archive\_tables(Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)를 참조하세요.

Azure에서 스트레치 지원 테이블이 사용하는 공간을 확인하려면 다음 문을 실행합니다.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### 마이그레이션 데이터 삭제  
Azure로 이미 마이그레이션된 데이터를 삭제하려는 경우 [sys.sp\_rda\_reconcile\_batch](https://msdn.microsoft.com/library/mt707768.aspx)에 설명된 단계를 따르세요.

## 테이블 스키마 관리

### 원격 테이블의 스키마 변경 금지
스트레치 데이터베이스에 대해 구성된 SQL Server 테이블과 연결된 원격 Azure 테이블의 스키마를 변경하지 마세요. 특히 열의 데이터 형식 또는 이름을 수정해서는 안 됩니다. 스트레치 데이터베이스 기능에서는 SQL Server 테이블의 스키마와 관련하여 원격 테이블의 스키마에 대해 다양한 가정을 합니다. 원격 스키마를 변경하면 변경된 테이블에 대해 스트레치 데이터베이스의 작동이 중지됩니다.

### 테이블 열 조정  
원격 테이블에서 열을 실수로 삭제한 경우 **sp\_rda\_reconcile\_columns**를 실행하여 스트레치 지원 SQL Server 테이블에는 있지만 원격 테이블에는 없는 열을 원격 테이블에 추가합니다. 자세한 내용은 [sys.sp\_rda\_reconcile\_columns](https://msdn.microsoft.com/library/mt707765.aspx)를 참조하세요.

  > [!중요] **sp\_rda\_reconcile\_columns**는 실수로 원격 테이블에서 삭제된 열을 다시 만들 때 이전에 삭제된 열에 있던 데이터를 복원하지 않습니다.

**sp\_rda\_reconcile\_columns**는 원격 테이블에는 있지만 스트레치 지원 SQL Server 테이블에는 없는 열을 원격 테이블에서 삭제하지 않습니다. 스트레치 지원 SQL Server 테이블에 더 이상 존재하지 않는 열이 원격 Azure 테이블에 있는 경우 이러한 추가 열 때문에 스트레치 데이터베이스가 제대로 작동하지 않는 것은 아닙니다. 필요에 따라 추가 열을 수동으로 제거할 수 있습니다.

## 성능 및 비용 관리  

### 쿼리 성능 문제 해결
스트레치 지원 테이블이 포함된 쿼리는 테이블에서 스트레치를 사용하기 전보다 실행 속도가 느립니다. 쿼리 성능이 크게 저하된 경우 다음과 같은 문제를 검토하세요.

-   Azure 서버가 SQL Server와 다른 지리적 지역에 있습니까? 네트워크 대기 시간을 줄이기 위해 SQL Server와 동일한 지리적 지역에 있도록 Azure 서버를 구성합니다.

-   네트워크 상태가 저하될 수 있습니다. 최근에 발생한 문제 또는 중단에 대한 정보는 네트워크 관리자에게 문의하세요.

### 인덱싱 등 리소스를 많이 사용하는 작업에 대한 Azure 성능 수준 증가
스트레치 데이터베이스에 대해 구성된 큰 테이블에서 인덱스를 빌드하거나 다시 빌드하거나 다시 구성하고, 이 시간 동안 Azure에서 마이그레이션된 데이터에 대해 과도한 쿼리가 예상되는 경우 작업 기간 동안 해당 원격 Azure 데이터베이스의 성능 수준을 높이는 것이 좋습니다. 성능 수준 및 가격에 대한 자세한 내용은 [SQL Server 스트레치 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)을 참조하세요.

### Azure에서 SQL Server 스트레치 데이터베이스 서비스를 일시 중지할 수 없습니다.  
 적절한 성능 및 가격 수준을 선택해야 합니다. 리소스 집약적 작업을 위해 일시적으로 성능 수준을 높이는 경우 작업이 완료된 후에 이전 수준으로 복원하세요. 성능 수준 및 가격에 대한 자세한 내용은 [SQL Server 스트레치 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)을 참조하세요.

## 쿼리의 범위 변경  
 스트레치 지원 테이블에 대한 쿼리는 기본적으로 로컬 및 원격 데이터를 모두 반환합니다. 모든 사용자에 대해서는 모든 쿼리의 쿼리 범위를 변경할 수 있지만 관리자의 경우는 단일 쿼리의 쿼리 범위만 변경할 수 있습니다.

### 모든 사용자에 대해 모든 쿼리의 쿼리 범위 변경  
 모든 사용자에 대해 모든 쿼리의 범위를 변경하려면 저장 프로시저 **sys.sp\_rda\_set\_query\_mode**를 실행합니다. 로컬 데이터만 쿼리하도록 범위를 줄이거나, 모든 쿼리를 사용하지 않도록 설정하거나, 기본 설정을 복원할 수 있습니다. 자세한 내용은 [sys.sp\_rda\_set\_query\_mode](https://msdn.microsoft.com/library/mt703715.aspx)를 참조하세요.

### <a name="queryHints"></a>관리자에 대해 단일 쿼리의 쿼리 범위 변경  
 Db\_owner 역할 구성원의 단일 쿼리에 대한 범위를 변경하려면 **WITH ( REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *value* )** 쿼리 힌트를 SELECT 문에 추가합니다. REMOTE\_DATA\_ARCHIVE\_OVERRIDE 쿼리 힌트는 다음 값을 가질 수 있습니다.
 -   **LOCAL\_ONLY**. 로컬 데이터만 쿼리합니다.

 -   **REMOTE\_ONLY**. 원격 데이터만 쿼리합니다.

 -   **STAGE\_ONLY**. 스트레치 데이터베이스에서 마이그레이션에 적합한 행을 준비하고 마이그레이션 후 지정된 기간 동안 마이그레이션된 행을 유지하는 테이블의 데이터만 쿼리합니다. 이 쿼리 힌트는 스테이징 테이블을 쿼리하는 유일한 방법입니다.

예를 들어 다음 쿼리는 로컬 결과만 반환합니다.

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>관리 업데이트 및 삭제 수행  
 기본적으로 스트레치 지원 테이블에서 마이그레이션에 적합한 행이나 이미 마이그레이션된 행은 업데이트하거나 삭제할 수 없습니다. 이 문제를 해결해야 할 경우 Db\_owner 역할의 구성원은 문에 **WITH ( REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *value* )** 쿼리 힌트를 추가하여 업데이트 또는 삭제 작업을 실행할 수 있습니다. REMOTE\_DATA\_ARCHIVE\_OVERRIDE 쿼리 힌트는 다음 값을 가질 수 있습니다.
 -   **LOCAL\_ONLY**. 로컬 데이터만 업데이트 또는 삭제합니다.

 -   **REMOTE\_ONLY**. 원격 데이터만 업데이트 또는 삭제합니다.

 -   **STAGE\_ONLY**. 스트레치 데이터베이스에서 마이그레이션에 적합한 행을 준비하고 마이그레이션 후 지정된 기간 동안 마이그레이션된 행을 유지하는 테이블의 데이터만 업데이트하거나 삭제합니다.

## 참고 항목

[스트레치 데이터베이스 모니터링](sql-server-stretch-database-monitor.md)

[스트레치 지원 데이터베이스 백업](sql-server-stretch-database-backup.md)

[스트레치 지원 데이터베이스 복원](sql-server-stretch-database-restore.md)

<!---HONumber=AcomDC_0629_2016-->