---
title: 데이터에서 복제 구성-Azure Database for MariaDB
description: 이 문서에서는 Azure Database for MariaDB에서 입력 데이터 복제를 설정 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: 2de6b6311a1a5d452907b8c4b6a2ffeb9c0e133e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598192"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Azure Database for MariaDB에서 입력 데이터 복제 구성

이 문서에서는 원본 및 복제 서버를 구성 하 여 Azure Database for MariaDB에서 [입력 데이터 복제](concepts-data-in-replication.md) 를 설정 하는 방법을 설명 합니다. 이 문서에서는 사용자에 게 몇 가지 이전 경험이 있는 사용자가 있다고 가정 합니다.

Azure Database for MariaDB 서비스에서 복제본을 만들기 위해는 온-프레미스, Vm (가상 머신) 또는 클라우드 데이터베이스 서비스에서 원본 Fadb 서버의 데이터를 동기화 [입력 데이터 복제](concepts-data-in-replication.md) 합니다. 입력 데이터 복제는 MariaDB에 네이티브인 이진 로그(binlog) 파일 위치 기반 복제를 기반으로 합니다. binlog 복제에 대해 자세히 알아보려면 [binlog 복제 개요](https://mariadb.com/kb/en/library/replication-overview/)를 참조하세요.

이 문서의 단계를 수행 하기 전에 데이터 복제의 [제한 사항 및 요구 사항을](concepts-data-in-replication.md#limitations-and-considerations) 검토 합니다.

> [!NOTE]
> 원본 서버가 버전 10.2 이상이 면 [전역 트랜잭션 ID](https://mariadb.com/kb/en/library/gtid/)를 사용 하 여 입력 데이터 복제를 설정 하는 것이 좋습니다.


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>복제본으로 사용할 MariaDB 서버 만들기

1. 새 Azure Database for MariaDB 서버 (예: replica.mariadb.database.azure.com)를 만듭니다. 서버는 입력 데이터 복제의 복제 서버입니다.

    서버 생성에 대해 알아보려면 Azure Portal를 [사용 하 여 Azure Database for MariaDB 서버 만들기](quickstart-create-mariadb-server-database-using-azure-portal.md)를 참조 하세요.

   > [!IMPORTANT]
   > 범용 또는 메모리 액세스에 최적화 된 가격 책정 계층에서 Azure Database for MariaDB 서버를 만들어야 합니다.

2. 동일한 사용자 계정 및 해당 권한을 만듭니다.
    
    사용자 계정이 원본 서버에서 복제 서버로 복제 되지 않습니다. 복제 서버에 대 한 사용자 액세스를 제공 하려면 새로 만든 Azure Database for MariaDB 서버에 대 한 모든 계정 및 해당 권한을 수동으로 만들어야 합니다.

3. 원본 서버의 IP 주소를 복제본의 방화벽 규칙에 추가 합니다. 

   [Azure Portal](howto-manage-firewall-portal.md) 또는 [Azure CLI](howto-manage-firewall-cli.md)를 사용하여 방화벽 규칙을 업데이트합니다.

> [!NOTE]
> 바이어스 없는 통신
>
> Microsoft는 다양 한 inclusionary 환경을 지원 합니다. 이 문서에는 word _슬레이브_에 대 한 참조가 포함 되어 있습니다. [바이어스 없는 통신을 위한 Microsoft 스타일 가이드](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) 는이를 exclusionary 단어로 인식 합니다. 이 문서는 현재 소프트웨어에 표시 되는 단어 이므로 일관성을 위해 사용 됩니다. 소프트웨어를 업데이트 하 여 단어를 제거 하면이 문서는 맞춤으로 업데이트 됩니다.
>

## <a name="configure-the-source-server"></a>원본 서버 구성

다음 단계는 온-프레미스, VM 또는 클라우드 데이터베이스 서비스에서 호스트 되는 입력 데이터 복제에 대해 온-프레미스에 호스트 된 Aadb 서버를 준비 하 고 구성 합니다. 이 서버는 입력 데이터 복제의 원본입니다.

1. 계속 하기 전에 [마스터 서버 요구 사항을](concepts-data-in-replication.md#requirements) 검토 하십시오. 

2. 원본 서버에서 포트 3306에 대 한 인바운드 및 아웃 바운드 트래픽을 모두 허용 하는지 확인 하 **고, DNS가 공개적으로 액세스할**수 있거나 FQDN (정규화 된 도메인 이름)이 있는지 확인 합니다. 
   
   다른 컴퓨터에서 호스트 되는 MySQL 명령줄 또는 Azure Portal에서 사용할 수 있는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 와 같은 도구에서 연결을 시도 하 여 원본 서버에 대 한 연결을 테스트 합니다.

   조직에서 엄격한 보안 정책을 사용 하 고 원본 서버의 모든 IP 주소가 Azure에서 원본 서버로의 통신을 사용 하도록 허용 하지 않는 경우, 아래 명령을 사용 하 여 Azure Database for MariaDB 서버의 IP 주소를 확인할 수 있습니다.
    
   1. MySQL 명령줄과 같은 도구를 사용 하 여 Azure Database for MariaDB에 로그인 합니다.
   2. 아래 쿼리를 실행합니다.
      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```
      다음은 몇 가지 샘플 출력입니다.
      ```bash 
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```
   3. MySQL 명령줄에서 종료 합니다.
   4. Ping 유틸리티에서 아래를 실행 하 여 IP 주소를 가져옵니다.
      ```bash
      ping <output of step 2b>
      ``` 
      예를 들면 다음과 같습니다. 
      ```bash      
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. 이전 단계의 출력 된 IP 주소를 포트 3306에 포함 하도록 원본 서버의 방화벽 규칙을 구성 합니다.

   > [!NOTE]
   > 이 IP 주소는 유지 관리/배포 작업으로 인해 변경 될 수 있습니다. 이 연결 방법은 3306 포트에서 모든 IP 주소를 허용 하지 않는 고객만을 위한 것입니다.

2. 이진 로깅을 설정 합니다.
    
    마스터에서 이진 로깅이 사용 되는지 확인 하려면 다음 명령을 입력 합니다.

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   변수가 값을 반환 하는 경우 [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) `ON` 서버에서 이진 로깅이 사용 됩니다.

   에서 `log_bin` 값을 반환 `OFF` 하는 경우 이진 로깅을 설정 하도록 **my.cnf** 파일을 편집 합니다 `log_bin=ON` . 서버를 다시 시작 하 여 변경 내용을 적용 합니다.

3. 원본 서버 설정을 구성 합니다.

    입력 데이터 복제 `lower_case_table_names` 원본 서버와 복제 서버 간에 매개 변수가 일치 해야 합니다. `lower_case_table_names`매개 변수는 `1` 기본적으로 Azure Database for MariaDB로 설정 됩니다.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. 새 복제 역할을 만들고 사용 권한을 설정 합니다.

   복제 권한으로 구성 된 원본 서버에서 사용자 계정을 만듭니다. SQL 명령 또는 MySQL 워크 벤치를 사용 하 여 계정을 만들 수 있습니다. SSL로 복제할 계획인 경우 사용자 계정을 만들 때이를 지정 해야 합니다.
   
   원본 서버에서 사용자 계정을 추가 하는 방법에 대 한 자세한 내용은 [Mariadb 설명서](https://mariadb.com/kb/en/library/create-user/)를 참조 하세요.

   다음 명령을 사용 하 여 새 복제 역할은 원본 자체를 호스트 하는 컴퓨터 뿐 아니라 모든 컴퓨터에서 원본에 액세스할 수 있습니다. 이 액세스의 경우 사용자를 만들기 위해 명령에 **syncuser \@ '% '** 를 지정 합니다.
   
   MariaDB 설명서에 대해 자세히 알아보려면 [계정 이름 지정](https://mariadb.com/kb/en/library/create-user/#account-names)을 참조 하세요.

   **SQL 명령**

   - SSL을 사용한 복제

       모든 사용자 연결에 대해 SSL을 요구 하려면 다음 명령을 입력 하 여 사용자를 만듭니다.

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - SSL 없이 복제

       모든 연결에 SSL이 필요 하지 않은 경우 다음 명령을 입력 하 여 사용자를 만듭니다.
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   MySQL 워크 벤치에서 복제 역할을 만들려면 **관리** 창에서 **사용자 및 권한**을 선택 합니다. 그런 다음 **계정 추가**를 선택 합니다.
 
   ![사용자 및 권한](./media/howto-data-in-replication/users_privileges.png)

   **로그인 이름** 필드에 사용자 이름을 입력 합니다.

   ![사용자 동기화](./media/howto-data-in-replication/syncuser.png)
 
   **관리 역할** 패널을 선택한 다음 **글로벌 권한**목록에서 **복제 슬레이브**를 선택 합니다. **적용** 을 선택 하 여 복제 역할을 만듭니다.

   ![복제 슬레이브](./media/howto-data-in-replication/replicationslave.png)


5. 원본 서버를 읽기 전용 모드로 설정 합니다.

   데이터베이스를 덤프 하려면 먼저 서버를 읽기 전용 모드로 전환 해야 합니다. 읽기 전용 모드에서는 원본에서 쓰기 트랜잭션을 처리할 수 없습니다. 비즈니스 영향을 방지 하기 위해 사용량이 적은 시간에 읽기 전용 창을 예약 합니다.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. 현재 이진 로그 파일 이름 및 오프셋을 가져옵니다.

   현재 이진 로그 파일 이름과 오프셋을 확인 하려면 명령을 실행 [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) 합니다.
    
   ```sql
   show master status;
   ```
   결과는 다음 테이블과 유사 합니다.
   
   ![마스터 상태 결과](./media/howto-data-in-replication/masterstatus.png)

   이진 파일 이름은 이후 단계에서 사용 되므로 기록해 둡니다.
   
7. GTID 위치를 가져옵니다 (선택 사항, GTID를 사용 하는 복제에 필요).

   함수를 실행 [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) 하 여 해당 binlog 파일 이름 및 오프셋에 대 한 GTID 위치를 가져옵니다.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-source-server"></a>원본 서버 덤프 및 복원

1. 원본 서버에서 모든 데이터베이스를 덤프 합니다.

   Mysqldump를 사용 하 여 원본 서버에서 모든 데이터베이스를 덤프 합니다. MySQL 라이브러리 및 테스트 라이브러리를 덤프할 필요는 없습니다.

    자세한 내용은 [덤프 및 복원](howto-migrate-dump-restore.md)을 참조 하세요.

2. 원본 서버를 읽기/쓰기 모드로 설정 합니다.

   데이터베이스를 덤프 한 후에는 원본 Aadb 서버를 읽기/쓰기 모드로 다시 변경 합니다.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 덤프 파일을 새 서버에 복원 합니다.

   덤프 파일을 Azure Database for MariaDB 서비스에서 생성된 서버로 복원합니다. 덤프 파일을 MariaDB 서버에 복원 하는 방법은 [덤프 & 복원](howto-migrate-dump-restore.md) 을 참조 하세요.

   덤프 파일이 크면 복제 서버와 동일한 지역 내에서 Azure의 VM에 업로드 합니다. VM에서 Azure Database for MariaDB 서버로 복원 합니다.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>원본 서버와 복제 서버를 연결 하 여 시작 입력 데이터 복제

1. 원본 서버를 설정 합니다.

   모든 데이터 내부 복제 기능은 저장 프로시저에 의해 수행됩니다. [데이터 내부 복제 저장 프로시저](reference-data-in-stored-procedures.md)에서 모든 프로시저를 확인할 수 있습니다. 저장 프로시저는 MySQL 셸 또는 MySQL 워크 벤치에서 실행할 수 있습니다.

   두 서버를 연결 하 고 복제를 시작 하려면 Azure DB for MariaDB 서비스에서 대상 복제본 서버에 로그인 합니다. 그런 다음, `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` Azure DB For MariaDB 서버에서 또는 저장 프로시저를 사용 하 여 외부 인스턴스를 원본 서버로 설정 합니다.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   또는
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: 원본 서버의 호스트 이름
   - master_user: 원본 서버에 대 한 사용자 이름
   - master_password: 원본 서버의 암호
   - master_log_file: 실행 중인 `show master status`의 이진 로그 파일 이름
   - master_log_pos: 실행 중인 `show master status`의 이진 로그 위치
   - master_gtid_pos: GTID position 실행 중 `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: CA 인증서의 컨텍스트입니다. SSL을 사용 하지 않는 경우 빈 문자열을 전달 합니다. *
    
    
    * Master_ssl_ca 매개 변수를 변수로 전달 하는 것이 좋습니다. 자세한 내용은 다음 예를 참조하세요.

   **예**

   - SSL을 사용한 복제

       `@cert`다음 명령을 실행 하 여 변수를 만듭니다.

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       SSL로 복제는 도메인 companya.com에서 호스트 되는 원본 서버와 Azure Database for MariaDB에서 호스트 되는 복제본 서버 간에 설정 됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - SSL 없이 복제

       SSL을 사용 하지 않는 복제는 도메인 companya.com에서 호스트 되는 원본 서버와 Azure Database for MariaDB에서 호스트 되는 복제본 서버 간에 설정 됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. 복제를 시작 합니다.

   `mysql.az_replication_start`저장 프로시저를 호출 하 여 복제를 시작 합니다.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 복제 상태를 확인 합니다.

   [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/)복제 서버에서 명령을 호출 하 여 복제 상태를 확인 합니다.
    
   ```sql
   show slave status;
   ```

   `Slave_IO_Running`및가 `Slave_SQL_Running` 상태이 `yes` 고 값 `Seconds_Behind_Master` 이 이면 `0` 복제가 작동 하는 것입니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이가 아니면 `0` 복제본이 업데이트를 처리 하는 것입니다.

4. 해당 서버 변수를 업데이트 하 여 데이터 인 복제를 안전 하 게 만듭니다 (GTID를 사용 하지 않는 복제에만 필요).
    
    MariaDB의 기본 복제 제한으로 인해  [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) GTID 시나리오를 제외 하 고 복제 시 및 변수를 설정 해야 합니다 [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) .

    슬레이브 서버의 `sync_master_info` 및 변수를 확인 `sync_relay_log_info` 하 여 데이터 인 복제가 안정적인 지 확인 하 고 변수를로 설정 `1` 합니다.
    
## <a name="other-stored-procedures"></a>기타 저장 프로시저

### <a name="stop-replication"></a>복제 중지

원본 서버와 복제 서버 간의 복제를 중지 하려면 다음 저장 프로시저를 사용 합니다.

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>복제 관계 제거

원본 서버와 복제 서버 간의 관계를 제거 하려면 다음 저장 프로시저를 사용 합니다.

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>복제 오류 건너뛰기

복제 오류를 건너뛰고 복제를 허용 하려면 다음 저장 프로시저를 사용 합니다.
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>다음 단계
Azure Database for MariaDB를 위한 [입력 데이터 복제](concepts-data-in-replication.md)에 대해 자세히 알아보세요.
