---
title: Azure Search 인덱스에 제안기 추가
description: 제안된 쿼리가 Azure Search 인덱스의 필드에 있는 텍스트로 구성되는 자동 완성(type-ahead) 쿼리 작업에 대한 필드를 사용하도록 설정합니다.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 35025d69865aa6890e1cd921e31ac6c26c015789
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007701"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Azure Search 인덱스에 제안기 추가

**제안기**는 "입력하는 동시에 검색"(search-as-you-type) [제안](https://docs.microsoft.com/rest/api/searchservice/suggestions) 기능과 [자동 완성(미리 보기)](search-autocomplete-tutorial.md) 기능을 지원하는 Azure Search 구문입니다. 제안 API를 호출하기 전에 특정 필드에 대한 제안을 사용하도록 설정하려면 인덱스에 **제안기**를 정의해야 합니다.

**제안기**에는 여러 속성이 있지만 기본적으로 [제안 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)를 사용하도록 설정되는 필드의 컬렉션입니다. 예를 들어 여행 앱은 목적지, 도시 및 명소에 대한 자동 완성(typeahead) 검색을 사용하도록 설정할 수도 있습니다. 따라서 세 가지 필드가 모두 필드 컬렉션에 포함됩니다.

각 인덱스에 대해 하나의 **제안기** 리소스만 사용할 수 있습니다(특히 **suggesters** 컬렉션에서 하나의 **제안기**).

**제안기**는 언제든지 만들 수 있지만 인덱스에 미치는 영향은 필드에 따라 달라집니다. 동일한 업데이트의 일환으로 제안기에 추가된 새 필드는 인덱스를 다시 작성할 필요가 없다는 점에서 영향력이 가장 작습니다. 그러나 기존 필드를 추가하면 필드 정의가 변경되므로 인덱스를 완전히 다시 작성해야 합니다.

## <a name="usage"></a>사용 현황  

 **제안기**는 모호한 용어나 구보다는 특정 문서를 제안하는 데 사용하는 경우에 가장 효율적입니다. 확인기를 사용하기에 적합한 필드는 제목, 이름 및 항목을 식별할 수 있는 비교적 짧은 기타 구입니다. 반면 범주/태그 등의 반복 필드나 설명/주석 등의 매우 긴 필드는 확인기를 사용하기에 덜 적합합니다.  

제안기가 만들어지면 쿼리 논리에 [제안 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)를 추가하여 기능을 호출합니다.  

**제안기**를 정의하는 속성은 다음과 같습니다.  

|자산|설명|  
|--------------|-----------------|  
|`name`|**제안기**의 이름입니다. [제안&#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)을 호출할 때 **제안기**의 이름을 사용합니다.|  
|`searchMode`|후보 구를 검색하는 데 사용되는 전략입니다. 현재 지원되는 모드는 `analyzingInfixMatching`뿐입니다. 이 모드에서는 문장 시작 부분이나 중간에서 유동적 구 일치를 수행합니다.|  
|`sourceFields`|제안 내용의 원본인 하나 이상의 필드 목록입니다. 형식이 `Edm.String` 및 `Collection(Edm.String)`인 필드만 제안 원본으로 사용할 수 있습니다. 또한 사용자 지정 언어 분석기가 설정되지 않은 필드만 사용할 수 있습니다. |  

## <a name="suggester-example"></a>제안기 예제  
 **제안기**는 인덱스 정의의 일부입니다. 현재 버전의 **제안기** 컬렉션에는 **fields** 컬렉션 및 **scoringProfiles**와 함께 하나의 **제안기**만 있을 수 있습니다.  

```  
{  
  "name": "hotels",  
  "fields": [  
     . . .   
   ],  
  "suggesters": [  
    {  
    "name": "sg",  
    "searchMode": "analyzingInfixMatching",  
    "sourceFields": ["hotelName", "category"]  
    }  
  ],  
  "scoringProfiles": [  
     . . .   
  ]  
}  

```  

## <a name="see-also"></a>참고 항목  
 [인덱스 만들기&#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [인덱스 업데이트&#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)   
 [제안&#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)   
 [인덱스 작업&#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)   
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Azure Search.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
