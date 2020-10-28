---
title: 복제 대기 시간 문제 해결-Azure Database for MySQL
description: Azure Database for MySQL 읽기 복제본을 사용 하 여 복제 대기 시간 문제를 해결 하는 방법에 대해 알아봅니다.
keywords: mysql, 문제 해결, 복제 대기 시간 (초)
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/25/2020
ms.openlocfilehash: af82b9e2feee3e03d2a0703d771c68b67ddd08c9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791582"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Azure Database for MySQL의 복제 지연 문제 해결

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

[복제본 읽기](concepts-read-replicas.md) 기능을 사용 하면 Azure Database for MySQL 서버에서 읽기 전용 복제 서버로 데이터를 복제할 수 있습니다. 응용 프로그램에서 복제 서버로 읽기 및 보고 쿼리를 라우팅하여 워크 로드를 확장할 수 있습니다. 이 설정은 원본 서버의 압력을 줄입니다. 또한 확장 될 때 응용 프로그램의 전반적인 성능 및 대기 시간을 향상 시킵니다. 

복제본은 MySQL 엔진의 기본 이진 로그 (binlog) 파일 위치 기반 복제 기술을 사용 하 여 비동기적으로 업데이트 됩니다. 자세한 내용은 [MySQL binlog 파일 위치 기반 복제 구성 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조 하세요. 

보조 읽기 복제본의 복제 지연 시간은 여러 요소에 따라 다릅니다. 이러한 요소에는 다음이 포함 되지만이에 국한 되지 않습니다. 

- 네트워크 대기 시간.
- 원본 서버의 트랜잭션 볼륨입니다.
- 원본 서버와 보조 읽기 복제본 서버의 Compute 계층입니다.
- 원본 서버와 보조 서버에서 실행 되는 쿼리입니다. 

이 문서에서는 Azure Database for MySQL의 복제 대기 시간 문제를 해결 하는 방법을 알아봅니다. 또한 복제 서버에서 복제 대기 시간이 증가 하는 몇 가지 일반적인 원인을 이해 하 게 됩니다.

## <a name="replication-concepts"></a>복제 개념

이진 로그를 사용 하도록 설정 하면 원본 서버가 커밋된 트랜잭션을 이진 로그에 기록 합니다. 이진 로그는 복제에 사용 됩니다. 최대 16TB의 저장소를 지 원하는 새로 프로 비전 된 모든 서버에 대해 기본적으로 설정 되어 있습니다. 복제 서버에서는 두 개의 스레드가 각 복제 서버에서 실행 됩니다. 한 스레드는 *IO 스레드* 이 고 다른 하나는 *SQL 스레드* 입니다.

- IO 스레드는 원본 서버에 연결 되 고 업데이트 된 이진 로그를 요청 합니다. 이 스레드는 이진 로그 업데이트를 수신 합니다. 이러한 업데이트는 복제 서버, *릴레이 로그* 라는 로컬 로그에 저장 됩니다.
- SQL 스레드는 릴레이 로그를 읽은 다음 복제본 서버에 데이터 변경 내용을 적용 합니다.

## <a name="monitoring-replication-latency"></a>복제 대기 시간 모니터링

Azure Database for MySQL은 [Azure Monitor](concepts-monitoring.md)에서 복제 지연 (초)의 메트릭을 제공 합니다. 이 메트릭은 읽기 복제 서버 에서만 사용할 수 있습니다. MySQL에서 사용할 수 있는 seconds_behind_master 메트릭으로 계산 됩니다. 

복제 대기 시간의 증가 원인을 이해 하려면 [MySQL 워크 벤치](connect-workbench.md) 또는 [Azure Cloud Shell](https://shell.azure.com)를 사용 하 여 복제 서버에 연결 합니다. 그런 후 다음 명령을 실행 합니다.

>[!NOTE] 
> 코드에서 예제 값을 복제 서버 이름 및 관리자 사용자 이름으로 바꿉니다. 관리자 사용자 이름은 `@\<servername>` Azure Database for MySQL 해야 합니다.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Cloud Shell 터미널의 환경은 다음과 같습니다.

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

동일한 Cloud Shell 터미널에서 다음 명령을 실행 합니다.

```
mysql> SHOW SLAVE STATUS;
```

일반적인 출력은 다음과 같습니다.
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="복제 대기 시간 모니터링":::


출력에 많은 정보가 포함 되어 있습니다. 일반적으로 다음 표에서 설명 하는 행에만 초점을 맞춰야 합니다.

|메트릭|Description|
|---|---|
|Slave_IO_State| IO 스레드의 현재 상태를 나타냅니다. 원본 (마스터) 서버를 동기화 하는 경우 일반적으로 상태는 "마스터에서 이벤트를 보낼 때까지 대기 중입니다."입니다. "마스터에 연결"과 같은 상태는 복제본이 원본 서버에 대 한 연결을 잃어버린 것을 나타냅니다. 원본 서버가 실행 중인지 확인 하거나 방화벽에서 연결을 차단 하 고 있는지 확인 하십시오.|
|Master_Log_File| 원본 서버에서 쓰고 있는 이진 로그 파일을 나타냅니다.|
|Read_Master_Log_Pos| 원본 서버에서 이진 로그 파일에 쓰는 위치를 나타냅니다.|
|Relay_Master_Log_File| 복제본 서버가 원본 서버에서 읽는 이진 로그 파일을 나타냅니다.|
|Slave_IO_Running| IO 스레드가 실행 중인지 여부를 나타냅니다. 값은 이어야 `Yes` 합니다. 값이 이면 `NO` 복제가 손상 된 것일 수 있습니다.|
|Slave_SQL_Running| SQL 스레드가 실행 중인지 여부를 나타냅니다. 값은 이어야 `Yes` 합니다. 값이 이면 `NO` 복제가 손상 된 것일 수 있습니다.|
|Exec_Master_Log_Pos| 복제본이 적용 되는 Relay_Master_Log_File의 위치를 나타냅니다. 대기 시간이 있으면이 위치 시퀀스는 Read_Master_Log_Pos 보다 작아야 합니다.|
|Relay_Log_Space|릴레이 로그 크기의 상한 값을 나타냅니다. 와 같이 쿼리 하 여 크기를 확인할 수 있습니다 `SHOW GLOBAL VARIABLES` `relay_log_space_limit` .|
|Seconds_Behind_Master| 복제 대기 시간 (초)을 표시 합니다.|
|Last_IO_Errno|IO 스레드 오류 코드 (있는 경우)를 표시 합니다. 이러한 코드에 대 한 자세한 내용은 [MySQL 서버 오류 메시지 참조](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)를 참조 하세요.|
|Last_IO_Error| IO 스레드 오류 메시지 (있는 경우)를 표시 합니다.|
|Last_SQL_Errno|SQL 스레드 오류 코드 (있는 경우)를 표시 합니다. 이러한 코드에 대 한 자세한 내용은 [MySQL 서버 오류 메시지 참조](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)를 참조 하세요.|
|Last_SQL_Error|SQL 스레드 오류 메시지 (있는 경우)를 표시 합니다.|
|Slave_SQL_Running_State| 현재 SQL 스레드 상태를 나타냅니다. 이 상태에서 `System lock` 는 정상입니다. 또한 상태를 확인 하는 것이 정상입니다 `Waiting for dependent transaction to commit` . 이 상태는 복제본이 원본 서버가 커밋된 트랜잭션을 업데이트할 때까지 대기 중임을 나타냅니다.|

Slave_IO_Running가이 `Yes` 고 Slave_SQL_Running 이면 `Yes` 복제가 제대로 실행 되 고 있는 것입니다. 

다음으로 Last_IO_Errno, Last_IO_Error, Last_SQL_Errno 및 Last_SQL_Error를 확인 합니다.  이러한 필드에는 SQL 스레드를 중지 시킨 가장 최근의 오류에 대 한 오류 번호 및 오류 메시지가 표시 됩니다. 오류 번호 `0` 와 빈 메시지는 오류가 없음을 의미 합니다. [MySQL server 오류 메시지 참조](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)에서 오류 코드를 확인 하 여 0이 아닌 오류 값을 조사 합니다.

## <a name="common-scenarios-for-high-replication-latency"></a>높은 복제 대기 시간에 대 한 일반적인 시나리오

다음 섹션에서는 높은 복제 대기 시간이 공통적인 시나리오를 다룹니다.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>원본 서버의 네트워크 대기 시간 또는 높은 CPU 사용량

다음 값이 표시 되는 경우 복제 대기 시간이 네트워크 대기 시간이 많거나 원본 서버에서 CPU 사용량이 많은 경우에 발생할 수 있습니다. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

이 경우 IO 스레드가 실행 중이 고 원본 서버에서 대기 중입니다. 원본 서버가 이미 이진 로그 파일 번호 20에 기록 되었습니다. 복제본이 최대 파일 수 10 까지만 받았습니다. 이 시나리오에서 높은 복제 대기 시간에 대 한 주요 요인은 원본 서버의 네트워크 속도 또는 CPU 사용률이 높은 것입니다.  

Azure에서 지역 내의 네트워크 대기 시간은 일반적으로 밀리초 단위로 측정 될 수 있습니다. 지역에서 대기 시간은 밀리초에서 초 사이입니다. 

대부분의 경우 IO 스레드와 원본 서버 간의 연결 지연은 원본 서버에서 CPU 사용률이 높은 경우에 발생 합니다. IO 스레드는 느리게 처리 됩니다. Azure Monitor를 사용 하 여 원본 서버의 동시 연결 수와 CPU 사용률을 확인 하면이 문제를 감지할 수 있습니다.

원본 서버에 높은 CPU 사용률이 표시 되지 않으면 네트워크 대기 시간 문제일 수 있습니다. 네트워크 대기 시간이 갑자기 비정상적으로 높으면 [Azure 상태 페이지](https://status.azure.com/status) 에서 알려진 문제나 작동 중단을 확인 하세요. 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>원본 서버에서 많은 양의 트랜잭션

다음 값이 표시 되 면 원본 서버에서 많은 양의 트랜잭션이 발생 하 여 복제 대기 시간이 발생할 수 있습니다. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

출력은 복제본이 원본 서버 뒤에 있는 이진 로그를 검색할 수 있음을 보여 줍니다. 그러나 복제본 IO 스레드는 릴레이 로그 공간이 이미 꽉 차는 것을 나타냅니다. 

네트워크 속도 때문에 지연이 발생 하지 않습니다. 복제본을 파악 하려고 합니다. 하지만 업데이트 된 이진 로그 크기가 릴레이 로그 공간의 상한을 초과 합니다. 

이 문제를 해결 하려면 원본 서버에서 [느리게 쿼리 로그](concepts-server-logs.md) 를 사용 하도록 설정 합니다. 저속 쿼리 로그를 사용 하 여 원본 서버에서 장기 실행 트랜잭션을 식별할 수 있습니다. 그런 다음 확인 된 쿼리를 조정 하 여 서버의 대기 시간을 줄입니다. 

이 정렬의 복제 대기 시간은 주로 원본 서버의 데이터 로드로 인해 발생 합니다. 원본 서버에 매주 또는 매월 데이터 로드가 있는 경우 복제 대기 시간이 피할 수 있습니다. 원본 서버에서 데이터 로드를 완료 한 후에 복제 서버는 결국 데이터를 파악 합니다.


### <a name="slowness-on-the-replica-server"></a>복제 서버의 속도 저하

다음 값을 확인할 경우 복제 서버에 문제가 있을 수 있습니다. 

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

이 시나리오에서 출력은 IO 스레드와 SQL 스레드가 모두 제대로 실행 되 고 있음을 보여 줍니다. 복제본은 원본 서버에서 작성 하는 것과 동일한 이진 로그 파일을 읽습니다. 그러나 복제 서버의 일부 대기 시간은 원본 서버와 동일한 트랜잭션을 반영 합니다. 

다음 섹션에서는 이러한 종류의 대기 시간에 대 한 일반적인 원인을 설명 합니다.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>테이블에 기본 키 또는 고유 키가 없습니다.

Azure Database for MySQL은 행 기반 복제를 사용 합니다. 원본 서버는 이진 로그에 이벤트를 기록 하 여 개별 테이블 행의 변경 내용을 기록 합니다. 그런 다음 SQL 스레드는 이러한 변경 내용을 복제 서버의 해당 테이블 행에 복제 합니다. 테이블에 기본 키 또는 고유 키가 없는 경우 SQL 스레드는 대상 테이블의 모든 행을 검색 하 여 변경 내용을 적용 합니다. 이 검색을 수행 하면 복제 대기 시간이 발생할 수 있습니다.

MySQL에서 기본 키는 NULL 값을 포함할 수 없기 때문에 쿼리 성능을 향상 시키는 연결 된 인덱스입니다. InnoDB 저장소 엔진을 사용 하는 경우 테이블 데이터는 매우 빠른 조회를 수행 하 고 기본 키를 기준으로 정렬 되도록 물리적으로 구성 됩니다. 

복제본 서버를 만들기 전에 원본 서버에 있는 테이블에 기본 키를 추가 하는 것이 좋습니다. 원본 서버에서 기본 키를 추가 하 고 복제 대기 시간을 개선 하는 데 도움이 되는 읽기 복제본을 다시 만듭니다.

다음 쿼리를 사용 하 여 원본 서버에 기본 키가 없는 테이블을 찾습니다.

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

#### <a name="long-running-queries-on-the-replica-server"></a>복제본 서버에서 장기 실행 쿼리

복제 서버의 워크 로드는 IO 스레드 뒤에 SQL 스레드 지연 시간을 만들 수 있습니다. 복제 서버에서 장기 실행 쿼리는 높은 복제 대기 시간의 일반적인 원인 중 하나입니다. 이 문제를 해결 하려면 복제 서버에서 [느리게 쿼리 로그](concepts-server-logs.md) 를 사용 하도록 설정 합니다. 

저속 쿼리를 사용 하면 복제본이 원본 서버를 통해 catch 할 수 없도록 리소스 사용량이 증가 하거나 서버 속도가 느려질 수 있습니다. 이 시나리오에서는 저속 쿼리를 튜닝 합니다. 더 빠른 쿼리를 통해 SQL 스레드를 차단을 하 고 복제 대기 시간을 크게 향상 시킬 수 있습니다.


#### <a name="ddl-queries-on-the-source-server"></a>원본 서버의 DDL 쿼리
원본 서버에서와 같은 DDL (데이터 정의 언어) 명령에는 [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) 시간이 오래 걸릴 수 있습니다. DDL 명령이 실행 되는 동안 수천 개의 다른 쿼리가 원본 서버에서 병렬로 실행 될 수 있습니다. 

DDL이 복제 될 때 데이터베이스 일관성을 유지 하기 위해 MySQL 엔진은 단일 복제 스레드에서 DDL을 실행 합니다. 이 태스크를 수행 하는 동안 다른 모든 복제 된 쿼리가 차단 되며, 복제 서버에서 DDL 작업이 완료 될 때까지 기다려야 합니다. 온라인 DDL 작업을 수행 하는 경우에도이 지연이 발생 합니다. DDL 작업을 수행 하면 복제 대기 시간이 증가 합니다.

원본 서버에서 [저속 쿼리 로그](concepts-server-logs.md) 를 사용 하도록 설정한 경우 원본 서버에서 실행 된 DDL 명령을 확인 하 여이 대기 시간 문제를 검색할 수 있습니다. 인덱스 삭제, 이름 바꾸기 및 만들기를 통해 ALTER TABLE에 내부 알고리즘을 사용할 수 있습니다. 테이블 데이터를 복사 하 고 테이블을 다시 작성 해야 할 수도 있습니다. 

일반적으로 동시 DML은 내부 알고리즘에 대해 지원 됩니다. 그러나 작업을 준비 하 고 실행할 때 테이블에 대 한 배타적 메타 데이터 잠금을 간단히 사용할 수 있습니다. 따라서 CREATE INDEX 문의 경우 절 알고리즘과 잠금을 사용 하 여 테이블 복사에 대 한 메서드와 읽기 및 쓰기를 위한 동시성 수준에 영향을 줄 수 있습니다. 전체 텍스트 인덱스 또는 공간 인덱스를 추가 하 여 DML 작업을 계속 방지할 수 있습니다. 

다음 예에서는 ALGORITHM 및 LOCK 절을 사용 하 여 인덱스를 만듭니다.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

아쉽게도 잠금이 필요한 DDL 문의 경우 복제 대기 시간을 방지할 수 없습니다. 잠재적 영향을 줄이기 위해 야간 시간 동안에는 사용량이 적은 시간에 이러한 유형의 DDL 작업을 수행 합니다.

#### <a name="downgraded-replica-server"></a>복제본 서버 다운 그레이드

Azure Database for MySQL에서 읽기 복제본은 원본 서버와 동일한 서버 구성을 사용 합니다. 복제 서버 구성을 만든 후에 변경할 수 있습니다. 

복제본 서버가 다운 그레이드 되 면 워크 로드에서 더 많은 리소스를 소비할 수 있으며,이로 인해 복제 대기 시간이 발생할 수 있습니다. 이 문제를 검색 하려면 Azure Monitor를 사용 하 여 복제 서버의 CPU 및 메모리 사용량을 확인 합니다. 

이 시나리오에서는 복제 서버의 구성을 원본 서버 값 보다 크거나 같은 값으로 유지 하는 것이 좋습니다. 이 구성을 사용 하면 복제본이 원본 서버와 함께 유지 됩니다.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>원본 서버 매개 변수를 튜닝 하 여 복제 대기 시간 향상

Azure Database for MySQL 기본적으로 복제는 복제본의 병렬 스레드와 함께 실행 되도록 최적화 됩니다. 원본 서버에서 동시성 높은 작업을 수행 하 여 복제본 서버가 중단 되는 경우 원본 서버에서 binlog_group_commit_sync_delay 매개 변수를 구성 하 여 복제 대기 시간을 향상 시킬 수 있습니다. 

Binlog_group_commit_sync_delay 매개 변수는 이진 로그 파일을 동기화 하기 전에 이진 로그 커밋이 대기 하는 마이크로초 수를 제어 합니다. 이 매개 변수의 혜택은 커밋된 모든 트랜잭션을 즉시 적용 하는 대신 소스 서버에서 이진 로그 업데이트를 대량으로 전송 한다는 것입니다. 이렇게 지연 하면 복제본의 IO가 줄어들고 성능이 향상 됩니다. 

Binlog_group_commit_sync_delay 매개 변수를 1000로 설정 하는 것이 유용할 수 있습니다. 그런 다음 복제 대기 시간을 모니터링 합니다. 이 매개 변수를 신중 하 게 설정 하 고 동시성 워크 로드에만 사용 합니다. 

여러 단일 트랜잭션을 포함 하는 낮은 동시성 워크 로드의 경우 binlog_group_commit_sync_delay 설정으로 대기 시간이 증가할 수 있습니다. 몇 개의 트랜잭션만 커밋된 경우에도 IO 스레드가 대량 이진 로그 업데이트를 대기 하므로 대기 시간이 길어질 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 확인 하세요.
