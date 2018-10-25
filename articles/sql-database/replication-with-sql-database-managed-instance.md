---
title: Azure SQL Database Managed Instance를 사용하여 복제 | Microsoft Docs
description: Azure SQL Database Managed Instance로 SQL Server 복제 사용에 대해 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 25d13ba53eb5a8b411a557b5eaf05d278faa3733
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869315"
---
# <a name="replication-with-sql-database-managed-instance"></a>SQL Database Managed Instance를 사용한 복제

복제는 [Azure SQL Database Managed Instance](sql-database-managed-instance.md)에서 공개 미리 보기로 제공됩니다. Managed Instance는 게시자, 배포자 및 구독자 데이터베이스를 호스트할 수 있습니다.

## <a name="common-configurations"></a>일반 구성

일반적으로 게시자와 배포자는 모두 클라우드 또는 온-프레미스에 있어야 합니다. 현재 지원되는 구성은 다음과 같습니다.

- **관리되는 인스턴스에서 로컬 배포자로 게시자**

   ![Replication-with-azure-sql-db-single-managed-instance-publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   게시자 및 배포자 데이터베이스는 단일 관리되는 인스턴스에서 구성됩니다.

- **관리되는 인스턴스에서 원격 배포자로 게시자**

   ![Replication-with-azure-sql-db-separate-managed-instances-publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   게시자 및 배포자는 두 개의 관리되는 인스턴스에서 구성됩니다. 이 구성에서:

  - 두 관리되는 인스턴스는 동일한 vNet에 있습니다.

  - 두 관리되는 인스턴스는 동일한 위치에 있습니다.

- **관리되는 인스턴스에서 구독자로 게시자 및 배포자 온-프레미스**

   ![Replication-from-on-premises-to-azure-sql-db-subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   이 구성에서 Azure SQL 데이터베이스는 구독자입니다. 이 구성은 온-프레미스에서 Azure로의 마이그레이션을 지원합니다. 구독자 역할에서 SQL 데이터베이스는 Managed Instance가 필요하지 않지만 온-프레미스에서 Azure로 마이그레이션의 단계로 SQL Database Managed Instance를 사용할 수 있습니다. Azure SQL Database 구독자에 대한 자세한 내용은 [SQL Database에 복제](replication-to-sql-database.md)를 참조하세요.

## <a name="requirements"></a>요구 사항

Azure SQL Database에서 게시자 및 배포자는 다음이 필요합니다.

- Azure SQL Database Managed Instance

   >[!NOTE]
   >Managed Instance로 구성되지 않은 Azure SQL Database는 구독자일 수만 있습니다.

- SQL Server의 모든 인스턴스는 동일한 vNet에 있어야 합니다.

- 연결은 복제 참가자 간에 SQL 인증을 사용합니다.

- 복제 작업 디렉터리에 대한 Azure Storage 계정 공유

## <a name="features"></a>기능

지원:

- 온-프레미스 및 Azure SQL Database Managed Instance 인스턴스의 트랜잭션 및 스냅숏 복제 조합

- 구독자는 온-프레미스, Azure SQL Database에서 단일 데이터베이스 또는 Azure SQL Database 탄력적 풀에서 풀링된 데이터베이스일 수 있습니다.

- 단방향 또는 양방향 복제

## <a name="configure-publishing-and-distribution-example"></a>게시 및 배포 예제 구성

1. 포털에서 [Azure SQL Database Managed Instance를 만듭니다](sql-database-managed-instance-create-tutorial-portal.md).
2. 작업 디렉터리에 대한 [Azure Storage 계정을 만듭니다](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account).

   저장소 키를 복사해야 합니다. [저장소 액세스 키 보기 및 복사](../storage/common/storage-account-manage.md#access-keys
)를 참조하세요.
3. 게시자에 대한 데이터베이스를 만듭니다.

   아래 예제 스크립트에서 `<Publishing_DB>`를 이 데이터베이스의 이름으로 바꿉니다.

4. 배포자에 대한 SQL 인증을 사용하여 데이터베이스 사용자를 만듭니다. [데이터베이스 사용자 만들기](http://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users)를 참조하세요. 보안 암호를 사용합니다.

   아래 예제 스크립트에서 이 SQL Server 계정 데이터베이스 사용자 및 암호로 `<SQL_USER>` 및 `<PASSWORD>`를 사용합니다.

5. [SQL Database Managed Instance에 연결합니다](http://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

6. 다음 쿼리를 실행하여 배포자 및 배포 데이터베이스를 추가합니다.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. 지정된 배포 데이터베이스를 사용하도록 게시자를 구성하려면 다음 쿼리를 업데이트하고 실행합니다.

   `<SQL_USER>` 및 `<PASSWORD>`를 SQL Server 계정 및 암호로 바꿉니다.

   `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>`를 저장소 계정 값으로 바꿉니다.  

   `<STORAGE_CONNECTION_STRING>`을 Microsoft Azure 저장소 계정의 **액세스 키** 탭에서 연결 문자열로 바꿉니다.

   다음 쿼리를 업데이트한 후 실행합니다.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. 복제에 대한 게시자를 구성합니다.

    다음 쿼리에서 `<Publishing_DB>`를 게시자 데이터베이스의 이름으로 바꿉니다.

    `<Publication_Name>`를 게시의 이름으로 바꿉니다.

    `<SQL_USER>` 및 `<PASSWORD>`를 SQL Server 계정 및 암호로 바꿉니다.

    쿼리를 업데이트한 후 실행하여 게시를 만듭니다.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. 문서, 구독 및 밀어넣기 구독 에이전트를 추가합니다.

   이러한 개체를 추가하려면 다음 스크립트를 업데이트합니다.

   - `<Object_Name>`을 게시 개체의 이름으로 바꿉니다.
   - `<Object_Schema>`를 원본 스키마의 이름으로 바꿉니다.
   - 꺾쇠 괄호(`<>`)의 다른 매개 변수를 이전 스크립트의 값과 일치하도록 바꿉니다.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```

## <a name="limitations"></a>제한 사항

다음 기능은 지원되지 않습니다.

- 업데이트할 수 있는 구독

- 활성 지역 복제

## <a name="see-also"></a>참고 항목

- [Managed Instance란?](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
