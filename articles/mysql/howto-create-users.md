---
title: 데이터베이스 및 사용자 만들기-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL 서버와 상호 작용 하는 새 사용자 계정을 만드는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 3e1f24b3ae6133241660751293f52fec63dfbe73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766875"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 데이터베이스 및 사용자 만들기

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

이 문서에서는 Azure Database for MySQL에서 사용자를 만드는 방법을 설명 합니다.

> [!NOTE]
> **바이어스 없는 통신**
>
> Microsoft는 다양 한 inclusionary 환경을 지원 합니다. 이 문서에는 word *슬레이브*에 대 한 참조가 포함 되어 있습니다. [바이어스 없는 통신을 위한 Microsoft 스타일 가이드](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) 는이를 exclusionary 단어로 인식 합니다. 이 문서는 현재 소프트웨어에 표시 되는 단어 이므로 일관성을 위해이 문서에서 사용 됩니다. 소프트웨어를 업데이트 하 여 단어를 제거 하면이 문서는 맞춤으로 업데이트 됩니다.
>

Azure Database for MySQL 서버를 처음 만들 때 서버 관리자 사용자 이름과 암호를 제공 했습니다. 자세한 내용은이 [빠른](quickstart-create-mysql-server-database-using-azure-portal.md)시작을 참조 하세요. Azure Portal에서 서버 관리자 사용자 이름을 확인할 수 있습니다.

서버 관리 사용자에 게는 다음 권한이 있습니다. 

   SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER


Azure Database for MySQL 서버를 만든 후에는 첫 번째 서버 관리자 계정을 사용 하 여 추가 사용자를 만들고 해당 사용자에 게 관리자 액세스 권한을 부여할 수 있습니다. 또한 서버 관리자 계정을 사용 하 여 개별 데이터베이스 스키마에 대 한 액세스 권한이 있는 사용자가 적은 권한으로 사용자를 만들 수 있습니다.

> [!NOTE]
> SUPER 권한 및 DBA 역할은 지원 되지 않습니다. 제한 사항 문서의 [권한](concepts-limits.md#privileges--data-manipulation-support) 을 검토 하 여 서비스에서 지원 되지 않는 기능을 파악 합니다.
>
> 및와 같은 암호 플러그 인 `validate_password` `caching_sha2_password` 은 서비스에서 지원 되지 않습니다.


## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 관리자가 아닌 사용자를 사용 하 여 데이터베이스를 만들려면

1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. 서버 **개요** 페이지 또는 Azure Portal의 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. MySQL 워크 벤치, mysql.exe 또는 HeidiSQL와 같은 기본 설정 클라이언트 도구를 사용 합니다.
   
   연결 방법을 잘 모르겠으면 [단일 서버에 대 한 데이터 연결 및 쿼리](./connect-workbench.md) 또는 [유연한 서버에 대 한 데이터 연결 및 쿼리](./flexible-server/connect-workbench.md)를 참조 하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. 자리 표시자 값을 `db_user` 원하는 새 사용자 이름으로 바꿉니다. 자리 표시자 값을 `testdb` 데이터베이스 이름으로 바꿉니다.

   이 SQL 코드는 testdb 라는 새 데이터베이스를 만듭니다. 그런 다음 MySQL 서비스에서 새 사용자를 만들고 해당 사용자에 게 새 데이터베이스 스키마 (testdb.)에 대 한 모든 권한을 부여 \* 합니다.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. 데이터베이스의 부여를 확인 합니다.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 지정 된 데이터베이스를 지정 하 고 새 사용자 이름과 암호를 사용 하 여 서버에 로그인 합니다. 이 예에서는 mysql 명령줄을 보여 줍니다. 이 명령을 사용 하면 사용자의 암호를 입력 하 라는 메시지가 표시 됩니다. 사용자의 서버 이름, 데이터베이스 이름 및 사용자 이름을 사용 합니다.

   # <a name="single-server"></a>[단일 서버](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   # <a name="flexible-server"></a>[유연한 서버](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-additional-admin-users-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 추가 관리자 사용자를 만들려면

1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. 서버 **개요** 페이지 또는 Azure Portal의 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. MySQL 워크 벤치, mysql.exe 또는 HeidiSQL와 같은 기본 설정 클라이언트 도구를 사용 합니다.
   
   연결 방법을 잘 모르겠으면 [MySQL 워크 벤치를 사용 하 여 데이터 연결 및 쿼리](./connect-workbench.md)를 참조 하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. 자리 표시자 값을 `new_master_user` 새 사용자 이름으로 바꿉니다. 이 구문은 모든 데이터베이스 스키마 (*.*)에 대해 나열 된 권한을 사용자에 게 부여 합니다 ( `new_master_user` 이 예제에서는).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. 권한 부여를 확인 합니다.

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

"Azure_superuser" 라는 사용자를 사용 하 여 모든 Azure Database for MySQL 서버를 만듭니다. 모니터링, 백업 및 기타 정기 유지 관리를 수행 하기 위해 서버를 관리 하기 위해 Microsoft에서 만든 시스템 계정입니다. 또한 콜 공학 엔지니어가 인증서 인증을 사용 하 여 인시던트 중에이 계정을 사용 하 여 서버에 액세스 하 고 JIT (just-in-time) 프로세스를 사용 하 여 액세스를 요청 해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

새 사용자의 머신 IP 주소에 대한 방화벽을 열고 연결하도록 설정합니다.
- [단일 서버에서 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-portal.md) 
- [ 유연한 서버에서 방화벽 규칙 만들기 및 관리](flexible-server/how-to-connect-tls-ssl.md)

사용자 계정 관리에 대 한 자세한 내용은 [사용자 계정 관리](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [GRANT 구문](https://dev.mysql.com/doc/refman/5.7/en/grant.html)및 [권한에](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)대 한 MySQL 제품 설명서를 참조 하세요.
