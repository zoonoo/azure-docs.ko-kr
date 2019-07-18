---
title: Azure Search 2019-05-06-미리 보기-REST API 미리 보기 Azure Search
description: Azure Search 서비스 REST API 버전 2019-05-06-Preview에는 지식 저장소 및 고객이 관리 하는 암호화 키와 같은 실험적 기능이 포함 됩니다.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 5374ff896613dd8f8563a2054be8a92103e63fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523912"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Azure Search 서비스 REST api 버전 2019-05-06-미리 보기
이 문서에서는 `api-version=2019-05-06-Preview` 버전의 Azure Search Service REST API를 설명하면서 아직 일반 공개되지 않은 실험적 기능을 제공합니다.

> [!NOTE]
> 미리 보기 기능은 사용자 의견을 수집하고 변경하기 위해 테스트 및 실험에 사용할 수 있습니다. 프로덕션 애플리케이션에서 미리 보기 API를 사용하지 않는 것이 좋습니다.


## <a name="new-in-2019-05-06-preview"></a>2019-05-06-미리 보기의 새로운 기능

[**정보 저장소** ](knowledge-store-concept-intro.md) 대상이 되는 새 AI 기반 보강 파이프라인. 인덱스 외에도 이제 Azure storage에서 인덱싱 중에 만들어진 채워진된 데이터 구조를 유지할 수 있습니다. 이 데이터는 Table storage 또는 Blob storage에 저장 하는지 여부를 모양 데이터 및 뷰를 여러 개 있는지 여부를 나타내는 포함 하 여 기술 집합에서 요소를 통해 데이터의 물리적 구조를 제어 합니다.

[**고객이 관리 하는 암호화 키** ](search-security-manage-encryption-keys.md) 서비스 쪽-미사용 암호화는 또한 새 미리 보기 기능에 대 한 합니다. 기본 제공 암호화 미사용 시 Microsoft에서 관리 되는, 하는 것 외에도 암호화 키의 유일한 소유자는 강화를 적용할 수 있습니다.

## <a name="other-preview-features"></a>기타 미리 보기 기능

이전 미리 보기에서 공지된 기능이 여전히 공개 미리 보기 상태로 제공됩니다. 이전 미리 보기 API 버전에서 API를 호출하는 경우 계속 해당 버전을 사용하거나 예상되는 동작을 변경하지 않고 `2019-05-06-Preview`로 전환할 수 있습니다.

+ [moreLikeThis 쿼리 매개 변수](search-more-like-this.md)는 특정 문서와 관련된 문서를 찾습니다. 이 기능은 이전 미리 보기에 있었습니다. 
* [CSV blob 인덱싱](search-howto-index-csv-blobs.md) 텍스트 blob 당 문서 하나 대신 줄 당 문서 하나를 만듭니다.
* [Cosmos DB 인덱서 용 MongoDB API 지원](search-howto-index-cosmosdb.md) 미리 보기로 제공에서 됩니다.


## <a name="how-to-call-a-preview-api"></a>미리 보기 API를 호출하는 방법

이전 미리 보기가 여전히 작동하지만 시간이 지남에 따라 부실해집니다. 코드가 `api-version=2016-09-01-Preview` 또는 `api-version=2017-11-11-Preview`를 호출하는 경우 이러한 호출은 여전히 유효합니다. 그러나 최신 미리 보기 버전만 향상된 기능으로 새로 고쳐집니다. 

다음 예제 구문은 미리 보기 API 버전에 대한 호출을 설명합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Search 서비스는 여러 버전으로 제공됩니다. 자세한 내용은 [API 버전](search-api-versions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Search 서비스 REST API 참조 설명서를 검토 합니다. 문제가 있는 경우 미국에서 지원 요청 [StackOverflow](https://stackoverflow.com/) 하거나 [지원에 문의](https://azure.microsoft.com/support/community/?product=search)합니다.

> [!div class="nextstepaction"]
> [Search 서비스 REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)