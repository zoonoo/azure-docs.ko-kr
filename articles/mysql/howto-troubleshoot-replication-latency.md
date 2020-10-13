---
title: 복제 대기 시간 문제 해결-Azure Database for MySQL
description: Azure Database for MySQL 읽기 복제본을 사용 하 여 복제 대기 시간 문제를 해결 하는 방법을 알아봅니다.
keywords: mysql, 문제 해결, 복제 대기 시간 (초)
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877109"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Azure Database for MySQL의 복제 대기 시간 문제 해결

[복제본 읽기](concepts-read-replicas.md) 기능을 사용 하면 Azure Database for MySQL 서버에서 읽기 전용 복제 서버로 데이터를 복제할 수 있습니다. 읽기 복제본은 읽기/보고 쿼리를 응용 프로그램에서 복제 서버로 라우팅하는 방법으로 작업을 확장 하는 데 사용 됩니다. 이렇게 하면 주 서버의 압력을 줄이고 응용 프로그램 크기를 조정할 때 응용 프로그램의 전반적인 성능 및 대기 시간을 향상 시킵니다. 복제본은 MySQL 엔진의 네이티브 이진 로그(binlog) 파일의 위치 기반 복제 기술을 사용하여 비동기식으로 업데이트됩니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다. 

보조 읽기 복제본의 복제 지연 시간은 다음을 포함 하지만이에 제한 되지 않는 요소 수에 따라 달라 집니다. 

- 네트워크 대기 시간
- 원본 서버의 트랜잭션 볼륨
- 원본 및 보조 읽기 복제본 서버의 계산 계층
- 주 서버와 보조 서버에서 실행 되는 쿼리입니다. 

이 문서에서는 Azure Database for MySQL의 복제 대기 시간 문제를 해결 하는 방법을 설명 합니다. 또한 복제본 서버에서 복제 대기 시간이 증가 하는 일반적인 원인 중 일부를 이해 합니다.

## <a name="replication-concepts"></a>복제 개념

이진 로그를 사용 하도록 설정 하면 원본 서버가 커밋된 트랜잭션을 복제에 사용 되는 이진 로그에 씁니다. 이진 로그는 최대 16TB의 저장소를 지 원하는 새로 프로 비전 된 모든 서버에 대해 기본적으로 설정 되어 있습니다. 복제 서버에는 복제 서버당 실행 되는 두 개의 스레드가 있습니다. 하나는 IO 스레드이 고 다른 하나는 SQL 스레드 라고 합니다.

- **IO 스레드** 는 원본 서버에 연결 되 고 업데이트 된 이진 로그를 요청 합니다. 이 스레드가 이진 로그 업데이트를 받은 후에는 복제본 서버에 릴레이 로그 라는 로컬 로그에 저장 됩니다.
- **SQL 스레드** 는 릴레이 로그를 읽고 복제 서버에 데이터 변경 내용을 적용 합니다.

## <a name="monitoring-replication-latency"></a>복제 대기 시간 모니터링

Azure Database for MySQL은 [Azure Monitor](concepts-monitoring.md)에서 복제 지연 시간 (초)을 제공 합니다. 이 메트릭은 읽기 복제본 서버 에서만 사용할 수 있습니다. 이 메트릭은 MySQL에서 사용할 수 있는 seconds_behind_master 메트릭을 사용 하 여 계산 됩니다. 복제 대기 시간이 늘어나는 근본 원인을 이해 하려면 [MySQL 워크 벤치](connect-workbench.md) 또는 [Azure Cloud shell](https://shell.azure.com) 을 사용 하 여 복제 서버에 연결 하 고 다음 명령을 실행 합니다.

 값을 실제 복제 서버 이름 및 관리 사용자 로그인 이름으로 바꿉니다. 관리자 사용자 이름은 Azure Database for MySQL에 대해 ' @ '가 필요 합니다 \<servername> .

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Cloud Shell 터미널에서의 환경은 다음과 같습니다.
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  동일한 Azure Cloud Shell 터미널에서 다음 명령을 실행 합니다.

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  일반적인 출력은 다음과 같습니다.
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="복제 대기 시간 모니터링&quot;:::


출력에 많은 정보가 포함 되어 있지만 일반적으로 다음 열에 집중 하는 것이 중요 합니다.

|메트릭|설명|
|---|---|
|Slave_IO_State| IO 스레드의 현재 상태입니다. 일반적으로 상태는 동기화 하는 경우 &quot;마스터가 이벤트를 보낼 때까지 기다리는 중&quot;입니다. 그러나 &quot;마스터에 연결&quot;과 같은 상태가 표시 되 면 복제본이 마스터 서버와의 연결이 끊어졌습니다. 마스터가 실행 중인지 또는 방화벽이 연결을 차단 하 고 있는지 확인 합니다.|
|Master_Log_File| 마스터가 쓰고 있는 이진 로그 파일입니다.|
|Read_Master_Log_Pos| 마스터가 쓰고 있는 위의 이진 로그 파일의 위치를 나타냅니다.|
|Relay_Master_Log_File| 표시 됨은 복제본 서버가 마스터에서 읽고 있는 이진 로그 파일을 나타냅니다.|
|Slave_IO_Running| IO 스레드가 실행 중인지 여부를 나타냅니다. &quot;Yes&quot; 여야 합니다. &quot;NO&quot; 인 경우 복제가 손상 될 가능성이 높습니다.|
|Slave_SQL_Running| SQL 스레드가 실행 중인지 여부를 나타냅니다. &quot;Yes&quot; 여야 합니다. &quot;NO" 인 경우 복제가 손상 될 가능성이 높습니다.|
|Exec_Master_Log_Pos| 복제본이 적용 되는 Relay_Master_Log_File의 위치를 표시 합니다. 대기 시간이 있으면이 위치 시퀀스는 Read_Master_Log_Pos 보다 작아야 합니다.|
|Relay_Log_Space|릴레이 로그 크기의 상한 값을 표시 합니다. "Relay_log_space_limit"와 같은 전역 변수 표시를 쿼리하여 크기를 확인할 수 있습니다.|
|Seconds_Behind_Master| 복제 대기 시간 (초)을 표시 합니다.|
|Last_IO_Errno|IO 스레드 오류 코드 (있는 경우)를 표시 합니다. 이러한 코드에 대 한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)를 참조 하세요.|
|Last_IO_Error| IO 스레드 오류 메시지 (있는 경우)를 표시 합니다.|
|Last_SQL_Errno|SQL 스레드 오류 코드 (있는 경우)를 표시 합니다. 이러한 코드에 대 한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)를 참조 하세요.|
|Last_SQL_Error|SQL 스레드 오류 메시지 (있는 경우)를 표시 합니다.|
|Slave_SQL_Running_State| 현재 SQL 스레드 상태를 나타냅니다. 이 상태에서 표시 되는 "시스템 잠금"은 일반적인 동작입니다. "종속 트랜잭션을 커밋할 때까지 기다리는 중"으로 상태를 확인 하는 것이 정상입니다. 이는 복제본이 마스터에서 커밋된 트랜잭션을 업데이트할 때까지 대기 중임을 나타냅니다.|

Slave_IO_Running이 예이 고 Slave_SQL_Running가 예 이면 복제가 제대로 실행 되 고 있는 것입니다. 

다음으로 Last_IO_Errno, Last_IO_Error Last_SQL_Errno 및 Last_SQL_Error를 확인 해야 합니다.  이러한 필드에는 SQL 스레드를 중지 시킨 가장 최근의 오류의 오류 번호 및 오류 메시지가 포함 됩니다. 오류 번호 0 및 빈 메시지는 오류가 없음을 의미 합니다. 오류에서 0이 아닌 값은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)에서 오류 코드를 조회 하 여 자세히 조사 해야 합니다.

## <a name="common-scenarios-for-high-replication-latency"></a>높은 복제 대기 시간에 대 한 일반적인 시나리오

### <a name="network-latency-or-high-cpu-on-source-server"></a>원본 서버의 네트워크 대기 시간 또는 높은 cpu

다음 값을 관찰 하는 경우 복제 대기 시간의 가장 일반적인 원인은 네트워크 대기 시간이 많거나 원본 서버에서 cpu 사용량이 많은 경우입니다. 이 경우 IO 스레드가 실행 중이 고 마스터에서 대기 중입니다. Master (원본 서버)는 이진 로그 파일 #20에 이미 기록 되었지만 복제본은 파일 #10 까지만 수신 했습니다. 이 시나리오에서 높은 복제 대기 시간에 영향을 주는 주요 요인은 원본 서버의 네트워크 속도 또는 cpu 사용률이 높습니다.  Azure에서 지역 내의 네트워크 대기 시간은 일반적으로 시간 (밀리초)이 고 지역 간에는 몇 초까지 걸릴 수 있습니다. 대부분의 경우 원본 서버에 대 한 IO 스레드의 지연으로 인해 IO 스레드 처리가 느려지는 원인이 되는 원본 서버의 cpu 사용률이 높아질 수 있습니다. Cpu 사용률을 모니터링 하 고 Azure monitor를 사용 하 여 원본 서버에서 동시 연결 수를 관찰 하 여이를 감지할 수 있습니다.

원본 서버에서 cpu 사용률이 높은 경우 네트워크 대기 시간이 원인일 수 있습니다. 비정상적으로 발생 하는 네트워크 대기 시간이 비정상적으로 많은 경우에는 [Azure 상태 페이지](https://status.azure.com/status) 를 확인 하 여 알려진 문제나 중단이 발생 하지 않도록 하는 것이 좋습니다. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>원본 서버에서 많은 양의 트랜잭션 버스트

다음 값을 관찰 하는 경우 복제 대기 시간의 가장 일반적인 원인은 원본 서버에서 트랜잭션 급증으로 인 한 것입니다. 아래 출력에서 복제본이 마스터 뒤의 이진 로그를 검색할 수 있지만 복제본 IO 스레드는 릴레이 로그 공간이 이미 꽉 차는 것을 나타냅니다. 따라서 복제본이 이미 최대한 빠르게 처리 하려고 했으므로 네트워크 속도에서 지연이 발생 하지 않습니다. 대신 업데이트 된 이진 로그 크기가 릴레이 로그 공간의 상한 값을 초과 합니다. 이 문제를 해결 하기 위해 마스터 서버에서 [느리게 쿼리 로그](concepts-server-logs.md) 를 사용 하도록 설정 해야 합니다. 저속 쿼리 로그를 사용 하면 원본 서버에서 장기 실행 트랜잭션을 식별할 수 있습니다. 서버의 대기 시간을 줄이기 위해 식별 된 쿼리를 조정 해야 합니다. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

이 범주에 있는 대기 시간의 일반적인 원인은 다음과 같습니다.

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>원본 서버의 데이터 로드로 인 한 복제 대기 시간
경우에 따라 원본 서버에 매주 또는 매월 데이터 로드가 있습니다. 그러나이 경우에는 복제 대기 시간이 피할 수 없습니다. 이 시나리오에서는 원본 서버의 데이터 로드가 완료 된 후 복제본 서버가 성공적으로 처리 됩니다.


### <a name="slowness-on-the-replica-server"></a>복제 서버의 속도 저하

다음 값을 확인 하는 경우 가장 일반적인 원인은 추가 조사가 필요한 복제 서버에서 문제가 될 수 있습니다. 이 시나리오에서 출력에 표시 된 것 처럼 IO와 SQL 스레드 모두 제대로 실행 되 고 복제본이 마스터 쓰기와 동일한 이진 로그 파일을 읽습니다. 그러나 복제본 서버에서 원본 서버와 동일한 트랜잭션을 반영 하기 위해 몇 가지 대기 시간이 발생 합니다. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

이 범주에 있는 대기 시간의 일반적인 원인은 다음과 같습니다.

#### <a name="no-primary-or-unique-key-on-a-table"></a>테이블에 기본 키 또는 고유 키가 없습니다.

Azure Database for MySQL은 행 기반 복제를 사용 합니다. 행 기반 복제를 사용 하는 경우 마스터 서버는 개별 테이블 행의 변경 내용에 대 한 이진 로그에 이벤트를 기록 합니다. SQL 스레드는 복제 서버의 해당 테이블 행에 대 한 변경 내용을 실행 합니다. 테이블의 기본 키 또는 고유 키가 복제 대기 시간의 일반적인 원인 중 하나입니다. 기본 키 또는 고유 키가 없으면 SQL 스레드에서 대상 테이블의 모든 행을 검사 하 여 변경 내용을 적용 합니다.

MySQL에서 기본 키는 NULL 값을 포함할 수 없으므로 빠른 쿼리 성능을 보장 하는 연결 된 인덱스입니다. InnoDB 저장소 엔진을 사용 하 여 테이블 데이터는 매우 빠른 조회를 수행 하 고 기본 키를 기준으로 정렬 되도록 물리적으로 구성 됩니다. 따라서 복제본 서버를 만들기 전에 원본 서버의 테이블에 기본 키를 추가 하는 것이 좋습니다. 이 시나리오에서는 복제 대기 시간을 개선 하기 위해 원본 서버에 기본 키를 추가 하 고 읽기 복제본을 다시 만들어야 합니다.

다음 쿼리를 사용 하 여 원본 서버에서 기본 키가 없는 테이블을 확인할 수 있습니다.

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>복제본 서버에서 장기 실행 쿼리로 인 한 복제 대기 시간

복제본 서버의 워크 로드가 IO 스레드에 대해 SQL 스레드를 유지 하지 못할 수 있습니다. 이는 복제 서버에 장기 실행 쿼리가 있는 경우 복제 대기 시간이 높은 일반적인 원인 중 하나입니다. 이 경우 문제 해결에 도움이 되도록 복제 서버에서 [저속 쿼리 로그](concepts-server-logs.md) 를 사용 하도록 설정 해야 합니다. 쿼리 속도가 느리면 리소스 사용 제한을 증가 하거나 서버 속도가 느려질 수 있으므로 복제본은 마스터를 사용 하 여 파악할 수 없습니다. 이 시나리오에서는 느리게 쿼리를 튜닝 해야 합니다. 쿼리 속도가 빠를수록 SQL 스레드 차단이 방지 되 고 복제 대기 시간이 크게 향상 됩니다.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>원본 서버에서 DDL 쿼리로 인 한 복제 대기 시간
원본 서버에서 실행 되는 [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) 과 같은 장기 실행 DDL 명령이 있는 경우 실행 하는 데 1 시간이 걸렸습니다. 이 시간 동안에는 원본 서버에서 병렬로 실행 되는 수천 개의 쿼리가 있을 수 있습니다. DDL이 복제본에 복제 되 면 데이터베이스의 일관성을 유지 하기 위해 MySQL 엔진은 단일 복제 스레드에서 DDL을 실행 해야 합니다. 따라서 복제 된 다른 모든 쿼리는 차단 되며 복제본 서버에서 DDL 작업이 완료 될 때까지 한 시간 이상 기다려야 합니다. 이는 온라인 DDL 작업에 관계 없이 적용 됩니다. DDL 작업을 사용 하면 복제에서 복제 대기 시간이 증가 하는 것으로 예상 됩니다.

원본 서버에서 [느리게 쿼리 로그](concepts-server-logs.md) 를 사용 하도록 설정한 경우 저속 쿼리 로그를 보고 원본 서버에서 DDL 명령이 실행 되었는지 확인 하 여이 시나리오를 검색할 수 있습니다. 인덱스 삭제, 이름 바꾸기 및 만들기는 ALTER TABLE에 내부 알고리즘을 사용 해야 합니다. 테이블 데이터를 복사 하 고 테이블을 다시 작성 해야 할 수 있습니다. 일반적으로 내부 알고리즘의 경우 동시 DML이 지원 되지만 작업 준비 및 실행 단계에서 테이블에 대 한 배타적 메타 데이터 잠금이 잠시 수행 될 수 있습니다. 이와 같이 CREATE INDEX 문의 경우 절 알고리즘과 잠금을 사용 하 여 읽기 및 쓰기를 위해 테이블 복사 방법 및 동시성 수준에 영향을 줄 수 있지만 전체 텍스트 또는 공간 인덱스를 추가 하는 경우에도 DML 작업을 방지할 수 있습니다. 알고리즘 및 잠금 절을 사용 하 여 인덱스를 만드는 예는 아래를 참조 하세요.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

불행 하 게 잠금이 필요한 DDL 문의 경우 복제 대기 시간을 피할 수 없습니다. 대신 이러한 유형의 DDL 작업은 야간 중에 발생 하는 경우에 대비 하 여 사용량이 많은 시간에 수행 되어야 합니다.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>복제 서버 낮은 SKU로 인 한 복제 대기 시간

Azure Database for MySQL 읽기 복제본은 마스터 서버와 동일한 서버 구성으로 만들어집니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 그러나 복제본 서버를 다운 그레이드 하는 경우 작업으로 인해 리소스 사용량이 증가 하 여 복제 대기 시간이 길어질 수 있습니다. 이는 Azure Monitor에서 복제 서버의 CPU 및 메모리 사용량을 모니터링 하 여 관찰할 수 있습니다. 이 시나리오에서는 복제본이 마스터를 유지할 수 있도록 복제본 서버 구성을 원본과 같거나 큰 값으로 유지 하는 것이 좋습니다.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>원본 서버에서 서버 매개 변수 튜닝을 사용 하 여 복제 대기 시간 향상

Azure Database for MySQL에서 복제는 기본적으로 복제본의 병렬 스레드를 사용 하 여 실행 되도록 최적화 됩니다. 복제본 서버가 catch 하지 못하는 원본 서버의 동시성 워크 로드의 경우 원본 서버에서 매개 변수 binlog_group_commit_sync_delay를 구성 하 여 복제 대기 시간을 높일 수 있습니다. 이 매개 변수는 이진 로그 파일을 동기화 하기 전에 이진 로그 커밋이 대기 하는 마이크로초 수를 제어 합니다. 이는 모든 트랜잭션을 커밋한 직후에 적용 하는 대신 마스터에서 이진 로그 업데이트를 대량으로 전송 한다는 것입니다. 이렇게 하면 복제본의 IO가 줄어들고 성능이 향상 됩니다. 이 시나리오에서는 binlog_group_commit_sync_delay를 1000로 설정 하 고 복제 대기 시간을 모니터링 하는 것이 유용할 수 있습니다. 이 매개 변수는 매우 신중 하 게 설정 하 고 높은 동시 작업에만 활용 해야 합니다. 단일 트랜잭션이 많은 동시성 시나리오의 경우 IO 스레드가 대량 이진 로그 업데이트를 대기 하는 동안 몇 개의 트랜잭션만 커밋할 수 있으므로 대기 시간에 binlog_group_commit_sync_delay를 설정할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)에 대해 자세히 알아보세요.
