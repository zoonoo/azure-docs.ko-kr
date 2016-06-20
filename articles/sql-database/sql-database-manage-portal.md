<properties
	pageTitle="Azure 포털을 사용하여 Azure SQL 데이터베이스 관리"
	description="Azure 포털을 사용하여 클라우드의 관계형 데이터베이스를 관리하는 방법을 알아보세요."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.date="05/20/2016"
	ms.author="sstein"/>


# Azure 포털을 사용하여 Azure SQL 데이터베이스 관리


> [AZURE.SELECTOR]
- [Azure 포털](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

[Azure 포털](https://portal.azure.com/)을 사용하여 Azure SQL 데이터베이스 및 논리 서버를 만들고, 모니터링하고, 관리할 수 있습니다. 이 문서에서는 보다 일반적인 작업에 속하는 몇 가지 사항을 중점적으로 설명합니다.

![데이터베이스 개요](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\. 데이터베이스 관리 작업

![데이터베이스 관리 작업](./media/sql-database-manage-portal/sqldatabase_actions.png)

Azure 포털은 데이터베이스 블레이드 상단에 액세스 가능한 일반적인 데이터베이스 작업 집합을 제공합니다. 사용자는 데이터베이스를 이전 시점으로 복원하고, Visual Studio에서 데이터베이스를 열고, 데이터베이스를 새 서버로 복사하고, 데이터베이스를 Azure 저장소 계정으로 내보낼 수 있습니다.

- [SQL 데이터베이스 복원](sql-database-user-error-recovery.md)
- [Visual Studio에서 SQL 데이터베이스 열기](sql-database-connect-query.md)
- [SQL 데이터베이스 내보내기](sql-database-export.md)

## 2\. 데이터베이스 모니터링

![데이터베이스 모니터링](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Azure SQL 데이터베이스는 기본적으로 DTU(데이터베이스 트랜잭션 단위), 데이터베이스 크기 및 연결 상태에 대한 모니터링 차트를 제공합니다. CPU 비율, 데이터 IO 비율, 교착 상태, 로그 IO 비율 또는 심지어 방화벽에 의해 차단된 요청 비율을 추가로 표시하도록 이러한 모니터링 차트를 사용자 지정하고 확장할 수 있습니다.

또한 지정된 메트릭을 모니터링하다가 미리 설정된 임계값에 도달하면 지정된 관리자 및 공동 관리자에게 경고하도록 경고 규칙을 설정할 수 있습니다.

## 3\. 데이터베이스 보안 및 감사

![데이터베이스 보안](./media/sql-database-manage-portal/sqldatabase_security.png)

모든 데이터베이스 이벤트를 추적하여 Azure 저장소 계정의 감사 로그에 기록하도록 Azure SQL 데이터베이스를 구성할 수 있습니다. 이 기능은 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치를 파악하는 데 도움이 될 수 있습니다.

- [SQL 데이터베이스 감사](sql-database-auditing-get-started.md)

또한 중요한 데이터를 권한 없는 사용자에게 마스킹하도록 Azure SQL 데이터베이스를 구성할 수 있습니다.

- [동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)


## 4\. 지역에서 복제

![지역에서 복제](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

커밋된 트랜잭션을 보조 데이터베이스에 비동기적으로 복제하도록 Azure SQL 데이터베이스를 구성할 수 있습니다. 포털의 지역에서 복제 파트를 사용하여 보조 데이터베이스가 상주할 Azure 지역을 선택할 수 있습니다.

- [지역에서 복제](sql-database-geo-replication-overview.md)



## 추가 리소스

- [SQL 데이터베이스](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0608_2016-->