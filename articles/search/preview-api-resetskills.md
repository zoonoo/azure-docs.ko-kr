---
title: 기술 다시 설정 (api-version = 2019-050 -06-Preview)
titleSuffix: Azure Cognitive Search
description: 기술의 전체 또는 부분 다시 처리가 필요한 경우 증분 보강 사용 되는 미리 보기 REST API입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832152"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>기술 다시 설정 (api-version = 2019-05 -06-Preview)

> [!IMPORTANT] 
> 증분 보강 현재 공개 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 포털 또는 .NET SDK가 지원 되지 않습니다.

**기술 다시 설정** 요청은 다음 인덱서 실행 시 처리할 기술을 지정 합니다. 캐싱이 활성화 된 인덱서의 경우 인덱서가 검색할 수 없는 기술 업데이트에 대 한 처리를 명시적으로 요청할 수 있습니다. 예를 들어 사용자 지정 기술에 대 한 수정 버전과 같은 외부 변경을 수행 하는 경우이 API를 사용 하 여 기술을 다시 실행할 수 있습니다. 기술 자료 저장소 또는 검색 인덱스와 같은 출력은 캐시의 재사용 가능한 데이터 및 업데이트 된 기술 당 새 콘텐츠를 사용 하 여 새로 고쳐집니다.

요청 URI에서 업데이트할 기술의 이름을 지정 하 여 HTTP PUT을 사용 하 여 기존 [기술](https://docs.microsoft.com/rest/api/searchservice/create-skillset) 를 다시 설정할 수 있습니다. 요청에서 **api-version = 2019-05 -06-Preview** 를 사용 해야 합니다.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

또는 POST를 사용 하 여 요청 본문에 인덱서 이름을 입력 합니다.

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>헤더 요청  

 다음 표에서는 필수 요청 헤더와 선택적 요청 헤더에 대해 설명합니다.  

|요청 헤더|Description|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`|  
|*api-key:*|필수 사항입니다. `api-key`는 Search 서비스에 대한 요청을 인증하는 데 사용되며, 서비스에 고유한 문자열 값입니다. **Reset 기술** 요청은 쿼리 키가 아니라 관리 키로 설정 된 `api-key` 헤더를 포함 해야 합니다.|  

요청 URL을 구성하려면 서비스 이름도 필요합니다. Azure Portal 서비스 개요 페이지에서 서비스 이름과 `api-key`를 모두 가져올 수 있습니다. 페이지 탐색 도움말 [은 Azure Cognitive Search 서비스 만들기](https://docs.microsoft.com/azure/search/search-create-service-portal) 를 참조 하세요.  

## <a name="request-body-syntax"></a>요청 본문 구문  

요청의 본문은 기술 이름 배열입니다.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>응답  

상태 코드: 응답에 성공하면 ‘204 콘텐츠 없음'이 반환됩니다. 

## <a name="see-also"></a>참고 항목

+ [REST Api 검색](https://docs.microsoft.com/rest/api/searchservice)
+ [HTTP 상태 코드](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [AI 보강 개요](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [캐싱 및 증분 보강 구성](search-howto-incremental-index.md)
+ [증분 보강](cognitive-search-incremental-indexing-conceptual.md)