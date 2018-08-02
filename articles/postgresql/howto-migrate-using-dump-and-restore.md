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
ms.date: 07/19/2018
ms.openlocfilehash: 94d196ceecc0b63b9f0b0fe94f71363dc2086c30
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213653"
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

> [!IMPORTANT]
> Azure blob/저장소에 백업 파일을 복사하고, 인터넷을 통해 복원을 수행할 때보다 훨씬 더 빨리 수행할 수 있는 위치에서 복원을 수행합니다.
> 

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

## <a name="next-steps"></a>다음 단계
- 내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스를 마이그레이션하려면 [내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스 마이그레이션](howto-migrate-using-export-and-import.md)을 참조하세요.
- Azure Database for PostgreSQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](http://aka.ms/datamigration)를 참조하세요.
