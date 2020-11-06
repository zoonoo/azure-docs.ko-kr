---
title: 덤프 및 복원을 사용 하 여 업그레이드-Azure Database for PostgreSQL-단일 서버
description: 데이터베이스를 덤프 하 고 복원 하는 두 가지 방법에 대해 설명 합니다. Azure Database for PostgreSQL-단일 서버를 상위 버전으로 마이그레이션합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 6dfcf0b2ec1d46821007123908a8e7ba8df29744
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421767"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>덤프 및 복원을 사용 하 여 PostgreSQL 데이터베이스 업그레이드

Azure Database for PostgreSQL 단일 서버에서 다음 방법 중 하나를 사용 하 여 PostgreSQL 데이터베이스 엔진을 더 높은 주 버전으로 업그레이드 하는 것이 좋습니다.
* PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)를 사용 하는 오프 라인 메서드입니다. 이 방법에서는 먼저 원본 서버에서 덤프를 수행 하 고 대상 서버에서 해당 덤프를 복원 합니다.
* [**Database Migration Service**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS)를 사용 하는 온라인 메서드입니다. 이 메서드는 대상 데이터베이스를 원본과 동기화 상태로 유지 하 고, 이동할 시기를 선택할 수 있습니다. 그러나 몇 가지 필수 구성 요소 및 제한 사항을 해결 해야 합니다. 

온라인 및 오프 라인 방법 중에서 주 버전 업그레이드를 수행 하는 방법을 결정할 때 다음 권장 사항을 사용할 수 있습니다.

| **데이터베이스** | **덤프/복원 (오프 라인)** | **DMS (온라인)** |
| ------ | :------: | :-----: |
| 작은 데이터베이스가 있고 업그레이드 하는 데 가동 중지 시간을 감당할 수 있습니다.  | X | |
| 소규모 데이터베이스 (< 10gb)  | X | X | 
| 중소 Db (10gb – 100 g b) | X | X |
| 대량 데이터베이스 (> 100 GB) |  | X |
| 데이터베이스 크기와 관계 없이 업그레이드 하는 데 가동 중지 시간을 감당할 수 있습니다. | X |  |
| 다시 부팅을 포함 하 여 DMS [필수](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) 구성 요소를 처리할 수 있나요? |  | X |
| 업그레이드 프로세스 중에 DDLs 및 기록 되지 않은 테이블을 피할 수 있나요? | |  X |

이 방법 가이드에서는 PostgreSQL pg_dump 및 pg_restore 명령을 사용 하 여 데이터베이스를 업그레이드 하는 방법에 대 한 두 가지 예제 메서드를 제공 합니다. 이 문서의 프로세스는 데이터베이스를 원본 서버에서 대상 서버로 **마이그레이션하** 는 경우 **업그레이드** 라고 합니다. 

> [!NOTE]
> PostgreSQL 덤프 및 복원은 여러 가지 방법으로 수행할 수 있습니다. 이 문서에서 참조 하는 것과 다른 방법을 사용 하도록 선택할 수 있습니다. 예를 들어 덤프를 수행 하 고 PostgreSQL client에서 복원 하는 방법에 대 한 자세한 내용은 단계별 절차 세부 정보 및 모범 사례에 대 한 [설명서](./howto-migrate-using-dump-and-restore.md) 를 참조 하세요. 추가 매개 변수를 사용 하는 자세한 덤프 및 복원 구문은 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)문서를 참조 하세요. 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Azure PostgreSQL에서 덤프 및 복원을 사용 하기 위한 필수 구성 요소
 
이 방법 가이드를 단계별로 실행 하려면 다음이 필요 합니다.
- 9.5, 9.6 또는 10 (Azure Database for PostgreSQL – 단일 서버)을 실행 하는 원본 데이터베이스
- 원하는 PostgreSQL 주 버전 [Azure Database for PostgreSQL 서버](quickstart-create-server-database-portal.md)를 사용 하는 대상 데이터베이스 서버입니다. 
- PostgreSQL가 설치 되 고 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 명령줄 유틸리티가 설치 된 클라이언트 시스템 (Linux) 
- 또는 [Azure Cloud Shell](https://shell.azure.com) 를 사용 하거나 [Azure Portal](https://portal.azure.com)의 오른쪽 위에 있는 메뉴 모음에서 Azure Cloud Shell를 클릭 하 여 사용할 수 있습니다. `az login`덤프 및 복원 명령을 실행 하기 전에 계정에 로그인 해야 합니다.
- 원본 및 대상 서버와 동일한 지역에서 실행 되는 VM과 같은 PostgreSQL 클라이언트 위치 

## <a name="additional-details-and-considerations"></a>추가 세부 정보 및 고려 사항
- 포털에서 "연결 문자열"을 클릭 하 여 원본 및 대상 데이터베이스에 대 한 연결 문자열을 찾을 수 있습니다. 
- 서버에서 둘 이상의 데이터베이스를 실행 하 고 있을 수 있습니다. 원본 서버에 연결 하 고를 실행 하 여 데이터베이스 목록을 찾을 수 있습니다 `\l` .
- 대상 데이터베이스 서버에 해당 데이터베이스를 만듭니다.
- 업그레이드 또는 템플릿 데이터베이스를 건너뛸 수 있습니다 `azure_maintenance` .
- 이 마이그레이션 모드에 적합 한 데이터베이스를 확인 하려면 위의 표를 참조 하세요.
- Azure Cloud Shell 사용 하려는 경우 20 분 후 세션 시간이 초과 됩니다. 데이터베이스 < 크기가 10gb 이면 시간 초과 없이 업그레이드를 완료할 수 있습니다. 그렇지 않으면 <Enter> 10-15 분에 한 번 키 누르기와 같이 다른 방법으로 세션을 열어 두어야 할 수 있습니다. 

> [!TIP] 
> - 원본 및 대상 데이터베이스에 대해 동일한 암호를 사용 하는 경우 환경 변수를 설정할 수 있습니다 `PGPASSWORD=yourPassword` .  그런 다음 psql, pg_dump 및 pg_restore와 같은 명령을 실행 하면 암호 때마다를 제공할 필요가 없습니다.  마찬가지로, 등의 추가 변수를 설정할 수 있습니다 `PGUSER` `PGSSLMODE` . [PostgreSQL 환경 변수](https://www.postgresql.org/docs/11/libpq-envars.html)를 참조 하세요.
>  
> - PostgreSQL 서버에 TLS/SSL 연결이 필요한 경우 (기본적으로 Azure Database for PostgreSQL 서버의 경우) `PGSSLMODE=require` pg_restore 도구가 tls와 연결 되도록 환경 변수를 설정 합니다. TLS를 사용 하지 않으면 오류를 읽을 수 있습니다.  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - Windows 명령줄에서 pg_restore 명령을 실행하기 전에 명령 `SET PGSSLMODE=require`를 실행합니다. Linux 또는 Bash에서 pg_restore 명령을 실행하기 전에 명령 `export PGSSLMODE=require`를 실행합니다.

## <a name="example-database-used-in-this-guide"></a>이 가이드에 사용 되는 예제 데이터베이스

 | **설명** | **값** |
 | ------- | ------- |
 | 원본 서버 (v 9.5) | pg-95.postgres.database.azure.com |
 | 원본 데이터베이스 | bench5gb |
 | 원본 데이터베이스 크기 | 5GB |
 | 원본 사용자 이름 | pg@pg-95 |
 | 대상 서버 (v11) | pg-11.postgres.database.azure.com |
 | 대상 데이터베이스 | bench5gb |
 | 대상 사용자 이름 | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>방법 1: 스트리밍 백업을 사용 하 여 대상으로 업그레이드 

이 메서드에서 전체 데이터베이스 덤프는 대상 데이터베이스 서버로 직접 스트리밍되 며 덤프를 클라이언트에 저장 하지 않습니다. 따라서 저장소는 제한 된 클라이언트와 함께 사용할 수 있으며 Azure Cloud Shell 에서도 실행할 수 있습니다. 

1. 명령을 사용 하 여 대상 서버에 데이터베이스가 있는지 확인 `\l` 합니다. 데이터베이스가 없는 경우 데이터베이스를 만듭니다.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. 파이프를 사용 하 여 덤프를 실행 하 고 하나의 명령줄로 복원 합니다. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    예를 들면 다음과 같습니다.

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. 업그레이드 (마이그레이션) 프로세스가 완료 되 면 대상 서버를 사용 하 여 응용 프로그램을 테스트할 수 있습니다. 
4. 서버 내의 모든 데이터베이스에 대해이 프로세스를 반복 합니다.

 다음 표에서는이 방법을 사용 하 여 업그레이드 하는 데 걸린 시간을 보여 줍니다. [Pgbench](https://www.postgresql.org/docs/10/pgbench.html)를 사용 하 여 데이터를 채웁니다. 데이터베이스의 개체 수가 pgbench에서 생성 된 테이블 및 인덱스 보다 크기가 다를 수 있으므로 데이터베이스를 업그레이드 하는 데 걸리는 실제 시간을 이해 하기 위해 데이터베이스의 덤프와 복원을 테스트 하는 것이 좋습니다. 

| **데이터베이스 크기** | **대략적인 시간** | 
| ----- | ------ |
| 1 GB  | 1-2 분 |
| 5GB | 8-10 분 |
| 10 GB | 15-20분 |
| 50GB | 1-1.5 시간 |
| 100GB | 2.5-3 시간|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>방법 2: 병렬 덤프 및 복원으로 업그레이드 

이 방법은 데이터베이스에 적은 수의 테이블이 있고 해당 데이터베이스의 덤프 및 복원 프로세스를 병렬화 하려는 경우에 유용 합니다. 데이터베이스에 대 한 백업 덤프를 수용할 수 있는 충분 한 로컬 디스크 저장소가 필요 합니다. 이러한 병렬 덤프 및 복원 프로세스는 전체 마이그레이션/업그레이드를 완료 하는 데 걸리는 시간을 줄입니다. 예를 들어 마이그레이션하는 데 1-1.5 시간이 걸린 50의 pgbench 데이터베이스는 30 분 이내에 완료 되었습니다.

1. 원본 서버의 각 데이터베이스에 대해 대상 서버에 해당 데이터베이스를 만듭니다.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   예를 들면 다음과 같습니다.
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. 작업 수 = 4 (데이터베이스의 테이블 수)를 사용 하 여 디렉터리 형식으로 pg_dump 명령을 실행 합니다. 더 큰 계산 계층과 더 많은 테이블이 있는 경우 더 큰 값으로 늘릴 수 있습니다. 이 pg_dump는 각 작업에 대해 압축 된 파일을 저장할 디렉터리를 만듭니다.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    예를 들면 다음과 같습니다.
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. 그런 다음 대상 서버에서 백업을 복원 합니다.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    예를 들면 다음과 같습니다.
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> 이 문서에서 설명 하는 프로세스를 사용 하 여 미리 보기 상태인 Azure Database for PostgreSQL 유연한 서버를 업그레이드할 수도 있습니다. 주요 차이점은를 제외 하 고 유연한 서버 대상에 대 한 연결 문자열입니다 `@dbName` .  예를 들어 사용자 이름이 인 경우 `pg` 연결 문자열의 단일 서버 사용자 이름은이 고 `pg@pg-95` , 유연한 서버에서는를 사용 하기만 하면 됩니다 `pg` .

## <a name="next-steps"></a>다음 단계

- 대상 데이터베이스 함수에 만족 하면 이전 데이터베이스 서버를 삭제할 수 있습니다. 
- 원본 서버와 동일한 데이터베이스 끝점을 사용 하려는 경우 이전 원본 데이터베이스 서버를 삭제 한 후에는 이전 데이터베이스 서버 이름을 사용 하 여 읽기 복제본을 만들 수 있습니다. 안정적인 상태가 설정 되 면 복제본을 중지할 수 있습니다. 그러면 복제본 서버가 독립 서버로 승격 됩니다. 자세한 내용은 [복제](./concepts-read-replicas.md)를 참조하세요.
- 이러한 명령은 프로덕션 환경에서 사용하기 전에 테스트 환경에서 테스트하여 유효성을 검사해야 합니다.
