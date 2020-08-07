---
title: 관리 되는 인스턴스 간 복제 구성
titleSuffix: Azure SQL Managed Instance
description: 이 자습서에서는 Azure SQL Managed Instance 게시자/배포자와 SQL Managed Instance 구독자 간에 트랜잭션 복제를 구성 하는 방법을 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 114d4f41ad48af3d1e585fcb01eb0794a8e349b5
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920114"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>자습서: 두 개의 관리 되는 인스턴스 간에 복제 구성

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

트랜잭션 복제를 사용 하면 한 데이터베이스에서 SQL Server 또는 [AZURE SQL Managed Instance](sql-managed-instance-paas-overview.md)에 호스트 된 다른 데이터베이스로 데이터를 복제할 수 있습니다. SQL Managed Instance는 복제 토폴로지의 게시자, 배포자 또는 구독자 일 수 있습니다. 사용 가능한 구성에 대 한 [트랜잭션 복제 구성](replication-transactional-overview.md#common-configurations) 을 참조 하세요. 

트랜잭션 복제는 현재 SQL Managed Instance에 대 한 공개 미리 보기로 제공 됩니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - 관리 되는 인스턴스를 복제 게시자 및 배포자로 구성 합니다.
> - 관리되는 인스턴스를 복제 배포자로 구성합니다.

![두 개의 관리 되는 인스턴스 간 복제](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

이 자습서는 숙련된 사용자를 대상으로 하며, 사용자가 Azure 내에서 관리형 인스턴스와 SQL Server VM 모두를 배포하고 연결하는 데 익숙하다고 가정합니다. 


> [!NOTE]
> - 이 문서에서는 Azure SQL Managed Instance에서 [트랜잭션 복제](/sql/relational-databases/replication/transactional/transactional-replication) 를 사용 하는 방법을 설명 합니다. [장애 조치 (failover) 그룹과](../database/auto-failover-group-overview.md)는 관련이 없으며, 개별 인스턴스의 완전 한 읽기 가능한 복제본을 만들 수 있는 Azure SQL Managed Instance 기능입니다. [장애 조치 그룹을 사용 하 여 트랜잭션 복제를](replication-transactional-overview.md#with-failover-groups)구성할 때 추가로 고려해 야 할 사항이 있습니다.



## <a name="requirements"></a>요구 사항

게시자 및/또는 배포자로 작동 하도록 SQL Managed Instance를 구성 하려면 다음이 필요 합니다.

- 게시자 관리 되는 인스턴스는 배포자 및 구독자와 동일한 가상 네트워크에 있거나, 세 엔터티의 가상 네트워크 간에 [가상 네트워크 피어 링](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) 이 구성 되어 있습니다. 
- 연결은 복제 참가자 간에 SQL 인증을 사용합니다.
- 복제 작업 디렉터리에 대 한 Azure storage 계정 공유입니다.
- Azure 파일 공유에 액세스 하기 위해 관리 되는 인스턴스에 대 한 NSG의 보안 규칙에서 포트 445 (TCP 아웃 바운드)이 열립니다.  오류가 발생 하는 경우 `failed to connect to azure storage \<storage account name> with os error 53` 적절 한 SQL Managed Instance 서브넷의 NSG에 아웃 바운드 규칙을 추가 해야 합니다.

## <a name="1---create-a-resource-group"></a>1-리소스 그룹 만들기

[Azure Portal](https://portal.azure.com) 를 사용 하 여 이름이 인 리소스 그룹을 만듭니다 `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2-관리 되는 인스턴스 만들기

[Azure Portal](https://portal.azure.com) 를 사용 하 여 동일한 가상 네트워크 및 서브넷에서 두 개의 [SQL 관리 되는 인스턴스](instance-create-quickstart.md) 를 만듭니다. 예를 들어 두 개의 관리 되는 인스턴스의 이름을로 합니다.

- `sql-mi-pub`(임의 임의 문자를 포함 하는 일부 문자 포함)
- `sql-mi-sub`(임의 임의 문자를 포함 하는 일부 문자 포함)

또한 [AZURE VM을 구성](connect-vm-instance-configure.md) 하 여 관리 되는 인스턴스에 연결 해야 합니다. 

## <a name="3---create-an-azure-storage-account"></a>3-Azure storage 계정 만들기

작업 디렉터리에 대한 [Azure 스토리지 계정](/azure/storage/common/storage-create-storage-account#create-a-storage-account)을 만든 다음, 스토리지 계정 내에서 [파일 공유](../../storage/files/storage-how-to-create-file-share.md)를 만듭니다. 

파일 공유 경로를 `\\storage-account-name.file.core.windows.net\file-share-name` 형식으로 복사합니다.

예: `\\replstorage.file.core.windows.net\replshare`

다음 형식으로 저장소 액세스 키를 복사 합니다.`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

예: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

자세한 내용은 [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요. 

## <a name="4---create-a-publisher-database"></a>4-게시자 데이터베이스 만들기

`sql-mi-pub`SQL Server Management Studio를 사용 하 여 관리 되는 인스턴스에 연결 하 고 다음 transact-sql (t-sql) 코드를 실행 하 여 게시자 데이터베이스를 만듭니다.

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5-구독자 데이터베이스 만들기

`sql-mi-sub`SQL Server Management Studio를 사용 하 여 관리 되는 인스턴스에 연결 하 고 다음 t-sql 코드를 실행 하 여 빈 구독자 데이터베이스를 만듭니다.

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6-배포 구성

`sql-mi-pub`SQL Server Management Studio를 사용 하 여 관리 되는 인스턴스에 연결 하 고 다음 t-sql 코드를 실행 하 여 배포 데이터베이스를 구성 합니다.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-배포자를 사용 하도록 게시자 구성

게시자 SQL Managed Instance에서 `sql-mi-pub` 쿼리 실행을 [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 모드로 변경 하 고 다음 코드를 실행 하 여 새 배포자를 게시자에 등록 합니다.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > `\`File_storage 매개 변수에는 백슬래시 ()만 사용 해야 합니다. 슬래시(`/`)를 사용하면 파일 공유에 연결할 때 오류가 발생할 수 있습니다.

이 스크립트는 관리 되는 인스턴스에서 로컬 게시자를 구성 하 고, 연결 된 서버를 추가 하 고, SQL Server 에이전트에 대 한 작업 집합을 만듭니다.

## <a name="8---create-publication-and-subscriber"></a>8-게시 및 구독자 만들기

[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 모드에서 다음 t-sql 스크립트를 실행 하 여 데이터베이스에 대 한 복제를 사용 하도록 설정 하 고 게시자, 배포자 및 구독자 간의 복제를 구성 합니다.

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reader agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9-에이전트 매개 변수 수정

현재 Azure SQL Managed Instance에 복제 에이전트와의 연결에 대 한 몇 가지 백 엔드 문제가 발생 하 고 있습니다. 이 문제를 해결 하는 동안 해결 방법은 복제 에이전트에 대 한 로그인 제한 시간 값을 늘리는 것입니다.

게시자에서 다음 T-sql 명령을 실행 하 여 로그인 제한 시간을 늘립니다.

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

다음 T-sql 명령을 다시 실행 하 여 로그인 제한 시간을 기본값으로 다시 설정 합니다. 이렇게 하려면 다음을 수행 해야 합니다.

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

이러한 변경 내용을 적용 하려면 3 개의 에이전트를 모두 다시 시작 합니다.

## <a name="10---test-replication"></a>10-복제 테스트

복제가 구성되면 새 항목을 게시자에 삽입하고 구독자에 전파되는 변경 내용을 감시하여 이를 테스트할 수 있습니다.

다음 T-SQL 코드 조각을 실행하여 구독자의 행을 확인합니다.

```sql
select * from dbo.ReplTest
```

다음 T-SQL 코드 조각을 실행하여 추가 행을 게시자에 삽입한 다음, 구독자에서 해당 행을 다시 확인합니다.

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>리소스 정리

게시를 삭제 하려면 다음 T-sql 명령을 실행 합니다.

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

데이터베이스에서 복제 옵션을 제거 하려면 다음 T-sql 명령을 실행 합니다.

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

게시 및 배포를 사용 하지 않도록 설정 하려면 다음 T-sql 명령을 실행 합니다.

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

[리소스 그룹에서 SQL Managed Instance 리소스를 삭제](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) 한 다음 리소스 그룹을 삭제 하 여 Azure 리소스를 정리할 수 있습니다 `SQLMI-Repl` . 

## <a name="next-steps"></a>다음 단계

[AZURE sql Managed Instance를 사용 하 여 트랜잭션 복제](replication-transactional-overview.md) 에 대 한 자세한 내용을 보거나 [sql Managed Instance 게시자/배포자와 azure VM 구독자의 sql](replication-two-instances-and-sql-server-configure-tutorial.md)간에 복제를 구성 하는 방법에 대해 알아볼 수도 있습니다. 
