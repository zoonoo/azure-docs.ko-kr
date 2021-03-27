---
title: 덤프 및 복원을 사용 하 여 마이그레이션-Azure Database for MariaDB
description: 이 문서에서는 mysqldump, MySQL 워크 벤치, phpMyAdmin 등의 도구를 사용 하 여 Azure database for MariaDB에서 데이터베이스를 백업 하 고 복원 하는 두 가지 일반적인 방법을 설명 합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628223"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>덤프 및 복원을 사용 하 여 Azure database for MariaDB로 Fadb 데이터베이스 마이그레이션

이 문서에서는 Azure database for MariaDB에서 데이터베이스를 백업 하 고 복원 하는 두 가지 일반적인 방법을 설명 합니다.
- 명령줄 도구를 사용 하 여 덤프 및 복원 (mysqldump 사용) 
- PhpMyAdmin를 사용 하 여 덤프 및 복원

## <a name="prerequisites"></a>전제 조건
데이터베이스 마이그레이션을 시작 하기 전에 다음을 수행 합니다.
- [Azure Database for MariaDB 서버-Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)를 만듭니다.
- [Mysqldump](https://mariadb.com/kb/en/library/mysqldump/) 명령줄 유틸리티를 설치 합니다.
- 덤프 및 복원 명령을 실행 하기 위한 [Mysql 워크 벤치](https://dev.mysql.com/downloads/workbench/) 또는 다른 타사 mysql 도구를 다운로드 하 여 설치 합니다.

## <a name="use-common-tools"></a>일반 도구 사용
MySQL 워크 벤치 또는 mysqldump와 같은 일반적인 유틸리티와 도구를 사용 하 여 원격으로 데이터를 연결 하 고 Azure database for MariaDB에 복원 합니다. 인터넷에 연결 된 클라이언트 컴퓨터에서 이러한 도구를 사용 하 여 Azure database for MariaDB에 연결 합니다. 최상의 보안 방법으로 SSL로 암호화 된 연결을 사용 합니다. 자세한 내용은 [Azure Database for MariaDB에서 SSL 연결 구성](concepts-ssl-connection-security.md)을 참조 하세요. Azure database for MariaDB로 데이터를 마이그레이션할 때 덤프 파일을 특수 한 클라우드 위치로 이동할 필요가 없습니다. 

## <a name="common-uses-for-dump-and-restore"></a>덤프 및 복원을 위한 일반적인 사용
몇 가지 일반적인 시나리오에서 mysqldump 및 mysqlpump 같은 MySQL 유틸리티를 사용 하 여 Azure database for MariaDB 서버에 데이터베이스를 덤프 하 고 로드할 수 있습니다. 

- 전체 데이터베이스를 마이그레이션할 때 데이터베이스 덤프를 사용 합니다. 이 권장 사항은 많은 양의 데이터를 이동할 때 또는 라이브 사이트 또는 응용 프로그램에 대 한 서비스 중단을 최소화 하려는 경우에 적용 됩니다. 
-  Azure database for MariaDB에 데이터를 로드할 때 데이터베이스의 모든 테이블에서 InnoDB 저장소 엔진을 사용 하는지 확인 합니다. Azure Database for MariaDB는 InnoDB 저장소 엔진만 지원 하 고 다른 저장소 엔진은 지원 하지 않습니다. 다른 저장소 엔진을 사용 하 여 테이블을 구성 하는 경우에는 InnoDB 엔진 형식으로 변환 해야 합니다.
   
   예를 들어 MyISAM 테이블을 사용 하는 WordPress 앱 또는 웹 앱이 있는 경우 먼저 해당 테이블을 InnoDB 형식으로 마이그레이션하여이 테이블을 Azure database for MariaDB에 복원 하기 전에 변환 합니다. 절을 사용 하 여 `ENGINE=InnoDB` 새 테이블을 만드는 데 사용할 엔진을 설정한 다음 복원 하기 전에 데이터를 호환 되는 테이블로 전송 합니다. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- 데이터베이스를 덤프할 때 호환성 문제가 발생 하지 않도록 하려면 원본 및 대상 시스템에서 동일한 버전의 Aadb를 사용 하 고 있는지 확인 합니다. 예를 들어 기존에 있는 Microsoft Azure server가 버전 10.2 인 경우 버전 10.2을 실행 하도록 구성 된 Azure database for Aadb로 마이그레이션해야 합니다. `mysql_upgrade`명령은 Azure Database for MariaDB 서버에서 작동 하지 않으며 지원 되지 않습니다. 모든 진행 중인 Iadb 버전에서 업그레이드 해야 하는 경우 먼저 이전 버전의 데이터베이스를 해당 환경에 있는 최신 버전의 Aadb로 덤프 하거나 내보냅니다. 그런 다음 `mysql_upgrade` , Azure database For MariaDB로 마이그레이션을 시도 하기 전에을 실행할 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항
대량 데이터베이스를 덤프할 때 성능을 최적화 하려면 다음 사항을 고려해 야 합니다.
-   `exclude-triggers`Mysqldump에서 옵션을 사용 합니다. 데이터 복원 중에 트리거 명령이 발생 하지 않도록 덤프 파일에서 트리거를 제외 합니다. 
-   옵션을 사용 `single-transaction` 하 여 트랜잭션 격리 모드를 반복 읽기로 설정 하 고 데이터를 덤프 하기 전에 서버에 START TRANSACTION SQL 문을 보냅니다. 단일 트랜잭션 내에서 여러 테이블을 덤프 하면 복원 중에 몇 가지 추가 저장소가 사용 됩니다. `single-transaction`옵션과 `lock-tables` 옵션은 함께 사용할 수 없습니다. 이는 잠금 테이블 때문에 보류 중인 트랜잭션이 암시적으로 커밋될 수 있기 때문입니다. 대형 테이블을 덤프하려면 `single-transaction` 옵션을 `quick` 옵션과 결합합니다. 
-   여러 VALUE 목록을 포함하는 `extended-insert` 여러 행 구문을 사용합니다. 이 방법을 사용 하면 덤프 파일이 작아지고 파일이 다시 로드 될 때 삽입 속도가 빨라집니다.
-  `order-by-primary`데이터베이스를 덤프할 때 mysqldump에서 옵션을 사용 하 여 데이터를 기본 키 순서로 스크립팅할 수 있습니다.
-   `disable-keys`데이터를 덤프할 때 mysqldump의 옵션을 사용 하 여 로드 전에 foreign key 제약 조건을 사용 하지 않도록 설정 합니다. 외래 키 검사를 사용 하지 않도록 설정 하면 성능을 향상 시킬 수 있습니다. 제약 조건을 활성화하고 참조 무결성을 확인하도록 로드 후 데이터를 확인합니다.
-   적절한 경우 분할된 테이블을 사용합니다.
-   병렬로 데이터를 로드합니다. 과도 한 병렬 처리를 방지 하 여 리소스 제한에 도달 하 고 Azure Portal에서 사용 가능한 메트릭을 사용 하 여 리소스를 모니터링 합니다. 
-   `defer-table-indexes`데이터베이스를 덤프할 때 mysqlpump의 옵션을 사용 하 여 테이블 데이터를 로드 한 후 인덱스 생성이 발생 하도록 합니다.
-   백업 파일을 Azure blob 저장소에 복사 하 고 여기에서 복원을 수행 합니다. 이 방법은 인터넷을 통해 복원 하는 것 보다 훨씬 더 빠릅니다.

## <a name="create-a-backup-file"></a>Backup 파일 만들기

로컬 온-프레미스 서버 또는 가상 컴퓨터에서 기존 mysqldump Iadb 데이터베이스를 백업 하려면 다음 명령을 사용 하 여 실행 합니다. 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

제공할 매개 변수:
- *\<uname>*: 데이터베이스 사용자 이름 
- *\<pass>*: 데이터베이스의 암호 (-p와 암호 사이에 공백이 없음) 
- *\<dbname>*: 데이터베이스의 이름입니다. 
- *\<backupfile.sql>*: 데이터베이스 백업에 대 한 파일 이름 
- *\<--opt>*: Mysqldump 옵션 

예를 들어 testuser testdb_backup 파일에 대 한 암호 없이 사용자 이름  을 사용 하 여 MariaDB 서버에서 *testdb* 라는 데이터베이스를 백업 하려면 다음 명령을 사용 합니다. 명령은 데이터베이스를 다시 만드는 데 필요한 모든 SQL 문을 포함하는 `testdb_backup.sql`이라는 파일로 `testdb` 데이터베이스를 백업합니다. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
데이터베이스에서 백업할 특정 테이블을 선택 하려면 테이블 이름을 공백으로 구분 하 여 나열 합니다. 예를 들어 *testdb* 에서 table1 및 table2 테이블만 백업 하려면 다음 예제를 따르세요. 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

한 번에 둘 이상의 데이터베이스를 백업 하려면--database 스위치를 사용 하 고 데이터베이스 이름을 공백으로 구분 하 여 나열 합니다. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>대상 서버에서 데이터베이스 만들기
데이터를 마이그레이션하려는 대상 Azure Database for MariaDB 서버에서 빈 데이터베이스를 만듭니다. MySQL Workbench와 같은 도구를 사용하여 데이터베이스를 만듭니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 같을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

연결 하려면 Azure database for MariaDB의 **개요** 창에서 연결 정보를 찾습니다.

![Azure Portal의 Azure database for MariaDB 서버에 대 한 개요 창의 스크린샷](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

MySQL 워크 벤치에서 연결 정보를 추가 합니다.

![MySQL 워크 벤치에서 MySQL 연결 창의 스크린샷.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>MariaDB 데이터베이스 복원
대상 데이터베이스를 만든 후에는 mysql 명령 또는 MySQL 워크 벤치를 사용 하 여 덤프 파일에서 새로 만든 데이터베이스로 데이터를 복원할 수 있습니다.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

이 예에서는 대상 Azure Database for MariaDB 서버에서 새로 만든 데이터베이스로 데이터를 복원 합니다.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>PhpMyAdmin를 사용 하 여 MariaDB 데이터베이스 내보내기

내보내려면 환경에 로컬로 이미 설치 되어 있을 수 있는 일반적인 도구인 phpMyAdmin를 사용할 수 있습니다. MariaDB 데이터베이스를 내보내려면 다음을 수행 합니다.
1. phpMyAdmin을 엽니다.
1. 왼쪽 창에서 데이터베이스를 선택 하 고 **내보내기** 링크를 선택 합니다. 데이터베이스의 덤프를 보는 새 페이지가 나타납니다.
1. **내보내기** 영역에서 **모두 선택** 링크를 선택 하 여 데이터베이스의 테이블을 선택 합니다. 
1. **SQL 옵션** 영역에서 적절 한 옵션을 선택 합니다. 
1. **파일로 저장** 옵션 및 해당 압축 옵션을 선택한 다음, **이동** 을 선택 합니다. 프롬프트에서 파일을 로컬로 저장 합니다.

## <a name="import-your-database-by-using-phpmyadmin"></a>PhpMyAdmin를 사용 하 여 데이터베이스 가져오기

가져오기 프로세스는 내보내기 프로세스와 유사 합니다. 다음을 수행합니다.
1. phpMyAdmin을 엽니다. 
1. PhpMyAdmin 설치 페이지에서 **추가** 를 선택 하 여 Azure Database for MariaDB 서버를 추가 합니다. 
1. 연결 정보 및 로그인 정보를 입력 합니다.
1. 적절 하 게 명명 된 데이터베이스를 만든 다음 왼쪽 창에서 선택 합니다. 기존 데이터베이스를 다시 작성 하려면 데이터베이스 이름을 선택 하 고 테이블 이름 옆의 확인란을 모두 선택한 다음 **삭제를 선택 하 여 기존** 테이블을 삭제 합니다. 
1. **Sql 링크를** 선택 하 여 sql 명령을 입력 하거나 sql 파일을 업로드할 수 있는 페이지를 표시 합니다. 
1. **찾아보기** 단추를 선택 하 여 데이터베이스 파일을 찾습니다. 
1. **이동** 단추를 선택 하 여 백업을 내보내고 SQL 명령을 실행 한 다음 데이터베이스를 다시 만듭니다.

## <a name="next-steps"></a>다음 단계
- [Azure database for MariaDB에 응용 프로그램을 연결](./howto-connection-string.md)합니다.
