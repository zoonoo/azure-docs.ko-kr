---
title: 관리되는 인스턴스 간 복제 구성
titleSuffix: Azure SQL Managed Instance
description: 이 자습서에서는 Azure SQL Managed Instance 게시자/배포자와 SQL Managed Instance 구독자 간에 트랜잭션 복제를 구성하는 방법을 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: b0f2a6fcd888afd7eb99a810fad6e876fe6ff4ac
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617063"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>자습서: 두 관리형 인스턴스 간 복제 구성

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

트랜잭션 복제를 사용하면 한 데이터베이스의 SQL Server 또는 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)에서 호스팅되는 다른 데이터베이스로 데이터를 복제할 수 있습니다. SQL Managed Instance는 복제 토폴로지의 게시자, 배포자 또는 구독자일 수 있습니다. 사용 가능한 구성에 대해서는 [트랜잭션 복제 구성](replication-transactional-overview.md#common-configurations)을 참조하세요. 

트랜잭션 복제는 현재 SQL Managed Instance에 대한 공개 미리 보기로 제공됩니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - 관리되는 인스턴스를 복제 게시자 및 배포자로 구성합니다.
> - 관리되는 인스턴스를 복제 배포자로 구성합니다.

![두 관리형 인스턴스 간 복제](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

이 자습서는 숙련된 사용자를 대상으로 하며, 사용자가 Azure 내에서 관리형 인스턴스와 SQL Server VM 모두를 배포하고 연결하는 데 익숙하다고 가정합니다. 


> [!NOTE]
> - 이 문서에서는 Azure SQL Managed Instance에서 [트랜잭션 복제](/sql/relational-databases/replication/transactional/transactional-replication)를 사용하는 방법을 설명합니다. 개별 인스턴스의 전체 읽기 가능 복제본을 만들 수 있는 Azure SQL Managed Instance 기능인 [장애 조치(failover) 그룹](../database/auto-failover-group-overview.md)과는 관련이 없습니다. [장애 조치(failover) 그룹을 사용하여 트랜잭션 복제](replication-transactional-overview.md#with-failover-groups)를 구성할 때 추가 고려 사항이 있습니다.



## <a name="requirements"></a>요구 사항

SQL Managed Instance가 게시자 및/또는 배포자로 작동하도록 구성할 때 요구 사항은 다음과 같습니다.

- 게시자 관리형 인스턴스가 배포자 및 구독자와 동일한 가상 네트워크에 있거나 [가상 네트워크 피어링](../../virtual-network/tutorial-connect-virtual-networks-powershell.md)이 세 개 엔터티 모두의 가상 네트워크 간에 구성되어 있습니다. 
- 연결은 복제 참가자 간에 SQL 인증을 사용합니다.
- 복제 작업 디렉터리에 대한 Azure Storage 계정 공유
- 관리되는 인스턴스에서 Azure 파일 공유에 액세스할 수 있도록 NSG 보안 규칙의 445 포트(TCP 아웃바운드)가 열려 있습니다.  `failed to connect to azure storage \<storage account name> with os error 53` 오류가 발생하면 적절한 SQL Managed Instance 서브넷의 NSG에 아웃바운드 규칙을 추가해야 합니다.

## <a name="1---create-a-resource-group"></a>1 - 리소스 그룹 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 이름이 `SQLMI-Repl`인 리소스 그룹을 만듭니다.  

## <a name="2---create-managed-instances"></a>2 - 관리되는 인스턴스 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 동일한 가상 네트워크 및 서브넷에 [SQL Managed Instance](instance-create-quickstart.md) 두 개를 만듭니다. 예를 들어 두 개의 관리되는 인스턴스에 다음과 같은 이름을 지정합니다.

- `sql-mi-pub`(임의 지정을 위한 일부 문자 포함)
- `sql-mi-sub`(임의 지정을 위한 일부 문자 포함)

또한 관리되는 인스턴스에 [연결할 Azure VM을 구성](connect-vm-instance-configure.md)해야 합니다. 

## <a name="3---create-an-azure-storage-account"></a>3 - Azure 스토리지 계정 만들기

작업 디렉터리에 대한 [Azure 스토리지 계정](/azure/storage/common/storage-create-storage-account#create-a-storage-account)을 만든 다음, 스토리지 계정 내에서 [파일 공유](../../storage/files/storage-how-to-create-file-share.md)를 만듭니다. 

파일 공유 경로를 `\\storage-account-name.file.core.windows.net\file-share-name` 형식으로 복사합니다.

예: `\\replstorage.file.core.windows.net\replshare`

`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net` 형식의 스토리지 액세스 키를 복사합니다.

예: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

자세한 내용은 [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요. 

## <a name="4---create-a-publisher-database"></a>4 - 게시자 데이터베이스 만들기

SQL Server Management Studio를 사용하여 관리되는 인스턴스 `sql-mi-pub`에 연결하고 다음 T-SQL(Transact-SQL) 코드를 실행하여 구독자 데이터베이스를 만듭니다.

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

## <a name="5---create-a-subscriber-database"></a>5 - 구독자 데이터베이스 만들기

SQL Server Management Studio를 사용하여 관리되는 인스턴스 `sql-mi-sub`에 연결하고 다음 T-SQL 코드를 실행하여 빈 구독자 데이터베이스를 만듭니다.

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

## <a name="6---configure-distribution"></a>6 - 배포 구성

SQL Server Management Studio를 사용하여 관리되는 인스턴스 `sql-mi-pub`에 연결하고 다음 T-SQL 코드를 실행하여 배포 데이터베이스를 구성합니다.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - 배포자를 사용하도록 게시자 구성

게시자 SQL Managed Instance `sql-mi-pub`에서 쿼리 실행을 [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 모드로 변경하고 다음 코드를 실행하여 새 배포자를 게시자에 등록합니다.

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
   > file_storage 매개 변수에는 백슬래시(`\`)만 사용해야 합니다. 슬래시(`/`)를 사용하면 파일 공유에 연결할 때 오류가 발생할 수 있습니다.

이 스크립트는 관리되는 인스턴스에서 로컬 게시자를 구성하고, 연결된 서버를 추가하고, SQL Server 에이전트에 대한 작업 세트를 만듭니다.

## <a name="8---create-publication-and-subscriber"></a>8 - 게시 및 구독자 만들기

[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 모드를 통해 다음 T-SQL 스크립트를 실행하여 데이터베이스 복제를 사용하도록 설정하고, 게시자, 배포자 및 구독자 간에 복제를 구성합니다.

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

## <a name="9---modify-agent-parameters"></a>9 - 에이전트 매개 변수 수정

현재 Azure SQL Managed Instance에는 복제 에이전트와의 연결과 관련한 몇 가지 백 엔드 문제가 발생하고 있습니다. 이 문제가 완전히 해결될 때까지 임시 해결 방법은 복제 에이전트의 로그인 제한 시간 값을 늘리는 것입니다.

게시자에서 다음 T-SQL 명령을 실행하여 로그인 제한 시간을 늘립니다.

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

다음 T-SQL 명령을 다시 실행하여 로그인 제한 시간을 기본값으로 다시 설정합니다(그렇게 해야 하는 경우).

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

이러한 변경 내용을 적용하려면 3개의 에이전트를 모두 다시 시작합니다.

## <a name="10---test-replication"></a>10 - 복제 테스트

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

게시를 삭제하려면 다음 T-SQL 명령을 실행합니다.

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

데이터베이스에서 복제 옵션을 제거하려면 다음 T-SQL 명령을 실행합니다.

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

게시 및 배포를 사용하지 않으려면 다음 T-SQL 명령을 실행합니다.

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

[리소스 그룹에서 SQL Managed Instance 리소스를 삭제](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources)한 후 리소스 그룹 `SQLMI-Repl`을 삭제하여 Azure 리소스를 정리할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

또한 [Azure SQL Managed Instance를 사용한 트랜잭션 복제](replication-transactional-overview.md)에 대해 자세히 알아보거나 [SQL Managed Instance 게시자/배포자와 Azure의 SQL VM 구독자](replication-two-instances-and-sql-server-configure-tutorial.md) 간에 복제를 구성하는 방법을 알아볼 수도 있습니다. 
