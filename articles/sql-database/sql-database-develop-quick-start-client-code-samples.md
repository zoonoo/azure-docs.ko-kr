<properties 
	pageTitle="SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플 | Microsoft Azure" 
	description="Linux에서 Node.js, Mac OS에서 Python, Java 및 Windows 및 Azure SQL 데이터베이스 클라이언트에 대 한 코드 샘플 및 드라이버를 제공합니다."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플


이 항목에서는 Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 빠른 시작 코드 샘플의 링크를 제공합니다.


- 짧은 샘플 연결 및 쿼리입니다.
- 재시도 샘플 연결 및 쿼리지만 발생한 오류가 [*일시적인 오류*](sql-database-develop-error-messages.md#bkmk_connection_errors)(예: 연결 시간 제한)로 분류되는 경우 자동으로 재시도합니다.


샘플에서 다루는 내용은 다음과 같습니다.


- 다양한 프로그래밍 언어
- 클라이언트 프로그램이 실행될 운영 체제로서의 Windows, Linux 및 Mac OS
- 모든 필요한 연결 드라이버 다운로드에 대한 링크입니다.
- 짧은 빠른 시작 코드 샘플입니다.
- 자동화된 재시도 논리 형태의 일시적인 오류를 포함하는 긴 샘플입니다.
- 관계형 결과 집합을 개체 지향 형식으로 변환하는 코드 샘플


> [AZURE.NOTE]더 많은 언어의 코드 샘플이 준비되고 있으며, 이 샘플에 대한 링크가 이 항목에 추가됩니다.


## Linux 기반 클라이언트


이 섹션에서는 Linux에서 실행되는 클라이언트 프로그램에 대한 코드 샘플 항목의 링크를 제공합니다.


| 언어 | 간단한 샘플 | 재시도 샘플 | 관계형에서 개체로 |
| :-- | :-- | :-- | :-- |
| Node.js | [지루한](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-unbutu-linux.md) | . | . |
| Ruby | [FreeTDS, TinyTDS](sql-database-develop-ruby-simple-linux.md) | . | . |


## Mac OS 기반 클라이언트


이 섹션에서는 Mac OS에서 실행되는 클라이언트 프로그램에 대한 코드 샘플 항목의 링크를 제공합니다.


| 언어 | 간단한 샘플 | 재시도 샘플 | 관계형에서 개체로 |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |
| Ruby | [Homebrew<br/>FreeTDS, TinyTDS](sql-database-develop-ruby-simple-mac-osx.md) | . | . |


## Windows 기반 클라이언트


이 섹션에서는 Windows에서 실행되는 클라이언트 프로그램에 대한 코드 샘플 항목의 링크를 제공합니다.


| 언어 | 간단한 샘플 | 재시도 샘플 | 관계형에서 개체로 |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET 사용자 지정](sql-database-develop-csharp-retry-windows.md)<br/><br/>[ADO.NET 및 Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx) | [ADO.NET Entity Framework](http://msdn.microsoft.com/library/azure/ff951633.aspx) |
| C++ | [ODBC 드라이버](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. 삽입, 트랜잭션, 선택.](sql-database-develop-java-simple-windows.md)<br/><br/>[Java. Eclipse](sql-data-java-how-to-use-sql-database.md)<br/><br/>[Java. JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [ODBC 사용자 지정](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## 참고 항목


- [다양한 언어 및 플랫폼에 대한 도구 및 SDK 다운로드](http://azure.microsoft.com/downloads/#cmd-line-tools)
- [SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)
- [일시적인 오류에 대한 숫자 코드 목록](sql-database-develop-error-messages.md#bkmk_connection_errors)<br/>&nbsp;
- [Azure SQL 데이터베이스 개발: 방법 도움말 항목](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [SQL 데이터베이스 연결: 링크, 모범 사례, 설계 지침](sql-database-connect-central-recommendations.md)
- [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)

<!---HONumber=July15_HO5-->