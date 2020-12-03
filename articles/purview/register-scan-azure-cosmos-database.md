---
title: Azure Cosmos 데이터베이스를 검색 하는 방법 (SQL API)
description: 이 방법 가이드에서는 Azure Cosmos 데이터베이스 (SQL API)를 검색 하는 방법에 대해 자세히 설명 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: e1d2035b787380d9b93943b92fbe81c09fc6a527
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554987"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Azure Cosmos 데이터베이스 등록 및 검색 (SQL API)

이 문서에서는 azure 부서의 범위에서 Azure Cosmos 데이터베이스 (SQL API) 계정을 등록 하 고 검색을 설정 하는 방법을 설명 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Cosmos 데이터베이스 (SQL API)는 메타 데이터 및 스키마를 캡처하기 위해 전체 및 증분 검색을 지원 합니다. 또한 검색은 시스템 및 사용자 지정 분류 규칙을 기반으로 데이터를 자동으로 분류 합니다.

## <a name="prerequisites"></a>필수 조건

- 데이터 원본을 등록 하기 전에 Azure 부서의 범위 계정을 만듭니다. 부서의 범위 계정을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
- Azure 부서의 범위 데이터 원본 관리자 여야 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검색에 대 한 인증 설정

Azure Cosmos 데이터베이스 (SQL API)에 대 한 인증을 설정 하는 방법은 한 가지 뿐입니다.

- 계정 키
 
### <a name="account-key"></a>계정 키

선택 된 인증 방법이 **계정 키** 인 경우 액세스 키를 가져와서 key vault에 저장 해야 합니다.

1. Azure Portal에서 Cosmos DB 계정으로 이동 합니다. 
1. **설정**  >  **키** 선택 
1. *키* 를 복사 하 고 다음 단계를 위해 어딘가에 저장 합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 선택
1. **+ 생성/가져오기** 를 선택 하 고 저장소 계정의 *키* 로 **이름과** **값** 을 입력 합니다.
1. **만들기** 를 선택 하 여 완료 합니다.
1. 키 자격 증명 모음이 부서의 범위에 아직 연결 되지 않은 경우 [새 키 자격 증명 모음 연결을 만들어야](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) 합니다.
1. 마지막으로, 키를 사용 하 여 [새 자격 증명을 만들어](manage-credentials.md#create-a-new-credential) 검색을 설정 합니다.

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Azure Cosmos 데이터베이스 (SQL API) 계정 등록

데이터 카탈로그에 새 Azure Cosmos 데이터베이스 (SQL API) 계정을 등록 하려면 다음을 수행 합니다.

1. 부서의 범위 계정으로 이동 합니다.
1. 왼쪽 탐색 영역에서 **원본** 선택
1. **등록** 을 선택합니다.
1. **소스 등록** 에서 **AZURE COSMOS DB (SQL API)** 를 선택 합니다.
1. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="새 데이터 원본 등록" border="true":::

**원본 등록 Azure Cosmos DB (SQL API)** 화면에서 다음을 수행 합니다.

1. 카탈로그에서 데이터 원본이 나열 될 **이름을** 입력 합니다.
1. 원하는 저장소 계정을 가리키는 방법을 선택 합니다.
   1. **Azure 구독에서** 를 선택 하 고, **azure 구독** 드롭다운 상자에서 적절 한 구독을 선택 하 고, **Cosmos DB 계정 이름** 드롭다운 상자에서 적절 한 cosmosDB 계정을 선택 합니다.
   1. 또는 **수동으로 입력** 을 선택 하 고 서비스 끝점 (URL)을 입력할 수 있습니다.
1. 데이터 원본 등록을 **완료** 합니다.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="소스 등록 옵션" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure 부서의 범위 Data catalog 찾아보기](how-to-browse-catalog.md)
- [Azure 부서의 범위 Data Catalog 검색](how-to-search-catalog.md)
