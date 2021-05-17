---
title: Azure Cosmos Database(SQL API)를 검색하는 방법
description: 이 방법 가이드에서는 Azure Cosmos Database(SQL API)를 검사하는 방법을 자세히 설명합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696256"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Azure Cosmos Database(SQL API) 등록 및 검색

이 문서에서는 Azure Purview에서 Azure Cosmos Database(SQL API) 계정을 등록하고 검색을 설정하는 방법을 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Cosmos Database(SQL API)는 메타데이터 및 스키마를 캡처하기 위해 전체 및 증분 검색을 지원합니다. 또한 검색은 시스템 및 사용자 지정 분류 규칙을 기반으로 데이터를 자동으로 분류합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- Azure Purview 데이터 원본 관리자여야 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검사 인증 설정

Azure Cosmos Database(SQL API)에 대한 인증을 설정하는 방법은 다음 한 가지 뿐입니다.

- 계정 키
 
### <a name="account-key"></a>계정 키

선택한 인증 방법이 **계정 키** 인 경우 액세스 키를 가져와서 Key Vault에 저장해야 합니다.

1. Azure Portal에서 Cosmos DB 계정으로 이동합니다. 
1. **설정** > **키** 를 차례로 선택합니다. 
1. *키* 를 복사하고 다음 단계를 위해 저장해 둡니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 을 선택합니다.
1. **+ 생성/가져오기** 를 선택하고, 스토리지 계정의 *키* 로 **이름** 및 **값** 을 입력합니다.
1. **만들기** 를 선택하여 완료합니다.
1. 키 자격 증명 모음이 아직 Purview에 연결되지 않은 경우 [새 키 자격 증명 모음 연결을 생성](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)해야 합니다.
1. 마지막으로 키를 사용하여 검사를 설정하기 위한 [새 자격 증명](manage-credentials.md#create-a-new-credential)을 만듭니다.

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Azure Cosmos Database(SQL API) 계정 등록

새 Azure Cosmos Database(SQL API)를 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록** 에서 **Azure Cosmos DB(SQL API)** 를 선택합니다.
1. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="새 데이터 원본 등록" border="true":::

**원본 등록(Azure Cosmos DB(SQL API))** 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
1. 원하는 스토리지 계정을 가리키는 방법을 선택합니다.
   1. **Azure 구독에서** 를 선택하고, **Azure 구독** 드롭다운 상자에서 적절한 구독을 선택한 후 **Cosmos DB 계정 이름** 드롭다운 상자에서 적절한 cosmosDB 계정을 선택합니다.
   1. 또는 **수동으로 입력** 을 선택하고 서비스 계정(URL)을 입력할 수 있습니다.
1. **마침** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="원본 등록 옵션" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
