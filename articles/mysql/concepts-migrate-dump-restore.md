---
title: 덤프 및 복원을 사용하여 마이그레이션 - Azure Database for MySQL
description: 이 문서에서는 mysqldump, MySQL Workbench 및 PHPMyAdmin과 같은 도구를 사용하여 MySQL용 Azure Database에서 데이터베이스를 백업 및 복원하는 2가지 일반적인 방법에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: c30faa31f6f733f80d4bfd5184c09d9fdbd6f389
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85971184"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>덤프 및 복원을 사용하여 MySQL Database를 MySQL용 Azure 데이터베이스로 마이그레이션
이 문서에서는 MySQL용 Azure Database에서 데이터베이스를 백업 및 복원하는 2가지 일반적인 방법에 대해 설명합니다.
- 명령줄에서 덤프 및 복원(mysqldump 사용) 
- PHPMyAdmin을 사용하여 덤프 및 복원 

## <a name="before-you-begin"></a>시작하기 전에
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [MySQL용 Azure 데이터베이스 서버 만들기 - Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) 명령줄 유틸리티가 컴퓨터에 설치되어 있어야 함
- 덤프 및 복원 명령을 수행할 MySQL Workbench [MySQL Workbench 다운로드](https://dev.mysql.com/downloads/workbench/) 또는 다른 타사 MySQL 도구.

데이터베이스 크기가 1TB를 초과하는 대량 데이터베이스를 마이그레이션하려는 경우 병렬 내보내기 및 가져오기를 지원하는 mydumper/myloader와 같은 커뮤니티 도구를 사용하는 것이 좋습니다. 병렬 덤프 및 복원은 큰 데이터베이스의 마이그레이션 시간을 크게 줄이는 데 도움이 될 수 있습니다. mydumper/myloader 도구를 사용하여 큰 데이터베이스를 Azure Database for MySQL 서비스로 마이그레이션하기 위한 모범 사례는 [techcommunity 블로그](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)를 참조하세요.

## <a name="use-common-tools"></a>일반 도구 사용
MySQL Workbench 또는 mysqldump와 같은 일반 유틸리티 및 도구를 사용하여 Azure Database for MySQL에 원격으로 연결하고 데이터를 복원합니다. 인터넷에 연결된 클라이언트 컴퓨터에서 이러한 도구를 사용하여 MySQL용 Azure 데이터베이스에 데이터에 연결합니다. 최상의 보안을 위해 SSL 암호화 연결을 사용합니다. [MySQL용 Azure 데이터베이스에서 SSL 연결 구성](concepts-ssl-connection-security.md)을 참조하세요. MySQL용 Azure 데이터베이스로 마이그레이션할 때 덤프 파일을 특수한 클라우드 위치로 이동할 필요가 없습니다. 

## <a name="common-uses-for-dump-and-restore"></a>덤프 및 복원을 위한 일반적인 사용
몇 가지 일반적인 시나리오에서 mysqldump 및 mysqlpump와 같은 MySQL 유틸리티를 사용하여 데이터베이스를 Azure MySQL Database로 덤프 및 로드할 수 있습니다. 다른 시나리오에서 대신 [가져오기 및 내보내기](concepts-migrate-import-export.md) 방법을 사용할 수 있습니다.

- 전체 데이터베이스를 마이그레이션할 때 데이터베이스 덤프를 사용합니다. 이 권장 사항은 많은 양의 MySQL 데이터를 이동하거나 실시간 사이트 또는 애플리케이션에 대한 서비스 중단을 최소화하려는 경우 유지합니다. 
-  데이터베이스의 모든 테이블이 데이터를 Azure Database for MySQL로 로드할 때 InnoDB 스토리지 엔진을 사용하도록 합니다. Azure Database for MySQL은 InnoDB 스토리지 엔진만을 지원하므로 대체 스토리지 엔진을 지원하지 않습니다. 테이블이 다른 스토리지 엔진으로 구성된 경우 Azure Database for MySQL로 마이그레이션 전에 InnoDB 엔진 형식으로 변환합니다.
   예를 들어 MyISAM 테이블을 사용하는 WordPress 또는 WebApp이 있는 경우 Azure Database for MySQL로 복원하기 전에 InnoDB 형식으로 마이그레이션하여 먼저 해당 테이블을 변환합니다. `ENGINE=InnoDB` 절을 사용하여 새 테이블을 만들 때 사용되는 엔진을 설정한 다음 복원 전에 데이터를 호환되는 테이블로 전송합니다. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- 호환성 문제를 방지하려면 데이터베이스를 덤프할 때 동일한 버전의 MySQL이 원본 및 대상 시스템에서 사용되도록 합니다. 예를 들어 기존 MySQL 서버가 버전 5.7이면 버전 5.7을 수행하도록 구성된 Azure Database for MySQL로 마이그레이션해야 합니다. `mysql_upgrade` 명령은 Azure Database for MySQL 서버에서 작동하지 않으며 지원되지 않습니다. MySQL 버전 간에 업그레이드해야 하는 경우 먼저 사용자 환경에서 낮은 버전의 데이터베이스를 더 높은 버전의 MySQL로 덤프하거나 내보냅니다. 그런 다음 Azure Database for MySQL로 마이그레이션을 시도하기 전에 `mysql_upgrade`를 실행합니다.

## <a name="performance-considerations"></a>성능 고려 사항
성능을 최적화하려면 큰 데이터베이스를 덤프할 때 이러한 고려 사항을 숙지합니다.
-   데이터베이스를 덤프할 때 mysqldump에서 `exclude-triggers` 옵션을 사용합니다. 데이터 복원 중 발생하는 트리거 명령을 방지하기 위해 덤프 파일에서 트리거를 제외합니다. 
-   `single-transaction` 옵션을 사용하여 트랜잭션 격리 모드를 REPEATABLE READ로 설정하고 데이터를 덤프하기 전에 START TRANSACTION SQL 문을 서버로 보냅니다. 단일 트랜잭션 내에서 많은 테이블을 덤프하면 복원 중 스토리지가 추가로 소비됩니다. LOCK TABLES는 보류 중인 트랜잭션을 암시적으로 커밋되도록 하기 때문에 `single-transaction` 옵션과 `lock-tables` 옵션은 상호 배타적입니다. 대형 테이블을 덤프하려면 `single-transaction` 옵션을 `quick` 옵션과 결합합니다. 
-   여러 VALUE 목록을 포함하는 `extended-insert` 여러 행 구문을 사용합니다. 그러면 덤프 파일이 작아지고 파일을 다시 로드할 때 삽입 속도가 빨라집니다.
-  데이터가 기본 키 순서로 스크립팅되도록 데이터베이스를 덤프할 때 mysqldump에서 `order-by-primary` 옵션을 사용합니다.
-   로드 전에 외래 키 제약 조건을 비활성화하려면 데이터를 덤프할 때 mysqldump에서 `disable-keys` 옵션을 사용합니다. 외래 키 검사 비활성화는 성능 향상을 제공합니다. 제약 조건을 활성화하고 참조 무결성을 확인하도록 로드 후 데이터를 확인합니다.
-   적절한 경우 분할된 테이블을 사용합니다.
-   병렬로 데이터를 로드합니다. 리소스 제한에 도달하도록 하는 너무 많은 병렬 처리를 피하고 Azure Portal에서 사용할 수 있는 메트릭을 사용하여 리소스를 모니터링합니다. 
-   테이블 데이터가 로드된 후 인덱스 생성이 발생하도록 데이터베이스를 덤프할 때 mysqlpump에서 `defer-table-indexes` 옵션을 사용합니다.
-   mysqlpump의 `skip-definer` 옵션을 사용하여 뷰 및 저장 프로시저에 대한 create 문에서 definer 및 SQL SECURITY 절을 생략합니다.  덤프 파일을 다시 로드하면 기본 DEFINER 및 SQL SECURITY 값을 사용하는 개체가 생성됩니다.
-   Azure blob/저장소에 백업 파일을 복사하고, 인터넷을 통해 복원을 수행할 때보다 훨씬 더 빨리 수행할 수 있는 위치에서 복원을 수행합니다.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>mysqldump를 사용하여 명령줄에서 백업 파일 만들기
로컬 온-프레미스 서버 또는 가상 머신에 기존 MySQL 데이터베이스를 백업하려면 다음 명령을 실행합니다. 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

제공할 매개 변수:
- [uname] 데이터베이스 사용자 이름 
- [pass] 데이터베이스에 대한 암호(-p와 암호 사이를 띄어쓰지 말 것) 
- [dbname] 데이터베이스의 이름 
- [backupfile.sql] 데이터베이스 백업의 파일 이름 
- [-opt] mysqldump 옵션 

예를 들어 사용자 이름이 'testuser'이고 암호가 없는 'testdb'라는 MySQL 서버의 데이터베이스를 파일 testdb_backup.sql에 백업하려면 다음 명령을 사용합니다. 명령은 데이터베이스를 다시 만드는 데 필요한 모든 SQL 문을 포함하는 `testdb_backup.sql`이라는 파일로 `testdb` 데이터베이스를 백업합니다. 사용자 이름 ' testuser '에 적어도 덤프 된 테이블에 대 한 SELECT 권한이 있는지 확인 하 고, 덤프 된 뷰에 대 한 뷰를 표시 하 고, 덤프 된 트리거의 트리거를 사용 하 고,--단일 트랜잭션 옵션이 사용 되지 않는 경우 테이블을 잠급니다.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
백업할 데이터베이스의 특정 테이블을 선택하려면 테이블 이름을 공백으로 구분해서 나열합니다. 예를 들어 'testdb'에서 table1 및 table2 테이블만 백업하려면 다음 예제를 따릅니다. 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
한 번에 둘 이상의 데이터베이스를 백업하려면 --database 스위치를 사용하고 데이터베이스 이름을 공백으로 구분합니다. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>대상 Azure Database for MySQL 서버에서 데이터베이스 만들기
데이터를 마이그레이션하려는 대상 Azure Database for MySQL 서버에서 빈 데이터베이스를 만듭니다. MySQL Workbench와 같은 도구를 사용하여 데이터베이스를 만듭니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 같을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

연결하려면 Azure Database for MySQL의 **개요**에서 연결 정보를 찾습니다.

![Azure Portal에서 연결 정보 찾기](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

MySQL Workbench에 연결 정보를 추가합니다.

![MySQL Workbench 연결 문자열](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>빠른 데이터 로드를 위해 대상 Azure Database for MySQL 서버 준비
더 빠른 데이터 로드를 위해 대상 Azure Database for MySQL 서버를 준비하려면 다음 서버 매개 변수 및 구성을 변경해야 합니다.
- max_allowed_packet – 긴 행으로 인한 오버플로 문제를 방지하기 위해 1073741824(예: 1GB)로 설정합니다.
- slow_query_log – 저속 쿼리 로그를 해제하려면 OFF로 설정합니다. 이렇게 하면 데이터 로드 중 느린 쿼리 로깅으로 인한 오버헤드가 제거됩니다.
- query_store_capture_mode – 쿼리 저장소 해제 하려면 없음으로 설정 합니다. 이렇게 하면 쿼리 저장소의 샘플링 작업으로 인한 오버헤드가 제거됩니다.
- innodb_buffer_pool_size – 마이그레이션 중에 포털의 가격 책정 계층에서 서버를 32개의 vCore 메모리 최적화 SKU로 확장하여 innodb_buffer_pool_size를 늘립니다. Innodb_buffer_pool_size는 Azure Database for MySQL 서버에 대한 컴퓨팅을 확장해야만 늘릴 수 있습니다.
- innodb_io_capacity & innodb_io_capacity_max-Azure Portal의 서버 매개 변수에서 9000로 변경 하 여 마이그레이션 속도를 최적화 하기 위해 IO 사용률을 향상 시킵니다.
- innodb_write_io_threads & innodb_write_io_threads-마이그레이션의 속도를 개선 하기 위해 Azure Portal의 서버 매개 변수에서 4로 변경 합니다.
- 스토리지 계층 스케일 업 – 스토리지 계층이 증가함에 따라 Azure Database for MySQL 서버의 IOP가 점진적으로 증가합니다. 더 빠른 로드를 위해 스토리지 계층을 늘려 프로비저닝된 IOP를 늘릴 수 있습니다. 스토리지는 축소할 수 없고 확장만 할 수 있습니다.

마이그레이션이 완료되면 서버 매개 변수 및 컴퓨팅 계층 구성을 이전 값으로 되돌릴 수 있습니다. 

## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>명령줄 또는 MySQL Workbench를 사용하여 MySQL 데이터베이스 복원
대상 데이터베이스를 만든 후에는 mysql 명령 또는 MySQL Workbench를 사용하여 덤프 파일에서 새로 만든 특정 데이터베이스로 데이터를 복원할 수 있습니다.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
이 예제에서는 대상 Azure Database for MySQL 서버의 새로 만든 데이터베이스로 데이터를 복원합니다.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
## <a name="export-using-phpmyadmin"></a>PHPMyAdmin을 사용하여 내보내기
내보내려면 환경에 로컬로 이미 설치했을 수 있는 일반 도구 phpMyAdmin을 사용할 수 있습니다. PHPMyAdmin을 사용하여 MySQL 데이터베이스를 내보내려면
1. phpMyAdmin을 엽니다.
2. 데이터베이스를 선택합니다. 왼쪽 목록에서 데이터베이스 이름을 클릭합니다. 
3. **내보내기** 링크를 클릭합니다. 데이터베이스의 덤프를 보는 새 페이지가 나타납니다.
4. 내보내기 영역에서 **모두 선택** 링크를 클릭하여 데이터베이스의 테이블을 선택합니다. 
5. SQL 옵션 영역에서 적절한 옵션을 클릭합니다. 
6. **파일로 저장** 옵션 및 해당 압축 옵션을 클릭하고 **이동** 단추를 클릭합니다. 파일을 로컬로 저장할지 묻는 대화 상자가 나타납니다.

## <a name="import-using-phpmyadmin"></a>PHPMyAdmin을 사용하여 가져오기
데이터베이스 가져오기는 내보내기와 비슷합니다. 다음 작업을 수행합니다.
1. phpMyAdmin을 엽니다. 
2. phpMyAdmin 설치 페이지에서 **추가**를 클릭하여 Azure Database for MySQL 서버를 추가합니다. 연결 정보 및 로그인 정보를 제공합니다.
3. 적절하게 명명된 데이터베이스를 만들고 화면 왼쪽에서 선택합니다. 기존 데이터베이스를 다시 작성하려면 데이터베이스 이름을 클릭하고 테이블 이름 옆에 있는 모든 확인란을 선택하고 **삭제**를 선택하여 기존 테이블을 삭제합니다. 
4. **SQL** 링크를 클릭하여 SQL 명령을 입력하거나 SQL 파일을 업로드할 수 있는 페이지를 표시합니다. 
5. **찾아보기** 단추를 사용하여 데이터베이스 파일을 찾습니다. 
6. **이동** 단추를 클릭하여 백업을 내보내고 SQL 명령을 실행하고, 데이터베이스를 다시 만듭니다.

## <a name="known-issues"></a>알려진 문제
알려진 문제, 팁과 요령을 보려면 [techcommunity 블로그](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912)를 확인하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL에 애플리케이션을 연결합니다](./howto-connection-string.md).
- Azure Database for MySQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://aka.ms/datamigration)을 참조합니다.
