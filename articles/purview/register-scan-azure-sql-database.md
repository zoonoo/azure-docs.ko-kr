---
title: Azure SQL Database 등록 및 검사
description: 이 자습서에서는 Azure SQL Database를 검사하는 방법을 설명합니다.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 1fbeedd8643a777b29ebe4993eed7b664240621c
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920271"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Azure SQL Database 등록 및 검사

이 문서에서는 Purview에서 Azure SQL Database 데이터 원본을 등록하고 검사를 설정하는 방법에 대해 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure SQL Database 데이터 원본에서 지원하는 기능은 다음과 같습니다.

- **전체 및 증분 검사** - Azure SQL Database에서 메타데이터 및 분류를 캡처합니다.

- **계보** - ADF 복사 및 데이터 흐름 작업에 대한 데이터 자산 간의 계보입니다.

### <a name="known-limitations"></a>알려진 제한 사항

Azure Purview는 Azure SQL Database의 [보기](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15)에 대한 검사를 지원하지 않습니다. 

## <a name="prerequisites"></a>필수 조건

1. 아직 Purview 계정이 없는 경우 새로 만듭니다.

1. Purview 계정과 Azure SQL Database 간의 네트워킹 액세스.


### <a name="set-up-authentication-for-a-scan"></a>검사 인증 설정

Azure SQL Database를 검사하기 위한 인증입니다. 새 인증을 만들어야 하는 경우 [SQL Database에 대한 데이터베이스 액세스 권한을 부여](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)해야 합니다. 현재 Purview에서 지원하는 세 가지 인증 방법은 다음과 같습니다.

- SQL 인증
- 서비스 주체
- 관리 ID

#### <a name="sql-authentication"></a>SQL 인증

> [!Note]
> 프로비전 프로세스를 통해 만들어진 서버 수준의 보안 주체 로그인이나 master 데이터베이스에서 `loginmanager` 데이터베이스 역할이 할당된 멤버만 새 로그인을 만들 수 있습니다. 이 작업에는 권한을 부여한 후 **15분** 정도 걸립니다. Purview 계정에는 리소스를 검사할 수 있는 적절한 권한이 있어야 합니다.

Azure SQL Database에 대한 로그인을 사용할 수 없는 경우 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)의 지침에 따라 해당 로그인을 만들 수 있습니다. 다음 단계를 수행하려면 **사용자 이름** 및 **암호** 가 필요합니다.

1. Azure Portal에서 키 자격 증명 모음으로 이동합니다.
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, Azure SQL Database의 *암호* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 **사용자 이름** 및 **암호** 를 사용하여 검사를 설정하기 위한 [새 자격 증명을 만듭니다](manage-credentials.md#create-a-new-credential).

#### <a name="service-principal-and-managed-identity"></a>서비스 주체 및 관리 ID

Purview에서 서비스 주체 또는 Purview의 **관리 ID** 를 사용하여 Azure SQL Database를 검사할 수 있도록 허용하는 몇 가지 단계가 있습니다.

   > [!Note]
   > Purview는 검사를 위해 **애플리케이션(클라이언트) ID** 및 **클라이언트 암호** 가 필요합니다.

##### <a name="create-or-use-an-existing-service-principal"></a>기존 서비스 주체 만들기 또는 사용

> [!Note]
> Purview의 **관리 ID** 를 사용하는 경우 이 단계를 건너뛰세요.

서비스 주체를 사용하려면 기존 주체를 사용하거나 새 주체를 만들 수 있습니다. 

> [!Note]
> 새 서비스 주체를 만들어야 하는 경우 다음 단계를 수행하세요.
> 1. [Azure Portal](https://portal.azure.com)로 이동합니다.
> 1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택합니다.
> 1. **앱 등록** 을 선택합니다.
> 1. **+ 새 애플리케이션 등록** 을 선택합니다.
> 1. **애플리케이션** 의 이름(서비스 사용자 이름)을 입력합니다.
> 1. **이 조직 디렉터리의 계정만** 을 선택합니다.
> 1. 리디렉션 URI에 대해 **웹** 을 선택하고 원하는 URL을 입력합니다. 실제 또는 작업 URL일 필요가 없습니다.
> 1. 그런 다음, **등록** 을 선택합니다.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>데이터베이스 계정에서 Azure AD 인증 구성

서비스 주체 또는 관리 ID에는 데이터베이스, 스키마 및 테이블에 대한 메타데이터를 가져올 수 있는 권한이 있어야 합니다. 또한 분류를 위해 샘플링할 테이블을 쿼리할 수 있어야 합니다.

- [Azure SQL에서 Azure AD 인증 구성 및 관리](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure)
- 관리 ID를 사용하는 경우 Purview 계정에는 생성할 때 기본적으로 Purview 이름인 자체 관리 ID가 있습니다. [Azure SQL Database에서 서비스 주체 사용자 만들기](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial#create-the-service-principal-user-in-azure-sql-database)에 대한 자습서를 수행하여 정확한 Purview의 관리 ID 또는 자체 서비스 주체를 사용하여 Azure SQL Database에서 Azure AD 사용자를 만들어야 합니다. `db_owner`(**추천**) 권한을 ID에 할당해야 합니다. 사용자를 만들고 권한을 부여하는 SQL 구문 예제:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username`은 사용자 고유의 서비스 주체 또는 Purview의 관리 ID입니다.
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>키 자격 증명 모음 및 Purview의 자격 증명에 서비스 주체 추가

> [!Note]
> Purview의 **관리 ID** 를 사용하려는 경우 기본 Purview의 관리 ID가 이미 **Purview-MSI** 자격 증명에 있으므로 이 단계를 건너뛸 수 있습니다.

서비스 주체의 애플리케이션 ID 및 비밀을 가져오는 데 필요합니다.

1. [Azure Portal](https://portal.azure.com)에서 서비스 주체로 이동합니다.
1. **개요** 에서 **애플리케이션(클라이언트) ID** 값을 복사하고, **인증서 및 비밀** 에서 **클라이언트 암호** 값을 복사합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 선택한 **이름** 및 **값** 을 서비스 주체의 **클라이언트 암호** 로 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 서비스 주체를 통해 [새 자격 증명을 생성](manage-credentials.md#create-a-new-credential)하여 검사를 설정합니다.

### <a name="firewall-settings"></a>방화벽 설정

데이터베이스 서버에서 Azure 연결이 사용되도록 허용해야 합니다. 이렇게 하면 Azure Purview에서 서버에 도달하여 연결할 수 있습니다. [Azure 내부에서 연결](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)에 대한 방법 가이드를 따를 수 있습니다.

1. 데이터베이스 계정으로 이동합니다.
1. **개요** 페이지에서 서버 이름을 선택합니다.
1. **보안 > 방화벽 및 가상 네트워크** 를 선택합니다.
1. **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 에 대해 **예** 를 선택합니다.

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용합니다." border="true":::
    
> [!Note]
> 현재 Azure Purview는 VNET 구성을 지원하지 않습니다. 따라서 IP 기반 방화벽 설정을 수행할 수 없습니다.

## <a name="register-an-azure-sql-database-data-source"></a>Azure SQL Database 데이터 원본 등록

새 Azure SQL Database를 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.

1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.

1. **등록** 을 선택합니다.

1. **원본 등록** 에서 **Azure SQL Database** 를 선택합니다. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="새 데이터 원본 등록" border="true":::

**원본 등록(Azure SQL Database)** 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
1. 원하는 스토리지 계정을 가리키는 방법을 선택합니다.
   1. **Azure 구독에서** 를 선택하고, **Azure 구독** 드롭다운 상자에서 적절한 구독을 선택하고, **서버 이름** 드롭다운 상자에서 적절한 서버를 선택합니다.
   1. 또는 **수동으로 입력** 을 선택하고 **서버 이름** 을 입력할 수 있습니다.
1. **마침** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="원본 등록 옵션" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> 검사를 삭제해도 이전 Azure SQL Database 검사에서 자산이 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
