---
title: "MySQL용 Azure 데이터베이스에서 덤프 및 복원을 사용하여 MySQL Database 마이그레이션 | Microsoft Docs"
description: "MySQL용 Azure 데이터베이스 마이그레이션 작업을 소개합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/17/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: c0029e025cf6d0af478d1f21dc6acc7860905a81
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>덤프 및 복원을 사용하여 MySQL Database를 MySQL용 Azure 데이터베이스로 마이그레이션
이 문서에서는 MySQL용 Azure Database에서 데이터베이스를 백업 및 복원하는 2가지 일반적인 방법에 대해 설명합니다.
- 명령줄에서 백업 및 복원(mysqldump 사용) 
- PHPMyAdmin을 사용하여 백업 및 복원 

## <a name="before-you-begin"></a>시작하기 전에
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [MySQL용 Azure 데이터베이스 서버 만들기 - Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) 명령줄 유틸리티가 컴퓨터에 설치되어 있어야 함
- MySQL Workbench [MySQL Workbench 다운로드](https://dev.mysql.com/downloads/workbench/), Toad, Navicat 또는 타사 MySQL 도구

## <a name="use-common-tools"></a>일반 도구 사용
MySQL Workbench, mysqldump, Toad 또는 Navicat과 같은 일반 도구를 사용하여 원격으로 연결하고 MySQL용 Azure 데이터베이스에 데이터를 복원합니다. 인터넷에 연결된 클라이언트 컴퓨터에서 이러한 도구를 사용하여 MySQL용 Azure 데이터베이스에 데이터에 연결합니다. 최상의 보안을 위해 SSL 암호화 연결을 사용합니다. [MySQL용 Azure 데이터베이스에서 SSL 연결 구성](concepts-ssl-connection-security.md)을 참조하세요. MySQL용 Azure 데이터베이스로 마이그레이션할 때 덤프 파일을 특수한 클라우드 위치로 이동할 필요가 없습니다. 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>mysqldump를 사용하여 명령줄에서 백업 파일 만들기
온-프레미스 또는 VM에서 기존 MySQL 데이터베이스를 백업하려면 다음 명령을 실행합니다. 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

제공할 매개 변수:
- [uname] 데이터베이스 사용자 이름 
- [pass] 데이터베이스에 대한 암호(-p와 암호 사이를 띄어쓰지 말 것) 
- [dbname] 데이터베이스의 이름 
- [backupfile.sql] 데이터베이스 백업의 파일 이름 
- [-opt] mysqldump 옵션 

예를 들어 사용자 이름이 'testuser'이고 암호가 없는 'testdb'라는 데이터베이스를 파일 testdb_backup.sql에 백업하려면 다음 명령을 사용합니다. 이 명령은 'testdb' 데이터베이스를 다시 만드는 데 필요한 모든 SQL 문을 포함하는 testdb_backup.sql이라는 파일에 백업합니다. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
백업할 데이터베이스의 특정 테이블을 선택하려면 테이블 이름을 공백으로 구분해서 나열합니다. 예를 들어 'testdb'에서 table1 및 table2 테이블만 백업하려면 다음 예제를 따릅니다. 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

한 번에 둘 이상의 데이터베이스를 백업하려면 --database 스위치를 사용하고 데이터베이스 이름을 공백으로 구분합니다. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
서버의 모든 데이터베이스를 한 번에 백업하려면 --all-databases 옵션을 사용해야 합니다.
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>대상 Azure MySQL 서버에서 데이터베이스 만들기
MySQL Workbench, Toad, Navicat 또는 MySQL용 타사 도구를 사용하여 데이터를 마이그레이션하려는 대상 MySQL용 Azure Database 서버에서 빈 데이터베이스를 만들어야 합니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 같을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

![MySQL용 Azure 데이터베이스 연결 문자열](./media/concepts-migrate-import-export/p5.png)

![MySQL Workbench 연결 문자열](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>명령줄 또는 MySQL Workbench를 사용하여 MySQL 데이터베이스 복원
대상 데이터베이스를 만든 후에는 mysql 명령 또는 MySQL Workbench를 사용하여 덤프 파일에서 새로 만든 특정 데이터베이스로 데이터를 복원할 수 있습니다.
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
이 예제에서는 대상 서버에서 새로 만든 데이터베이스 testdb3에 데이터를 복원할 것입니다.
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>PHPMyAdmin을 사용하여 내보내기
내보내려면 환경에 로컬로 이미 설치했을 수 있는 일반 도구 phpMyAdmin을 사용할 수 있습니다. PHPMyAdmin을 사용하여 MySQL 데이터베이스를 내보내려면
- phpMyAdmin을 엽니다.
- 화면 왼쪽에 있는 목록에서 데이터베이스 이름을 클릭하여 데이터베이스를 선택합니다. 
- 내보내기 링크를 클릭합니다. 그러면 데이터베이스 덤프 보기라는 새 화면이 표시됩니다. 
- 내보내기 영역에서 모두 선택 링크를 클릭하여 데이터베이스의 모든 테이블을 선택합니다. 
- SQL 옵션 영역에서 적절한 옵션을 클릭합니다. 
- 파일로 저장 옵션 및 해당 압축 옵션을 클릭하고 '이동' 단추를 클릭합니다. 파일을 로컬로 저장할지 묻는 대화 상자가 나타납니다.

## <a name="import-using-phpmyadmin"></a>PHPMyAdmin을 사용하여 가져오기
데이터베이스 가져오기는 내보내기와 비슷합니다. 다음 작업을 수행합니다.
- phpMyAdmin을 엽니다. 
- 명명된 데이터베이스를 만들고 화면 왼쪽에 있는 목록에서 해당 데이터베이스 이름을 클릭하여 선택합니다. 기존 데이터베이스에 대해 가져오기를 다시 작성하려는 경우 데이터베이스 이름을 클릭하고, 테이블 이름 옆에 있는 확인란을 모두 선택한 후 삭제를 선택하여 데이터베이스의 기존 테이블을 모두 삭제합니다. 
- SQL 링크를 클릭합니다. 그러면 SQL 명령을 입력하거나 SQL 파일을 업로드할 수 있는 새 화면이 표시됩니다. 
- 찾아보기 단추를 사용하여 데이터베이스 파일을 찾습니다. 
- 이동 단추를 클릭합니다. 그러면 백업이 내보내지고, SQL 명령이 실행되고, 데이터베이스가 다시 만들어집니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 MySQL용 Azure 데이터베이스 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md) 
[Azure CLI를 사용하여 MySQL용 Azure 데이터베이스 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)

