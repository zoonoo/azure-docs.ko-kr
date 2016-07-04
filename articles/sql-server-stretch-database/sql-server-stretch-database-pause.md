<properties
	pageTitle="데이터 마이그레이션 일시 중지 및 다시 시작(스트레치 데이터베이스) | Microsoft Azure"
	description="Azure에 대한 데이터 마이그레이션을 일시 중지하거나 다시 시작하는 방법에 대해 알아봅니다."
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
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# 데이터 마이그레이션 일시 중지 및 다시 시작(스트레치 데이터베이스)

Azure에 대한 데이터 마이그레이션을 일시 중지하거나 다시 시작하려면 SQL Server Management Studio의 테이블에 대해 **스트레치**를 선택한 다음 **일시 중지**를 선택하여 데이터 마이그레이션을 일시 중지하거나 **다시 시작**을 선택하여 데이터 마이그레이션을 다시 시작합니다. 또한 TRANSACT-SQL을 사용하여 데이터 마이그레이션을 일시 중지하거나 다시 시작할 수 있습니다.

로컬 서버에서 문제를 해결하거나 사용 가능한 네트워크 대역폭을 최대화하려는 경우 개별 테이블에서 데이터 마이그레이션을 일시 중지합니다.

## 데이터 마이그레이션 일시 중지

### SQL Server Management Studio를 사용하여 데이터 마이그레이션 일시 중지

1.  SQL Server Management Studio의 개체 탐색기에서 데이터 마이그레이션을 일시 중지하려는 스트레치 사용 테이블을 선택합니다.

2.  마우스 오른쪽 단추로 클릭하고 **스트레치**를 선택한 다음 **일시 중지**를 선택합니다.

### TRANSACT-SQL을 사용하여 데이터 마이그레이션 일시 중지
다음 명령을 실행합니다.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## 데이터 마이그레이션 다시 시작

### SQL Server Management Studio를 사용하여 데이터 마이그레이션 다시 시작

1.  SQL Server Management Studio의 개체 탐색기에서 데이터 마이그레이션을 다시 시작하려는 스트레치 사용 테이블을 선택합니다.

2.  마우스 오른쪽 단추로 클릭하고 **스트레치**를 선택한 다음 **다시 시작**을 선택합니다.

### TRANSACT-SQL을 사용하여 데이터 마이그레이션 다시 시작
다음 명령을 실행합니다.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## 마이그레이션이 활성화되었는지 또는 일시 중지되었는지 확인

### SQL Server Management Studio를 사용하여 마이그레이션이 활성화되었는지 또는 일시 중지되었는지 확인
SQL Server Management Studio를 열고 **스트레치 데이터베이스 모니터**를 열고 **마이그레이션 상태** 열 값을 확인합니다. 자세한 내용은 [데이터 마이그레이션 모니터링 및 문제 해결](sql-server-stretch-database-monitor.md)을 참조하세요.

### Transact-SQL을 사용하여 마이그레이션이 활성화되었는지 또는 일시 중지되었는지 확인
카탈로그 뷰 **sys.remote\_data\_archive\_tables**를 쿼리하고 **is\_migration\_paused** 열의 값을 확인합니다. 자세한 내용은 [sys.remote\_data\_archive\_tables](https://msdn.microsoft.com/library/dn935003.aspx)를 참조하세요.

## 참고 항목

[ALTER TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx) [데이터 마이그레이션 모니터링 및 문제 해결](sql-server-stretch-database-monitor.md)

<!---HONumber=AcomDC_0622_2016-->