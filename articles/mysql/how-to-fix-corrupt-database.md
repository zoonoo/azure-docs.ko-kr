---
title: 데이터베이스 손상 해결-Azure Database for MySQL
description: Azure Database for MySQL에 대 한 데이터베이스 손상 문제를 해결 하는 방법에 대해 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938934"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Azure Database for MySQL의 데이터베이스 손상 문제 해결
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

데이터베이스 손상은 응용 프로그램의 가동 중지 시간이 발생할 수 있으며 데이터 손실을 방지 하기 위해 문제를 해결 하는 것도 중요 합니다. 데이터베이스가 손상 되 면 서버에서이 오류 **InnoDB: 디스크의 데이터베이스 페이지 손상 또는 실패를**표시 합니다.

이 가이드에서는 데이터베이스 또는 테이블이 손상 되었는지 여부를 해결 하는 방법을 설명 합니다. Azure Database for MySQL InnoDB 엔진을 사용 하 고 자동화 된 손상 확인 및 복구 작업을 제공 합니다. InnoDB는 읽은 모든 페이지에서 체크섬을 수행 하 여 손상 된 페이지를 확인 하 고 체크섬 불일치를 발견 하면 MySQL 서버를 자동으로 중지 합니다.

아래 옵션 중 하나를 시도 하 여 데이터베이스 손상 문제를 신속 하 게 완화할 수 있습니다.

## <a name="restart-your-mysql-server"></a>MySQL 서버 다시 시작

일반적으로 응용 프로그램에서 해당 테이블을 액세스할 때 데이터베이스 또는 테이블이 손상 된 것을 알 수 있습니다. InnoDB는 서버를 다시 시작할 때 대부분의 문제를 해결할 수 있는 충돌 복구 메커니즘을 갖추고 있습니다. 따라서 서버를 다시 시작 하면 데이터베이스의 상태가 잘못 된 것으로 발생 한 크래시에서 서버를 복구 하는 데 도움이 됩니다.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>덤프 및 복원 방법을 사용 하 여 데이터 손상 해결

**덤프 및 복원 방법**으로 손상 문제를 해결 하는 것이 좋습니다. 이를 위해서는 **mysqldump** 유틸리티를 사용 하 여 테이블의 논리적 백업을 만들어 테이블 구조와 테이블 내 데이터를 유지 한 다음 테이블을 다시 데이터베이스로 다시 로드 하는 방법으로 손상 된 테이블에 대 한 액세스 권한을 얻을 수 있습니다.

### <a name="backup-your-database-or-tables"></a>데이터베이스 또는 테이블 백업

> [!Important]
> - 클라이언트 컴퓨터에서 서버에 액세스 하기 위해 방화벽 규칙을 구성 하도록 합니다. [단일 서버에서 방화벽 규칙](howto-manage-firewall-using-portal.md) 을 구성 하는 방법 및 [유연한 서버에서 방화벽 규칙](flexible-server/how-to-connect-tls-ssl.md)을 참조 하세요.
> - ```--ssl-cert```Ssl을 사용 하는 경우 **MYSQLDUMP** 에 ssl 옵션을 사용 합니다.

명령줄에서 다음 명령을 사용 하 여 mysqldump를 사용 하 여 백업 파일을 만듭니다.

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

제공할 매개 변수:
- [ssl-cert =/path/to/pem] 클라이언트 컴퓨터에서 SSL 인증서를 다운로드 하 고 명령에서 해당 인증서의 경로를 설정 합니다. 사용 안 함 SSL이 사용 하지 않도록 설정 되어 있습니다.
- [host]는 Azure Database for MySQL 서버입니다.
- [uname]은 서버 관리자 사용자 이름입니다.
- [pass]는 관리 사용자의 암호입니다.
- [dbname]은 데이터베이스의 이름입니다.
- [backupfile .sql] 데이터베이스 백업에 대 한 파일 이름입니다.

> [!Important]
> - 단일 서버에 대해 ```admin-user@servername``` ```myserveradmin``` 아래 명령에서 바꿀 형식을 사용 합니다.
> - 유연한 서버의 경우 ```admin-user``` ```myserveradmin``` 아래 명령에서 바꿀 형식을 사용 합니다.

특정 테이블이 손상 된 경우 다음 예제를 사용 하 여 백업할 데이터베이스의 특정 테이블을 선택 합니다.
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

하나 이상의 데이터베이스를 백업 하려면--database 스위치를 사용 하 고 데이터베이스 이름을 공백으로 구분 하 여 나열 합니다.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>데이터베이스 또는 테이블 복원

다음 단계는 tp에서 데이터베이스를 복원 하는 방법을 보여 줍니다. 백업 파일을 만든 후에는 ***mysql** 유틸리티를 사용 하 여 테이블 또는 데이터베이스를 복원할 수 있습니다. 아래와 같이 명령을 실행 합니다.

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
다음은 ```testdb``` **mysqldump**를 사용 하 여 만든 백업 파일에서 복원 하는 예입니다. 

> [!Important]
> - 단일 서버에 대해 ```admin-user@servername``` ```myserveradmin``` 아래 명령에서 바꿀 형식을 사용 합니다.
> - 유연한 서버의 경우 ```admin-user``` ```myserveradmin``` 아래 명령에서 바꿀 형식을 사용 합니다. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>다음 단계
위의 단계를 수행 해도 문제가 해결 되지 않으면 전체 서버를 언제 든 지 복원할 수 있습니다.
- [단일 서버 Azure Database for MySQL 복원](howto-restore-server-portal.md)
- [유연한 서버 Azure Database for MySQL 복원](flexible-server/how-to-restore-server-portal.md)



