---
title: 관리 id를 사용 하 여 데이터 원본에 대 한 연결 설정
titleSuffix: Azure Cognitive Search
description: 관리 id를 사용 하 여 데이터 원본에 대 한 인덱서 연결을 설정 하는 방법을 알아봅니다.
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: a057c0d35a465447cc0be99b574ffeebb671115a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971545"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>관리 id를 사용 하 여 데이터 원본에 대 한 인덱서 연결 설정

Azure Cognitive Search의 [인덱서](search-indexer-overview.md)는 데이터 원본에서 Azure Cognitive Search로 데이터를 가져오는 방법을 제공하는 크롤러입니다. 인덱서는 사용자가 만든 데이터 원본 개체에서의 데이터 원본 연결을 획득합니다. 데이터 원본 개체는 일반적으로 대상 데이터 원본에 대한 자격 증명을 포함합니다. 예를 들어 Blob 스토리지 컨테이너에서 데이터를 인덱싱하는 경우 데이터 원본 개체에 Azure Storage 계정 키가 포함될 수 있습니다.

대부분의 경우 데이터 원본 개체에 직접 자격 증명을 제공하는 것은 문제가 되지 않지만 다음과 같은 몇 가지 문제가 발생할 수 있습니다.
* 데이터 원본 개체를 만드는 코드에서 자격 증명을 안전하게 유지하려면 어떻게 해야 하나요?
* 내 계정 키 또는 암호가 손상되어 변경해야 하는 경우, 이제 내 인덱서가 데이터 원본에 다시 연결할 수 있도록 새 계정 키 또는 암호를 사용하여 데이터 원본 개체를 업데이트해야 합니다.

이러한 문제는 관리 ID를 사용하여 연결을 설정하면 해결할 수 있습니다.

## <a name="using-managed-identities"></a>관리 ID 사용

[관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공하는 기능입니다. Azure Cognitive Search에서 이 기능을 사용하여 자격 증명을 포함하지 않는 연결 문자열을 사용하여 데이터 원본 개체를 만들 수 있습니다. 대신, 검색 서비스에 RBAC(역할 기반 액세스 제어)를 통해 데이터 원본에 대한 액세스 권한이 부여됩니다.

관리 ID를 사용하여 데이터 원본을 설정할 때 데이터 원본 자격 증명을 변경할 수 있으며, 인덱서는 여전히 데이터 원본에 연결할 수 있습니다. 계정 키를 포함하거나 Key Vault를 사용하여 계정 키를 검색하지 않고도 코드에서 데이터 원본 개체를 만들 수도 있습니다.

## <a name="limitations"></a>제한 사항

다음 데이터 원본은 관리 ID를 사용하는 인덱서 연결 설정을 지원합니다. 

* [Azure Blob 스토리지, Azure Data Lake Storage Gen2(미리 보기), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

다음 기능은 현재 관리 ID를 사용하는 연결 설정을 지원하지 않습니다.
* 지식 저장소
* 사용자 지정 기술
 
## <a name="next-steps"></a>다음 단계

관리 ID를 사용하여 인덱서 연결을 설정하는 방법에 대해 자세히 알아봅니다.

* [Azure Blob 스토리지, Azure Data Lake Storage Gen2(미리 보기), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)