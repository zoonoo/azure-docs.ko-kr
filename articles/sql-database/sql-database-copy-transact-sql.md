<properties 
    pageTitle="Transact-SQL을 사용하여 Azure SQL 데이터베이스 복사 | Microsoft Azure" 
    description="Transact-SQL을 사용하여 Azure SQL 데이터베이스 사본 만들기" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Transact-SQL을 사용하여 Azure SQL 데이터베이스 복사


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)



다음 단계는 Transact-SQL에서 SQL데이터베이스 사본을 만드는 방법을 보여 줍니다. 데이터베이스 복사 작업은 [CREATE DATABASE]() 문을 사용하여 SQL 데이터베이스를 새 데이터베이스에 복사합니다. 사본은 동일한 서버 또는 다른 서버에서 만든 데이터베이스의 스냅샷 백업입니다.


> [AZURE.NOTE] Azure SQL 데이터베이스는 모든 사용자 데이터베이스에 대해 복원할 수 있는 백업을 자동으로 만들고 유지 관리합니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.


복사 프로세스가 완료되면 새 데이터베이스가 원본 데이터베이스와는 독립적으로 완벽히 작동하는 데이터베이스가 됩니다. 새 데이터베이스는 복사가 완료된 시스템의 원본 데이터베이스와 트랜잭션이 일치합니다. 데이터베이스 사본의 서버 계층과 성능 수준(가격 책정 계층)은 원본 데이터베이스와 동일합니다. 복사 완료 후 사본은 완전히 작동하는 독립 데이터베이스입니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다.


데이터베이스를 동일한 논리적 서버에 복사할 때 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하느 보안 주체는 새 데이터베이스의 데이터베이스 소유자(DBO)가 됩니다. 모든 데이터베이스 사용자, 권한 및 보안 식별자(SID)가 데이터베이스 사본에 복사됩니다.


이 문서의 단계를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.
- [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx). SSMS가 없거나 이 문서에서 설명한 기능을 사용할 수 없는 경우 [최신 버전을 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)합니다.




## SQL 데이터베이스 복사

서버 수준 보안 주체 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 주 데이터베이스에 로그온합니다. 데이터베이스를 복사하려면 서버 수준 보안 주체가 아닌 로그인은 dbmanager 역할의 구성원이어야 합니다. 로그인 및 서버 연결에 대한 자세한 내용은 각각 Azure SQL 데이터베이스에서의 데이터베이스, 로그인 및 사용자 관리 및 Azure SQL 데이터베이스 개발: 방법 항목을 참조하세요.

CREATE DATABASE 문으로 원본 데이터베이스 복사를 시작합니다. 이 문을 실행하면 데이터베이스 복사 프로세스가 시작됩니다. 데이터베이스 복사는 비동기 프로세스이므로 CREATE DATABASE 문은 데이터베이스가 복사를 완료하기 전에 반환됩니다.


### 동일한 서버에 SQL 데이터베이스 복사

서버 수준 보안 주체 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 주 데이터베이스에 로그온합니다. 데이터베이스를 복사하려면 서버 수준 보안 주체가 아닌 로그인은 dbmanager 역할의 구성원이어야 합니다.

이 명령은 Database1을 동일한 서버에서 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### SQL 데이터베이스를 다른 서버에 복사

새 데이터베이스를 만들 Azure SQL데이터베이스 서버인 대상 서버의 주 데이터베이스에 로그온합니다. 원본 Azure SQL 데이터베이스 서버에서 원본 데이터베이스의 데이터베이스 소유자(DBO)와 이름 및 암호가 같은 로그인을 사용합니다. 대상 서버의 로그인도 dbmanager 역할의 구성원이거나 서버 수준 보안 주체 로그인이어야 합니다.

이 명령은 server1의 Database1을, server2에서 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## 복사 작업 진행률 모니터링

sys.databases 및 sys.dm\_database\_copies 뷰 쿼리를 통해 복사 프로세스를 모니터링합니다. 복사가 진행 중인 동안 새 데이터베이스의 sys.databases 뷰에 대한 state\_desc 열은 COPYING으로 설정됩니다.


- 복사가 실패하면 새 데이터베이스의 sys.databases 뷰에 대한 state\_desc 열은 SUSPECT로 설정됩니다. 이 경우 새 데이터베이스에서 DROP 문을 실행하고 나중에 다시 시도합니다.
- 복사에 성공하면 새 데이터베이스의 sys.databases 뷰에 대한 state\_desc 열은 ONLINE으로 설정됩니다. 이 경우 복사가 완료되었으며 새 데이터베이스가 일반 데이터베이스가 되어 원본 데이터베이스와는 독립적으로 변경 가능합니다.



## 다음 단계


- 진행 중인 복사를 취소하려면 새 데이터베이스에서 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 문을 실행합니다. 또는 원본 데이터베이스에서 DROP DATABASE 문을 실행해도 복사 프로세스가 취소됩니다.
- 대상 서버에서 새 데이터베이스가 온라인 상태가 되면 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 문을 사용하여 새 데이터베이스의 사용자를 대상 서버의 로그인과 다시 매핑합니다. 새 데이터베이스의 모든 사용자가 원본 데이터베이스에서 가졌던 사용 권한을 그대로 유지합니다. 데이터베이스 복사를 실행한 사용자가 새 데이터베이스의 소유자가 되며 새 보안 식별자(SID)를 할당 받습니다. 복사 성공 후 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자(DBO)인 복사를 시작한 로그인만 새 데이터베이스에 로그온할 수 있습니다.




## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0608_2016-->