---
title: 연결 라이브러리-Azure Database for MySQL
description: 이 문서에는 클라이언트 프로그램에서 MySQL용 Azure 데이터베이스에 연결하는 데 사용할 수 있는 각 라이브러리 또는 드라이버가 나열되어 있습니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537196"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 대한 연결 라이브러리
이 문서에는 클라이언트 프로그램에서 MySQL용 Azure 데이터베이스에 연결하는 데 사용할 수 있는 각 라이브러리 또는 드라이버가 나열되어 있습니다.

## <a name="client-interfaces"></a>클라이언트 인터페이스
MySQL은 산업 표준 ODBC 및 JDBC와 호환되는 애플리케이션 및 도구와 함께 MySQL을 사용하기 위한 표준 데이터베이스 드라이버 연결을 제공합니다. ODBC 또는 JDBC를 사용하는 모든 시스템은 MySQL을 사용할 수 있습니다.

| **언어** | **플랫폼** | **추가 리소스** | **다운로드** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [PHP용 MySQL 네이티브 드라이버 - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [다운로드](https://secure.php.net/downloads.php) |
| ODBC | Windows, Linux, Mac OS X 및 Unix 플랫폼 | [MySQL 커넥터/ODBC 개발자 가이드](https://dev.mysql.com/doc/connector-odbc/en/) | [다운로드](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL 커넥터/Net 개발자 가이드](https://dev.mysql.com/doc/connector-net/en/) | [다운로드](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | 플랫폼 독립적 | [MySQL 커넥터/J 5.1 개발자 가이드](https://dev.mysql.com/doc/connector-j/5.1/en/) | [다운로드](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [다운로드](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL 커넥터/Python 개발자 가이드](https://dev.mysql.com/doc/connector-python/en/) | [다운로드](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL 커넥터/C++ 개발자 가이드](https://dev.mysql.com/doc/connector-cpp/en/) | [다운로드](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [MySQL 커넥터/C 개발자 가이드](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [다운로드](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows, Linux, Mac OS X 및 Unix 플랫폼 | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [다운로드](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>다음 단계
선택한 언어를 사용하여 MySQL용 Azure Database에 연결하고 쿼리하는 방법에 대한 빠른 시작을 읽어보세요.

[PHP](./connect-php.md) | [Java](./connect-java.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [Go](./connect-go.md) [Python](./connect-python.md) | [.NET (C#)](./connect-csharp.md) | [C++](connect-cpp.md).net (c #) Python node.js Ruby c + + Go |  

