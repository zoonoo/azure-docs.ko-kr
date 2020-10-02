---
title: 사용자 만들기-Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB 서버와 상호 작용할 새로운 사용자 계정을 만드는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 7a3515850027963dcabbf2cef4efd8bf43802cbe
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653063"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Azure Database for MariaDB에서 사용자 만들기 
이 문서에서는 Azure Database for MariaDB 서버에서 사용자를 만드는 방법을 설명합니다.

> [!NOTE]
> 바이어스 없는 통신
>
> Microsoft는 다양 한 inclusionary 환경을 지원 합니다. 이 문서에는 word _슬레이브_에 대 한 참조가 포함 되어 있습니다. [바이어스 없는 통신을 위한 Microsoft 스타일 가이드](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) 는이를 exclusionary 단어로 인식 합니다. 이 문서는 현재 소프트웨어에 표시 되는 단어 이므로 일관성을 위해 사용 됩니다. 소프트웨어를 업데이트 하 여 단어를 제거 하면이 문서는 맞춤으로 업데이트 됩니다.
>

Azure Database for MariaDB를 처음으로 만든 경우 서버 관리자 로그인 사용자 이름과 암호를 제공했습니다. 자세한 내용은 [빠른 시작](quickstart-create-mariadb-server-database-using-azure-portal.md)을 참조하세요. Azure Portal에서 서버 관리자 로그인 사용자 이름을 찾을 수 있습니다.

서버 관리 사용자자는 서버에 대한 SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER 권한을 갖습니다.

Azure Database for MariaDB 서버가 생성되면 첫 번째 서버 관리 사용자 계정을 사용하여 추가 사용자를 만들어 이들에게 관리자 액세스 권한을 부여할 수 있습니다. 또한 서버 관리자 계정은 개별 데이터베이스 스키마에 액세스할 수 있는 권한의 사용자가 만드는 데 사용할 수 있습니다.

> [!NOTE]
> SUPER 권한 및 DBA 역할은 지원 되지 않습니다. 제한 사항 문서의 [권한](concepts-limits.md#privileges--data-manipulation-support) 을 검토 하 여 서비스에서 지원 되지 않는 기능을 파악 합니다.<br><br>
> "Validate_password" 및 "caching_sha2_password"와 같은 암호 플러그 인은 서비스에서 지원 되지 않습니다.

## <a name="create-additional-admin-users"></a>추가 관리 사용자 만들기
1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다. 

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. MySQL Workbench, mysql.exe, HeidiSQL 또는 그 외의 선호하는 클라이언트 도구를 사용하면 됩니다. 
   연결 방법을 잘 모르는 경우 [MySQL Workbench를 사용하여 연결하고 데이터 쿼리](./connect-workbench.md)를 참조하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. `new_master_user` 자리 표시자 값을 새로운 사용자 이름으로 바꿉니다. 이 구문은 모든 데이터베이스 스키마(*.*)에 나열된 권한을 사용자 이름(이 예에서는 new_master_user)에 부여합니다. 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. 권한 부여 확인 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>데이터베이스 사용자 만들기

1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다. 

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. MySQL Workbench, mysql.exe, HeidiSQL 또는 그 외의 선호하는 클라이언트 도구를 사용하면 됩니다. 
   연결 방법을 잘 모르는 경우 [MySQL Workbench를 사용하여 연결하고 데이터 쿼리](./connect-workbench.md)를 참조하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. `db_user` 자리 표시자 값을 원하는 새 사용자 이름으로 바꾸고, `testdb` 자리 표시자 값을 자신의 데이터베이스 이름으로 바꿉니다.

   이 sql 코드 구문은 예제를 제공할 목적으로 testdb라는 새 데이터베이스를 만듭니다. 그런 다음, Azure Database for MariaDB 서비스에서 새로운 사용자를 만들고 해당 사용자의 새 데이터베이스 스키마(testdb.\*)에 모든 권한을 부여합니다. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. 데이터베이스 내에서 권한 부여를 확인합니다.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 서버에 로그인하고, 지정된 데이터베이스를 지정하고, 새로운 사용자 이름 및 암호를 사용합니다. 이 예에서는 mysql 명령줄을 보여 줍니다. 이 명령을 사용하면 사용자 이름에 대한 암호를 묻는 메시지가 표시됩니다. 사용자 고유의 서버 이름, 데이터베이스 이름 및 사용자 이름으로 바꿉니다.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   사용자 계정 관리에 대한 자세한 내용은 [사용자 계정 관리](https://mariadb.com/kb/en/library/user-account-management/), [GRANT 구문](https://mariadb.com/kb/en/library/grant/) 및 [권한](https://mariadb.com/kb/en/library/grant/#privilege-levels)에 대한 MariaDB 설명서를 참조하세요.

## <a name="azure_superuser"></a>azure_superuser

"Azure_superuser" 라는 사용자를 사용 하 여 모든 Azure Database for MySQL 서버를 만듭니다. 모니터링, 백업 및 기타 정기 유지 관리를 수행 하기 위해 서버를 관리 하기 위해 Microsoft에서 만든 시스템 계정입니다. 또한 콜 공학 엔지니어가 인증서 인증을 사용 하 여 인시던트 중에이 계정을 사용 하 여 서버에 액세스 하 고 JIT (just-in-time) 프로세스를 사용 하 여 액세스를 요청 해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
새 사용자의 머신 IP 주소에 대한 방화벽을 열고 연결하도록 설정합니다: [Azure Portal을 사용하여 Azure Database for MariaDB 방화벽 규칙 만들기 및 관리](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
