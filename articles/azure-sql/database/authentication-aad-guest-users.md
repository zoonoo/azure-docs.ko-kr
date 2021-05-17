---
title: Azure AD 게스트 사용자 만들기
description: Azure AD 게스트 사용자를 만든 다음 Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics에서 Azure AD 그룹을 사용하지 않고 Azure AD 관리자로 설정하는 방법
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675053"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Azure AD 게스트 사용자 만들기 및 Azure AD 관리자로 설정

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 이 문서는 **공개 미리 보기** 에 있습니다.

Azure AD(Azure Active Directory)의 게스트 사용자는 다른 Azure Active Directory 또는 그 외부에서 현재 Azure AD로 가져온 사용자입니다. 예를 들어 게스트 사용자는 다른 Azure Active directory의 사용자 또는 *\@outlook.com*, *\@hotmail.com*, *\@live.com* 또는 *\@gmail.com* 과 같은 계정의 사용자일 수 있습니다. 이 문서는 Azure AD 게스트 사용자를 만든 다음 해당 게스트 사용자를 Azure AD 내 그룹의 일원으로 지정할 필요 없이 Azure SQL 논리 서버의 Azure AD 관리자로 설정하는 방법을 보여 줍니다.

## <a name="feature-description"></a>기능 설명

이 기능은 게스트 사용자가 Azure AD에서 만든 그룹의 멤버인 경우에만 Azure SQL Database, SQL Managed Instance 또는 Azure Synapse Analytics에 연결할 수 있도록 허용하는 현재 제한 사항을 없앱니다. 이 그룹은 지정된 데이터베이스에서 [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) 문을 사용하여 수동으로 사용자에게 매핑해야 했습니다. 게스트 사용자를 포함하는 Azure AD 그룹에 대해 데이터베이스 사용자를 만든 후에는 게스트 사용자가 Azure Active Directory를 사용하여 MFA 인증으로 데이터베이스에 로그인할 수 있습니다. **퍼블릭 미리 보기** 의 일부로 게스트 사용자를 만들어 SQL Database, SQL Managed Instance 또는 Azure Synapse에 직접 연결할 수 있습니다. 이때, 게스트 사용자를 Azure AD 그룹에 먼저 추가한 다음 Azure AD 그룹의 데이터베이스 사용자를 만들 필요가 없습니다.

이 기능의 일부로 Azure AD 게스트 사용자를 Azure SQL 논리 서버에 대한 AD 관리자로 직접 설정하는 기능도 있습니다. 게스트 사용자가 Azure AD 그룹에 속할 수 있고 해당 그룹을 Azure SQL 논리 서버의 Azure AD 관리자로 설정할 수 있는 기존 기능은 영향을 받지 않습니다. Azure AD 그룹의 일원인 데이터베이스의 게스트 사용자 역시 이 변경의 영향을 받지 않습니다.

Azure AD 그룹을 사용하는 게스트 사용자에 대한 기존 지원에 대한 자세한 내용은 [다단계 Azure Active Directory 인증 사용](authentication-mfa-ssms-overview.md)을 참조하십시오.

## <a name="prerequisite"></a>필수 요소

- PowerShell을 사용하여 게스트 사용자를 Azure SQL 논리 서버의 Azure AD 관리자로 설정하는 경우 [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 모듈 이상이 필요합니다.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Azure AD 게스트 사용자에 대한 데이터베이스 사용자 만들기 

Azure AD 게스트 사용자를 사용하여 데이터베이스 사용자를 만들려면 다음 단계를 따르세요.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>SQL Database와 Azure Synapse에서 게스트 사용자 만들기

1. 게스트 사용자(예: `user1@gmail.com`)가 이미 Azure AD에 추가되어 있고 데이터베이스 서버의 Azure AD 관리자가 설정되었는지 확인합니다. Azure AD 관리자는 Azure Active Directory 인증에 필요합니다.

1. 충분한 SQL 권한을 가진 Azure AD 관리자 또는 Azure AD 사용자로 SQL 데이터베이스에 연결하여 사용자를 만들고 게스트 사용자를 추가해야 하는 데이터베이스에 대해 아래 명령을 실행합니다.

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 이제 게스트 사용자 `user1@gmail.com`을 위해 만든 데이터베이스 사용자가 있어야 합니다.

1. 아래 명령을 실행하여 데이터베이스 사용자가 성공적으로 생성되었는지 확인합니다.

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. 연결을 끊고 [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)에서 인증 방법 **Azure Active Directory - MFA가 지원되는 유니버설** 을 사용하여 게스트 사용자 `user1@gmail.com`으로 데이터베이스에 로그인합니다. 자세한 내용은 [다단계 Azure Active Directory 인증 사용](authentication-mfa-ssms-overview.md)을 참조하세요.

### <a name="create-guest-user-in-sql-managed-instance"></a>SQL Managed Instance에서 게스트 사용자 만들기

> [!NOTE]
> SQL Managed Instance는 Azure AD 사용자와 Azure AD에 포함된 데이터베이스 사용자에 대한 로그인을 지원합니다. 아래 단계에서는 SQL Managed Instance에서 Azure AD 게스트 사용자를 위해 로그인 및 사용자를 만드는 방법을 보여 줍니다. [SQL Database와 Azure Synapse에서 게스트 사용자 만들기](#create-guest-user-in-sql-database-and-azure-synapse) 섹션의 방법을 사용하여 SQL Managed Instance에 [포함된 데이터베이스 사용자](/sql/relational-databases/security/contained-database-users-making-your-database-portable)를 만들도록 선택할 수도 있습니다.

1. 게스트 사용자(예: `user1@gmail.com`)가 이미 Azure AD에 추가되어 있고 SQL Managed Instance 서버의 Azure AD 관리자가 설정되었는지 확인합니다. Azure AD 관리자는 Azure Active Directory 인증에 필요합니다.

1. 사용자를 만들 수 있는 SQL 권한이 있는 Azure AD 관리자 또는 Azure AD 사용자로 SQL Managed Instance 서버에 연결하고 `master` 데이터베이스에서 다음 명령을 실행하여 게스트 사용자에 대한 로그인을 만듭니다.

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 이제 `master` 데이터베이스에서 게스트 사용자 `user1@gmail.com`에 대해 생성된 로그인이 있습니다.

1. 아래 명령을 실행하여 해당 로그인이 성공적으로 생성되었는지 확인합니다.

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. 게스트 사용자를 추가해야 하는 데이터베이스에서 아래 명령을 실행합니다. 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. 이제 게스트 사용자 `user1@gmail.com`을 위해 만든 데이터베이스 사용자가 있어야 합니다.

1. 연결을 끊고 [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)에서 인증 방법 **Azure Active Directory - MFA가 지원되는 유니버설** 을 사용하여 게스트 사용자 `user1@gmail.com`으로 데이터베이스에 로그인합니다. 자세한 내용은 [다단계 Azure Active Directory 인증 사용](authentication-mfa-ssms-overview.md)을 참조하세요.

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>게스트 사용자를 Azure AD 관리자로 설정

Azure AD 게스트 사용자를 SQL 논리 서버의 Azure AD 관리자로 설정하려면 다음 단계를 수행합니다.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>SQL Database 및 Azure Synapse의 Azure AD 관리자 설정

1. 게스트 사용자(예: `user1@gmail.com`)가 Azure AD에 이미 추가되어 있는지 확인합니다.

1. 다음 PowerShell 명령을 실행하여 게스트 사용자를 Azure SQL 논리 서버의 Azure AD 관리자로 추가합니다.

    - `<ResourceGroupName>`을 Azure SQL 논리 서버를 포함하는 Azure 리소스 그룹 이름으로 바꿉니다.
    - `<ServerName>`을 Azure SQL 논리 서버 이름으로 바꿉니다. 서버 이름이 `myserver.database.windows.net`인 경우 `<Server Name>`을 `myserver`로 바꿉니다.
    - `<DisplayNameOfGuestUser>`를 게스트 사용자 이름으로 바꿉니다.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    Azure CLI 명령 [az sql server ad-admin](/cli/azure/sql/server/ad-admin)을 사용하여 게스트 사용자를 Azure SQL 논리 서버에 대한 Azure AD 관리자로 설정할 수도 있습니다.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>SQL Managed Instance의 Azure AD 관리자 설정

1. 게스트 사용자(예: `user1@gmail.com`)가 Azure AD에 이미 추가되어 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com), **Azure Active Directory** 리소스로 차례로 이동합니다. **관리** 에서 **사용자** 창으로 이동합니다. 게스트 사용자를 선택하고 `Object ID`를 기록합니다. 

1. 다음 PowerShell 명령을 실행하여 게스트 사용자를 SQL Managed Instance의 Azure AD 관리자로 추가합니다.

    - `<ResourceGroupName>`을 SQL Managed Instance를 포함하는 Azure 리소스 그룹 이름으로 바꿉니다.
    - `<ManagedInstanceName>`을 SQL Managed Instance 이름으로 바꿉니다.
    - `<DisplayNameOfGuestUser>`를 게스트 사용자 이름으로 바꿉니다.
    - `<AADObjectIDOfGuestUser>`를 이전에 수집한 `Object ID`로 바꿉니다.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    Azure CLI 명령 [az sql mi ad-admin](/cli/azure/sql/mi/ad-admin)을 사용하여 게스트 사용자를 SQL Managed Instance의 Azure AD 관리자로 설정할 수도 있습니다.

## <a name="limitations"></a>제한 사항

Azure Portal에는 Azure AD 게스트 사용자를 SQL Managed Instance의 Azure AD 관리자로 선택할 수 없도록 방지하는 제한이 있습니다. Azure AD 외부의 게스트 계정(예: *\@outlook.com*, *\@hotmail.com*, *\@live.com* 또는 *\@gmail.com*)의 경우 AD 관리자 선택기에 해당 계정이 표시되긴 하지만 회색으로 표시되어 선택할 수 없습니다. 위에 나열된 [PowerShell 또는 CLI 명령](#setting-a-guest-user-as-an-azure-ad-admin)을 사용하여 Azure AD 관리자를 설정합니다. 또는 게스트 사용자를 포함하는 Azure AD 그룹을 SQL Managed Instance의 Azure AD 관리자로 설정할 수 있습니다.

이 기능은 SQL Managed Instance에서 먼저 사용한 후 일반에게 공급될 예정입니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL에서 Azure AD 인증 구성 및 관리](authentication-aad-configure.md)
- [다단계 Azure Active Directory 인증 사용](authentication-mfa-ssms-overview.md)
- [CREATE USER(Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)