---
title: 데이터베이스 손상 해결-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL에서 데이터베이스 손상 문제를 해결 하는 방법에 대해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766888"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Azure Database for MySQL의 데이터베이스 손상 문제 해결
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

데이터베이스가 손상 되 면 응용 프로그램에 대 한 가동 중지 시간이 발생할 수 있습니다. 데이터 손실을 방지 하기 위해 시간에 손상 문제를 해결 하는 것도 중요 합니다. 데이터베이스 손상이 발생 하면 서버 로그에이 오류가 표시 됩니다. `InnoDB: Database page corruption on disk or a failed.`

이 문서에서는 데이터베이스 또는 테이블 손상 문제를 해결 하는 방법에 대해 알아봅니다. Azure Database for MySQL InnoDB 엔진을 사용 합니다. 자동화 된 손상 검사 및 복구 작업을 제공 합니다. InnoDB는 읽은 모든 페이지에서 체크섬을 실행 하 여 손상 된 페이지를 확인 합니다. 체크섬이 일치 하지 않으면 MySQL 서버가 자동으로 중지 됩니다.

데이터베이스 손상 문제를 빠르게 완화 하려면 다음 옵션을 시도해 보세요.

## <a name="restart-your-mysql-server"></a>MySQL 서버 다시 시작

일반적으로 응용 프로그램에서 테이블이 나 데이터베이스에 액세스 하면 데이터베이스 또는 테이블이 손상 된 것을 알 수 있습니다. InnoDB는 서버를 다시 시작할 때 대부분의 문제를 해결할 수 있는 충돌 복구 메커니즘을 제공 합니다. 따라서 서버를 다시 시작 하면 데이터베이스의 상태가 잘못 된 것으로 발생 하는 크래시에서 서버를 복구 하는 데 도움이 될 수 있습니다.

## <a name="use-the-dump-and-restore-method"></a>덤프 및 복원 방법 사용

*덤프 및 복원* 방법을 사용 하 여 손상 문제를 해결 하는 것이 좋습니다. 이 방법에는 다음이 포함 됩니다.
1. 손상 된 테이블에 액세스 하 고 있습니다.
1. Mysqldump 유틸리티를 사용 하 여 테이블의 논리적 백업을 만듭니다. 이 백업에는 테이블 구조와 그 안의 데이터가 유지 됩니다.
1. 데이터베이스에 테이블을 다시 로드 합니다.

### <a name="back-up-your-database-or-tables"></a>데이터베이스 또는 테이블 백업

> [!Important]
> - 클라이언트 컴퓨터에서 서버에 액세스할 수 있도록 방화벽 규칙을 구성 했는지 확인 합니다. 자세한 내용은 [단일 서버에서 방화벽 규칙 구성](howto-manage-firewall-using-portal.md) 및 [유연한 서버에서 방화벽 규칙 구성](flexible-server/how-to-connect-tls-ssl.md)을 참조 하세요.
> - SSL을 사용 `--ssl-cert` 하는 경우 mysqldump에 ssl 옵션을 사용 합니다.

Mysqldump를 사용 하 여 명령줄에서 백업 파일을 만듭니다. 다음 명령을 사용합니다.

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

매개 변수 설명:
- `[ssl-cert=/path/to/pem]`: SSL 인증서에 대 한 경로입니다. 클라이언트 컴퓨터에서 SSL 인증서를 다운로드 하 고 명령에서 해당 인증서의 경로를 설정 합니다. SSL을 사용 하지 않도록 설정한 경우이 매개 변수를 사용 하지 마세요.
- `[host]`: Azure Database for MySQL 서버입니다.
- `[uname]`: 서버 관리자의 사용자 이름입니다.
- `[pass]`: 관리 사용자의 암호입니다.
- `[dbname]`: 데이터베이스의 이름입니다.
- `[backupfile.sql]`: 데이터베이스 백업의 파일 이름입니다.

> [!Important]
> - 단일 서버의 경우 `admin-user@servername` 다음 명령에서 바꿀 형식을 사용 합니다 `myserveradmin` .
> - 유연한 서버의 경우 `admin-user` 다음 명령에서 바꿀 형식을 사용 합니다 `myserveradmin` .

특정 테이블이 손상 된 경우 백업할 데이터베이스의 특정 테이블을 선택 합니다.
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

하나 이상의 데이터베이스를 백업 하려면 스위치를 사용 하 `--database` 고 데이터베이스 이름을 공백으로 구분 하 여 나열 합니다.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>데이터베이스 또는 테이블 복원

다음 단계는 데이터베이스 또는 테이블을 복원 하는 방법을 보여 줍니다. 백업 파일을 만든 후 mysql 유틸리티를 사용 하 여 테이블 또는 데이터베이스를 복원할 수 있습니다. 다음 명령을 실행합니다.

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
다음은 `testdb` mysqldump를 사용 하 여 만든 백업 파일에서 복원 하는 예입니다. 

> [!Important]
> - 단일 서버의 경우 `admin-user@servername` 다음 명령에서 바꿀 형식을 사용 `myserveradmin` 합니다.
> - 유연한 서버의 경우 ```admin-user``` 다음 명령에서 바꿀 형식을 사용 `myserveradmin` 합니다. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>다음 단계
위의 단계를 수행 해도 문제가 해결 되지 않으면 전체 서버를 언제 든 지 복원할 수 있습니다.
- [Azure Database for MySQL에서 서버 복원-단일 서버](howto-restore-server-portal.md)
- [Azure Database for MySQL 유연한 서버에서 서버 복원](flexible-server/how-to-restore-server-portal.md)



