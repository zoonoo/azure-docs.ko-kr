<properties
	pageTitle="Azure SQL 데이터베이스 복사 | Microsoft Azure"
	description="Azure SQL 데이터베이스 사본 만들기"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="01/20/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure SQL 데이터베이스 복사

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)

다음 단계는 [Azure 포털](https://portal.azure.com)로 SQL 데이터베이스 사본을 만드는 방법을 보여 줍니다. 데이터베이스 복사 작업은 새 SQL 데이터베이스를 만듭니다. 사본은 동일한 서버 또는 다른 서버에서 만든 데이터베이스의 스냅샷 백업입니다.

> [AZURE.NOTE]Azure SQL 데이터베이스는 모든 사용자 데이터베이스에 대해 복원할 수 있는 백업을 자동으로 만들고 유지 관리합니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

복사 프로세스가 완료되면 새 데이터베이스가 원본 데이터베이스와는 독립적으로 완벽히 작동하는 데이터베이스가 됩니다. 새 데이터베이스는 복사가 완료된 시스템의 원본 데이터베이스와 트랜잭션이 일치합니다. 데이터베이스 사본의 서버 계층과 성능 수준(가격 책정 계층)은 원본 데이터베이스와 동일합니다. 복사 완료 후 사본은 완전히 작동하는 독립 데이터베이스입니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다.


데이터베이스를 동일한 논리적 서버에 복사할 때 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하느 보안 주체는 새 데이터베이스의 데이터베이스 소유자(DBO)가 됩니다. 모든 데이터베이스 사용자, 권한 및 보안 식별자(SID)가 데이터베이스 사본에 복사됩니다.


SQL 데이터베이스를 복사하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- 복사할 SQL 데이터베이스. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.



## SQL 데이터베이스 복사

복사할 데이터베이스에 대한 SQL 데이터베이스 블레이드를 엽니다.

1.	[Azure 포털](https://portal.azure.com)로 이동합니다.
2.	복사할 데이터베이스로 이동: 찾아보기 > SQL 데이터베이스
3.	SQL 데이터베이스 블레이드에서 **복사**를 클릭하여 **복사** 블레이드를 엽니다.

    ![데이터베이스 복사][1]

1.  데이터베이스 사본의 이름을 입력합니다. 기본 이름이 제공되지만 원하면 변경할 수 있습니다.
2.  **대상 서버**를 선택합니다. 대상 서버는 데이터베이스 복사본이 만들어지는 위치입니다. 새 서버를 만들거나 목록에서 기존 서버 목록를 선택할 수 있습니다.
3.  **확인**을 클릭하여 복사 프로세스를 시작합니다.

    ![데이터베이스 이름 및 서버][2]




## 복사 작업 진행률 모니터링

2.	복사 시작 후 세부 정보를 확인하려면 포털 알림을 클릭합니다.


    ![알림][3]

 
    ![모니터][4]





## 데이터베이스가 서버에서 라이브 상태인지 확인

- **찾아보기** > **SQL 데이터베이스**를 클릭하고 새 데이터베이스가 **온라인** 상태인지 확인합니다.



## 다음 단계

- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)
- [데이터베이스를 BACPAC로 내보내기](sql-database-export.md)



## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-copy/copy.png
[2]: ./media/sql-database-copy/copy-ok.png
[3]: ./media/sql-database-copy/copy-notification.png
[4]: ./media/sql-database-copy/monitor-copy.png

<!---HONumber=AcomDC_0121_2016-->