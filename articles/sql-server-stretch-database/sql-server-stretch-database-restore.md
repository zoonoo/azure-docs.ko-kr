<properties
	pageTitle="스트레치 지원 데이터베이스 복원 | Microsoft Azure"
	description="스트레치 지원 데이터베이스를 복원하는 방법을 알아봅니다."
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
	ms.date="08/01/2016"
	ms.author="douglasl"/>

# 스트레치 지원 데이터베이스 복원

다양한 유형의 실패, 오류 및 재해로부터 복구하는 데 필요할 경우 백업한 데이터베이스를 복원합니다.

백업에 대한 자세한 내용은 [스트레치 지원 데이터베이스 백업](sql-server-stretch-database-backup.md)을 참조하세요.

>   [AZURE.NOTE] 백업은 전체 고가용성 및 비즈니스 연속성 솔루션의 한 부분입니다. 고가용성에 대한 자세한 내용은 [고가용성 솔루션](https://msdn.microsoft.com/library/ms190202.aspx)을 참조하세요.

## SQL Server 데이터 복원
하드웨어 오류 또는 손상으로부터 복구하려면 백업에서 스트레치 지원 SQL Server 데이터베이스를 복원합니다. 현재 사용하는 SQL Server 복원 메서드를 계속 사용할 수 있습니다. 자세한 내용은 [복원 및 복구 개요](https://msdn.microsoft.com/library/ms191253.aspx)를 참조하세요.

SQL Server 데이터베이스를 복원한 후에는 저장 프로시저 **sys.sp\_rda\_reauthorize\_db**를 실행하여 스트레치 지원 SQL Server 데이터베이스와 원격 Azure 데이터베이스 간에 연결을 다시 설정해야 합니다. 자세한 내용은 [SQL Server 데이터베이스와 원격 Azure 데이터베이스 간 연결 복원](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database)을 참조하세요.

## 원격 Azure 데이터 복원

### 라이브 Azure 데이터베이스 복구
Azure의 SQL Server 스트레치 데이터베이스 서비스는 Azure 저장소 스냅숏을 사용하여 적어도 8시간마다 모든 라이브 데이터의 스냅숏을 만듭니다. 이러한 스냅숏은 7일 동안 유지됩니다. 이를 통해 지난 7일 내에 적어도 21개의 특정 시점 중 하나로 마지막 스냅숏 작업 시까지 데이터를 복원할 수 있습니다.

Azure 포털을 사용하여 Azure 데이터베이스를 이전 시점으로 복원하려면 다음을 수행합니다.

1. Azure 포털에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 데이터베이스**를 선택합니다.
3. 사용자의 데이터베이스로 이동한 후 선택합니다.
4. 데이터베이스 블레이드의 위쪽에서 **복원**을 클릭합니다.
5. 새 **데이터베이스 이름**을 지정하고 **복원 지점**을 선택한 다음 **만들기**를 클릭합니다.
6. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.

### 삭제된 Azure 데이터베이스 복구
Azure의 SQL Server 스트레치 데이터베이스 서비스는 데이터베이스가 삭제되기 전에 데이터베이스 스냅숏을 생성하여 7일 동안 유지합니다. 이 작업이 발생하면 라이브 데이터베이스에서 스냅숏을 더 이상 유지하지 않습니다. 이렇게 하면 삭제된 지점으로 삭제된 데이터베이스를 복원할 수 있습니다.

Azure 포털을 사용하여 Azure 데이터베이스를 삭제되었던 시점으로 복원하려면 다음을 수행합니다.

1. Azure 포털에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 서버**를 선택합니다.
3. 사용자의 서버로 이동한 후 선택합니다.
4. 서버 블레이드에서 작업까지 아래로 스크롤하여 **삭제된 데이터베이스** 타일을 클릭합니다.
5. 복원할 삭제된 데이터베이스를 선택합니다.
5. 새 **데이터베이스 이름**을 지정하고 **만들기**를 클릭합니다.
6. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.

## SQL Server 데이터베이스와 원격 Azure 데이터베이스 간 연결 복원

1.  다른 이름을 사용하거나 다른 지역에 있는 복원된 Azure 데이터베이스에 연결하려는 경우 저장 프로시저 [sys.sp\_rda\_deauthorize\_db](https://msdn.microsoft.com/library/mt703716.aspx)를 실행하여 이전 Azure 데이터베이스에서의 연결을 끊습니다.

2.  저장 프로시저 [sys.sp\_rda\_reauthorize\_db](https://msdn.microsoft.com/library/mt131016.aspx)를 실행하여 로컬 스트레치 지원 데이터베이스를 Azure 데이터베이스에 다시 연결합니다.

	-   기존 데이터베이스 범위 자격 증명을 sysname 또는 varchar(128) 값으로 제공합니다. varchar(max)를 사용하지 마세요. **sys.database\_scoped\_credentials** 뷰에서 자격 증명 이름을 조회할 수 있습니다.

	-   원격 데이터의 복사본을 만들고 복사본에 연결할 것인지 지정합니다(권장).

    ```tsql  
    USE <Stretch-enabled database name>;
	GO
	EXEC sp_rda_reauthorize_db
	    @credential = N'<existing_database_scoped_credential_name>',
		@with_copy = 1 ;  
	GO
	```  

## 참고 항목

[스트레치 데이터베이스 관리 및 문제 해결](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db(Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[SQL Server 데이터베이스 백업 및 복원](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0803_2016-->