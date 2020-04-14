---
title: 질문과 대답(FAQ)
titleSuffix: Azure Cognitive Search
description: Microsoft Azure에서 클라우드 호스팅 검색 서비스인 Microsoft Azure 인지 검색 서비스에 대한 일반적인 질문에 대한 답변을 가져옵니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d7ba62c795e23e41a1947def77300ffe5d2cc010
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262454"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure 인지 검색 - 자주 묻는 질문(FAQ)

 Azure Cognitive Search와 관련된 개념, 코드 및 시나리오에 대해 자주 묻는 질문에 대한 답변을 찾습니다.

## <a name="platform"></a>플랫폼

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Azure 인지 검색내 DBMS의 전체 텍스트 검색과 어떻게 다른가요?

Azure Cognitive Search는 여러 데이터 원본, [여러 언어에 대한 언어 분석,](https://docs.microsoft.com/rest/api/searchservice/language-support) [흥미롭고 특이한 데이터 입력에 대한 사용자 지정 분석,](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) [점수 매기기 프로필을](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)통한 검색 순위 컨트롤, 유형전, 적중 강조 표시 및 패싯 탐색과 같은 사용자 환경 기능을 지원합니다. 또한 동의어 및 다양한 쿼리 구문 등과 같은 다른 기능도 지원하지만 보통은 차별화된 기능이 아닙니다.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Azure 인지 검색 서비스를 일시 중지하고 청구를 중지할 수 있습니까?

서비스는 일시 중지할 수 없습니다. 서비스가 만들어지면 고객의 독점적 사용을 위해 계산 및 스토리지 리소스가 할당됩니다. 요청에 따라 이러한 리소스를 해제하고 재청구하는 것은 불가능합니다.

## <a name="indexing-operations"></a>인덱싱 작업

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>인덱스 또는 인덱스 스냅샷을 이동, 백업 및 복원하고 있습니까?

개발 단계에서 검색 서비스 간에 인덱스를 이동할 수 있습니다. 예를 들어 기본 또는 무료 가격 책정 계층을 사용하여 인덱스를 개발한 다음 프로덕션 용으로 표준 또는 상위 계층으로 이동하려고 할 수 있습니다. 

또는 나중에 복원하는 데 사용할 수 있는 파일에 인덱스 스냅숏을 백업할 수 있습니다. 

이 Azure 인지 검색 .NET 샘플 [리포지토리에서](https://github.com/Azure-Samples/azure-search-dotnet-samples) **인덱스 백업 복원** 샘플 코드로 이러한 모든 작업을 수행할 수 있습니다. 

Azure 인지 검색 REST [API를](https://docs.microsoft.com/rest/api/searchservice/get-index) 사용하여 언제든지 인덱스 정의를 얻을 수도 있습니다.

현재 Azure 포털에는 기본 제공 인덱스 추출, 스냅숏 또는 백업 복원 기능이 없습니다. 그러나 향후 릴리스에서 백업 및 복원 기능을 추가하는 것을 고려하고 있습니다. 이 기능에 대한 지원을 표시하려면 [사용자 음성에](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index)대한 투표를 합니다.

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>내 인덱스 또는 서비스가 삭제된 경우 복원할 수 있나요?

아니요, Azure 인지 검색 인덱스 또는 서비스를 삭제하면 복구할 수 없습니다. Azure Cognitive Search 서비스를 삭제하면 서비스의 모든 인덱스가 영구적으로 삭제됩니다. 하나 이상의 Azure 인지 검색 서비스가 포함된 Azure 리소스 그룹을 삭제하면 모든 서비스가 영구적으로 삭제됩니다.  

인덱스, 인덱서, 데이터 원본 및 기술 집합과 같은 리소스를 다시 만들려면 코드에서 리소스를 다시 만들어야 합니다. 

인덱스를 다시 만들려면 외부 원본의 데이터를 다시 인덱싱해야 합니다. 따라서 Azure SQL Database 또는 Cosmos DB와 같은 다른 데이터 저장소에 원래 데이터의 마스터 복사본 또는 백업을 유지하는 것이 좋습니다.

또는 이 [Azure Cognitive Search .NET 샘플 리포지토리에서](https://github.com/Azure-Samples/azure-search-dotnet-samples) **인덱스 백업 복원** 샘플 코드를 사용하여 인덱스 정의 및 인덱스 스냅숏을 일련의 JSON 파일에 백업할 수 있습니다. 나중에 필요한 경우 도구와 파일을 사용하여 인덱스를 복원할 수 있습니다.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>SQL 데이터베이스 복제본에서 인덱싱할 수 있나요([Azure SQL Database 인덱서](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)에 적용)?

처음부터 새롭게 인덱스를 구축할 때 주 또는 보조 복제본을 데이터 원본으로 사용하는 데는 제약이 없습니다. 그러나 증분 업데이트(변경된 레코드 기준)를 통해 인덱스를 새로 고칠 때는 주 복제본이 필요합니다. 이것은 주 복제본에서만 변경 추적을 보장하는 SQL Database에 따른 요구 사항입니다. 인덱스 새로 고침 작업에 보조 복제본을 사용할 경우 전체 데이터를 확보한다고는 보장할 수 없습니다.

## <a name="search-operations"></a>검색 작업

### <a name="can-i-search-across-multiple-indexes"></a>여러 인덱스에서 검색할 수 있나요?

아니요. 이 작업은 지원되지 않습니다. 검색 범위는 항상 단일 인덱스입니다.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>사용자 ID로 검색 인덱스 액세스를 제한할 수 있습니까?

`search.in()` 필터를 사용하여 [보안 필터](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)를 구현할 수 있습니다. 이 필터는 [AAD(Azure Active Directory) 등의 ID 관리 서비스](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad)를 사용하여 정의된 사용자 그룹 멤버 자격에 따라 검색 결과를 자릅니다.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>유효하다고 알고 있는 용어에 대해 일치가 0인 이유는 무엇인가요?

가장 일반적인 경우는 각 쿼리 입력이 언어적 분석의 다른 검색 동작과 수준을 지원하는 것을 인지하지 못하기 때문입니다. 주요 워크로드인 전체 텍스트 검색에는 용어를 루트 양식으로 나누는 언어 분석 단계가 포함됩니다. 토큰화된 용어는 더 많은 변형 항목과 일치하기 때문에 이 쿼리 구문 분석 단계에는 가능한 일치보다 범위가 더 넓어집니다.

그러나 와일드카드, 유사 항목 및 regex 쿼리는 일반적인 용어 또는 구문 쿼리처럼 분석되지 않으며, 쿼리가 검색 인덱스에 있는 분석된 형태의 단어와 일치하지 않을 경우 제대로 불러올 수 없습니다. 쿼리 구문 분석 및 분석에 대한 자세한 내용은 [쿼리 아키텍처를](https://docs.microsoft.com/azure/search/search-lucene-query-architecture)참조하십시오.

### <a name="my-wildcard-searches-are-slow"></a>내 와일드카드 검색 속도가 느립니다.

접두사, 유사 항목 및 regex와 같은 대부분의 와일드카드 검색 쿼리는 검색 인덱스에서 일치하는 용어를 사용하여 내부적으로 다시 작성됩니다. 검색 인덱스를 검사하는 이 추가 처리로 인해 대기 시간이 추가됩니다. 예를 들어 많은 용어로 다시 작성될 가능성이 큰 `a*`와 같은 광범위한 검색 쿼리는 매우 느릴 수 있습니다. 고성능 와일드카드 검색을 수행하려면 [사용자 지정 분석기](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)를 정의하는 것이 좋습니다.

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>검색 순위가 일정하거나 모든 적중에 대해 점수가 1.0으로 같은 이유는 무엇인가요?

기본적으로 검색 결과는 [일치하는 용어의 통계적 속성](search-lucene-query-architecture.md#stage-4-scoring)에 따라 점수가 매겨지며 결과 집합에서 높은 점수에서 낮은 점수 순으로 순위가 매겨집니다. 그러나 일부 쿼리 유형(와일드카드, 접두사, regex)은 전체적인 문서 점수에 항상 상수 점수를 부여합니다. 이 동작은 의도된 것입니다. Azure Cognitive Search는 순위에 영향을 주지 않고 쿼리 확장을 통해 검색된 일치 항목이 결과에 포함될 수 있도록 일정한 점수를 부과합니다.

예를 들어 와일드카드 검색에서 "tour*"의 입력이 "투어", "투렛", "투르말린"에 일치하는 항목을 생성한다고 가정합니다. 이러한 결과의 특성을 감안할 때, 어떤 용어가 더 중요한지 합리적으로 유추할 방법이 없습니다. 이러한 이유로 와일드카드, 접두사, regex 쿼리 유형의 결과에 대한 점수를 매길 때에는 용어 빈도를 무시합니다. 예기치 않은 일치의 가능성에 따른 왜곡을 방지하기 위해 부분 이력 기준 검색 결과에는 일정 점수가 부여됩니다.

## <a name="design-patterns"></a>디자인 패턴

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>특정 언어 검색을 구현하기 위한 가장 좋은 방법은 무엇인가요?

대부분의 고객은 동일한 인덱스에서 다양한 로캘(언어)을 지원하기 위해 컬렉션보다 전용 필드를 선택합니다. 로캘 특정 필드를 사용하면 적합한 분석기를 할당할 수 있습니다. 예를 들어, 프랑스어 문자열이 들어간 필드에는 Microsoft French Analyzer를 할당합니다. 그렇게 하면 필터링도 간소화됩니다. 쿼리가 Fr-Fr 페이지에서 시작되었음을 알고 있다면 검색 결과를 이 필드로 제한할 수 있습니다. 또는 [점수 매기기 프로필](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)을 만들어 해당 필드에 더 상대적인 가중치를 부여합니다. Azure Cognitive Search는 선택할 수 있는 [50개](https://docs.microsoft.com/azure/search/search-language-support) 이상의 언어 분석기를 지원합니다.

## <a name="next-steps"></a>다음 단계

없는 기능 또는 특징에 대해 질문이 있으면 [사용자 의견 웹 사이트](https://feedback.azure.com/forums/263029-azure-search)에서 요청하세요.

## <a name="see-also"></a>참고 항목

 [스택 오버플로: Azure 인지 검색](https://stackoverflow.com/questions/tagged/azure-search)   
 [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)  
 [Azure Cognitive Search란?](search-what-is-azure-search.md)
