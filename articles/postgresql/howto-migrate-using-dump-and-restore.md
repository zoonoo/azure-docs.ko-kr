---
title: 덤프 및 복원-Azure Database for PostgreSQL-단일 서버
description: PostgreSQL 데이터베이스를 덤프 파일로 추출 하 고 Azure Database for PostgreSQL 단일 서버에서 pg_dump으로 만든 파일에서 복원 하는 방법에 대해 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 4fe15d1bd23f36b7289c54bedf575ae4760600e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710807"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>덤프 및 복원을 사용하여 PostgreSQL 데이터베이스 마이그레이션
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html)를 사용하여 PostgreSQL 데이터베이스를 덤프 파일로 추출하고 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)를 사용하여 pg_dump에 의해 생성된 보관 파일에서 PostgreSQL 데이터베이스를 복원할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- 액세스를 허용하기 위한 방화벽 규칙을 사용하는 [PostgreSQL용 Azure Database 서버](quickstart-create-server-database-portal.md) 및 이에 속한 데이터베이스
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 명령줄 유틸리티 설치

다음 단계를 수행하여 PostgreSQL 데이터베이스를 덤프 및 복원합니다.

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>로드할 데이터가 들어 있는 pg_dump를 사용하여 덤프 파일 만들기
온-프레미스 또는 VM에서 기존 PostgreSQL 데이터베이스를 백업하려면 다음 명령을 실행합니다.
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
예를 들어, 로컬 서버와 **testdb**라는 데이터베이스가 있는 경우
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>Pg_restore를 사용 하 여 데이터를 대상 Azure Database for PostgreSQL으로 복원
대상 데이터베이스를 만든 후에는 pg_restore 명령과 -d, --dbname 매개 변수를 사용하여 데이터를 덤프 파일에서 대상 데이터베이스로 복원할 수 있습니다.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

--no-owner 매개 변수를 포함하면 복원 중에 만들어진 모든 개체가 --username으로 지정된 사용자의 소유가 됩니다. 자세한 내용은 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)에서 공식 PostgreSQL 설명서를 참조하세요.

> [!NOTE]
> PostgreSQL 서버에 TLS/SSL 연결이 필요한 경우 (기본적으로 Azure Database for PostgreSQL 서버의 경우) `PGSSLMODE=require` pg_restore 도구가 tls와 연결 되도록 환경 변수를 설정 합니다. TLS를 사용 하지 않으면 오류를 읽을 수 있습니다.  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Windows 명령줄에서 pg_restore 명령을 실행하기 전에 명령 `SET PGSSLMODE=require`를 실행합니다. Linux 또는 Bash에서 pg_restore 명령을 실행하기 전에 명령 `export PGSSLMODE=require`를 실행합니다.
>

이 예제에서는 덤프 파일 **testdb.dump**에서 대상 서버 **mydemoserver.postgres.database.azure.com**의 **mypgsqldb** 데이터베이스로 데이터를 복원합니다.

**단일 서버**에이 **pg_restore** 를 사용 하는 방법에 대 한 예제는 다음과 같습니다.

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```
이 **pg_restore** 를 **유연한 서버**에 사용 하는 방법에 대 한 예제는 다음과 같습니다.

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```
---

## <a name="optimizing-the-migration-process"></a>마이그레이션 프로세스 최적화

기존 PostgreSQL 데이터베이스를 Azure Database for PostgreSQL 서비스로 마이그레이션하는 한 가지 방법은 원본에서 데이터베이스를 백업한 다음 Azure에서 복원하는 것입니다. 마이그레이션을 완료하는 데 필요한 시간을 최소화하려면 백업 및 복원 명령에 다음 매개 변수를 사용하는 것이 좋습니다.

> [!NOTE]
> 자세한 구문 정보는 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 문서를 참조하세요.
>

### <a name="for-the-backup"></a>백업
- 복원을 병렬로 수행하여 시간을 단축할 수 있도록 -Fc 스위치를 사용해 백업을 가져옵니다. 예를 들면 다음과 같습니다.

    ```bash
    pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
    ```

### <a name="for-the-restore"></a>복원
- 네트워크 대기 시간을 줄이기 위해 마이그레이션 대상인 Azure Database for PostgreSQL 서버와 같은 지역의 Azure VM으로 백업 파일을 이동한 다음 해당 VM에서 pg_restore를 수행하는 것이 좋습니다. 또한 [가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-powershell.md)을 사용하도록 설정하여 VM을 만드는 것이 좋습니다.

- create index 문이 이미 기본적으로 포함되어 있겠지만, 덤프 파일을 열어 create index 문이 데이터 삽입 부분 뒤에 있는지 확인합니다. 해당 위치에 문이 없으면 create index 문을 데이터 삽입 부분 뒤로 이동합니다.

- 스위치-Fc 및-j로 복원 *#* 하 여 복원을 병렬화 합니다. *#* 대상 서버의 코어 수입니다. 또한 *#* 대상 서버의 코어 수를 두 배로 설정 하 여 영향을 확인할 수 있습니다. 예를 들면 다음과 같습니다.

**단일 서버**에이 **pg_restore** 를 사용 하는 방법에 대 한 예제는 다음과 같습니다.
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
```
이 **pg_restore** 를 **유연한 서버**에 사용 하는 방법에 대 한 예제는 다음과 같습니다.
```bash
 pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
 ```

- *set synchronous_commit = off;* 명령을 시작 부분에, *set synchronous_commit = on;* 명령을 끝부분에 추가하여 덤프 파일을 편집할 수도 있습니다. 복원 종료 시 앱이 데이터를 변경하기 전에 동기화 커밋을 설정하지 않으면 이후 데이터가 손실될 수도 있습니다.

- 복원하기 전에 대상 Azure Database for PostgreSQL 서버에서 다음을 수행하는 것이 좋습니다.
    - 마이그레이션 동안에는 이러한 통계가 필요하지 않으므로 쿼리 성능 추적을 해제합니다. 이 작업은 setting pg_stat_statements.track, pg_qs.query_capture_mode 및 pgms_wait_sampling.query_capture_mode를 NONE으로 설정하여 수행할 수 있습니다.

    - 마이그레이션 속도를 높이려면 32개의 메모리 최적화 vCore와 같이 높은 컴퓨팅 및 높은 메모리 SKU를 사용합니다. 복원이 완료되면 원하는 기본 설정 SKU로 쉽게 다시 축소할 수 있습니다. SKU가 높을수록 pg_restore 명령에서 해당 `-j` 매개 변수를 늘리면 더 많은 병렬 처리를 수행할 수 있습니다.

    - 대상 서버에서 IOPS를 높이면 복원 성능이 향상될 수 있습니다. 서버의 스토리지 크기를 늘려 더 많은 IOPS를 프로비저닝할 수 있습니다. 이 설정은 취소할 수 없지만 나중에 더 높은 IOPS가 실제 워크로드에 도움이 되는지 여부를 고려합니다.

이러한 명령은 프로덕션 환경에서 사용하기 전에 테스트 환경에서 테스트하여 유효성을 검사해야 합니다.

## <a name="next-steps"></a>다음 단계
- 내보내기 및 가져오기를 사용 하 여 PostgreSQL 데이터베이스를 마이그레이션하려면 [내보내기 및 가져오기를 사용 하 여 PostgreSQL 데이터베이스 마이그레이션](howto-migrate-using-export-and-import.md)을 참조 하세요.
- Azure Database for PostgreSQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://aka.ms/datamigration)를 참조하세요.
