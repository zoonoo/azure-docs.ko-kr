---
title: Azure SQL 데이터베이스 연결 및 쿼리 빠른 시작 | Microsoft Docs
description: Azure SQL 데이터베이스 빠른 시작에서는 Azure SQL 데이터베이스에 연결하고 쿼리하는 방법을 보여 줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a8513344c35c14ebf06f3693da618ed20047d07b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792043"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>빠른 시작: Azure SQL Database 연결 및 쿼리

다음 문서는 Azure SQL 데이터베이스에 연결하고 쿼리하는 방법을 보여주는 Azure 예제에 대한 링크를 포함합니다. 또한 전송 수준 보안에 대한 몇 가지 권장 사항을 제공합니다.

## <a name="quickstarts"></a>빠른 시작

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|이 빠른 시작은 SSMS를 사용하여 Azure SQL 데이터베이스에 연결한 다음, Transact-SQL 문을 사용하여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|이 빠른 시작에서는 Azure Data Studio를 사용하여 Azure SQL 데이터베이스에 연결한 다음, T-SQL(Transact-SQL) 문을 사용하여 Azure Data Studio 자습서에서 사용할 TutorialDB를 만드는 방법을 보여줍니다.|
|[Azure Portal](sql-database-connect-query-portal.md)|이 빠른 시작에서는 쿼리 편집기를 사용하여 SQL 데이터베이스에 연결한 다음, Transact-SQL 문을 사용하여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|이 빠른 시작은 Visual Studio 코드를 사용하여 Azure SQL 데이터베이스에 연결한 다음, Transact-SQL 문을 사용하여 데이터베이스에서 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다.|
|[Visual Studio에서 .NET 사용](sql-database-connect-query-dotnet-visual-studio.md)|이 빠른 시작에서는 .NET Framework를 사용하여 Visual Studio에서 C# 프로그램을 만들어 Azure SQL 데이터베이스에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|이 빠른 시작에서는 Windows/Linux/macOS에서 .NET Core를 통해 Azure SQL 데이터베이스에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 C# 프로그램을 만드는 방법을 보여 줍니다.|
|[Go](sql-database-connect-query-go.md)|이 빠른 시작에서는 Go를 사용하여 Azure SQL 데이터베이스에 연결하는 방법을 보여 줍니다. 데이터 쿼리 및 수정을 위한 Transact SQL 문도 보여 줍니다.|
|[Java](sql-database-connect-query-java.md)|이 빠른 시작에서는 Java를 사용하여 Azure SQL 데이터베이스에 연결한 다음, Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다.|
|[Node.JS](sql-database-connect-query-nodejs.md)|이 빠른 시작에서는 Node.js를 사용하여 Azure SQL 데이터베이스에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[PHP](sql-database-connect-query-php.md)|이 빠른 시작에서는 PHP를 사용하여 Azure SQL 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|[Python](sql-database-connect-query-python.md)|이 빠른 시작에서는 Python을 사용하여 Azure SQL 데이터베이스에 연결하고 Transact-SQL 문을 사용하여 데이터를 쿼리하는 방법을 보여 줍니다. |
|[Ruby](sql-database-connect-query-ruby.md)|이 빠른 시작에서는 Ruby를 사용하여 Azure SQL 데이터베이스에 연결하고, Transact-SQL 문을 사용하여 데이터를 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>SQL Database 연결에 대한 TLS 고려 사항
TLS(전송 계층 보안)는 Microsoft가 Azure SQL Database에 대한 연결을 위해 제공하거나 지원하는 모든 드라이버에서 사용됩니다. 특별한 구성은 필요하지 않습니다. SQL Server 또는 Azure SQL Database에 대한 모든 연결의 경우 모든 애플리케이션에서 다음 구성 또는 해당 사항을 설정하는 것이 좋습니다.

 - **암호화 = 켜짐**
 - **TrustServerCertificate = 꺼짐**

일부 시스템은 해당 구성 키워드에 대해 각기 다르지만 해당 키워드를 사용합니다. 이러한 구성은 클라이언트 드라이버에서 서버에서 받은 TLS 인증서의 ID를 확인하도록 합니다.

또한 PCI-DSS(Payment Card Industry - Data Security Standard)를 준수해야 하는 경우 클라이언트에서 TLS 1.1 및 1.0을 비활성화하는 것이 좋습니다.

타사 드라이버는 기본적으로 TLS를 사용할 수 있습니다. Azure SQL Database에 연결할 때 요인이 될 수 있습니다. 포함된 드라이버가 있는 애플리케이션은 이러한 연결 설정을 제어하도록 허용할 수 없습니다. 중요한 데이터와 상호 작용하는 시스템에서 사용하기 전에 이러한 드라이버와 애플리케이션의 보안을 검사하는 것이 좋습니다.

## <a name="libraries"></a>라이브러리

다양한 라이브러리 및 프레임워크를 사용하여 Azure SQL Database에 연결할 수 있습니다. C#, Java, Node.js, PHP 및 Python과 같은 프로그래밍 언어를 빠르게 시작하려면 [자습서 시작](https://aka.ms/sqldev)을 확인하세요. 그런 다음 Linux 또는 Windows 또는 macOS의 Docker에서 SQL Server를 사용하여 앱을 빌드합니다.

다음 표는 클라이언트 애플리케이션이 다양한 언어를 사용하여 온-프레미스 또는 클라우드에서 실행 중인 SQL Server에 연결하고 사용할 수 있는 연결 라이브러리 또는 *드라이버*를 나열합니다. Linux, Windows 또는 Docker에서 사용할 수 있으며 이를 사용하여 Azure SQL Database 및 Azure SQL Data Warehouse에 연결할 수 있습니다. 

| 언어 | 플랫폼 | 추가 리소스 | 다운로드 | 시작하기 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [SQL Server용 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [다운로드](https://www.microsoft.com/net/download/) | [시작](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [SQL Server용 Microsoft JDBC 드라이버](https://msdn.microsoft.com/library/mt484311.aspx) | [다운로드](https://go.microsoft.com/fwlink/?linkid=852460) |  [시작](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [SQL Server용 PHP SQL 드라이버](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [다운로드](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [시작](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [SQL Server용 Node.js 드라이버](https://msdn.microsoft.com/library/mt652093.aspx) | [설치](https://msdn.microsoft.com/library/mt652094.aspx) |  [시작](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL 드라이버](https://msdn.microsoft.com/library/mt652092.aspx) | 다음 선택 항목을 설치합니다. <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [시작](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [SQL Server용 Ruby 드라이버](https://msdn.microsoft.com/library/mt691981.aspx) | [설치](https://msdn.microsoft.com/library/mt711041.aspx) | [시작](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [SQL Server용 Microsoft ODBC 드라이버](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [다운로드](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

다음 표는 클라이언트 애플리케이션이 온-프레미스 또는 클라우드에서 실행 중인 SQL Server를 사용할 수 있는 ORM(개체-관계형 매핑) 프레임워크 및 웹 프레임워크의 예를 나열합니다. Linux, Windows 또는 Docker에서 프레임워크를 사용할 수 있으며 이를 사용하여 SQL Database 및 SQL Data Warehouse에 연결할 수 있습니다. 

| 언어 | 플랫폼 | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel(Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>다음 단계

- 연결 아키텍처 정보는 [Azure SQL Database 연결 아키텍처](sql-database-connectivity-architecture.md)를 참조하세요.
- 클라이언트 애플리케이션에서 연결하는 데 사용되는 [SQL Server 드라이버](https://msdn.microsoft.com/library/mt654049.aspx) 찾기
- SQL Database에 연결:
  - [.NET(C#)을 사용하여 SQL Database에 연결](sql-database-connect-query-dotnet.md) 
  - [PHP를 사용하여 SQL Database에 연결](sql-database-connect-query-php.md) 
  - [Node.js를 사용하여 SQL Database에 연결](sql-database-connect-query-nodejs.md) 
  - [Java를 사용하여 SQL Database에 연결](sql-database-connect-query-java.md) 
  - [Python을 사용하여 SQL Database에 연결](sql-database-connect-query-python.md)
  - [Ruby를 사용하여 SQL Database에 연결](sql-database-connect-query-ruby.md)
- 다시 시도 논리 코드 예제:
  - [ADO.NET으로 SQL에 탄력적으로 연결][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [PHP로 SQL에 탄력적으로 연결][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
