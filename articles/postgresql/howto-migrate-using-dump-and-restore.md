---
title: Azure Database for PostgreSQL에서 덤프 및 복원 방법
description: Azure Database for PostgreSQL에서 PostgreSQL 데이터베이스를 덤프 파일로 추출하고 pg_dump에 의해 생성된 파일에서 복원하는 방법을 설명합니다.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 2aa59bcf0d56358601b81730abe330a56ca35d02
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966911"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>덤프 및 복원을 사용하여 PostgreSQL 데이터베이스 마이그레이션
[pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html)를 사용하여 PostgreSQL 데이터베이스를 덤프 파일로 추출하고 [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html)를 사용하여 pg_dump에 의해 생성된 보관 파일에서 PostgreSQL 데이터베이스를 복원할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- 액세스를 허용하기 위한 방화벽 규칙을 사용하는 [PostgreSQL용 Azure Database 서버](quickstart-create-server-database-portal.md) 및 이에 속한 데이터베이스
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 명령줄 유틸리티 설치

다음 단계를 수행하여 PostgreSQL 데이터베이스를 덤프 및 복원합니다.

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>로드할 데이터가 들어 있는 pg_dump를 사용하여 덤프 파일 만들기
온-프레미스 또는 VM에서 기존 PostgreSQL 데이터베이스를 백업하려면 다음 명령을 실행합니다.
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
예를 들어, 로컬 서버와 **testdb**라는 데이터베이스가 있는 경우
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>pg_restore를 사용하여 데이터를 대상 PostrgeSQL용 Azure Database로 복원
대상 데이터베이스를 만든 후에는 pg_restore 명령과 -d, --dbname 매개 변수를 사용하여 데이터를 덤프 파일에서 대상 데이터베이스로 복원할 수 있습니다.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
--no-owner 매개 변수를 포함하면 복원 중에 만들어진 모든 개체가 --username으로 지정된 사용자의 소유가 됩니다. 자세한 내용은 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html)에서 공식 PostgreSQL 설명서를 참조하세요.

> [!NOTE]
> PostgreSQL 서버에 SSL 연결이 필요한 경우(기본적으로 PostgreSQL 서버용 Azure Database에서), pg_restore 도구가 SSL과 연결되도록 환경 변수 `PGSSLMODE=require`를 설정합니다. SSL을 사용하지 않으면 `FATAL:  SSL connection is required. Please specify SSL options and retry.`라는 오류가 발생할 수 있음
>
> Windows 명령줄에서 pg_restore 명령을 실행하기 전에 명령 `SET PGSSLMODE=require`를 실행합니다. Linux 또는 Bash에서 pg_restore 명령을 실행하기 전에 명령 `export PGSSLMODE=require`를 실행합니다.
>

이 예제에서는 덤프 파일 **testdb.dump**에서 대상 서버 **mydemoserver.postgres.database.azure.com**의 **mypgsqldb** 데이터베이스로 데이터를 복원합니다. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>마이그레이션 프로세스 최적화

기존 PostgreSQL 데이터베이스를 Azure Database for PostgreSQL 서비스로 마이그레이션하는 한 가지 방법은 원본에서 데이터베이스를 백업한 다음 Azure에서 복원하는 것입니다. 마이그레이션을 완료하는 데 필요한 시간을 최소화하려면 백업 및 복원 명령에 다음 매개 변수를 사용하는 것이 좋습니다.

> [!NOTE]
> 자세한 구문 정보는 [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) 및 [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) 문서를 참조하세요.
>

### <a name="for-the-backup"></a>백업
- 복원을 병렬로 수행하여 시간을 단축할 수 있도록 -Fc 스위치를 사용해 백업을 가져옵니다. 예: 

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>복원
- 네트워크 대기 시간을 줄이기 위해 마이그레이션 대상인 Azure Database for PostgreSQL 서버와 같은 지역의 Azure VM으로 백업 파일을 이동한 다음 해당 VM에서 pg_restore를 수행하는 것이 좋습니다. 또한 [가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-powershell.md)을 사용하도록 설정하여 VM을 만드는 것이 좋습니다.
- create index 문이 이미 기본적으로 포함되어 있겠지만, 덤프 파일을 열어 create index 문이 데이터 삽입 부분 뒤에 있는지 확인합니다. 해당 위치에 문이 없으면 create index 문을 데이터 삽입 부분 뒤로 이동합니다.
- -Fc 및 -j *#* 스위치를 사용하여 복원을 병렬로 수행합니다. *#* 는 대상 서버의 코어 수입니다. *#* 를 대상 서버 코어 수의 2배로 설정한 다음 복원을 수행하여 영향을 확인해 볼 수도 있습니다. 예: 

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- *set synchronous_commit = off;* 명령을 시작 부분에, *set synchronous_commit = on;* 명령을 끝부분에 추가하여 덤프 파일을 편집할 수도 있습니다. 복원 종료 시 앱이 데이터를 변경하기 전에 동기화 커밋을 설정하지 않으면 이후 데이터가 손실될 수도 있습니다.

이러한 명령은 프로덕션 환경에서 사용하기 전에 테스트 환경에서 테스트하여 유효성을 검사해야 합니다.

## <a name="next-steps"></a>다음 단계
- 내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스를 마이그레이션하려면 [내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스 마이그레이션](howto-migrate-using-export-and-import.md)을 참조하세요.
- Azure Database for PostgreSQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://aka.ms/datamigration)를 참조하세요.
