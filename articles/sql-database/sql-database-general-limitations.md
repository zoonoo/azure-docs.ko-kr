<properties 
   pageTitle="Azure SQL 데이터베이스 일반 제한 사항 및 지침"
   description="이 페이지는 Azure SQL 데이터베이스의 일반 제한 사항 일부와 상호 운용성 및 지원 영역을 설명합니다."
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/28/2015"
   ms.author="jroth" />

# Azure SQL 데이터베이스 일반 제한 사항 및 지침

이 항목에서는 Azure SQL 데이터베이스에 대한 일반적인 제한 사항 및 지침을 제공합니다. 할당량, 리소스 관리 및 지원을 완전히 이해하려면 이 항목의 끝에 있는 [추가 리소스](#additional-guidelines)를 참조하세요.

## 연결

 - Windows 인증은 지원되지 않습니다. [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요. 

 - Microsoft Azure SQL 데이터베이스는 TDS(Tabular Data Stream) 프로토콜 클라이언트 버전 7.3 이상을 지원합니다.

 - TCP/IP 연결만 허용됩니다.

 - Microsoft Azure SQL 데이터베이스에 동적 포트가 없고 포트 1433만 있기 때문에 SQL Server 2008 SQL Server 브라우저는 지원되지 않습니다.

## SQL Server 에이전트/작업

Microsoft Azure SQL 데이터베이스는 SQL Server 에이전트 또는 작업을 지원하지 않습니다. 그러나 온-프레미스 SQL Server에서 SQL Server 에이전트를 실행하고 Microsoft Azure SQL 데이터베이스에 연결할 수 있습니다.

## 트랜잭션

Azure SQL 데이터베이스는 여러 리소스에 영향을 주는 트랜잭션인 분산 트랜잭션을 지원하지 않습니다. 자세한 내용은 [분산 트랜잭션(ADO.NET)](https://msdn.microsoft.com/library/ms254973.aspx)을 참조하세요. SQL 데이터베이스는 장애 조치에서 현재 데이터베이스(DBTS)의 커밋되지 않은 타임스탬프 값을 보존하지 않을 수 있습니다.

> [AZURE.NOTE]특정한 상황에서 트랜잭션은 분산 트랜잭션으로 자동 승격할 수 있습니다. 자세한 내용은 [SQL Server와 System.Transactions 통합](https://msdn.microsoft.com/library/ms172070.aspx)을 참조하세요.

## SQL Server 데이터 정렬 지원

Microsoft Azure SQL 데이터베이스에서 사용하는 기본 데이터베이스 데이터 정렬은 **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**이며 여기서 **LATIN1\_GENERAL**은 영어(미국), **CP1**은 코드 페이지 1252, **CI**는 대/소문자 구분, **AS**는 악센트를 구분합니다.

온-프레미스 SQL Server서버 사용할 때 서버, 데이터베이스, 열 및 식 수준 데이터 정렬을 설정할 수 있습니다. Microsoft Azure SQL 데이터베이스는 서버 수준에서 데이터 정렬을 설정할 수 없도록 합니다. Microsoft Azure SQL 데이터베이스로 기본이 아닌 데이터 정렬을 사용하려면 Create Database Collate 옵션을 사용하여 또는 열 수준 또는 식 수준에서 데이터 정렬을 설정합니다. SQL 데이터베이스는 Alter Database 명령으로 Collate 옵션을 지원하지 않습니다. 기본적으로 SQL 데이터베이스에서 임시 데이터에는 데이터베이스와 동일한 데이터 정렬이 있습니다. 데이터 정렬을 설정하는 방법에 대한 자세한 내용은 [COLLATE(Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)를 참조하세요.

## 명명 요구 사항

보안상의 이유로 특정 사용자 이름은 허용되지 않습니다. 다음 이름은 사용할 수 없습니다.

 - **admin** 
 - **administrator** 
 - **guest** 
 - **root** 
 - **sa** 

모든 새 개체에 대한 이름은 식별자에 대한 SQL Server 규칙을 준수해야 합니다. 자세한 내용은 [Identifiers](https://msdn.microsoft.com/library/ms175874.aspx)를 참조하세요.

또한 로그인 및 사용자 이름에는 \\문자(Windows 인증이 지원되지 않음)가 포함될 수 없습니다.

## 추가 지침

- 이 문서에 설명된 일반 제한 사항 외에도 SQL 데이터베이스에는 [서비스 계층](sql-database-service-tiers.md)을 기반으로 특정 리소스 할당량 및 제한 사항이 있습니다. 서비스 계층 제한 사항의 자세한 설명은 [서비스 계층 기능 및 제한](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)을 참조하세요.

- 기타 SQL 데이터베이스 제한은 [Azure SQL 데이터베이스 리소스 제한](sql-database-limits.md)을 참조하세요.

- 보안 관련 지침은 [Azure SQL 데이터베이스 보안 지침 및 제한 사항](sql-database-security-guidelines.md)을 참조하세요.

- 다른 관련된 영역은 Azure SQL 데이터베이스와 SQL Server 2014 등 SQL Server 온-프레미스 버전과의 호환성을 둘러쌉니다. Azure SQL 데이터베이스의 최신 V12 버전에는 이 영역에 향상된 기능이 많이 있습니다. 자세한 내용은 [SQL 데이터베이스 V12의 새로운 기능](sql-database-v12-whats-new.md)을 참조하세요.

- 드라이버 가용성 및 SQL 데이터베이스 지원에 대한 내용은 [SQL 데이터베이스 및 SQL Server의 연결 라이브러리](sql-database-libraries.md)를 참조하세요.

<!---HONumber=Nov15_HO1-->