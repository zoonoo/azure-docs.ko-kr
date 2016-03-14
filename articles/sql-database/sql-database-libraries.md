<properties
	pageTitle="SQL 데이터베이스 및 SQL Server용 연결 라이브러리"
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
	ms.date="02/23/2016"
	ms.author="pehteh"/>

# SQL 데이터베이스 및 SQL Server용 연결 라이브러리

이 항목에는 클라이언트 프로그램에서 Azure SQL 데이터베이스 또는 Microsoft SQL Server에 연결하는 데 사용할 수 있는 각 라이브러리/드라이버의 최소 버전 번호가 나열되어 있습니다.

## Microsoft에서 출시한 드라이버 라이브러리 표

다음은 Microsoft에서 출시한 라이브러리 표입니다. **라이브러리** 열은 각 라이브러리를 다운로드할 수 있는 링크를 제공합니다. **버전** 열에는 Azure SQL 데이터베이스 및 Microsoft SQL Server와 상호작용하기 위해 권장되는 최소 버전이 나열됩니다.

| 플랫폼 | 운영 체제 | 라이브러리<br/>다운로드 | 드라이버의 버전<br/> | 드라이버의 설명<br/> | 자세한<br/>정보 |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | 크로스 플랫폼(.NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4\.5 이상 | .NET Framework에 대한 SQL Server 공급자 | . |
| PHP | Windows | [PHP for SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2\.0 이상 | SQL Server용 PHP 드라이버 | [링크](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2\.0 이상 | 표준 JDBC API를 통해 데이터베이스를 연결하는 Type 4 JDBC 드라이버 | [링크](https://msdn.microsoft.com/library/mt654048.aspx) |
| ODBC | Windows | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11\.0 이상 | SQL Server용 Microsoft ODBC 드라이버 | [링크](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0 이상 | SQL Server용 Microsoft ODBC 드라이버 | [링크](https://msdn.microsoft.com/ko-KR/library/hh568451.aspx) |
| ODBC | Redhat Linux | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0 이상 | SQL Server용 Microsoft ODBC 드라이버 | [링크](https://msdn.microsoft.com/ko-KR/library/hh568451.aspx) |

### ODBC 지원

DSN(데이터 원본 이름) 마법사를 사용하여 Azure SQL 데이터베이스의 데이터 원본을 정의하는 경우 **사용자가 입력한 로그인 ID 및 암호를 사용하여 SQL Server 인증** 옵션을 클릭하고 **SQL Server에 연결하여 추가 구성 옵션에 대한 기본 설정 가져오기**를 선택합니다. 사용자 이름 및 암호를 입력하여 Azure SQL 데이터베이스 서버에 **로그인 ID** 및 **암호**로 연결합니다. **SQL Server에 연결하여 추가 구성 옵션에 대한…** 확인란을 선택 취소합니다. **기본 데이터베이스를 다음으로 변경:**을 클릭하고 목록에 표시되지 않더라도 Azure SQL 데이터베이스 이름을 입력합니다. 마법사가 **SQL Server 시스템 메시지 언어를 다음으로 변경:** 목록에서 여러 언어를 나열합니다.

이 릴리스에서 Microsoft Azure SQL 데이터베이스는 영어만 지원하므로 영어를 언어로 선택합니다. Microsoft Azure SQL 데이터베이스는 **미러 서버** 또는 **데이터베이스 연결**을 지원하지 않으므로 이러한 항목을 비워둡니다. **연결 테스트**를 클릭합니다.

SQL Server 2008 Native Client ODBC 드라이버를 사용하는 경우 **연결 테스트** 단추는 **master.dbo.syscharsets**가 지원되지 않는 오류를 야기할 수 있습니다. 이 오류를 무시하고 DSN을 저장하여 사용합니다.

### DB2 및 DRDA 디자인에 대한 SQL Server에 대한 OLEDB

Microsoft OLE DB Provider for DB2 버전 5.0 (데이터 공급자)를 사용 하면 IBM DB2 데이터베이스를 대상으로 하는 분산된 응용 프로그램을 만들 수 있습니다. 데이터 공급자는 Microsoft SQL Server 데이터 액세스 아키텍처는 관계형 데이터베이스 아키텍처 (Distributed Relational Database Architecture, DRDA) 응용 프로그램 요청자로 기능하는 DB2 용 Microsoft 네트워크 클라이언트와 함께 활용 합니다. 데이터 공급자는 DRDA 프로토콜 코드 포인트와 데이터 형식에 구성 요소 개체 모델 (COM) OLE DB 명령 및 데이터 형식을 변환합니다.

자세한 내용은 다음을 참조하세요.

- [Microsoft OLE DB Provider for DB2 버전 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Microsoft OLEDB Provider for DB2 v4.0 for Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)

## 타사 라이브러리

> [AZURE.IMPORTANT] 다음은 타사 라이선스 조건에 따라 타사에서 출시한 라이브러리 표입니다. 이러한 라이브러리를 사용하려면 사용자는 관련된 타사 라이선스를 확인하고 준수할 책임이 있습니다. 이러한 라이브러리를 사용하여 발생하는 위험은 사용자의 책임입니다. Microsoft는 여기에 제공된 정보에 대해 어떠한 명시적 또는 묵시적 보증도 하지 않으며, 사용자의 편의를 위해 정보를 제공할 뿐입니다. 여기에 포함된 그 어떤 것도 Microsoft의 보증을 의미하지 않습니다. <br/><br/>GitHub.com에 있는 **Azure** 소유의 [azure-content](http://github.com/Azure/azure-content/) 리포지토리를 사용하여 이 "타사 라이브러리" 섹션의 정보를 업데이트 및 유지 관리하는 것은 개발자 공개 커뮤니티의 몫입니다. Microsoft에서는 개발자들이 이 섹션을 업데이트하기를 권장합니다. Microsoft 직원은 이 섹션의 정보를 유지 관리하지 *않습니다*. 그 이유 중 하나는, 다른 사람들이 특정 타사 라이브러리에 대해 우리보다 잘 알고 있기 때문입니다. 감사합니다.

다음은 타사 또는 타 커뮤티니 같은 제3자가 출시한 라이브러리 표입니다. Microsoft에서 출시한 라이브러리는 이 항목의 앞부분에 있습니다.

| 플랫폼 | 라이브러리 |
| :-- | :-- |
| Ruby | [tinytds*(org, stable)*](https://rubygems.org/gems/tiny_tds/versions/0.7.0) |
| GO | [go-mssqldb*(org, stable)*](https://github.com/denisenkom/go-mssqldb) |
| Python | [pymssql*(org, stable)*](http://pymssql.org/en/stable/) |
| Node.js | [NODE-MSSQL*(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector*(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| C++ | [FreeTDS*(org)*](http://www.freetds.org/) |



<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=AcomDC_0302_2016-->