---
title: "PostgreSQL용 Azure 데이터베이스에 대한 연결 라이브러리 | Microsoft Docs"
description: "클라이언트 프로그램에서 PostgreSQL용 Azure 데이터베이스에 연결하는 데 사용할 수 있는 각 라이브러리 또는 드라이버가 나열되어 있습니다."
keywords: "azure 클라우드 postgresql postgres"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에 대한 연결 라이브러리
이 항목에는 클라이언트 프로그램에서 PostgreSQL용 Azure 데이터베이스에 연결하는 데 사용할 수 있는 각 라이브러리 또는 드라이버가 나열되어 있습니다.

## <a name="client-interfaces"></a>클라이언트 인터페이스
PostgreSQL 서버에 연결하기 위한 대부분의 언어 클라이언트 라이브러리는 외부 프로젝트이며 독립적으로 배포됩니다. 이러한 라이브러리는 Windows, Linux 및 Mac 플랫폼에서 지원됩니다. 일부 인기 있는 클라이언트 드라이버는 다음과 같습니다.
| **언어** | **클라이언트 인터페이스** | **추가 정보** | **다운로드** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 호환 | [다운로드](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | 데이터베이스 확장 | [설치](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm package](https://www.npmjs.com/package/pg) | 순수 JavaScript 비차단 클라이언트 | [설치](https://www.npmjs.com/package/pg) |
| 자바 | [JDBC](http://jdbc.postgresql.org/) | 형식 4 JDBC 드라이버 | [다운로드](https://jdbc.postgresql.org/download.html)  |
| 루비 | [Pg gem](https://deveiate.org/code/pg/) | Ruby 인터페이스 | [다운로드](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 순수 Go postgres 드라이버 | [설치](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | ADO.NET 데이터 공급자 | [다운로드](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC 드라이버 | [다운로드](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | 기본 C 언어 인터페이스 | 포함됨 |
| C++ | [libpqxx](http://pqxx.org/) | 새 스타일 C++ 인터페이스 | [다운로드](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>다음 단계
- 서비스 개요를 보려면 [PostgreSQL용 Azure 데이터베이스 개요](overview.md)를 참조하세요.
- 서버에 대한 자세한 내용은 [PostgreSQL용 Azure Databse 서버](concepts-servers.md)를 참조하세요.
- 첫 번째 PostgreSQL 서버를 만들려면 [Azure Portal에서 PostgreSQL용 Azure 데이터베이스 만들기](quickstart-create-server-database-portal.md)를 참조하세요.

