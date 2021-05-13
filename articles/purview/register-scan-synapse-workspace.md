---
title: Azure Synapse 작업 영역을 검사하는 방법
description: Azure Purview 데이터 카탈로그에서 Synapse 작업 영역을 검사하는 방법을 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031324"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Azure Synapse 작업 영역 등록 및 검사

이 문서에서는 Purview에서 Azure Synapse 작업 영역을 등록하고 작업 영역의 검사를 설정하는 방법을 간략하게 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Synapse 작업 영역 검사는 작업 영역 내에서 전용 및 서버리스 SQL 데이터베이스의 메타데이터 및 스키마 캡처를 지원합니다. 또한 시스템 및 사용자 지정 분류 규칙에 따라 데이터를 자동으로 분류합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- Azure Purview 데이터 원본 관리자여야 합니다.
- 아래 섹션에 설명된 대로 인증 설정

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Synapse 작업 영역에서 전용 SQL 데이터베이스 리소스를 열거하기 위한 인증 설정

1. Azure Portal에서 Synapse 작업 영역이 있는 **리소스 그룹** 또는 **구독** 으로 이동합니다.  
1. 왼쪽 탐색 메뉴에서  **액세스 제어(IAM)**  를 선택합니다. 
1. 리소스 그룹 또는 구독과 관련된 역할을 추가하려면 소유자 또는 사용자 액세스 관리자여야 합니다. ‘+추가’ 단추를 선택합니다. 
1. **읽기 권한자** 역할을 설정하고 입력 선택 상자 아래에 Azure Purview 계정 이름(해당 MSI를 나타냄)을 입력합니다. ‘저장’을 클릭하여 역할 할당을 완료합니다.
1. 위의 2~4단계를 수행하여 Synapse 작업 영역이 있는 리소스 그룹 또는 구독에서 Azure Purview MSI의 **Storage Blob 데이터 읽기 권한자** 역할을 추가합니다.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Synapse 작업 영역에서 서버리스 SQL 데이터베이스 리소스를 열거하기 위한 인증 설정

> [!NOTE]
> 이 명령을 실행하려면 작업 영역에서 **Synapse 관리자** 여야 합니다. [여기](../synapse-analytics/security/how-to-set-up-access-control.md)에서 Synapse 권한에 관해 자세히 알아봅니다.

1. Synapse 작업 영역으로 이동합니다.
1. **데이터** 섹션으로 이동하고 서버리스 SQL 데이터베이스 중 하나로 이동합니다.
1. 줄임표 아이콘을 클릭하고 새 SQL 스크립트를 시작합니다.
1. SQL 스크립트에서 아래 명령을 실행하여 서버리스 SQL 데이터베이스에서 **sysadmin** 으로 Azure Purview 계정 MSI(계정 이름으로 표시됨)를 추가합니다.
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Synapse 작업 영역에서 리소스를 검사하기 위한 인증 설정

Azure Synapse 원본의 인증을 설정하는 방법에는 세 가지가 있습니다.

- 관리 ID
- 서비스 주체
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>전용 SQL 데이터베이스에 관리 ID 사용

1. **Synapse 작업 영역** 으로 이동합니다.
1. **데이터** 섹션으로 이동하고 서버리스 SQL 데이터베이스 중 하나로 이동합니다.
1. 줄임표 아이콘을 클릭하고 새 SQL 스크립트를 시작합니다.
1. SQL 스크립트에서 아래 명령을 실행하여 전용 SQL 데이터베이스에서 **db_owner** 로 Azure Purview 계정 MSI(계정 이름으로 표시됨)를 추가합니다.

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>서버리스 SQL 데이터베이스에 관리 ID 사용

1. **Synapse 작업 영역** 으로 이동합니다.
1. **데이터** 섹션으로 이동하고 서버리스 SQL 데이터베이스 중 하나로 이동합니다.
1. 줄임표 아이콘을 클릭하고 새 SQL 스크립트를 시작합니다.
1. SQL 스크립트에서 아래 명령을 실행하여 서버리스 SQL 데이터베이스에서 **sysadmin** 으로 Azure Purview 계정 MSI(계정 이름으로 표시됨)를 추가합니다.
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>전용 SQL 데이터베이스에 서비스 주체 사용

> [!NOTE]
> 먼저 [여기](manage-credentials.md)에 있는 지침에 따라 서비스 주체 유형의 새 **자격 증명** 을 설정해야 합니다.

1. **Synapse 작업 영역** 으로 이동합니다.
1. **데이터** 섹션으로 이동하고 서버리스 SQL 데이터베이스 중 하나로 이동합니다.
1. 줄임표 아이콘을 클릭하고 새 SQL 스크립트를 시작합니다.
1. SQL 스크립트에서 아래 명령을 실행하여 전용 SQL 데이터베이스에서 **db_owner** 로 **서비스 주체 ID** 를 추가합니다.

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>서버리스 SQL 데이터베이스에 서비스 주체 사용

1. **Synapse 작업 영역** 으로 이동합니다.
1. **데이터** 섹션으로 이동하고 서버리스 SQL 데이터베이스 중 하나로 이동합니다.
1. 줄임표 아이콘을 클릭하고 새 SQL 스크립트를 시작합니다.
1. SQL 스크립트에서 아래 명령을 실행하여 서버리스 SQL 데이터베이스에서 **sysadmin** 으로 Azure Purview 계정 MSI(계정 이름으로 표시됨)를 추가합니다.
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> 등록하고 검사하려는 Synapse 작업 영역 내 각 전용 SQL 데이터베이스에서 인증을 설정해야 합니다. 서버리스 SQL 데이터베이스의 위에서 언급한 권한은 작업 영역 내 모든 항목에 적용됩니다. 즉, 한 번만 실행해야 합니다.
    
## <a name="register-an-azure-synapse-source"></a>Azure Synapse 원본 등록

새 Azure Synapse 원본을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록** 에서 **Azure Synapse Analytics(여러 개)** 를 선택합니다.
1. **계속** 을 선택합니다.

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Azure Synapse 원본 설정":::

**원본 등록(Azure Synapse Analytics)** 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
1. 필요한 경우 필터링할 **구독** 을 선택합니다.
1. 드롭다운에서 **Synapse 작업 영역 이름을 선택** 합니다. SQL 엔드포인트는 작업 영역 선택에 따라 자동으로 채워집니다. 
1. **컬렉션** 을 선택하거나 새로 만듭니다(선택 사항).
1. **마침** 을 선택하여 데이터 원본을 등록합니다.

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Azure Synapse 원본의 세부 정보 채우기":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. **원본** 섹션으로 이동합니다.

1. 등록한 데이터 원본을 선택합니다.

1. 세부 정보 보기를 클릭하고 **+ 새 검사** 를 선택하거나 원본 타일에서 빠른 작업 검사 아이콘을 사용합니다.

1. ‘이름’을 입력하고 이 원본 내에서 검사하려는 모든 종류의 리소스를 선택합니다. **SQL Database** 는 현재 Synapse 작업 영역 내에서 지원되는 유일한 종류입니다.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure Synapse 원본 검사":::

1. **자격 증명** 을 선택하여 데이터 원본 내 리소스에 연결합니다. 
  
1. 각 종류 내에서 이름으로 모든 리소스 또는 해당 하위 집합을 검사하도록 선택할 수 있습니다.
1.  계속하려면 **계속** 을 클릭합니다. 

1.  Azure Synapse SQL 유형의 **검사 규칙 집합** 을 선택합니다. 검사 규칙 집합을 인라인으로 만들 수도 있습니다.

1. 검사 트리거를 선택합니다. **매주/매월** 또는 **한 번** 실행되도록 예약할 수 있습니다.

1. 검사를 검토하고 저장을 선택하여 설치를 완료합니다.   

## <a name="viewing-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. 원본 섹션 아래 타일에서 **세부 정보 보기** 를 클릭하여 원본 세부 정보를 봅니다. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Azure Synapse 원본 세부 정보"::: 

1. **검사 세부 정보** 페이지로 이동하여 검사 실행 세부 정보를 봅니다.
    1. ‘상태 표시줄’은 자식 리소스의 실행 상태에 관한 간단한 요약입니다. 이는 작업 영역 수준 검사에서 표시됩니다.
    1. 녹색은 성공을 의미하지만 빨간색은 실패를 의미합니다. 회색은 검사가 진행 중임을 의미합니다.
    1. 각 검사를 클릭하여 더 세분화된 세부 정보를 볼 수 있습니다.

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Azure Synapse 검사 세부 정보" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. 원본 세부 정보 페이지의 아래쪽에서 최근 실패한 검사 실행의 요약을 봅니다. 이 실행과 관련된 더 세분화된 세부 정보를 클릭하여 볼 수도 있습니다.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>검사 관리 - 편집, 삭제 또는 취소
검사를 관리하거나 삭제하려면 다음을 수행합니다.

- 관리 센터로 이동합니다. 원본 및 검사 섹션에서 데이터 원본을 선택한 다음, 원하는 데이터 원본을 선택합니다.

- 관리하려는 검사를 선택합니다. 편집을 선택하여 검사를 편집할 수 있습니다.

- 삭제를 선택하여 검사를 삭제할 수 있습니다.
- 검사가 실행 중인 경우에도 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)   