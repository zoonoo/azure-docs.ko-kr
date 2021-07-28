---
title: 덤프 및 복원 - Azure Database for PostgreSQL - 단일 서버
description: PostgreSQL 데이터베이스를 덤프 파일로 추출할 수 있습니다. 그런 다음 Azure Database for PostgreSQL 단일 서버에서 pg_dump에 의해 생성된 파일에서 복원할 수 있습니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: f99e966d3f4230320c71a10cca9cb2a80abc47da
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108135512"
---
# <a name="migrate-your-postgresql-database-by-using-dump-and-restore"></a>덤프 및 복원을 사용하여 PostgreSQL 데이터베이스 마이그레이션
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server.md)]

[pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html)를 사용하여 PostgreSQL 데이터베이스를 덤프 파일로 추출할 수 있습니다. 그런 다음 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)를 사용하여 `pg_dump`에 의해 생성된 보관 파일에서 PostgreSQL 데이터베이스를 복원합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- 액세스를 허용하는 방화벽 규칙을 포함하는 [Azure Database for PostgreSQL 서버](quickstart-create-server-database-portal.md).
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) 명령줄 유틸리티 설치

## <a name="create-a-dump-file-that-contains-the-data-to-be-loaded"></a>로드할 데이터가 들어 있는 덤프 파일 만들기

온-프레미스 또는 VM에서 기존 PostgreSQL 데이터베이스를 백업하려면 다음 명령을 실행합니다.

```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
예를 들어, 로컬 서버와 **testdb** 라는 데이터베이스가 있는 경우 다음을 실행합니다.

```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```

## <a name="restore-the-data-into-the-target-database"></a>대상 데이터베이스로 데이터 복원

대상 데이터베이스를 만든 후에는 `pg_restore` 명령과 -d, `--dbname` 매개 변수를 사용하여 데이터를 덤프 파일에서 대상 데이터베이스로 복원할 수 있습니다.

```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user-name> --dbname=<target database name> <database>.dump
```

`--no-owner` 매개 변수를 포함하면 복원 중에 만들어진 모든 개체가 `--username`으로 지정된 사용자의 소유가 됩니다. 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)를 참조하세요.

> [!NOTE]
> Azure Database for PostgreSQL 서버에서는 TLS/SSL 연결이 기본적으로 설정되어 있습니다. PostgreSQL 서버에 TLS/SSL 연결이 필요하지만 연결되어 있지 않은 경우 pg_restore 도구가 TLS와 연결하도록 환경 변수 `PGSSLMODE=require`를 설정합니다. TLS가 연결되지 않으면 다음 오류가 표시될 수 있습니다. "오류: SSL 연결이 필요합니다. SSL 옵션을 지정하고 다시 시도하세요." Windows 명령줄에서 `pg_restore` 명령을 실행하기 전에 명령 `SET PGSSLMODE=require`를 실행합니다. Linux 또는 Bash에서는 `pg_restore` 명령을 실행하기 전에 `export PGSSLMODE=require` 명령을 실행합니다. 
>

이 예제에서는 덤프 파일 **testdb.dump** 에서 대상 서버 **mydemoserver.postgres.database.azure.com** 의 **mypgsqldb** 데이터베이스로 데이터를 복원합니다.

단일 서버에 이 `pg_restore`를 사용하는 방법에 대한 예는 다음과 같습니다.

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

유연한 서버에 이 `pg_restore`를 사용하는 방법에 대한 예는 다음과 같습니다.

```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin --dbname=mypgsqldb testdb.dump
```

## <a name="optimize-the-migration-process"></a>마이그레이션 프로세스 최적화

기존 PostgreSQL 데이터베이스를 Azure Database for PostgreSQL로 마이그레이션하는 한 가지 방법은 원본에서 데이터베이스를 백업한 다음 Azure에서 복원하는 것입니다. 마이그레이션을 완료하는 데 필요한 시간을 최소화하려면 백업 및 복원 명령에 다음 매개 변수를 사용하는 것이 좋습니다.

> [!NOTE]
> 자세한 구문 정보는 [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)를 참조하세요.
>

### <a name="for-the-backup"></a>백업

복원을 병렬로 수행하여 시간을 단축할 수 있도록 `-Fc` 스위치를 사용해 백업을 가져옵니다. 예를 들어:

```bash
pg_dump -h my-source-server-name -U source-server-username -Fc -d source-databasename -f Z:\Data\Backups\my-database-backup.dump
```

### <a name="for-the-restore"></a>복원

- 마이그레이션하는 Azure Database for PostgreSQL 서버와 동일한 지역에 있는 Azure VM으로 백업 파일을 이동합니다. 해당 VM에서 `pg_restore`를 수행하여 네트워크 대기 시간을 줄입니다. [가속 네트워킹](../virtual-network/create-vm-accelerated-networking-powershell.md)을 사용하는 VM을 만듭니다.

- 덤프 파일을 열어 create index 문이 데이터 삽입 부분 뒤에 있는지 확인합니다. 해당 위치에 문이 없으면 create index 문을 데이터 삽입 부분 뒤로 이동합니다. 이 작업은 기본적으로 이미 수행되어 있겠지만 확인하는 것이 좋습니다.

- 스위치 `-Fc` 및 `-j`(숫자 포함)를 사용하여 복원을 병렬로 수행합니다. 지정하는 숫자는 대상 서버의 코어 수입니다. 대상 서버 코어 수의 2배로 설정한 다음 복원을 수행하여 영향을 확인해 볼 수도 있습니다.

    단일 서버에 이 `pg_restore`를 사용하는 방법에 대한 예는 다음과 같습니다.

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

    유연한 서버에 이 `pg_restore`를 사용하는 방법에 대한 예는 다음과 같습니다.

    ```bash
     pg_restore -h my-target-server.postgres.database.azure.com -U azure-postgres-username@my-target-server -Fc -j 4 -d my-target-databasename Z:\Data\Backups\my-database-backup.dump
    ```

- `set synchronous_commit = off;` 명령을 시작 부분에 추가하고 `set synchronous_commit = on;` 명령을 끝 부분에 추가하여 덤프 파일을 편집할 수도 있습니다. 끝에 이를 설정하지 않으면 앱이 데이터를 변경하기 전에 이후에 데이터가 손실될 수도 있습니다.

- 복원하기 전에 대상 Azure Database for PostgreSQL 서버에서 다음을 수행하는 것이 좋습니다.
    
  - 쿼리 성능 추적을 해제합니다. 이러한 통계는 마이그레이션 중에 필요하지 않습니다. `pg_stat_statements.track`, `pg_qs.query_capture_mode` 및 `pgms_wait_sampling.query_capture_mode`를 `NONE`으로 설정하여 이 작업을 수행할 수 있습니다.

  - 마이그레이션 속도를 높이려면 32개의 메모리 최적화 vCore와 같이 높은 컴퓨팅 및 높은 메모리 SKU를 사용합니다. 복원이 완료되면 원하는 기본 설정 SKU로 쉽게 다시 축소할 수 있습니다. SKU가 높을수록 `pg_restore` 명령에서 해당 `-j` 매개 변수를 늘리면 더 많은 병렬 처리를 수행할 수 있습니다.

  - 대상 서버에서 IOPS를 높이면 복원 성능이 향상될 수 있습니다. 서버의 스토리지 크기를 늘려 더 많은 IOPS를 프로비저닝할 수 있습니다. 이 설정은 취소할 수 없지만 나중에 더 높은 IOPS가 실제 워크로드에 도움이 되는지 여부를 고려합니다.

이러한 명령은 프로덕션 환경에서 사용하기 전에 테스트 환경에서 테스트하여 유효성을 검사해야 합니다.

## <a name="next-steps"></a>다음 단계

- 내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스를 마이그레이션하려면 [내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스 마이그레이션](howto-migrate-using-export-and-import.md)을 참조하세요.
- Azure Database for PostgreSQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](/data-migration/)를 참조하세요.