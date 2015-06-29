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
	ms.date="06/10/2015" 
	ms.author="genemi"/>


# SQL 데이터베이스에 대한 클라이언트 빠른 시작 코드 샘플


이 항목에서는 Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 빠른 시작 코드 샘플의 링크를 제공합니다. 샘플에서 다루는 내용은 다음과 같습니다.


- 다양한 프로그래밍 언어
- 클라이언트 프로그램이 실행될 운영 체제로서의 Windows, Linux 및 Mac OS
- 짧은 빠른 시작 코드 샘플입니다.
- 자동 재시도 논리로 인한 일시적인 오류를 처리하는 긴 샘플입니다.
- 관계형 결과 집합을 개체 지향 형식으로 변환하는 코드 샘플


> [AZURE.NOTE]2015년 6월 10일 현재 더 많은 언어의 코드 샘플이 준비되고 있으며, 이 항목에 해당 샘플의 링크가 추가될 예정입니다.


## Linux 기반 클라이언트


이 섹션에서는 Linux에서 실행되는 클라이언트 프로그램에 대한 코드 샘플 항목의 링크를 제공합니다.


| 언어 | 짧음 | Retry | 관계형에서 개체로 |
| :-- | :-- | :-- | :-- |
| Node.js | [지루한](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-unbutu-linux.md) | . | . |


## Mac OS 기반 클라이언트


이 섹션에서는 Mac OS에서 실행되는 클라이언트 프로그램에 대한 코드 샘플 항목의 링크를 제공합니다.


| 언어 | 짧음 | Retry | 관계형에서 개체로 |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |


## Windows 기반 클라이언트


이 섹션에서는 Windows에서 실행되는 클라이언트 프로그램에 대한 코드 샘플 항목의 링크를 제공합니다.


| 언어 | 짧음 | Retry | 관계형에서 개체로 |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)<br/><br/>[ADO.NET 및 Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx) | [ADO.NET Entity Framework](http://msdn.microsoft.com/library/azure/ff951633.aspx) |
| C++ | [ODBC 드라이버](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. 삽입, 트랜잭션, 선택.](sql-database-develop-java-simple-windows.md)<br/><br/>[Java. Eclipse](sql-data-java-how-to-use-sql-database.md)<br/><br/>[Java. JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | . | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## 참고 항목


- [다양한 언어 및 플랫폼에 대한 도구 및 SDK 다운로드](http://azure.microsoft.com/downloads/#cmd-line-tools)
- [SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)
- [일시적인 오류에 대한 숫자 코드 목록](http://msdn.microsoft.com/library/azure/ff394106.aspx#bkmk_connection_errors)<br/>&nbsp;
- [Azure SQL 데이터베이스 개발: 방법 도움말 항목](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [SQL 데이터베이스 연결: 링크, 모범 사례, 설계 지침](sql-database-connect-central-recommendations.md)
- [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)

 

<!---HONumber=58_postMigration-->