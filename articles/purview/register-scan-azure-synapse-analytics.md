---
title: Azure Synapse Analytics 검사 방법
description: 이 방법 가이드에서는 Azure Synapse Analytics를 검사하는 방법에 관해 자세히 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: d287f5dc239339f79d2d8237e7739de7793920c4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108611"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Azure Synapse Analytics 등록 및 검사 방법

이 문서에서는 Purview에서 Azure Synapse Analytics(이전의 SQL DW)의 인스턴스를 등록하고 검색하는 방법을 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Synapse Analytics(이전의 SQL DW)는 메타데이터 및 스키마를 캡처하기 위한 전체 검사 및 증분 검사를 지원합니다. 또한 검사는 시스템 및 사용자 지정 분류 규칙을 기반으로 데이터를 자동으로 분류합니다.

### <a name="known-limitations"></a>알려진 제한 사항

Azure Purview는 Azure Synapse Analytics에서 [보기](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true)를 위한 검사를 지원하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- Azure Purview 데이터 원본 관리자여야 합니다.
- Purview 계정과 Azure Synapse Analytics 간의 네트워킹 액세스.
 
## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Azure Synapse Analytics 인증을 설정하는 방법에는 다음 세 가지가 있습니다.

- 관리 ID
- SQL 인증
- 서비스 주체

    > [!Note]
    > 프로비전 프로세스를 통해 만들어진 서버 수준의 보안 주체 로그인이나 master 데이터베이스에서 `loginmanager` 데이터베이스 역할이 할당된 멤버만 새 로그인을 만들 수 있습니다. 이 작업에는 권한을 부여한 후 15분 정도 걸립니다. Purview 계정에는 리소스를 검사할 수 있는 적절한 권한이 있어야 합니다.

### <a name="managed-identity-recommended"></a>관리 ID(권장) 
   
Purview 계정에는 생성할 때 기본적으로 Purview 이름인 자체 관리 ID가 있습니다. [Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기](../azure-sql/database/authentication-aad-service-principal-tutorial.md)의 필수 구성 요소 및 자습서에 따라 정확한 Purview 관리 ID 이름을 사용하여 Azure Synapse Analytics(이전의 SQL DW)에서 Azure AD 사용자를 만들어야 합니다.

사용자를 만들고 권한을 부여하는 SQL 구문 예제:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

인증에는 데이터베이스, 스키마, 테이블을 위한 메타데이터를 가져올 수 있는 권한이 있어야 합니다. 또한 분류를 위해 샘플링할 테이블을 쿼리할 수 있어야 합니다. ID에 `db_owner` 사용 권한을 할당하는 것이 좋습니다.

### <a name="service-principal"></a>서비스 주체

검사에 서비스 주체 인증을 사용하려면 기존 주체를 사용하거나 새 주체를 만들 수 있습니다. 

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

서비스 주체의 애플리케이션 ID 및 비밀을 가져오는 데 필요합니다.

1. [Azure Portal](https://portal.azure.com)에서 서비스 주체로 이동합니다.
1. **개요** 에서 **애플리케이션(클라이언트) ID** 값을 복사하고, **인증서 및 비밀** 에서 **클라이언트 암호** 값을 복사합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 선택한 **이름** 및 **값** 을 서비스 주체의 **클라이언트 암호** 로 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 서비스 주체를 통해 [새 자격 증명을 생성](manage-credentials.md#create-a-new-credential)하여 검사를 설정합니다. 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>서비스 주체에게 Azure Synapse Analytics(이전의 SQL DW)에 대한 액세스 권한 부여

또한 [Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기](../azure-sql/database/authentication-aad-service-principal-tutorial.md)를 위한 필수 조건 및 자습서에 따라 Azure Synapse Analytics에서 Azure AD 사용자를 만들어야 합니다. 사용자를 만들고 권한을 부여하는 SQL 구문 예제:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_owner ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> Purview는 검사를 위해 **애플리케이션(클라이언트) ID** 및 **클라이언트 암호** 가 필요합니다.

### <a name="sql-authentication"></a>SQL 인증

Azure Synapse Analytics(이전의 SQL DW)를 위한 로그인이 없는 경우 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1)의 지침에 따라 해당 로그인을 만들 수 있습니다.

선택한 인증 방법이 **SQL 인증** 인 경우 암호를 가져오고 키 자격 증명 모음에 저장해야 합니다.

1. SQL 로그인 암호 가져오기
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, SQL 로그인 *암호* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 키를 사용하여 검사를 설정하기 위한 [새 자격 증명](manage-credentials.md#create-a-new-credential)을 만듭니다.

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Azure Synapse Analytics(이전의 SQL DW) 인스턴스 등록

Data Catalog에서 새 Azure Synapse Analytics 서버를 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **소스 등록** 에서 **Azure Synapse Analytics(이전의 SQL DW)** 를 선택합니다.
1. **계속** 을 선택합니다.

**원본 등록(Azure Synapse Analytics)** 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
1. 원하는 논리 SQL Server를 가리키는 방법을 선택합니다.
   1. **Azure 구독에서** 를 선택하고, **Azure 구독** 드롭다운 상자에서 적절한 구독을 선택하고, **서버 이름** 드롭다운 상자에서 적절한 서버를 선택합니다.
   1. 또는 **수동으로 입력** 을 선택하고 **서버 이름** 을 입력할 수 있습니다.
1. **마침** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="원본 등록 옵션" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
