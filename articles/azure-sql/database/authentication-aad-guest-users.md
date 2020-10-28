---
title: Azure AD 게스트 사용자 만들기
description: Azure ad 게스트 사용자를 만들고 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에서 Azure AD 그룹을 사용 하지 않고 azure AD 관리자로 설정 하는 방법
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675053"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Azure AD 게스트 사용자 만들기 및 Azure AD 관리자로 설정

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 이 문서는 **공개 미리 보기** 에 있습니다.

Azure Active Directory (Azure AD)의 게스트 사용자는 다른 Azure Active Directory 또는 그 외부에서 현재 Azure AD로 가져온 사용자입니다. 예를 들어 게스트 사용자는 다른 Azure Active directory의 사용자 또는 *\@ outlook.com* , *\@ hotmail.com* , *\@ live.com* , *\@ gmail.com* 등의 계정을 포함할 수 있습니다. 이 문서에서는 azure ad 게스트 사용자를 만들고 해당 게스트 사용자가 Azure AD 내에서 그룹의 일부가 될 필요 없이 azure SQL 논리 서버에 대 한 Azure AD 관리자로 설정 하는 방법을 보여 줍니다.

## <a name="feature-description"></a>기능 설명

이 기능은 게스트 사용자가 Azure AD에서 만든 그룹의 멤버인 경우에만 Azure SQL Database, SQL Managed Instance 또는 Azure Synapse Analytics에 연결할 수 있도록 하는 현재 제한 사항을 리프트 합니다. 지정 된 데이터베이스에서 [CREATE user (transact-sql)](/sql/t-sql/statements/create-user-transact-sql) 문을 사용 하 여 수동으로 그룹을 사용자에 게 매핑해야 합니다. 게스트 사용자를 포함 하는 Azure AD 그룹에 대해 데이터베이스 사용자를 만든 후에는 게스트 사용자가 MFA 인증을 사용 하 여 Azure Active Directory를 사용 하 여 데이터베이스에 로그인 할 수 있습니다. 이 **공개 미리 보기** 의 일부로 게스트 사용자는 먼저 azure ad 그룹에 추가한 다음 해당 azure ad 그룹에 대 한 데이터베이스 사용자를 만들 필요 없이 SQL DATABASE, SQL Managed Instance 또는 azure Synapse에 직접 연결할 수 있습니다.

이 기능의 일부로 azure AD 게스트 사용자를 Azure SQL 논리 서버에 대 한 AD 관리자로 직접 설정 하는 기능도 있습니다. 게스트 사용자가 Azure AD 그룹에 속할 수 있는 기존 기능으로, Azure SQL 논리 서버의 Azure AD 관리자가 영향을 받지 않으므로 해당 그룹을 설정할 수 있습니다. Azure AD 그룹의 일부인 데이터베이스의 게스트 사용자도이 변경의 영향을 받지 않습니다.

Azure AD 그룹을 사용 하는 게스트 사용자에 대 한 기존 지원에 대 한 자세한 내용은 [multi-factor Azure Active Directory 인증 사용](authentication-mfa-ssms-overview.md)을 참조 하세요.

## <a name="prerequisite"></a>필수 조건

- Azure SQL 논리 서버에 대 한 Azure AD 관리자로 PowerShell을 사용 하 여 게스트 사용자를 설정 하는 경우 [Az. Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) module 이상이 필요 합니다.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Azure AD 게스트 사용자에 대 한 데이터베이스 사용자 만들기 

Azure AD 게스트 사용자를 사용 하 여 데이터베이스 사용자를 만들려면 다음 단계를 따르세요.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>SQL Database 및 Azure Synapse에서 게스트 사용자 만들기

1. 게스트 사용자 (예: `user1@gmail.com` )가 AZURE ad에 이미 추가 되었고 데이터베이스 서버에 대 한 AZURE ad 관리자가 설정 되어 있는지 확인 합니다. Azure Active Directory 인증을 위해 Azure AD 관리자가 필요 합니다.

1. 사용자를 만들 수 있는 SQL 권한이 있는 azure AD 관리자 또는 Azure AD 사용자로 SQL database에 연결 하 고, 게스트 사용자를 추가 해야 하는 데이터베이스에서 아래 명령을 실행 합니다.

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 이제 게스트 사용자에 대해 만들어진 데이터베이스 사용자가 있습니다 `user1@gmail.com` .

1. 다음 명령을 실행 하 여 데이터베이스 사용자가 성공적으로 생성 되었는지 확인 합니다.

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. `user1@gmail.com` **MFA와 Azure Active Directory** 인증 방법을 사용 하 여 [SSMS (SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) )를 사용 하는 게스트 사용자로 데이터베이스의 연결을 끊고 로그인 합니다. 자세한 내용은 [multi-factor Azure Active Directory 인증 사용](authentication-mfa-ssms-overview.md)을 참조 하세요.

### <a name="create-guest-user-in-sql-managed-instance"></a>SQL Managed Instance에서 게스트 사용자 만들기

> [!NOTE]
> SQL Managed Instance는 azure ad 사용자 및 Azure AD에 포함 된 데이터베이스 사용자에 대 한 로그인을 지원 합니다. 아래 단계에서는 SQL Managed Instance에서 Azure AD 게스트 사용자에 대 한 로그인 및 사용자를 만드는 방법을 보여 줍니다. [SQL Database 및 Azure Synapse에서 게스트 사용자 만들기](#create-guest-user-in-sql-database-and-azure-synapse) 섹션의 메서드를 사용 하 여 SQL Managed Instance에서 [포함 된 데이터베이스 사용자](/sql/relational-databases/security/contained-database-users-making-your-database-portable) 를 만들도록 선택할 수도 있습니다.

1. 게스트 사용자 (예: `user1@gmail.com` )가 AZURE ad에 이미 추가 되었고 SQL Managed Instance 서버에 대 한 AZURE ad 관리자가 설정 되어 있는지 확인 합니다. Azure Active Directory 인증을 위해 Azure AD 관리자가 필요 합니다.

1. 사용자를 만들 수 있는 sql 권한이 있는 azure ad 관리자 또는 Azure AD 사용자로 SQL Managed Instance 서버에 연결 하 고 데이터베이스에서 다음 명령을 실행 `master` 하 여 게스트 사용자에 대 한 로그인을 만듭니다.

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 이제 데이터베이스에서 게스트 사용자에 대해 생성 된 로그인이 있습니다 `user1@gmail.com` `master` .

1. 아래 명령을 실행 하 여 로그인이 성공적으로 만들어졌는지 확인 합니다.

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. 게스트 사용자를 추가 해야 하는 데이터베이스에서 아래 명령을 실행 합니다. 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. 이제 게스트 사용자에 대해 만들어진 데이터베이스 사용자가 있습니다 `user1@gmail.com` .

1. `user1@gmail.com` **MFA와 Azure Active Directory** 인증 방법을 사용 하 여 [SSMS (SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) )를 사용 하는 게스트 사용자로 데이터베이스의 연결을 끊고 로그인 합니다. 자세한 내용은 [multi-factor Azure Active Directory 인증 사용](authentication-mfa-ssms-overview.md)을 참조 하세요.

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>게스트 사용자를 Azure AD 관리자로 설정

Azure AD 게스트 사용자를 SQL 논리 서버에 대 한 Azure AD 관리자로 설정 하려면 다음 단계를 수행 합니다.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>SQL Database 및 Azure Synapse에 대 한 Azure AD 관리자 설정

1. 게스트 사용자 (예: `user1@gmail.com` )가 AZURE AD에 이미 추가 되어 있는지 확인 합니다.

1. 다음 PowerShell 명령을 실행 하 여 게스트 사용자를 Azure SQL 논리 서버에 대 한 Azure AD 관리자로 추가 합니다.

    - `<ResourceGroupName>`을 AZURE SQL 논리 서버를 포함 하는 Azure 리소스 그룹 이름으로 바꿉니다.
    - `<ServerName>`을 AZURE SQL 논리 서버 이름으로 바꿉니다. 서버 이름이 `myserver.database.windows.net`인 경우 `<Server Name>`을 `myserver`로 바꿉니다.
    - `<DisplayNameOfGuestUser>`을 게스트 사용자 이름으로 바꿉니다.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    Azure CLI 명령 [az sql server ad-admin](/cli/azure/sql/server/ad-admin) 을 사용 하 여 게스트 사용자를 azure sql 논리 서버에 대 한 azure ad 관리자로 설정할 수도 있습니다.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>SQL Managed Instance에 대 한 Azure AD 관리자 설정

1. 게스트 사용자 (예: `user1@gmail.com` )가 AZURE AD에 이미 추가 되어 있는지 확인 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **Azure Active Directory** 리소스로 이동 합니다. **관리** 아래에서 **사용자** 창으로 이동 합니다. 게스트 사용자를 선택 하 고을 기록 `Object ID` 합니다. 

1. 다음 PowerShell 명령을 실행 하 여 게스트 사용자를 SQL Managed Instance에 대 한 Azure AD 관리자로 추가 합니다.

    - 을 `<ResourceGroupName>` SQL Managed Instance를 포함 하는 Azure 리소스 그룹 이름으로 바꿉니다.
    - `<ManagedInstanceName>`을 SQL Managed Instance 이름으로 바꿉니다.
    - `<DisplayNameOfGuestUser>`을 게스트 사용자 이름으로 바꿉니다.
    - `<AADObjectIDOfGuestUser>`앞에서 수집한로 대체 합니다 `Object ID` .

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    Azure CLI [명령을 사용 하 여](/cli/azure/sql/mi/ad-admin) sql Managed Instance에 대 한 Azure ad 관리자로 게스트 사용자를 설정할 수도 있습니다.

## <a name="limitations"></a>제한 사항

Azure AD 게스트 사용자를 SQL Managed Instance에 대 한 Azure AD 관리자로 선택할 수 없도록 하는 Azure Portal에는 제한이 있습니다. *\@ Outlook.com* , *\@ hotmail.com* , *\@ live.com* 또는 *\@ gmail.com* 와 같은 Azure ad 외부의 게스트 계정의 경우 AD 관리 선택기는 이러한 계정을 표시 하지만 회색으로 표시 되며 선택할 수 없습니다. 위에 나열 된 [PowerShell 또는 CLI 명령을](#setting-a-guest-user-as-an-azure-ad-admin) 사용 하 여 Azure AD 관리자를 설정 합니다. 또는 게스트 사용자를 포함 하는 Azure AD 그룹을 SQL Managed Instance에 대 한 Azure AD 관리자로 설정할 수 있습니다.

이 기능은이 기능을 일반 공급 하기 전에 SQL Managed Instance 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL에서 Azure AD 인증 구성 및 관리](authentication-aad-configure.md)
- [Multi-factor Azure Active Directory 인증 사용](authentication-mfa-ssms-overview.md)
- [CREATE USER(Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)