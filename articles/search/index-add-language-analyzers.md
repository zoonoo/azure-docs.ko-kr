---
title: 문자열 필드에 언어 분석기 추가
titleSuffix: Azure Cognitive Search
description: 영어가 아닌 쿼리 및 Azure Cognitive Search의 인덱스에 대 한 다중 다국어 어휘 텍스트 분석.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
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
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283148"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Azure Cognitive Search 인덱스의 문자열 필드에 언어 분석기 추가

‘언어 분석기’는 대상 언어의 언어 규칙을 사용하여 어휘 분석을 수행하는 특정 유형의 [텍스트 분석기](search-analyzers.md)입니다. 검색 가능한 모든 필드에 **analyzer** 속성이 있습니다. 영어 및 중국어 텍스트의 개별 필드와 같이 인덱스에 번역된 문자열이 포함되어 있는 경우 각 필드에 언어 분석기를 지정하여 해당 분석기의 풍부한 언어 기능에 액세스할 수 있습니다.  

Azure Cognitive Search는 Lucene에 의해 지원 되는 35 분석기와 Office 및 Bing에서 사용 되는 독점적인 Microsoft 자연어 처리 기술로 지원 되는 50 분석기를 지원 합니다.

## <a name="comparing-analyzers"></a>분석기 비교

일부 개발자는 보다 친숙하고 간단한 Lucene의 오픈 소스 솔루션을 선호할 수 있습니다. Lucene 언어 분석기가 더 빠르지만 Microsoft 분석기에는 분류 정리, 단어 분해(독일어, 덴마크어, 네덜란드어, 스웨덴어, 노르웨이어, 에스토니아어, 핀란드어, 헝가리어, 슬로바키아어 등의 언어) 및 엔터티 인식(URL, 메일, 날짜, 숫자)과 같은 고급 기능이 있습니다. 가능한 경우 Microsoft 분석기와 Lucene 분석기를 비교하여 어떤 것이 더 적합한지 결정해야 합니다. 

언어에 따라 Microsoft 분석기를 사용한 인덱싱은 Lucene보다 평균 두세 배 정도 더 느립니다. 검색 성능은 평균 크기 쿼리에 크게 영향을 받지 않아야 합니다. 

### <a name="english-analyzers"></a>영어 분석기

기본 분석기는 표준 Lucene으로, 영어에서는 잘 작동하지만 Lucene 영어 분석기 또는 Microsoft 영어 분석기만큼 효과적이지 않을 수 있습니다. 
 
+ Lucene 영어 분석기는 표준 분석기를 확장합니다. 이 분석기는 단어에서 소유격(후행 's)을 제거하고, Porter 형태소 분석 알고리즘에 따라 형태소 분석을 적용하며, 영어의 중지 단어를 제거합니다.  

+ Microsoft 영어 분석기는 형태소 분석 대신 분류 정리를 수행합니다. 즉, 더 많은 관련 검색 결과가 발생 하는 만들거나 굴절 형 및 불규칙 한 단어 형태를 처리할 수 있습니다. 

## <a name="configuring-analyzers"></a>분석기 구성

언어 분석기는 있는 그대로 사용됩니다. 인덱스 정의의 각 필드에 대한 **analyzer** 속성으로 언어 및 언어 체계 스택(Microsoft 또는 Lucene)을 지정하는 분석기 이름을 설정할 수 있습니다. 해당 필드를 인덱싱 및 검색하는 경우 동일한 분석기를 적용합니다. 예를 들어 영어, 프랑스어, 스페인어 호텔 설명을 표시하는 개별 필드를 같은 인덱스에서 나란히 표시할 수 있습니다.

> [!NOTE]
> 필드에 대 한 쿼리 시간에는 인덱싱 시 다른 언어 분석기를 사용할 수 없습니다. 이 기능은 [사용자 지정 분석기](index-add-custom-analyzers.md)에 대해 예약 되어 있습니다. 이러한 이유로 **searchanalyzer** 또는 **indexanalyzer** 속성을 언어 분석기의 이름으로 설정 하려고 하면 REST API 오류 응답이 반환 됩니다. 대신 **analyzer** 속성을 사용 해야 합니다.

**searchFields** 쿼리 매개 변수를 사용하여 쿼리에서 검색할 언어별 필드를 지정합니다. analyzer 속성을 포함하는 쿼리 예제는 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents)에서 검토할 수 있습니다. 

인덱스 속성에 대 한 자세한 내용은 [Create index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조 하세요. Azure Cognitive Search 분석에 대 한 자세한 내용은 [azure Cognitive Search의 분석기](https://docs.microsoft.com/azure/search/search-analyzers)를 참조 하세요.

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>언어 분석기 목록 
 다음은 Lucene 및 Microsoft 분석기 이름으로 지원되는 언어의 목록입니다.  

|언어|Microsoft 분석기 이름|Lucene 분석기 이름|  
|--------------|-----------------------------|--------------------------|  
|아랍어|ar.microsoft|ar.lucene|  
|아르메니아어||hy.lucene|  
|벵골어|bn.microsoft||  
|바스크어||eu.lucene|  
|불가리아어|bg.microsoft|bg.lucene|  
|카탈로니아어|ca.microsoft|ca.lucene|  
|중국어 간체|zh-Hans.microsoft|zh-Hans.lucene|  
|중국어 번체|zh-Hant.microsoft|zh-Hant.lucene|  
|크로아티아어|hr.microsoft||  
|체코어|cs.microsoft|cs.lucene|  
|덴마크어|da.microsoft|da.lucene|  
|네덜란드어|nl.microsoft|nl.lucene|  
|영어|en.microsoft|en.lucene|  
|에스토니아어|et.microsoft||  
|핀란드어|fi.microsoft|fi.lucene|  
|프랑스어|fr.microsoft|fr.lucene|  
|갈리시아어||gl.lucene|  
|독일어|de.microsoft|de.lucene|  
|그리스어|el.microsoft|el.lucene|  
|구자라트어|gu.microsoft||  
|히브리어|he.microsoft||  
|힌디어|hi.microsoft|hi.lucene|  
|헝가리어|hu.microsoft|hu.lucene|  
|아이슬란드어|is.microsoft||  
|인도네시아어(공용어)|id.microsoft|id.lucene|  
|아일랜드어||ga.lucene|  
|이탈리아어|it.microsoft|it.lucene|  
|일본어|ja.microsoft|ja.lucene|  
|칸나다어|kn.microsoft||  
|한국어|ko.microsoft|ko.lucene|  
|라트비아어|lv.microsoft|lv.lucene|  
|리투아니아어|lt.microsoft||  
|말라얄람어|ml.microsoft||  
|말레이어(라틴 문자)|ms.microsoft||  
|마라티어|mr.microsoft||  
|노르웨이어|nb.microsoft|no.lucene|  
|페르시아어||fa.lucene|  
|폴란드어|pl.microsoft|pl.lucene|  
|포르투갈어(브라질)|pt-Br.microsoft|pt-Br.lucene|  
|포르투갈어(포르투갈)|pt-Pt.microsoft|pt-Pt.lucene|  
|펀잡어|pa.microsoft||  
|루마니아어|ro.microsoft|ro.lucene|  
|러시아어|ru.microsoft|ru.lucene|  
|세르비아어(키릴 자모)|sr-cyrillic.microsoft||  
|세르비아어(라틴 문자)|sr-latin.microsoft||  
|슬로바키아어|sk.microsoft||  
|슬로베니아어|sl.microsoft||  
|스페인어|es.microsoft|es.lucene|  
|스웨덴어|sv.microsoft|sv.lucene|  
|타밀어|ta.microsoft||  
|텔루구어|te.microsoft||  
|태국어|th.microsoft|th.lucene|  
|터키어|tr.microsoft|tr.lucene|  
|우크라이나어|uk.microsoft||  
|우르두어|ur.microsoft||  
|베트남어|vi.microsoft||  

 이름에 **Lucene** 주석이 포함된 모든 분석기는 [Apache Lucene 언어 분석기](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )를 통해 구동됩니다.

## <a name="see-also"></a>참고 항목  

+ [Azure Cognitive Search &#40;REST API 인덱스 만들기&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

