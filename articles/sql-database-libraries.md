<properties
	pageTitle="SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리"
	description="클라이언트 프로그램에서 Azure SQL 데이터베이스 또는 Microsoft SQL Server에 연결하는 데 사용할 수 있는 각 드라이버의 최소 버전 번호를 나열합니다. Microsoft가 아니라 커뮤니티에서 릴리즈된 드라이버에 대한 버전 정보 링크가 제공됩니다."
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/01/2015"
	ms.author="pehteh"/>


# SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리


이 항목에는 클라이언트 프로그램에서 Azure SQL 데이터베이스 또는 Microsoft SQL Server에 연결하는 데 사용할 수 있는 각 라이브러리/드라이버의 최소 버전 번호가 나열되어 있습니다.


이 항목은 두 섹션으로 구성되어 있습니다.


- *Microsoft에서 출시한 드라이버 라이브러리 표* -Microsoft에서 출시한 라이브러리를 다룹니다. Microsoft는 이 섹션에 포함된 정보를 유지 관리합니다.
- *타사 라이브러리* - Microsoft 이외의 타사에서 출시 및 유지 관리하는 라이브러리를 나열합니다. **개발자 공개 커뮤니티에서만 섹션을 유지 관리합니다. Microsoft는 이 섹션을 유지 관리하지 않습니다.**


## Microsoft에서 출시한 드라이버 라이브러리 표


다음은 Microsoft에서 출시한 라이브러리 표입니다. **라이브러리** 열은 각 라이브러리를 다운로드할 수 있는 링크를 제공합니다. **버전** 열에는 Azure SQL 데이터베이스 및 Microsoft SQL Server와 상호작용하기 위해 권장되는 최소 버전이 나열됩니다.


| 플랫폼 | 운영 체제 | 라이브러리 | 버전 | 설명 |
| :--- | :--- | :--- | :--- | :--- |
| .NET | 크로스 플랫폼(.NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4.5 이상 | .NET Framework에 대한 SQL Server 공급자 |
| PHP | Windows | [PHP for SQL Server](http://www.microsoft.com/ko-kr/download/details.aspx?id=20098) | 2.0 이상 | SQL Server용 PHP 드라이버 |
| Java | Windows | [JDBC for SQL Server](https://www.microsoft.com/ko-kr/download/details.aspx?id=11774) | 2.0 이상 | 표준 JDBC API를 통해 데이터베이스를 연결하는 Type 4 JDBC 드라이버 |
| ODBC | Windows | [ODBC for SQL Server](http://www.microsoft.com/ko-kr/download/details.aspx?id=36434) | 11.0 이상 | SQL Server용 Microsoft ODBC 드라이버 |
| ODBC | Suse Linux | [ODBC for SQL Server](http://www.microsoft.com/ko-kr/download/details.aspx?id=34687) | 11.0 이상 | SQL Server용 Microsoft ODBC 드라이버 |
| ODBC | Redhat Linux | [ODBC for SQL Server](http://www.microsoft.com/ko-kr/download/details.aspx?id=34687) | 11.0 이상 | SQL Server용 Microsoft ODBC 드라이버 |


## 타사 라이브러리


> [AZURE.IMPORTANT]다음은 타사 라이선스 조건에 따라 타사에서 출시한 라이브러리 표입니다. 이러한 라이브러리를 사용하려면 사용자는 관련된 타사 라이선스를 확인하고 준수할 책임이 있습니다. 이러한 라이브러리를 사용하여 발생하는 위험은 사용자의 책임입니다. Microsoft는 여기에 제공된 정보에 대해 어떠한 명시적 또는 묵시적 보증도 하지 않으며, 사용자의 편의를 위해 정보를 제공할 뿐입니다. 여기에 포함된 그 어떤 것도 Microsoft의 보증을 의미하지 않습니다. <br/><br/>GitHub.com에 있는 **Azure** 소유의 [azure-content](http://github.com/Azure/azure-content/) 리포지토리를 사용하여 이 "타사 라이브러리" 섹션의 정보를 업데이트 및 유지 관리하는 것은 개발자 공개 커뮤니티의 몫입니다. Microsoft에서는 개발자들이 이 섹션을 업데이트하기를 권장합니다. Microsoft 직원은 이 섹션의 정보를 유지 관리하지 *않습니다*. 그 이유 중 하나는, 다른 사람들이 특정 타사 라이브러리에 대해 우리보다 잘 알고 있기 때문입니다. 감사합니다.


다음은 타사 또는 타 커뮤티니 같은 제3자가 출시한 라이브러리 표입니다. Microsoft에서 출시한 라이브러리는 이 항목의 앞부분에 있습니다.


| 플랫폼 | 라이브러리 |
| :-- | :-- |
| Python | [pymssql*(org, stable)*](http://pymssql.org/en/stable/)<br/><br/>[pymssql*(org)*](http://pymssql.org/) |
| Node.js | [Tedious*(npmjs)*](http://www.npmjs.com/package/tedious) |
| Node.js | [Node-MSSQL*(github, patriksimek)*](https://github.com/patriksimek/node-mssql)<br/><br/>[Node-MSSQL*(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector*(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| Node.js | [Edge.js*(github com, tjanczuk)*](https://github.com/tjanczuk/edge)<br/><br/>[Edge.js*(tjanczuk, github io)*](http://tjanczuk.github.io/edge/) |
| . | [FreeTDS*(org)*](http://www.freetds.org/) |


<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=58-->