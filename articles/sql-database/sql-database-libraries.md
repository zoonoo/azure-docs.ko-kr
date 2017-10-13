---
title: "SQL Database용 연결 라이브러리 | Microsoft Docs"
description: "다양한 클라이언트 프로그래밍 언어에서 SQL Server 및 SQL Database에 연결할 수 있도록 하는 모듈 다운로드를 위한 링크를 제공합니다. 모듈은 커뮤니티 또는 Microsoft에서 출시합니다."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: genemi
ms.openlocfilehash: 082abf57b139b9f7d44774dce3a80e20b97f0e3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>Microsoft SQL Server의 연결 라이브러리 및 프레임워크

[시작 자습서](http://aka.ms/sqldev)를 확인하여 C#, Java, Node.js, PHP 및 Python 등의 프로그래밍 언어를 빠르게 시작하고 Linux 또는 Windows에서 SQL Server나 macOS에서 Docker를 사용하여 앱을 빌드합니다.

아래 표에서는 클라이언트 응용 프로그램이 다양한 언어로 사용하여 온-프레미스나 클라우드, Linux, Windows나 Docker 및 Azure SQL Database와 Azure SQL Data Warehouse에서 실행 중인 Microsoft SQL Server를 연결하고 사용할 수 있는 연결 라이브러리 또는 *드라이버*를 나열합니다. 

| 언어 | 플랫폼 | 추가 리소스 | 다운로드 | 시작 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [SQL Server용 Microsoft ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [다운로드](https://www.microsoft.com/net/download/) | [시작](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| 자바 | Windows, Linux, macOS | [SQL Server용 Microsoft JDBC Driver](http://msdn.microsoft.com/library/mt484311.aspx) | [다운로드](https://go.microsoft.com/fwlink/?linkid=852460) |  [시작](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [SQL Server용 PHP SQL 드라이버](http://msdn.microsoft.com/library/dn865013.aspx) | 운영 체제: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [시작](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [SQL Server용 Node.js 드라이버](http://msdn.microsoft.com/library/mt652093.aspx) | [설치](https://msdn.microsoft.com/library/mt652094.aspx) |  [시작](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| 파이썬 | Windows, Linux, macOS | [Python SQL 드라이버](http://msdn.microsoft.com/library/mt652092.aspx) | 다음 선택 항목을 설치합니다. <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [시작](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [SQL Server용 Ruby 드라이버](http://msdn.microsoft.com/library/mt691981.aspx) | [설치](https://msdn.microsoft.com/library/mt711041.aspx) | [시작](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [SQL Server용 Microsoft ODBC 드라이버](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [다운로드](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

아래 표에서는 클라이언트 응용 프로그램이 다양한 언어로 사용하여 온-프레미스나 클라우드, Linux, Windows나 Docker 및 Azure SQL Database와 Azure SQL Data Warehouse에서 실행 중인 Microsoft SQL Server를 사용할 수 있는 ORM(Object Relational Mapping) 프레임워크 및 웹 프레임워크의 일부 예제를 나열합니다. 

| 언어 | 플랫폼 | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/en-us/ef)<br>[Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/index) |
| 자바 | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel(Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| 파이썬 | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |

## <a name="related-links"></a>관련 링크
- 클라이언트 응용 프로그램에서 연결하기 위한 [SQL Server 드라이버](http://msdn.microsoft.com/library/mt654049.aspx)
- [.NET(C#)을 사용하여 SQL 데이터베이스에 연결](sql-database-connect-query-dotnet.md)
- [PHP를 사용하여 SQL 데이터베이스에 연결](sql-database-connect-query-php.md)
- [Node.js를 사용하여 SQL 데이터베이스에 연결](sql-database-connect-query-nodejs.md)
- [Java를 사용하여 SQL 데이터베이스에 연결](sql-database-connect-query-java.md)
- [Python을 사용하여 SQL 데이터베이스에 연결](sql-database-connect-query-python.md)
- [Ruby를 사용하여 SQL 데이터베이스에 연결](sql-database-connect-query-ruby.md)
