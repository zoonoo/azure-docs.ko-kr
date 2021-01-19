---
title: Azure Synapse Analytics를 검색 하는 방법
description: 이 방법 가이드에서는 Azure Synapse Analytics를 검색 하는 방법에 대해 자세히 설명 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 3ba43b83166b5548dee4ea4e52c7411db48d23f5
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567275"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Azure Synapse Analytics 등록 및 검사

이 문서에서는 부서의 범위에서 Azure Synapse Analytics (이전의 SQL DW)의 인스턴스를 등록 하 고 검색 하는 방법을 설명 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Synapse Analytics (이전의 SQL DW)는 메타 데이터 및 스키마를 캡처하기 위한 전체 및 증분 검색을 지원 합니다. 또한 검색은 시스템 및 사용자 지정 분류 규칙을 기반으로 데이터를 자동으로 분류 합니다.

### <a name="known-limitations"></a>알려진 제한 사항

Azure 부서의 범위는 Azure Synapse Analytics에서 [보기](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) 의 검색을 지원 하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 데이터 원본을 등록 하기 전에 Azure 부서의 범위 계정을 만듭니다. 부서의 범위 계정을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
- Azure 부서의 범위 데이터 원본 관리자 여야 합니다.
- 부서의 범위 계정과 Azure Synapse Analytics 간의 네트워킹 액세스.
 
## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Azure Synapse Analytics에 대 한 인증을 설정 하는 방법에는 다음 세 가지가 있습니다.

- 관리 ID
- SQL 인증
- 서비스 주체

    > [!Note]
    > 프로비전 프로세스를 통해 만들어진 서버 수준의 보안 주체 로그인이나 master 데이터베이스에서 `loginmanager` 데이터베이스 역할이 할당된 멤버만 새 로그인을 만들 수 있습니다. 이 작업에는 권한을 부여한 후 15분 정도 걸립니다. Purview 계정에는 리소스를 검사할 수 있는 적절한 권한이 있어야 합니다.

### <a name="managed-identity-recommended"></a>관리 Id (권장) 
   
부서의 범위 계정에는 사용자가 만들 때 기본적으로 부서의 범위 이름인 자체 관리 Id가 있습니다. Azure ad [응용 프로그램을 사용 하 여 azure ad 사용자 만들기](/azure/azure-sql/database/authentication-aad-service-principal-tutorial)의 필수 구성 요소 및 자습서에 따라 정확한 부서의 범위 관리 id 이름을 사용 하 여 Azure Synapse Analytics (이전의 SQL DW)에서 azure ad 사용자를 만들어야 합니다.

사용자를 만들고 권한을 부여하는 SQL 구문 예제:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

인증에는 데이터베이스, 스키마 및 테이블에 대 한 메타 데이터를 가져올 수 있는 권한이 있어야 합니다. 또한 분류를 위해 샘플링할 테이블을 쿼리할 수 있어야 합니다. Id에 사용 권한을 할당 하는 것이 좋습니다 `db_owner` .

### <a name="service-principal"></a>서비스 주체

검색에 대 한 서비스 주체 인증을 사용 하려면 기존 항목을 사용 하거나 새로 만들 수 있습니다. 

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

서비스 주체의 응용 프로그램 ID 및 암호를 가져오는 데 필요 합니다.

1. [Azure Portal](https://portal.azure.com)에서 서비스 주체로 이동합니다.
1. **개요** 에서 **애플리케이션(클라이언트) ID** 값을 복사하고, **인증서 및 비밀** 에서 **클라이언트 암호** 값을 복사합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 선택한 **이름** 및 **값** 을 서비스 주체의 **클라이언트 암호** 로 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 서비스 주체를 통해 [새 자격 증명을 생성](manage-credentials.md#create-a-new-credential)하여 검사를 설정합니다. 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>서비스 주체에 게 Azure Synapse Analytics에 대 한 액세스 권한 부여 (이전의 SQL DW)

또한 azure ad [응용 프로그램을 사용 하 여 azure ad 사용자 만들기](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial)에 대 한 필수 조건 및 자습서에 따라 Azure Synapse Analytics에서 azure ad 사용자를 만들어야 합니다. 사용자를 만들고 권한을 부여하는 SQL 구문 예제:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Purview는 검사를 위해 **애플리케이션(클라이언트) ID** 및 **클라이언트 암호** 가 필요합니다.

### <a name="sql-authentication"></a>SQL 인증

아직 없는 경우 [로그인 만들기](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) 의 지침에 따라 Azure Synapse Analytics (이전의 SQL DW)에 대 한 로그인을 만들 수 있습니다.

선택한 인증 방법이 **SQL 인증** 인 경우 암호를 가져오고 키 자격 증명 모음에 저장 해야 합니다.

1. SQL 로그인에 대 한 암호 가져오기
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택 하 고 SQL 로그인에 대 한 *암호로* **이름과** **값** 을 입력 합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로, 키를 사용 하 여 [새 자격 증명을 만들어](manage-credentials.md#create-a-new-credential) 검색을 설정 합니다.

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Azure Synapse Analytics 인스턴스 등록 (이전의 SQL DW)

Data Catalog에서 새 Azure Synapse Analytics 서버를 등록 하려면 다음을 수행 합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **소스 등록** 에서 **Azure Synapse ANALYTICS (이전의 SQL DW)** 를 선택 합니다.
1. **계속** 을 선택합니다.

**원본 등록 (Azure Synapse Analytics)** 화면에서 다음을 수행 합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
1. 원하는 스토리지 계정을 가리키는 방법을 선택합니다.
   1. **Azure 구독에서** 를 선택 하 고, **azure 구독** 드롭다운 상자에서 적절 한 구독을 선택 하 고, **서버 이름** 드롭다운 상자에서 적절 한 서버를 선택 합니다.
   1. 또는 **수동으로 입력** 을 선택하고 **서버 이름** 을 입력할 수 있습니다.
1. **마침** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="원본 등록 옵션" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)

