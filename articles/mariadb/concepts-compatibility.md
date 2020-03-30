---
title: 드라이버 및 도구 호환성 - MariaDB용 Azure 데이터베이스
description: 이 문서에서는 MariaDB에 대한 Azure 데이터베이스와 호환되는 MariaDB 드라이버 및 관리 도구에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a0cee198f028fd90e04dac15e98d7cd33aee9201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532351"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB에 대 한 Azure 데이터베이스와 호환 되는 MariaDB 드라이버 및 관리 도구

이 문서에서는 MariaDB용 Azure 데이터베이스와 호환되는 드라이버 및 관리 도구에 대해 설명합니다.

## <a name="mariadb-drivers"></a>마리아DB 드라이버

MariaDB에 대 한 Azure 데이터베이스 MariaDB 서버의 커뮤니티 버전을 사용 합니다. 따라서 다양한 프로그래밍 언어 및 드라이버와 호환됩니다. MariaDB의 API 및 프로토콜은 MySQL에서 사용하는 API와 호환됩니다. 즉, MySQL에서 작동하는 커넥터도 MariaDB와 함께 작동해야 합니다.

목표는 세 가지 최신 버전의 MariaDB 드라이버를 지원하고, 지속적으로 MariaDB 드라이버의 기능과 유용성을 개선하기 위해 오픈 소스 커뮤니티의 저자와 노력이 계속됩니다. MariaDB 10.2용 Azure 데이터베이스와 호환되는 것으로 테스트되고 발견된 드라이버 목록은 다음 표에 제공됩니다.

**드라이버** | **링크** | **호환되는 버전** | **호환되지 않는 버전** | **참고**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | PHP 7.0과 SSL MySQLi 연결의 경우 연결 문자열에 MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT를 추가합니다. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO 집합: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` 옵션을 false로 설정합니다.
.NET | [GitHub의 MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [Nuget의 설치 패키지](https://www.nuget.org/packages/MySqlConnector/) | 0.27 이상 | 0.26.5 이하 |
MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | 인코딩 버그로 인해 UTF8이 아닌 Windows 시스템 중 일부에서 연결이 실패할 수 있습니다.
Node.js |  [Github의 MySQLjs](https://github.com/mysqljs/mysql/) <br> NPM의 설치 패키지:<br> NPM에서 `npm install mysql` 실행 | 2.15 | 2.14.1 이하
이동 | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 이하 | 버전 `allowNativePasswords=true` 1.3의 연결 문자열에서 사용합니다. 버전 1.4에는 수정 `allowNativePasswords=true` 프로그램이 포함되어 있으며 더 이상 필요하지 않습니다.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 이하 |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 이하 |

## <a name="management-tools"></a>관리 도구

호환성 장점은 데이터베이스 관리 도구까지 확장됩니다. 데이터베이스 조작이 사용자 권한 범위 내에서 작동하는 한 기존 도구는 MariaDB용 Azure 데이터베이스에서 계속 작동해야 합니다. MariaDB 10.2용 Azure 데이터베이스와 호환되는 것으로 테스트되고 발견된 세 가지 일반적인 데이터베이스 관리 도구는 다음 표에 나열되어 있습니다.

| | **MySQL Workbench 6.x 이상** | **Navicat 12** | **PHPMyAdmin 4.x 이상**
---|---|---|---
만들기, 업데이트, 읽기, 쓰기, 삭제 | X | X | X
SSL 연결 | X | X | X
SQL 쿼리 자동 완성 | X | X |
데이터 가져오기 및 내보내기 | X | X | X
여러 형식으로 내보내기 | X | X | X
Backup 및 복원 |  | X |
서버 매개 변수 표시 | X | X | X
클라이언트 연결 표시 | X | X | X

## <a name="next-steps"></a>다음 단계

- [Azure Database for MariaDB에 대한 연결 문제 해결](howto-troubleshoot-common-connection-issues.md)