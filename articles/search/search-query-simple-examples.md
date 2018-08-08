---
title: Azure Search의 단순 쿼리 예제 | Microsoft Docs
description: 전체 텍스트 검색, 필터 검색, 지리적 검색, 패싯 검색 및 Azure Search 인덱스 쿼리에 사용되는 기타 쿼리 문자열에 대한 단순 쿼리 예제입니다.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: heidist
ms.openlocfilehash: e4bb72eb8ad6f15b0e5bc14e0e07556e76d0477b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368601"
---
# <a name="simple-syntax-query-examples-for-building-queries-in-azure-search"></a>Azure Search에서 퀴리를 만들기 위한 단순 구문 예제

[단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)은 Azure Search 인덱스에 대해 전체 텍스트 검색 쿼리를 실행하기 위해 기본 쿼리 파서를 호출합니다. 단순 쿼리 분석기는 신속하며, Azure Search에서 전체 텍스트 검색, 필터링 및 패싯 검색, 지리적 검색 등의 일반적인 시나리오를 처리합니다. 이 문서에서는 단순 구문을 사용할 때 사용할 수 있는 쿼리 작업을 보여 주는 예제를 단계별로 진행합니다.

대체 쿼리 구문은 유사 항목 및 와일드 카드 검색과 같은 보다 복잡한 쿼리 구문을 지원하며 처리하는 데 추가 시간이 소요될 수 있는 [전체 Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)입니다. 자세한 내용 및 전체 구문을 보여 주는 예제에 대해서는 [Lucene 구문 쿼리 예제](search-query-lucene-examples.md)를 참조하세요.

## <a name="formulate-requests-in-postman"></a>Postman에서 요청 작성

다음 예제는 [City of New York OpenData](https://nycopendata.socrata.com/) 이니셔티브에서 제공하는 데이터 집합에 기반하여 사용 가능한 작업으로 구성된 NYC Jobs 검색 인덱스를 활용합니다. 이 데이터는 최신 또는 완료로 간주되어서는 안 됩니다. 이 인덱스는 Microsoft에서 제공하는 Sandbox 서비스에 있으며 이러한 쿼리를 시도하기 위해 Azure 구독 또는 Azure Search가 필요하지 않음을 의미합니다.

따라서 GET에서 HTTP 요청을 실행하기 위한 Postman 또는 동급의 도구만 있으면 됩니다. 자세한 내용은 [REST 클라이언트로 테스트](search-fiddler.md)를 참조하세요.

### <a name="set-the-request-header"></a>요청 헤더 설정

1. 요청 헤더에서 **Content-Type**을 `application/json`으로 설정합니다.

2. **api-key**를 추가한 후 `252044BE3886FE4A8E3BAA4F595114BB` 문자열로 설정합니다. 이것은 NYC Jobs 인덱스를 호스트하는 샌드박스 Search 서비스용 쿼리 키입니다.

요청 헤더를 지정한 후 **search=** 문자열만 교환하여 이 문서의 모든 쿼리에 다시 사용할 수 있습니다. 

  ![Postman 요청 헤더](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>요청 URL 설정

요청은 Azure Search 끝점 및 검색 문자열을 포함하는 URL과 연결되는 GET 명령입니다.

  ![Postman 요청 헤더](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

URL 구성에는 다음 요소가 있습니다.

+ **`https://azs-playground.search.windows.net/`** 은 Azure Search 개발 팀에서 유지 관리하는 Sandbox Search 서비스입니다. 
+ **`indexes/nycjobs/`** 는 해당 서비스의 인덱스 컬렉션에 있는 NYC 작업 인덱스입니다. 요청에는 서비스 이름과 인덱스가 둘 다 필요합니다.
+ **`docs`** 는 모든 검색 가능한 콘텐츠를 포함하는 문서 컬렉션입니다. 요청 헤더에 제공되는 쿼리 api-key는 문서 컬렉션을 대상으로 하는 읽기 작업에만 작동합니다.
+ **`api-version=2017-11-11`** 은 모든 요청에서 필수 매개 변수인 api-version을 설정합니다.
+ **`search=*`** 는 초기 쿼리에서 null이며 처음 50개 결과(기본값)를 반환하는 쿼리 문자열입니다.

## <a name="send-your-first-query"></a>첫 번째 쿼리 전송

확인 단계에서 다음 요청을 GET에 붙여넣고 **보내기**를 클릭합니다. 결과는 자세한 JSON 문서로 반환됩니다. 아래의 첫 번째 예제에서는 이 URL을 복사하고 붙여 넣을 수 있습니다.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=*
  ```

쿼리 문자열 **`search=*`** 은 null에 해당하는 미지정 검색이거나 빈 검색입니다. 이 검색은 그리 유용하지 않지만 할 수 있는 가장 간단한 검색입니다.

필요에 따라 URL에 **`$count=true`** 를 추가하여 검색 조건과 일치하는 문서 수를 반환할 수 있습니다. 빈 검색 문자열에서는 인덱스(NYC 작업의 경우 2802)에 있는 모든 문서입니다.

## <a name="how-to-invoke-simple-query-parsing"></a>단순 쿼리 구문 분석을 호출하는 방법

대화형 쿼리의 경우 어떤 것도 지정할 필요가 없습니다. simple이 기본값입니다. 코드에서 이전에 전체 쿼리 구문에 대해 **queryType=full**을 호출한 경우 **queryType=simple**을 사용하여 기본값으로 재설정할 수 있습니다.

## <a name="example-1-field-scoped-query"></a>예제제 1: 필드 범위 쿼리

첫 번째 쿼리는 구문별 쿼리(쿼리가 단순 및 전체 구문 둘 다에 작동)가 아니지만 이 예제를 사용하여 판독 가능한 JSON 요청을 생성하는 초기 쿼리 개념을 소개하려고 합니다. 간단히 하기 위해 쿼리는 *business_title*만을 대상으로 하며, 직함만 반환되도록 지정합니다. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

**searchFields** 매개 변수는 검색을 직위 필드로만 제한합니다. **select** 매개 변수는 결과 집합에 포함되는 필드를 결정합니다.

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  ![Postman 샘플 응답](media/search-query-lucene-examples/postman-sample-results.png)

검색 점수를 지정하지 않더라도, 모든 문서에 대해 검색 점수가 반환된다는 것을 알고 있을 것입니다. 검색 점수는 결과의 순위를 나타내는 값을 포함하는 메타데이터이기 때문입니다. 순위가 없으면 검색이 전체 텍스트 검색이 아니거나 적용할 조건이 없기 때문에 균일하게 점수 1이 지정됩니다. null 검색의 경우 조건이 없고, 반환되는 행은 임의 순서로 표시됩니다. 검색 조건에 더 많은 정의가 포함되면 검색 점수가 의미 있는 값으로 바뀌는 것을 볼 수 있습니다.

## <a name="example-2-look-up-by-id"></a>예제 2: ID별 조회

이 예제는 약간 특이하지만, 검색 동작을 평가할 때 결과에 포함되거나 제외된 이유를 이해하기 위해 문서 전체를 확인하려고 할 수 있습니다. 전체 문서를 반환하려면 [조회 작업](https://docs.microsoft.com/rest/api/searchservice/lookup-document)을 사용하여 문서 ID를 전달합니다.

모든 문서에는 고유 식별자가 있습니다. 조회 쿼리를 위한 구문을 사용하려면 사용할 문서를 찾을 수 있도록 먼저 문서 ID 목록을 반환합니다. NYC Jobs의 경우 식별자는 `id` 필드에 저장됩니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
 ```

다음 예제는 이전 응답에서서 맨 처음에 나오는 `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5"를 기준으로 특정 문서를 반환하는 조회 쿼리입니다. 다음 쿼리는 선택한 필드만이 아닌 전체 문서를 반환합니다. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
 ```

## <a name="example-3-search-precision"></a>예제 3: 검색 정확도

용어 쿼리는 단일 용어로, 대부분이 개별적으로 평가될 것입니다. 구 쿼리는 따옴표로 묶인 후 약어 문자열로 평가됩니다. 일치의 정확도는 연산자 및 searchMode로 제어됩니다.

예제 1: **`&search=fire`** 는 모든 일치에 문서 임의 위치에 단어 fire를 포함되는 150개 결과를 반환합니다.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

예제 2: **`&search=fire department`** 는 2002개 결과를 반환합니다. fire 또는 department를 포함하는 문서가 일치하는 것으로 반환됩니다.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

예제 3: **`&search="fire department"`** 는 82개 결과를 반환합니다. 문자열을 따옴표로 묶으면 두 용어에 대해 약어 검색이 수행되고, 인덱스에서 결합된 용어로 구성된 토큰화된 용어에 대해 일치 문서가 검색됩니다. 이것은 **`search=+fire +department`** 와 같은 검색이 동일하지 않은 이유를 설명합니다. 두 용어는 필수하지만 별도로 검색됩니다. 

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-4-booleans-with-searchmode"></a>예제 4: SearchMode를 사용한 부울

간단한 구문은 문자 형태의 부울 연산자를 지원합니다(`+, -, |`). searchMode 매개 변수는 재현율에 좀 더 유리한 `searchMode=any`(조건 중 하나라도 일치할 경우 결과 집합에 문서 포함함) 및 정확도에 좀 더 유리한 `searchMode=all`(모든 조건이 일치해야 함)을 사용하여 정확도와 재현율 간의 절충을 알립니다. 기본값은 `searchMode=any`로, 여러 연산자를 사용하여 쿼리를 누적하고 좁은 범위의 결과 대신 좀 더 넓은 범위의 결과를 가져오는 경우 혼동을 일으킬 수 있습니다. 결과에 특정 용어를 “포함하지 않는” 모든 문서를 포함하게 되는 NOT을 사용할 경우에는 특히 이러한 현상이 두드러집니다.

기본 searchMode (any)를 사용하는 경우 합성어인 "fire department"를 포함하는 문서와 용어 "Metrotech Center"를 포함하지 않는 모든 문서에 해당하는 2,800개 문서가 반환됩니다.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```
searchMode를 `all`로 변경하면 조건에 대해 누적 효과가 적용되고 전체 구인 "fire department"를 포함하는 문서에서 Metrotech Center 주소에 직장이 있는 경우를 제외하여 더 적은 결과 집합인 21개 문서가 반환됩니다.

```
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```


## <a name="example-5-structuring-results"></a>예제 5: 결과 구성

여러 매개 변수가 결과 집합에 포함된 필드, 각 일괄 처리로 반환되는 문서의 수 및 정렬 순서를 제어합니다. 이 예제에서는 이전 예제의 일부를 다시 사용하고 **$select** 문과 약어 검색 조건을 사용하여 결과를 특정 필드로 제한한 후 82개 일치 항목을 반환합니다. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
이전 예제에 덧붙여, 제목을 기준으로 정렬할 수 있습니다. civil_service_title이 인덱스에서 *정렬 가능*하므로 이 정렬을 사용할 수 있습니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

페이징 결과가 **$top** 매개 변수를 사용하여 구현되어, 상위 5개 문서가 반환됩니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

다음 5개 문서를 가져오려면 첫 번째 일괄 처리를 건너뜁니다.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>다음 단계
코드에서 쿼리를 지정합니다. 다음 링크에서는 기본 단순 구문을 사용하여 .NET와 REST API 모두에 대한 검색 쿼리를 설정하는 방법에 대해 설명합니다.

* [.NET SDK를 사용하여 Azure Search 인덱스 쿼리](search-query-dotnet.md)
* [REST API를 사용하여 Azure Search 인덱스 쿼리](search-query-rest-api.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [전체 Lucene 쿼리](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
