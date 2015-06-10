<properties 
	pageTitle="Azure SQL 데이터베이스 연결 문제" 
	description="SQL 데이터베이스 연결 오류를 파악하고 판단합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="04/14/2015" 
	ms.author="sstein"/>


# SQL 데이터베이스 연결 문제

이 문서는 Azure SQL 데이터베이스에 연결할 수 없을 때 다양한 문제 해결 도구를 사용하는 방법에 대한 개요를 설명합니다.


## 연결 문제가 개별 응용 프로그램에 한정된 것인지, 데이터베이스에 연결만 할 수 없는 것인지 확인하셨습니까?

SQL Server Management Studio 또는 SQLCMD.EXE를 사용하여 데이터베이스에 연결할 수 있는지 확인할 수 있습니다.

- SSMS(SQL Server Management Studio)를 사용하여 SQL 데이터베이스에 연결하는 방법은 [SSMS를 사용하여 Azure SQL 데이터베이스에 연결하는 방법](sql-database-connect-to-database.md)을 참조하십시오.
- SQLCMD를 사용하여 SQL 데이터베이스에 연결하는 방법은 [방법: sqlcmd를 사용하여 Azure SQL 데이터베이스에 연결](https://msdn.microsoft.com/library/azure/ee336280.aspx)을 참조하십시오.



## SQL 데이터베이스에 연결하려고 시도하는 응용 프로그램이 Azure에서 실행되고 있습니까? 예를 들어 응용 프로그램에서 클라우드 서비스 또는 웹 응용 프로그램 데이터베이스에 연결할 수 없습니까?

방화벽 규칙에서 서버/데이터베이스에 대한 모든 Azure 서비스를 허용하는지 확인합니다.

- 방화벽 규칙과 Azure의 연결을 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)을 참조하십시오.



## SQL 데이터베이스에 연결하려고 시도하는 응용 프로그램이 사설 네트워크에 있습니까?

방화벽 규칙에서 서버/데이터베이스에 대해 특정 네트워크에서의 액세스를 허용하는지 확인합니다.

- 방화벽 규칙에 대한 일반 정보는 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx)을 참조하십시오.
- 방화벽 규칙 설정 방법은 [방법: 방화벽 설정 구성(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/azure/jj553530.aspx)을 참조하십시오.


## 방화벽 규칙이 올바르게 구성된 경우 [Microsoft Azure SQL 데이터베이스 연결 문제 해결](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1) 안내 방식 연습을 시도해 보십시오.

위 지원 문서는 다음 SQL 데이터베이스 연결 문제에 대한 도움말을 제공합니다.

- 방화벽 설정으로 인해 서버 연결을 열 수 없음 
- 서버를 찾을 수 없거나 액세스할 수 없음 
- 서버에 로그인할 수 없음 
- 연결 시간 초과 오류 
- 일시적인 오류 
- 일부 시스템 정의 제한에 도달하여 연결이 종료됨 
- SSMS(SQL Server Management Studio)에서 연결 실패 


## 추가 정보

- SQL 데이터베이스 연결에 대한 자세한 내용은[프로그래밍 방식으로 Azure SQL 데이터베이스에 연결하기 위한 지침](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조하십시오.   

- 특정 연결 오류에 대한 자세한 내용은 [오류 메시지(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors)의 **연결 끊김 오류** 섹션에서 찾을 수 있습니다.

- 연결 이벤트 데이터는 [**sys.event_log(Azure SQL 데이터베이스)**](https://msdn.microsoft.com/library/dn270018.aspx) 뷰를 사용하여 연결 이벤트를 쿼리하여 액세스할 수 있습니다.

- 데이터베이스 연결 이벤트의 메트릭은 [**sys.database_connection_stats (Azure SQL Database)**](https://msdn.microsoft.com/library/dn269986.aspx) 뷰를 쿼리하여 액세스할 수 있습니다.

<!---HONumber=58-->