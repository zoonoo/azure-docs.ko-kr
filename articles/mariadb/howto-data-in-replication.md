---
title: 데이터 에서 복제 - MariaDB에 대한 Azure 데이터베이스 구성
description: 이 문서에서는 MariaDB에 대 한 Azure 데이터베이스에서 데이터 에서 복제를 설정 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 51b800dde140affd222f2bdb341c0fbf3a57d8cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530158"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>MariaDB에 대한 Azure 데이터베이스에서 데이터 인 복제 구성

이 문서에서는 마스터 및 복제서버를 구성하여 MariaDB용 Azure 데이터베이스에서 데이터 인 복제를 설정하는 방법에 대해 설명합니다. 이 문서에서는 MariaDB 서버 및 데이터베이스에 대한 사전 경험이 있다고 가정합니다.

MariaDB 서비스에 대한 Azure 데이터베이스에서 복제본을 만들려면 Data-in Replication은 마스터 MariaDB 서버온-프레미스, 가상 시스템(VM) 또는 클라우드 데이터베이스 서비스의 데이터를 동기화합니다.

> [!NOTE]
> 마스터 서버가 버전 10.2 이상인 경우 [전역 트랜잭션 ID를](https://mariadb.com/kb/en/library/gtid/)사용하여 데이터 인 복제를 설정하는 것이 좋습니다.


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>복제본으로 사용할 MariaDB 서버 만들기

1. MariaDB 서버에 대한 새 Azure 데이터베이스(예: replica.mariadb.database.azure.com)를 만듭니다. 서버는 데이터 인 복제의 복제 서버입니다.

    서버 생성에 대해 자세히 알아보려면 [Azure 포털을 사용하여 MariaDB 서버에 대한 Azure 데이터베이스 만들기를](quickstart-create-mariadb-server-database-using-azure-portal.md)참조하십시오.

   > [!IMPORTANT]
   > 범용 또는 메모리 최적화 가격 책정 계층에서 MariaDB 서버에 대한 Azure 데이터베이스를 만들어야 합니다.

2. 동일한 사용자 계정 및 해당 권한을 만듭니다.
    
    사용자 계정은 마스터 서버에서 복제서버로 복제되지 않습니다. 복제본 서버에 대한 사용자 액세스를 제공하려면 MariaDB 서버에 대해 새로 만든 Azure Database에서 모든 계정과 해당 권한을 수동으로 만들어야 합니다.

## <a name="configure-the-master-server"></a>마스터 서버 구성

다음 단계는 데이터 인 복제를 위해 온-프레미스, VM 또는 클라우드 데이터베이스 서비스에서 호스팅되는 MariaDB 서버를 준비하고 구성합니다. MariaDB 서버는 데이터 인 복제의 마스터입니다.

1. 이진 로깅을 켭니다.
    
    마스터에서 바이너리 로깅이 활성화되어 있는지 확인하려면 다음 명령을 입력합니다.

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   변수가 [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) 값을 `ON`반환하는 경우 서버에서 바이너리 로깅이 활성화됩니다.

   값을 `log_bin` `OFF`반환하는 경우 이진 로깅을 `log_bin=ON` 켜지 않도록 **my.cnf** 파일을 편집합니다. 변경 이 적용되도록 서버를 다시 시작합니다.

2. 마스터 서버 설정을 구성합니다.

    데이터 인 복제는 마스터 `lower_case_table_names` 서버와 복제본 서버 간에 매개 변수가 일관되어야 합니다. `lower_case_table_names` 매개 변수는 `1` MariaDB에 대 한 Azure 데이터베이스에서 기본적으로 설정 됩니다.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 새 복제 역할을 만들고 사용 권한을 설정합니다.

   복제 권한으로 구성된 마스터 서버에서 사용자 계정을 만듭니다. SQL 명령 또는 MySQL 워크벤치를 사용하여 계정을 만들 수 있습니다. SSL을 사용하여 복제하려는 경우 사용자 계정을 만들 때 이를 지정해야 합니다.
   
   마스터 서버에 사용자 계정을 추가하는 방법을 알아보려면 [MariaDB 설명서를](https://mariadb.com/kb/en/library/create-user/)참조하십시오.

   다음 명령을 사용하여 새 복제 역할은 마스터 자체를 호스팅하는 컴퓨터뿐만 아니라 모든 컴퓨터에서 마스터에 액세스할 수 있습니다. 이 액세스의 경우 명령에서 **syncuser\@'%'를** 지정하여 사용자를 만듭니다.
   
   MariaDB 문서에 대한 자세한 내용은 [계정 이름 지정을](https://mariadb.com/kb/en/library/create-user/#account-names)참조하십시오.

   **SQL 명령**

   - SSL을 사용한 복제

       모든 사용자 연결에 대해 SSL을 요구하려면 다음 명령을 입력하여 사용자를 만듭니다.

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - SSL 없이 복제

       모든 연결에 SSL이 필요하지 않은 경우 다음 명령을 입력하여 사용자를 만듭니다.
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   MySQL 워크벤치에서 복제 역할을 만들려면 **관리** 창에서 사용자 및 권한 옵션을 **선택합니다.** 그런 다음 **계정 추가를**선택합니다.
 
   ![사용자 및 권한](./media/howto-data-in-replication/users_privileges.png)

   **로그인 이름** 필드에 사용자 이름을 입력합니다.

   ![사용자 동기화](./media/howto-data-in-replication/syncuser.png)
 
   관리 **역할** 패널을 선택한 다음 전역 권한 목록에서 **복제 슬레이브를** **선택합니다.** 복제 역할을 만들려면 **적용을** 선택합니다.

   ![복제 슬레이브](./media/howto-data-in-replication/replicationslave.png)


4. 마스터 서버를 읽기 전용 모드로 설정합니다.

   데이터베이스를 덤프하기 전에 서버를 읽기 전용 모드로 배치해야 합니다. 읽기 전용 모드에서 마스터는 쓰기 트랜잭션을 처리할 수 없습니다. 비즈니스 에 영향을 미치지 않도록 사용량이 많은 시간에 읽기 전용 창을 예약합니다.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. 현재 바이너리 로그 파일 이름 및 오프셋을 가져옵니다.

   현재 바이너리 로그 파일 이름과 오프셋을 확인하려면 명령을 [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)실행합니다.
    
   ```sql
   show master status;
   ```
   결과는 다음 표와 유사해야 합니다.
   
   ![마스터 상태 결과](./media/howto-data-in-replication/masterstatus.png)

   이진 파일 이름은 이후 단계에서 사용되므로 참고하십시오.
   
6. GTID 위치(선택 사항, GTID로 복제에 필요)를 가져옵니다.

   함수를 [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) 실행하여 해당 binlog 파일 이름 및 오프셋에 대한 GTID 위치를 가져옵니다.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>마스터 서버 덤프 및 복원

1. 마스터 서버에서 모든 데이터베이스를 덤프합니다.

   mysqldump를 사용하여 마스터 서버에서 모든 데이터베이스를 덤프합니다. MySQL 라이브러리및 테스트 라이브러리를 덤프할 필요는 없습니다.

    자세한 내용은 [덤프 및 복원](howto-migrate-dump-restore.md)을 참조하십시오.

2. 마스터 서버를 읽기/쓰기 모드로 설정합니다.

   데이터베이스가 덤프된 후 마스터 MariaDB 서버를 다시 읽기/쓰기 모드로 변경합니다.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 덤프 파일을 새 서버로 복원합니다.

   덤프 파일을 Azure Database for MariaDB 서비스에서 생성된 서버로 복원합니다. 덤프 파일을 MariaDB 서버로 복원하는 방법은 복원& 을 [참조하십시오.](howto-migrate-dump-restore.md)

   덤프 파일이 큰 경우 복제본 서버와 동일한 지역 내에서 Azure의 VM에 업로드합니다. VM에서 MariaDB 서버에 대한 Azure 데이터베이스로 복원합니다.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>마스터 서버와 복제본 서버를 연결하여 데이터 인 복제를 시작합니다.

1. 마스터 서버를 설정합니다.

   모든 데이터 내부 복제 기능은 저장 프로시저에 의해 수행됩니다. [데이터 내부 복제 저장 프로시저](reference-data-in-stored-procedures.md)에서 모든 프로시저를 확인할 수 있습니다. 저장 프로시저는 MySQL 셸 또는 MySQL 워크벤치에서 실행할 수 있습니다.

   두 대의 서버를 연결하고 복제를 시작하려면 MariaDB 서비스에 대한 Azure DB의 대상 복제서버에 로그인합니다. 다음으로, MariaDB 서버에 대 한 Azure `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` DB에 저장 된 프로시저를 사용 하 여 마스터 서버로 외부 인스턴스를 설정 합니다.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   또는
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: 마스터 서버의 호스트 이름
   - master_user: 마스터 서버의 사용자 이름
   - master_password: 마스터 서버의 암호
   - master_log_file: 실행 중인 `show master status`의 이진 로그 파일 이름
   - master_log_pos: 실행 중인 `show master status`의 이진 로그 위치
   - master_gtid_pos: 실행 중인 GTID 위치`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: CA 인증서의 컨텍스트입니다. SSL을 사용하지 않는 경우 빈 문자열을 전달합니다.*
    
    
    *master_ssl_ca 매개 변수를 변수로 전달하는 것이 좋습니다. 자세한 내용은 다음 예제를 참조하세요.

   **예**

   - SSL을 사용한 복제

       다음 명령을 `@cert` 실행하여 변수를 만듭니다.

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       SSL을 사용하여 복제는 도메인 companya.com 호스팅되는 마스터 서버와 MariaDB용 Azure 데이터베이스에서 호스팅되는 복제서버 간에 설정됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - SSL 없이 복제

       SSL이 없는 복제는 도메인 companya.com 호스팅되는 마스터 서버와 MariaDB용 Azure 데이터베이스에서 호스팅되는 복제 서버 간에 설정됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 복제를 시작합니다.

   `mysql.az_replication_start` 저장된 프로시저를 호출하여 복제를 시작합니다.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 복제 상태를 확인합니다.

   복제 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 서버의 명령을 호출하여 복제 상태를 확인합니다.
    
   ```sql
   show slave status;
   ```

   `Slave_SQL_Running` 상태에 `Slave_IO_Running` `yes`있는 경우 `Seconds_Behind_Master` 의 값은 `0`복제가 작동합니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이 아닌 `0`경우 복제본이 업데이트를 처리하고 있습니다.

4. 해당 서버 변수를 업데이트하여 데이터 입력 복제를 보다 안전하게 만듭니다(GTID 없이 복제에만 필요).
    
    MariaDB의 기본 복제 제한 으로 인해 [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) GTID 시나리오 없이 복제에 대 한 [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) 변수를 설정 해야 합니다.

    슬레이브 `sync_master_info` 서버와 `sync_relay_log_info` 변수를 확인하여 데이터 입력 복제가 안정적인지 확인하고 변수를 `1`로 설정합니다.
    
## <a name="other-stored-procedures"></a>기타 저장 프로시저

### <a name="stop-replication"></a>복제 중지

마스터 서버와 복제본 서버 간의 복제를 중지하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>복제 관계 제거

마스터 서버와 복제본 서버 간의 관계를 제거하려면 다음 저장 절차를 사용합니다.

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>복제 오류 건너뛰기

복제 오류를 건너뛰고 복제를 허용하려면 다음 저장 프로시저를 사용합니다.
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>다음 단계
Azure Database for MariaDB를 위한 [입력 데이터 복제](concepts-data-in-replication.md)에 대해 자세히 알아보세요.
