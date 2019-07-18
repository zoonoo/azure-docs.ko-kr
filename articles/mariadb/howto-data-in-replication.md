---
title: MariaDB에 대 한 Azure Database에서 데이터의 복제 구성 | Microsoft Docs
description: 이 문서에서 데이터의에서 복제 Azure Database for MariaDB 설정 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444800"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>MariaDB에 대 한 Azure Database에서 데이터의 복제 구성

이 문서에서는 마스터 및 복제본 서버를 구성 하 여 데이터의 복제 Azure Database for MariaDB 설정 하는 방법을 설명 합니다. 이 문서에서는 MariaDB 서버 및 데이터베이스를 사용 하 여 경험이 있다고 가정 합니다.

Azure Database for MariaDB 서비스의에서 복제본을 만들려면 복제 데이터의 마스터 MariaDB 서버 온-프레미스, 클라우드 데이터베이스 서비스 또는 virtual machines (Vm)에서에서 데이터를 동기화 합니다.

> [!NOTE]
> 마스터 서버 10.2 이상 버전이 인 경우 사용 하 여 데이터의 복제를 설정 하는 것이 좋습니다 [전역 트랜잭션 ID](https://mariadb.com/kb/en/library/gtid/)합니다.


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>복제본으로 사용할 MariaDB 서버 만들기

1. MariaDB 서버 (예를 들어 replica.mariadb.database.azure.com)에 대 한 새 Azure 데이터베이스를 만듭니다. 서버에서 데이터 복제에서 복제 서버의 경우

    서버 만들기를 알아보려면 [Azure portal을 사용 하 여 Azure Database for MariaDB 서버를 만들](quickstart-create-mariadb-server-database-using-azure-portal.md)합니다.

   > [!IMPORTANT]
   > 범용 또는 메모리 최적화 가격 책정 계층에서 Azure Database for MariaDB 서버에 만들어야 합니다.

2. 동일한 사용자 계정 및 해당 권한을 만듭니다.
    
    사용자 계정은 마스터 서버에서 복제본 서버로 복제 되지 않습니다. 복제본 서버에 대 한 사용자 액세스를 제공 하려면 수동으로 만들어야 모든 계정 및 해당 권한을 새로 만든된 Azure Database for MariaDB 서버에 있습니다.

## <a name="configure-the-master-server"></a>마스터 서버 구성

다음 단계를 준비 하 고 데이터의 복제에 대 한 클라우드 데이터베이스 서비스 또는 vm에서 MariaDB 호스팅하는 서버 온-프레미스를 구성 합니다. MariaDB 서버가 복제 데이터에 마스터입니다.

1. 이진 로깅을 설정 합니다.
    
    마스터에서 이진 로깅이 활성화 되어 있는지를 확인 하려면 다음 명령을 입력 합니다.

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   경우 변수 [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) 값을 반환 합니다 `ON`, 서버에서 이진 로깅이 사용 됩니다.

   경우 `log_bin` 값을 반환 합니다 `OFF`을 편집 합니다 **my.cnf** 파일 있도록 `log_bin=ON` 이진 로깅을 설정 합니다. 변경 내용이 적용 되도록 서버를 다시 시작 합니다.

2. 마스터 서버 설정을 구성 합니다.

    복제 데이터에서 매개 변수를 사용 하면 `lower_case_table_names` 마스터 및 복제본 서버 간에 일관 되도록 합니다. 합니다 `lower_case_table_names` 매개 변수는 설정 `1` Azure Database for MariaDB에서에서 기본적으로 합니다.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 새 복제 역할을 만들고 사용 권한을 설정 합니다.

   복제 권한으로 구성 된 마스터 서버의 사용자 계정을 만듭니다. SQL 명령 또는 MySQL Workbench를 사용 하 여 계정을 만들면 됩니다. SSL을 사용 하 여 복제 하려는 경우 사용자 계정을 만들 때이 지정 해야 합니다.
   
   마스터 서버에서 사용자 계정을 추가 하는 방법에 알아보려면 참조를 [MariaDB 설명서](https://mariadb.com/kb/en/library/create-user/)합니다.

   다음 명령을 사용 하 여 새 복제 역할 자체 마스터를 호스트 하는 컴퓨터 뿐 아니라 모든 컴퓨터에서 마스터를 액세스할 수 있습니다. 이 액세스에 대해 지정할 **syncuser\@'%'** 명령에 사용자를 만듭니다.
   
   MariaDB 설명서에 대 한 자세한 내용은 참조 하세요 [계정 이름 지정](https://mariadb.com/kb/en/library/create-user/#account-names)합니다.

   **SQL 명령**

   - SSL 사용 하 여 복제

       모든 사용자 연결에 대 한 SSL이 필요한, 사용자를 만들려면 다음 명령을 입력 합니다.

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - SSL 없이 복제

       SSL을 모든 연결에 필요 하지 않으면 사용자를 만들려면 다음 명령을 입력 합니다.
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   MySQL Workbench에서 복제 역할을 만드는 합니다 **관리** 창 **사용자 및 권한을**합니다. 선택한 **계정 추가**합니다.
 
   ![사용자 및 권한](./media/howto-data-in-replication/users_privileges.png)

   사용자 이름을 입력 합니다 **로그인 이름** 필드입니다.

   ![사용자 동기화](./media/howto-data-in-replication/syncuser.png)
 
   선택 합니다 **관리 역할** 패널 한 다음 목록을 **전역 권한**를 선택 **Replication Slave**. 선택 **적용** 복제 역할을 만듭니다.

   ![복제 슬레이브](./media/howto-data-in-replication/replicationslave.png)


4. 읽기 전용 모드로 마스터 서버를 설정 합니다.

   데이터베이스를 덤프 하기 전에 읽기 전용 모드에서 서버에 배치 되어야 합니다. 읽기 전용 모드에서 마스터 쓰기 트랜잭션을 처리할 수 없습니다. 비즈니스 영향을 방지 하려면 읽기 전용 창 사용량이 적은 시간을 예약 합니다.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. 현재 이진 로그 파일 이름과 오프셋을 가져옵니다.

   현재 이진 로그 파일 이름과 오프셋을 확인 하려면 명령을 [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/)합니다.
    
   ```sql
   show master status;
   ```
   결과 다음과 비슷해야 합니다.
   
   ![마스터 상태 결과](./media/howto-data-in-replication/masterstatus.png)

   이후 단계에서 사용할 수 없기 때문에 이진 파일 이름을 note 합니다.
   
6. (선택 사항, GTID 사용 하 여 복제에 필요) GTID 위치를 가져옵니다.

   함수를 실행할 [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) 해당 binlog 파일 이름과 오프셋 GTID 위치를 가져올 수 있습니다.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>덤프 및 마스터 서버를 복원 합니다.

1. 마스터 서버에서 모든 데이터베이스를 덤프 합니다.

   Mysqldump를 사용 하 여 마스터 서버에서 모든 데이터베이스를 덤프 합니다. MySQL 라이브러리를 덤프 및 라이브러리를 테스트 하려면 필요는 없습니다.

    자세한 내용은 [덤프 및 복원을](howto-migrate-dump-restore.md)합니다.

2. 마스터 서버에 읽기/쓰기 모드를 설정 합니다.

   데이터베이스에 덤프 되 었 더라도, 후 마스터 변경 MariaDB 서버 돌아갑니다 읽기/쓰기 모드입니다.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 새 서버에 덤프 파일을 복원 합니다.

   덤프 파일을 Azure Database for MariaDB 서비스에서 생성된 서버로 복원합니다. 참조 [덤프 및 복원](howto-migrate-dump-restore.md) MariaDB 서버에 덤프 파일을 복원 하는 방법에 대 한 합니다.

   덤프 파일 크면 Azure에서 VM에 복제본 서버와 동일한 지역 내에서 업로드 합니다. VM에서 Azure Database for MariaDB 서버에 복원 합니다.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>데이터의 복제를 시작 하려면 master 및 복제본 서버에 연결

1. 마스터 서버를 설정 합니다.

   모든 데이터 내부 복제 기능은 저장 프로시저에 의해 수행됩니다. [데이터 내부 복제 저장 프로시저](reference-data-in-stored-procedures.md)에서 모든 프로시저를 확인할 수 있습니다. MySQL 셸 또는 MySQL Workbench에서 저장된 프로시저를 실행할 수 있습니다.

   두 서버를 연결 하 고 복제를 시작 하려면 MariaDB 서비스에 대 한 Azure DB의 대상 복제본 서버에 로그인 합니다. 다음으로 사용 하 여 마스터 서버와 외부 인스턴스를 설정 합니다 `mysql.az_replication_change_master` 또는 `mysql.az_replication_change_master_with_gtid` MariaDB 서버에 대 한 Azure DB에서 저장 프로시저입니다.

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
   - master_gtid_pos: 실행에서 GTID 위치 `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: CA 인증서의 컨텍스트. SSL을 사용 하지 않는 경우에 빈 string.* 전달
    
    
    \* 좋습니다 변수로 master_ssl_ca 매개 변수를 전달을 합니다. 자세한 내용은 다음 예제를 참조하세요.

   **예**

   - SSL 사용 하 여 복제

       변수를 만듭니다 `@cert` 다음 명령을 실행 하 여:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       SSL 사용 하 여 복제 도메인 companya.com에서 호스트 되는 마스터 서버와 MariaDB에 대 한 Azure Database에서 호스팅되는 복제본 서버 간에 설정 됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - SSL 없이 복제

       SSL 없이 복제 도메인 companya.com에서 호스트 되는 마스터 서버와 MariaDB에 대 한 Azure Database에서 호스팅되는 복제본 서버 간에 설정 됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 복제를 시작 합니다.

   호출 된 `mysql.az_replication_start` 복제를 시작 하는 절차를 저장 합니다.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 복제 상태를 확인 합니다.

   복제본 서버에서 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 명령을 호출하여 복제 상태를 확인합니다.
    
   ```sql
   show slave status;
   ```

   하는 경우 `Slave_IO_Running` 및 `Slave_SQL_Running` 상태인 `yes`, 및의 값 `Seconds_Behind_Master` 는 `0`, 복제가 작동 합니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이 아닌 경우 `0`, 복제본에서 업데이트를 처리 합니다.

4. 복제를 하려면 데이터에 안전 하 게 (GTID 없이 복제에만 필요)는 해당 서버 변수를 업데이트 합니다.
    
    기본 복제의 제한 때문에 MariaDB 설정 해야 합니다 [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) 하 고 [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) 변수 GTID 시나리오가 없는 복제 합니다.

    슬레이브 서버의 확인 `sync_master_info` 하 고 `sync_relay_log_info` 데이터에 복제 되는지 확인 하는 변수 안정적 이며으로 변수를 설정 `1`합니다.
    
## <a name="other-stored-procedures"></a>기타 저장 프로시저

### <a name="stop-replication"></a>복제 중지

마스터 서버와 복제본 서버 간의 복제를 중지하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>복제 관계를 제거 합니다.

마스터 및 복제본 서버 간의 관계를 제거 하려면 다음 저장된 프로시저를 사용 합니다.

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>복제 오류를 건너뛰기

복제 오류를 건너뜁니다. 복제를 허용을 다음 저장된 프로시저를 사용 합니다.
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>다음 단계
Azure Database for MariaDB를 위한 [입력 데이터 복제](concepts-data-in-replication.md)에 대해 자세히 알아보세요.
