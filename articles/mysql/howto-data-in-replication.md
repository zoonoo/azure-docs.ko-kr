---
title: 데이터에서 복제 구성-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에 대해 데이터 내부 복제를 설정하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: c3a6f9b5831d4fed377d3f8702dbc0af0663b3a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91596498"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL 데이터 내부 복제를 구성하는 방법

이 문서에서는 원본 및 복제 서버를 구성 하 여 Azure Database for MySQL에서 [입력 데이터 복제](concepts-data-in-replication.md) 를 설정 하는 방법을 설명 합니다. 이 문서에서는 사용자에 게 MySQL 서버 및 데이터베이스를 사용 하는 이전 경험이 있다고 가정 합니다.

> [!NOTE]
> 바이어스 없는 통신
>
> Microsoft는 다양 한 inclusionary 환경을 지원 합니다. 이 문서에는 word _슬레이브_에 대 한 참조가 포함 되어 있습니다. [바이어스 없는 통신을 위한 Microsoft 스타일 가이드](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) 는이를 exclusionary 단어로 인식 합니다. 이 문서는 현재 소프트웨어에 표시 되는 단어 이므로 일관성을 위해 사용 됩니다. 소프트웨어를 업데이트 하 여 단어를 제거 하면이 문서는 맞춤으로 업데이트 됩니다.
>

Azure Database for MySQL 서비스에서 복제본을 만들기 위해 [입력 데이터 복제](concepts-data-in-replication.md)  원본 MySQL 서버 온-프레미스, vm (가상 머신) 또는 클라우드 데이터베이스 서비스에서 데이터를 동기화 합니다. 내부 데이터 복제는 MySQL에 네이티브인 이진 로그(binlog) 파일 위치 기반 복제를 기반으로 합니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다.

이 문서의 단계를 수행 하기 전에 데이터 복제의 [제한 사항 및 요구 사항을](concepts-data-in-replication.md#limitations-and-considerations) 검토 합니다.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>복제본으로 사용할 MySQL 서버 만들기

1. 새 Azure Database for MySQL 서버 만들기

   새 MySQL 서버(예: “replica.mysql.database.azure.com”)를 만듭니다. 서버를 만드는 방법은 [Azure Portal을 사용하여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md)를 참조하세요. 이 서버는 데이터 내부 복제의 “복제본” 서버입니다.

   > [!IMPORTANT]
   > Azure Database for MySQL 서버는 범용 또는 메모리 최적화 가격 책정 계층에 생성되어야 합니다.
   > 

1. 동일한 사용자 계정 및 해당 권한 만들기

   사용자 계정은 원본 서버에서 복제 서버로 복제 되지 않습니다. 복제본 서버에 대한 액세스 권한을 사용자에게 제공하려는 경우, 새로 만든 이 Azure Database for MySQL 서버에서 모든 계정과 해당 권한을 수동으로 만들어야 합니다.

1. 원본 서버의 IP 주소를 복제본의 방화벽 규칙에 추가 합니다. 

   [Azure Portal](howto-manage-firewall-using-portal.md) 또는 [Azure CLI](howto-manage-firewall-using-cli.md)를 사용하여 방화벽 규칙을 업데이트합니다.

## <a name="configure-the-source-server"></a>원본 서버 구성
다음 단계에서는 데이터 내부 복제를 위해 다른 클라우드 공급자가 호스팅하는 가상 머신 또는 데이터베이스 서비스에서 온-프레미스 호스팅 MySQL 서버를 준비하고 구성합니다. 이 서버는 데이터 내부 복제의 “마스터” 서버입니다.


1. 계속 하기 전에 [마스터 서버 요구 사항을](concepts-data-in-replication.md#requirements) 검토 하십시오. 

2. 원본 서버에서 포트 3306에 대 한 인바운드 및 아웃 바운드 트래픽을 모두 허용 하는지 확인 하 **고, DNS가 공개적으로 액세스할**수 있거나 FQDN (정규화 된 도메인 이름)이 있는지 확인 합니다. 
   
   다른 컴퓨터에서 호스트 되는 MySQL 명령줄 또는 Azure Portal에서 사용할 수 있는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 와 같은 도구에서 연결을 시도 하 여 원본 서버에 대 한 연결을 테스트 합니다.

   조직에서 엄격한 보안 정책을 사용 하 고 원본 서버의 모든 IP 주소가 Azure에서 원본 서버로의 통신을 사용 하도록 허용 하지 않는 경우, 아래 명령을 사용 하 여 MySQL 서버의 IP 주소를 확인할 수 있습니다.

   1. MySQL 명령줄과 같은 도구를 사용 하 여 Azure Database for MySQL에 로그인 합니다.
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
   
1. 이진 로깅 켜기

   다음 명령을 실행 하 여 원본에서 이진 로깅이 사용 하도록 설정 되었는지 확인 합니다. 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin)"ON" 값을 사용 하 여 변수를 반환 하는 경우에는 서버에서 이진 로깅이 사용 됩니다. 

   `log_bin`값이 "OFF"로 반환 되는 경우 my.cnf 파일을 편집 하 여 이진 로깅을 설정 하 `log_bin=ON` 고 변경 내용을 적용 하려면 서버를 다시 시작 합니다.

1. 원본 서버 설정

   입력 데이터 복제 `lower_case_table_names` 원본 서버와 복제 서버 간에 매개 변수가 일치 해야 합니다. Azure Database for MySQL에서 이 매개 변수는 기본적으로 1입니다. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

1. 새 복제 역할 만들기 및 권한 설정

   복제 권한으로 구성 된 원본 서버에서 사용자 계정을 만듭니다. 이 작업은 MySQL Workbench와 같은 도구 또는 SQL 명령을 통해 수행할 수 있습니다. 사용자를 만들 때 지정해야 하므로 SSL을 사용하여 복제할지 여부를 고려합니다. 원본 서버에서 [사용자 계정을 추가](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) 하는 방법을 이해 하려면 MySQL 설명서를 참조 하세요. 

   아래 명령에서 새로 만든 복제 역할은 원본 자체를 호스트 하는 컴퓨터 뿐 아니라 모든 컴퓨터에서 원본에 액세스할 수 있습니다. 이 작업은 create user 명령에 “syncuser@’%’”를 지정하여 수행합니다. [계정 이름 지정](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)에 대한 자세한 내용은 MySQL 설명서를 참조하세요.

   **SQL 명령**

   *SSL을 사용한 복제*

   모든 사용자 연결에 SSL이 필요하도록 설정하려면 다음 명령을 사용하여 사용자를 만듭니다. 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *SSL 없이 복제*

   일부 연결에 SSL이 필요하지 않은 경우 다음 명령을 사용하여 사용자를 만듭니다.

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   MySQL Workbench에서 복제 역할을 만들려면 **관리** 패널에서 **사용자 및 권한** 패널을 엽니다. 그런 다음, **계정 추가**를 클릭합니다. 
 
   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="사용자 및 권한":::

   **로그인 이름** 필드에 사용자 이름을 입력합니다. 

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="사용자 및 권한":::
 
   **관리 역할** 패널을 클릭하고 **전역 권한** 목록에서 **복제 슬레이브**를 선택합니다. 그런 다음, **적용** 을 클릭하여 복제 역할을 만듭니다.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="사용자 및 권한":::

1. 원본 서버를 읽기 전용 모드로 설정

   데이터베이스를 덤프하기 전에 서버를 읽기 전용 모드로 설정해야 합니다. 읽기 전용 모드에서는 원본에서 쓰기 트랜잭션을 처리할 수 없습니다. 비즈니스에 미치는 영향을 평가하고, 필요한 경우 사용량이 적은 시간에 읽기 전용 창을 예약합니다.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

1. 이진 로그 파일 이름 및 오프셋 가져오기

   명령을 실행 [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) 하 여 현재 이진 로그 파일 이름과 오프셋을 확인 합니다.
    
   ```sql
    show master status;
   ```
   결과는 다음과 같아야 합니다. 이후 단계에서 사용되므로 이진 파일 이름을 적어 두세요.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="사용자 및 권한":::
 
## <a name="dump-and-restore-source-server"></a>원본 서버 덤프 및 복원

1. Azure Database for MySQL에 복제 하려는 데이터베이스 및 테이블을 결정 하 고 원본 서버에서 덤프를 수행 합니다.
 
    mysqldump를 사용하여 마스터 서버에서 데이터베이스를 덤프할 수 있습니다. 자세한 내용은 [덤프 및 복원](concepts-migrate-dump-restore.md)을 참조하세요. MySQL 라이브러리 및 테스트 라이브러리는 덤프할 필요가 없습니다.

1. 원본 서버를 읽기/쓰기 모드로 설정

   데이터베이스가 덤프 된 후 원본 MySQL 서버를 읽기/쓰기 모드로 다시 변경 합니다.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

1. 덤프 파일을 새 서버로 복원

   덤프 파일을 Azure Database for MySQL 서비스에서 생성된 서버로 복원합니다. 덤프 파일을 MySQL 서버로 복원하는 방법은 [덤프 및 복원](concepts-migrate-dump-restore.md)을 참조하세요. 덤프 파일이 큰 경우, 복제본 서버와 동일한 지역 내의 Azure 가상 머신에 업로드합니다. 가상 머신에서 Azure Database for MySQL 서버로 복원합니다.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>원본 및 복제본 서버를 시작 입력 데이터 복제에 연결

1. 원본 서버 설정

   모든 데이터 내부 복제 기능은 저장 프로시저에 의해 수행됩니다. [데이터 내부 복제 저장 프로시저](reference-data-in-stored-procedures.md)에서 모든 프로시저를 확인할 수 있습니다. 저장 프로시저는 MySQL 셸 또는 MySQL Workbench에서 실행할 수 있습니다. 

   두 서버를 연결 하 고 복제를 시작 하려면 MySQL 용 Azure DB 서비스의 대상 복제본 서버에 로그인 하 고 외부 인스턴스를 원본 서버로 설정 합니다. 이 작업은 Azure DB for MySQL 서버의 `mysql.az_replication_change_master` 저장 프로시저를 사용하여 수행합니다.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: 원본 서버의 호스트 이름
   - master_user: 원본 서버에 대 한 사용자 이름
   - master_password: 원본 서버의 암호
   - master_log_file: 실행 중인 `show master status`의 이진 로그 파일 이름
   - master_log_pos: 실행 중인 `show master status`의 이진 로그 위치
   - master_ssl_ca: CA 인증서의 컨텍스트입니다. SSL을 사용하지 않는 경우 빈 문자열을 전달합니다.
       - 이 매개 변수를 변수로 전달하는 것이 좋습니다. 자세한 내용은 다음 예제를 참조하세요.

   > [!NOTE]
   > 원본 서버가 Azure VM에서 호스트 되는 경우 "Azure 서비스에 대 한 액세스 허용"을 "설정"으로 설정 하 여 원본 및 복제본 서버가 서로 통신할 수 있도록 합니다. 이 설정은 **연결 보안** 옵션에서 변경할 수 있습니다. 자세한 내용은 [포털을 사용하여 방화벽 규칙 관리](howto-manage-firewall-using-portal.md)를 참조하세요.
      
   **예**
   
   *SSL을 사용한 복제*
   
   `@cert` 변수는 다음 MySQL 명령을 실행하여 만듭니다. 
   
      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```
   
   SSL로 복제는 "companya.com" 도메인에 호스트 된 원본 서버와 Azure Database for MySQL에서 호스트 되는 복제본 서버 간에 설정 됩니다. 이 저장 프로시저는 복제본에서 실행됩니다. 
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```
   *SSL 없이 복제*
   
   SSL을 사용 하지 않는 복제는 "companya.com" 도메인에 호스트 된 원본 서버와 Azure Database for MySQL에서 호스트 되는 복제본 서버 간에 설정 됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

1. 필터링 
 
   Master에서 일부 테이블 복제를 건너뛰려면 `replicate_wild_ignore_table` 복제본 서버에서 서버 매개 변수를 업데이트 합니다. 쉼표로 구분 된 목록을 사용 하 여 둘 이상의 테이블 패턴을 제공할 수 있습니다.

   이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table)를 참조하세요. 
    
   매개 변수를 업데이트 하려면 [Azure Portal](howto-server-parameters.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md)를 사용할 수 있습니다.

1. 복제 시작

   `mysql.az_replication_start` 저장 프로시저를 호출하여 복제를 시작합니다.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. 복제 상태 확인

   [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html)복제 서버에서 명령을 호출 하 여 복제 상태를 확인 합니다.
    
   ```sql
   show slave status;
   ```

   및의 상태가 `Slave_IO_Running` `Slave_SQL_Running` "yes"이 고 값 `Seconds_Behind_Master` 이 "0" 이면 복제가 정상적으로 작동 합니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이 “0”이 아니면 복제본이 업데이트를 처리 중인 것입니다. 

## <a name="other-stored-procedures"></a>기타 저장 프로시저

### <a name="stop-replication"></a>복제 중지

원본 서버와 복제 서버 간의 복제를 중지 하려면 다음 저장 프로시저를 사용 합니다.

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>복제 관계 제거

원본 서버와 복제 서버 간의 관계를 제거 하려면 다음 저장 프로시저를 사용 합니다.

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>복제 오류 건너뛰기

복제 오류를 건너뛰고 복제를 계속 진행하려면 다음 저장 프로시저를 사용합니다.
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>다음 단계
- Azure Database for MySQL를 위한 [데이터 내부 복제](concepts-data-in-replication.md)에 대해 자세히 알아보세요. 
