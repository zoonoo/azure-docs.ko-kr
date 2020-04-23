---
title: 로그인 및 사용자 계정을 사용 하 여 서버 및 데이터베이스 액세스 권한 부여
description: 로그인 및 사용자 계정을 사용 하 여 Azure SQL Database 및 Azure Synapse Analytics에서 사용자에 게 액세스를 인증 하는 방법에 대해 알아봅니다. 또한 로그인 및 사용자에 게 작업을 수행 하 고 데이터를 쿼리 하는 권한을 부여 하는 명시적 사용 권한과 데이터베이스 역할에 대해 알아봅니다.
keywords: sql 데이터베이스 보안,데이터베이스 보안 관리,로그인 보안,데이터베이스 보안,데이터베이스 액세스
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 0f1611e6d3524cc78fc20fed9d1aac6f3fd453fa
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106443"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>로그인 및 사용자 계정을 사용 하 여 SQL Database 및 Azure Synapse Analytics에 대해 인증 된 사용자에 게 데이터베이스 액세스 권한 부여

이 문서에서는 다음에 대해 알아봅니다.

- 사용자가 관리 작업을 수행 하 고 이러한 데이터베이스에 저장 된 데이터에 액세스할 수 있도록 Azure SQL Database 및 Azure Synapse Analytics (이전의 Azure SQL Data Warehouse)를 구성 하는 옵션입니다.
- 처음으로 새 Azure SQL Database를 만든 후의 액세스 및 권한 부여 구성
- Master 데이터베이스 및 사용자 계정에 로그인 및 사용자 계정을 추가한 다음 이러한 계정에 관리자 권한을 부여 하는 방법
- 로그인 또는 포함 된 사용자 계정으로 사용자 데이터베이스의 사용자 계정을 추가 하는 방법
- 데이터베이스 역할 및 명시적 사용 권한을 사용 하 여 사용자 데이터베이스에서 사용 권한이 있는 사용자 계정 구성

> [!IMPORTANT]
> Azure SQL Database 및 Azure Synapse Analytics (이전의 Azure SQL Data Warehouse)의 데이터베이스는이 문서의 나머지 부분에서 데이터베이스 또는 Azure SQL (간단히 설명)로 통칭 됩니다.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

[**인증은**](sql-database-security-overview.md#authentication) 사용자를 증명 하는 사용자를 증명 하는 프로세스입니다. 사용자가 사용자 계정을 사용 하 여 데이터베이스에 연결 합니다.
사용자는 데이터베이스에 대 한 연결을 시도할 때 사용자 계정 및 인증 정보를 제공 합니다. 사용자는 다음 두 가지 인증 방법 중 하나를 사용 하 여 인증 됩니다.

- [SQL 인증](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  이 인증 방법을 사용 하는 경우 사용자는 사용자 계정 이름 및 연결 된 암호를 제출 하 여 연결을 설정 합니다. 이 암호는 로그인에 연결 되거나 로그인에 연결 *되지 않은* 사용자 계정을 포함 하는 데이터베이스에 저장 된 사용자 계정의 master 데이터베이스에 저장 됩니다.
- [Azure Active Directory 인증](sql-database-aad-authentication.md)

  이 인증 방법을 사용 하는 경우 사용자는 사용자 계정 이름을 제출 하 고 Azure Active Directory에 저장 된 자격 증명 정보를 서비스에서 사용 하도록 요청 합니다.

**로그인 및 사용자**: Azure SQL에서는 데이터베이스의 사용자 계정을 master 데이터베이스에 저장 된 로그인과 연결 하거나 개별 데이터베이스에 저장 된 사용자 이름일 수 있습니다.

- **로그인** 은 하나 이상의 데이터베이스에 있는 사용자 계정이 연결 될 수 있는 master 데이터베이스의 개별 계정입니다. 로그인을 사용 하면 사용자 계정에 대 한 자격 증명 정보가 로그인과 함께 저장 됩니다.
- **사용자 계정은** 데이터베이스의 개별 계정 이지만 로그인에 연결 될 수는 없습니다. 로그인에 연결 되지 않은 사용자 계정을 사용 하 여 자격 증명 정보는 사용자 계정으로 저장 됩니다.

데이터에 액세스 하 고 다양 한 작업을 수행 하기 위한 [**권한 부여**](sql-database-security-overview.md#authorization) 는 데이터베이스 역할과 명시적 사용 권한을 사용 하 여 관리 됩니다. 권한 부여는 사용자에 게 할당 된 사용 권한을 나타내며 사용자가 수행할 수 있는 작업을 결정 합니다. 권한 부여는 사용자 계정의 데이터베이스 [역할 멤버 자격과](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) [개체 수준 권한](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)으로 제어 됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>새 데이터베이스를 만든 후의 기존 로그인 및 사용자 계정

첫 번째 Azure SQL 배포를 만들 때 해당 로그인에 대 한 관리자 로그인 및 연결 된 암호를 지정 합니다. 이 관리 계정을 **서버 관리자**라고 합니다. Master 및 사용자 데이터베이스에서 다음 로그인 및 사용자 구성은 배포 중에 발생 합니다.

- 지정한 로그인 이름을 사용 하 여 관리 권한이 있는 SQL 로그인을 만듭니다. [로그인](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) 은 SQL Database에 로그온 하기 위한 개별 사용자 계정입니다.
- 이 로그인에는 [서버 수준 보안 주체로](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)모든 데이터베이스에 대 한 모든 관리 권한이 부여 됩니다. 이 로그인은 SQL Database 내에서 사용 가능한 모든 권한을 가지 며 제한할 수 없습니다. 관리 되는 인스턴스에서는이 로그인이 [sysadmin 고정 서버 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) 에 추가 됩니다 .이 역할은 단일 데이터베이스 또는 풀링된 데이터베이스와 함께 존재 하지 않습니다.
- 각 사용자 데이터베이스에서 `dbo` 이 로그인에 대해 라는 [사용자 계정이](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) 생성 됩니다. [Dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) 사용자는 데이터베이스의 모든 데이터베이스 사용 권한을 가지 며 `db_owner` 고정 데이터베이스 역할에 매핑됩니다. 추가 고정 데이터베이스 역할에 대해서는이 문서의 뒷부분에서 설명 합니다.

데이터베이스에 대 한 관리자 계정을 식별 하려면 Azure Portal을 열고 서버 또는 관리 되는 인스턴스의 **속성** 탭으로 이동 합니다.

![SQL Server 관리자](media/sql-database-manage-logins/sql-admins.png)

![SQL Server 관리자](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> 관리자 로그인 이름을 만든 후에는 변경할 수 없습니다. 논리 서버 관리자에 대 한 암호를 다시 설정 하려면 [Azure Portal](https://portal.azure.com)로 이동 하 고 **SQL**server를 클릭 한 다음 목록에서 서버를 선택 하 고 **암호 다시 설정**을 클릭 합니다. 관리 되는 인스턴스 서버에 대 한 암호를 다시 설정 하려면 Azure Portal로 이동 하 여 인스턴스를 클릭 하 고 **암호 다시 설정**을 클릭 합니다. PowerShell 또는 Azure CLI를 사용할 수도 있습니다.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>관리 권한이 있는 추가 로그인 및 사용자 만들기

이 시점에서 Azure SQL 인스턴스는 단일 SQL 로그인 및 사용자 계정을 사용 하는 액세스에 대해서만 구성 됩니다. 전체 또는 부분 관리 권한으로 추가 로그인을 만들려면 배포 모드에 따라 다음과 같은 옵션을 사용할 수 있습니다.

- **모든 관리 권한이 있는 Azure Active Directory 관리자 계정 만들기**

  Azure Active Directory 인증을 사용 하도록 설정 하 고 Azure AD 관리자 로그인을 만듭니다. 모든 관리 권한이 있는 SQL Database 배포의 관리자로 하나의 Azure Active Directory 계정을 구성할 수 있습니다. 이 계정은 개인 또는 보안 그룹 계정일 수 있습니다. Azure AD 계정을 사용 하 여 SQL Database에 연결 하려면 Azure AD 관리자를 구성 **해야 합니다** . 모든 SQL Database 배포 유형에 대해 Azure AD 인증을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

  - [SQL에서 인증을 위해 Azure Active Directory 인증 사용](sql-database-aad-authentication.md)
  - [SQL을 사용하여 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md)

- **관리 되는 인스턴스 배포에서 모든 관리 권한이 있는 SQL 로그인을 만듭니다.**

  - 관리 되는 인스턴스에서 추가 SQL Server 로그인 만들기
  - [ALTER SERVER role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) 문을 사용 하 여 [sysadmin 고정 서버 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) 에 로그인을 추가 합니다. 이 로그인에는 모든 관리 권한이 부여 됩니다.
  - 또는 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE login</a> 구문을 사용 하 여 [Azure AD 로그인](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) 을 만듭니다.

- **단일 또는 풀링된 배포에서 관리 권한이 제한 된 SQL 로그인을 만듭니다.**

  - Master 데이터베이스에서 단일 또는 풀링된 데이터베이스 배포 또는 관리 되는 인스턴스 배포에 대 한 추가 SQL 로그인을 만듭니다.
  - 이 새 로그인과 연결 된 master 데이터베이스에서 사용자 계정 만들기
  - [ALTER SERVER role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) 문을 사용 하 `dbmanager`여 `master` 데이터베이스 `loginmanager` 에서, 역할 또는 둘 다에 사용자 계정을 추가 합니다 (Azure Synapse Analytics의 경우 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 문을 사용).

  > [!NOTE]
  > `dbmanager`및 `loginmanager` 역할은 관리 되는 인스턴스 배포와 관련이 **없습니다** .

  단일 또는 풀링된 데이터베이스에 대 한 이러한 [특수 마스터 데이터베이스 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) 의 멤버는 사용자가 데이터베이스를 생성 및 관리 하거나 로그인을 만들고 관리할 수 있도록 합니다. `dbmanager` 역할의 멤버인 사용자가 만든 데이터베이스에서 멤버는 `db_owner` 고정 데이터베이스 역할에 매핑되고 `dbo` 사용자 계정을 사용 하 여 해당 데이터베이스에 로그인 하 고 해당 데이터베이스를 관리할 수 있습니다. 이러한 역할은 master 데이터베이스 외부에서 명시적 사용 권한이 없습니다.

  > [!IMPORTANT]
  > 단일 데이터베이스 또는 풀링된 데이터베이스에서 전체 관리 권한이 있는 추가 SQL 로그인을 만들 수 없습니다.

## <a name="create-accounts-for-non-administrator-users"></a>비관리자 사용자에 대 한 계정 만들기

다음 두 가지 방법 중 하나를 사용 하 여 관리자가 아닌 사용자에 대 한 계정을 만들 수 있습니다.

- **로그인을 만듭니다.**

  Master 데이터베이스에서 SQL 로그인을 만듭니다. 그런 다음 해당 사용자가 액세스 해야 하는 각 데이터베이스에서 사용자 계정을 만들고 사용자 계정을 해당 로그인에 연결 합니다. 사용자가 여러 데이터베이스에 액세스 해야 하 고 암호를 동기화 된 상태로 유지 하려는 경우이 방법을 사용 하는 것이 좋습니다. 그러나이 접근 방식은 주 서버와 보조 서버 모두에서 로그인을 만들어야 하므로 지역에서 복제와 함께 사용 하는 경우 복잡성이 있습니다. 자세한 내용은 [지역 복원 또는 장애 조치 (failover)를 위한 Azure SQL Database 보안 구성 및 관리](sql-database-geo-replication-security-config.md)를 참조 하세요.
- **사용자 계정 만들기**

  사용자가 액세스 해야 하는 데이터베이스에서 사용자 계정을 만듭니다 ( [포함 된 사용자](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)라고도 함).

  - 단일 또는 풀링된 데이터베이스를 사용 하 여 언제 든 지이 유형의 사용자 계정을 만들 수 있습니다.
  - [AZURE AD 서버 보안 주체](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)를 지원 하지 않는 관리 되는 인스턴스 데이터베이스에서는 [포함 된 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)에만 이러한 유형의 사용자 계정을 만들 수 있습니다. [AZURE AD 서버 보안 주체](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)를 지 원하는 관리 되는 인스턴스를 사용 하면 데이터베이스 사용자를 포함 된 데이터베이스 사용자로 만들 필요 없이 관리 되는 인스턴스에 인증 하는 사용자 계정을 만들 수 있습니다.

  이 방법을 사용 하면 사용자 인증 정보가 각 데이터베이스에 저장 되 고 지역에서 복제 된 데이터베이스에 자동으로 복제 됩니다. 그러나 동일한 계정이 여러 데이터베이스에 있고 SQL 인증을 사용 하는 경우 암호를 수동으로 동기화 해야 합니다. 또한 사용자가 다른 암호를 사용 하는 다른 데이터베이스에 계정이 있는 경우 해당 암호를 기억 하면 문제가 될 수 있습니다.

> [!IMPORTANT]
> Azure AD id에 매핑된 포함 된 사용자를 만들려면 SQL Database의 관리자 인 Azure AD 계정을 사용 하 여 로그인 해야 합니다. 관리 되는 인스턴스에서 사용 권한이 있는 `sysadmin` SQL 로그인은 Azure AD 로그인 또는 사용자를 만들 수도 있습니다.

로그인 및 사용자를 만드는 방법을 보여 주는 예제는 다음을 참조 하세요.

- [단일 데이터베이스 또는 풀링된 데이터베이스에 대 한 로그인 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [관리 되는 인스턴스 데이터베이스에 대 한 로그인 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Azure Synapse Analytics 데이터베이스에 대 한 로그인 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [사용자 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Azure AD에 포함 된 사용자 만들기](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> 단일 데이터베이스 또는 풀링된 데이터베이스에 포함 된 사용자 SQL Server 만들기를 포함 하는 보안 자습서는 [자습서: 단일 데이터베이스 또는 풀링된 데이터베이스 보안](sql-database-security-tutorial.md)을 참조 하세요.

## <a name="using-fixed-and-custom-database-roles"></a>고정 및 사용자 지정 데이터베이스 역할 사용

로그인 또는 포함 된 사용자에 따라 데이터베이스에서 사용자 계정을 만든 후에는 해당 사용자에 게 다양 한 작업을 수행 하 고 특정 데이터베이스의 데이터에 액세스 하도록 권한을 부여할 수 있습니다. 다음 방법을 사용 하 여 액세스 권한을 부여할 수 있습니다.

- **고정 데이터베이스 역할**

  [고정 데이터베이스 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)에 사용자 계정을 추가 합니다. 각각에는 정의 된 사용 권한 집합이 있는 9 개의 고정 데이터베이스 역할이 있습니다. 가장 일반적인 고정 데이터베이스 역할은 **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**및 **db_denydatareader**입니다. **db_owner**는 일반적으로 일부 사용자에게만 전체 권한을 부여하는 데 사용됩니다. 기타 고정된 데이터베이스 역할은 개발에서 단순한 데이터베이스를 신속하게 가져오는 데 유용하지만 대부분의 프로덕션 데이터베이스에는 권장되지 않습니다. 예를 들어 **db_datareader** 고정 데이터베이스 역할은 데이터베이스의 모든 테이블에 대 한 읽기 액세스 권한을 부여 합니다 .이는 반드시 필요 합니다.

  - 고정 데이터베이스 역할에 사용자를 추가 하려면 다음을 수행 합니다.

    - Azure SQL Database에서 [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) 문을 사용 합니다. 예제는 [ALTER ROLE 예](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples) 를 참조 하세요.
    - Azure Synapse Analytics는 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 문을 사용 합니다. 예제는 [sp_addrolemember 예](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)를 참조 하세요.

- **사용자 지정 데이터베이스 역할**

  [CREATE role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) 문을 사용 하 여 사용자 지정 데이터베이스 역할을 만듭니다. 사용자 지정 역할을 사용 하면 사용자 정의 데이터베이스 역할을 만들고 각 역할에 비즈니스 요구에 필요한 최소한의 권한을 부여할 수 있습니다. 사용자 지정 역할에 사용자를 추가할 수 있습니다. 사용자가 여러 역할의 멤버인 경우 모두에 대한 권한을 집계합니다.
- **직접 권한 부여**

  사용자 계정 [사용 권한을](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) 직접 부여 합니다. SQL Database에는 개별적으로 부여하거나 거부할 수 있는 100개가 넘는 사용 권한이 있습니다. 이러한 사용 권한은 대부분 중첩됩니다. 예를 들어 스키마에 대한 `UPDATE` 권한에는 해당 스키마 내에 있는 각 테이블에 대한 `UPDATE` 권한이 포함됩니다. 대부분의 사용 권한 시스템에서와 같이 사용 권한 거부는 권한 부여를 재정의합니다. 중첩된 특성과 사용 권한 수로 인해 데이터베이스를 제대로 보호할 적절한 사용 권한 시스템을 설계하는 데 신중을 기할 수 있습니다. [사용 권한(데이터베이스 엔진)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)에서 사용 권한 목록부터 시작하여 사용 권한의 [포스터 크기 그래픽](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png)을 검토하세요.

## <a name="using-groups"></a>그룹 사용

효율적인 액세스 관리는 개별 사용자가 아닌 보안 그룹 및 고정 또는 사용자 지정 역할에 Active Directory 할당 된 사용 권한을 사용 합니다.

- Azure Active Directory 인증을 사용 하는 경우 Azure Active Directory 사용자를 Azure Active Directory 보안 그룹에 추가 합니다. 해당 그룹에 대해 포함된 데이터베이스 사용자를 만듭니다. 사용자 그룹에 적절 한 특정 사용 권한이 있는 사용자 지정 데이터베이스 역할에 하나 이상의 데이터베이스 사용자를 추가 합니다.

- SQL 인증을 사용 하는 경우 데이터베이스에 포함 된 데이터베이스 사용자를 만듭니다. 사용자 그룹에 적절 한 특정 사용 권한이 있는 사용자 지정 데이터베이스 역할에 하나 이상의 데이터베이스 사용자를 추가 합니다.

  > [!NOTE]
  > 포함 되지 않은 데이터베이스 사용자에 대해서도 그룹을 사용할 수 있습니다.

사용 권한을 제한 또는 확장시키는 데 사용할 수 있는 다음 기능을 잘 알고 있어야 합니다.

- [가장](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) 및 [모듈 서명](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)은 일시적으로 권한을 안전하게 상승시키는 데 사용할 수 있습니다.
- [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 은 사용자가 액세스할 수 있는 행을 제한하는 데 사용할 수 있습니다.
- [데이터 마스킹](sql-database-dynamic-data-masking-get-started.md) 은 중요한 데이터의 노출을 제한하는 데 사용할 수 있습니다.
- [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)는 데이터베이스에서 수행할 수 있는 작업을 제한하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

모든 SQL Database 보안 기능에 대한 개요는 [SQL 보안 개요](sql-database-security-overview.md)를 참조하세요.
