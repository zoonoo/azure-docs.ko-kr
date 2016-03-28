<properties
	pageTitle="데이터 마이그레이션 모니터링 및 문제 해결(스트레치 데이터베이스) | Microsoft Azure"
	description="데이터 마이그레이션 상태를 모니터링하는 방법에 대해 알아봅니다."
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
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# 데이터 마이그레이션 모니터링 및 문제 해결(스트레치 데이터베이스)

스트레치 데이터베이스 모니터에서 데이터 마이그레이션을 모니터링하려면 SQL Server Management Studio에서 데이터베이스에 대한 **작업 | 스트레치 | 모니터**를 선택합니다.

## 스트레치 데이터베이스 모니터에서 데이터 마이그레이션 상태를 확인합니다.
SQL Server Management Studio의 데이터베이스에 대해 **작업 | 스트레치 | 모니터**를 선택하여 스트레치 데이터베이스 모니터를 열고 데이터 마이그레이션을 모니터링합니다.

-   모니터의 맨 위에는 스트레치 사용 SQL Server 데이터베이스 및 원격 Azure 데이터베이스에 대한 일반 정보가 표시됩니다.

-   모니터의 맨 아래에는 데이터베이스의 각 스트레치 사용 테이블에 대한 데이터 마이그레이션 상태가 표시됩니다.

![스트레치 데이터베이스 모니터][StretchMonitorImage1]

## <a name="Migration"></a>동적 관리 뷰에서 데이터 마이그레이션 상태를 확인합니다.
동적 관리 뷰 **sys.dm\_db\_rda\_migration\_status**를 열고 마이그레이션된 배치 수 및 데이터 행 수를 확인합니다. 자세한 내용은 [sys.dm\_db\_rda\_migration\_status(TRANSACT-SQL)](https://msdn.microsoft.com/library/dn935017.aspx)를 확인하세요.

## <a name="Firewall"></a>데이터 마이그레이션 문제 해결
**Azure 방화벽이 내 로컬 서버에서 연결을 차단합니다.**

SQL Server가 원격 Azure 서버와 통신할 수 있도록 Azure 서버의 Azure 방화벽 설정에서 규칙을 추가해야 합니다.

**내 스트레치 사용 테이블의 행이 Azure로 마이그레이션되지 않습니다. 문제가 뭔가요?**

마이그레이션에 영향을 줄 수 있는 몇 가지 문제가 있습니다. 다음 항목을 확인합니다.

-   SQL Server 컴퓨터의 네트워크 연결을 확인합니다.

-   Azure 방화벽이 SQL Server와 원격 끝점에 대한 연결을 차단하고 있지 않는지 확인합니다.

-   최신 배치 상태에 대해 동적 관리 뷰 **sys.dm\_db\_rda\_migration\_status**를 확인합니다. 오류가 발생한 경우 해당 배치에 대한 error\_number, error\_state 및 error\_severity 값을 확인합니다.

    -   뷰에 대한 자세한 내용은 [sys.dm\_db\_rda\_migration\_status(Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx)를 참조하세요.

    -   SQL Server 오류 메시지 내용에 대한 자세한 내용은 [sys.messages(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms187382.aspx)를 참조하세요.

## 참고 항목

[Manage and troubleshoot Stretch Database(스트레치 데이터베이스 관리 및 문제 해결)](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0316_2016-->