---
title: "PostgreSQL용 Azure Database에서 덤프 및 복원 방법 | Microsoft Docs"
description: "PostgreSQL 데이터베이스를 덤프 파일로 추출하고 PostgreSQL용 Azure Database에서 pg_dump에 의해 생성된 보관 파일에서 PostgreSQL 데이터베이스를 복원하는 방법을 설명합니다."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 28727117dbd37f9c595b488639a632b4c7404496
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
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
pg_restore -v –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
이 예제에서는 데이터를 덤프 파일 **testdb.dump**에서 대상 서버 **mypgserver-20170401.postgres.database.azure.com**의 데이터베이스 **mypgsqldb**로 복원합니다.
```bash
pg_restore -v --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>다음 단계
- 내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스를 마이그레이션하려면 [내보내기 및 가져오기를 사용하여 PostgreSQL 데이터베이스 마이그레이션](howto-migrate-using-export-and-import.md)을 참조하세요.