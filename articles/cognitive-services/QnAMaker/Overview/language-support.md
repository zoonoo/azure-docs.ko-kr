---
title: 언어 지원 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 기술 자료의 언어는 QnA Maker가 원본에서 질문 및 답변을 자동으로 추출하는 기능과 사용자 쿼리에 대한 응답으로 QnA Maker가 제공하는 결과의 관련성에도 영향을 미칩니다. 기술 자료에 대한 QnA Maker에서 지원되는 자연 언어인 문화권의 목록입니다. 동일한 기술 자료에서 언어를 혼합하지 마세요.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066674"
---
# <a name="language-support-for-qna-maker"></a>QnA Maker에 대 한 언어 지원

기술 자료의 언어는 QnA Maker가 [원본](../Concepts/data-sources-supported.md)에서 질문 및 답변을 자동으로 추출하는 기능과 사용자 쿼리에 대한 응답으로 QnA Maker가 제공하는 결과의 관련성에도 영향을 미칩니다.

## <a name="auto-extraction"></a>자동 추출
QnA Maker는 모든 언어 페이지에서 질문/답변 추출을 지원하지만, 키워드를 사용하여 질문을 식별하므로 추출의 효과는 다음 언어에서 훨씬 더 높게 나타납니다.

|지원되는 언어| 로캘|
|-----|----|
|영어|en-*|
|프랑스어|fr-*|
|이탈리아어|it-*|
|독일어|de-*|
|스페인어|es-*|

## <a name="primary-language-detection"></a>주 언어 검색

검색에 사용 되는 주 언어는 첫 번째 문서 또는 URL이 첫 번째 기술 자료에 추가 될 때 QnA Maker 리소스 및 해당 리소스에 대해 만들어진 모든 기술 자료에 대해 설정 됩니다. 언어를 변경할 수 없습니다. 

사용자가 여러 언어를 지원 하도록 계획 하는 경우 각 언어에 대 한 새 QnA Maker 리소스가 필요 합니다. [언어 기반 QnA Maker 기술 자료를 만드는](../how-to/language-knowledge-base.md)방법에 대해 알아봅니다.  

다음 단계를 수행 하 여 주 언어를 확인 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  
1. QnA Maker 리소스의 일부로 생성 된 Azure Search 리소스를 찾아 선택 합니다. Azure Search 리소스 이름은 QnA Maker 리소스와 동일한 이름으로 시작 되 고 유형 **검색 서비스**를 갖게 됩니다. 하나의 QnA Maker 리소스를 하나의 Azure Search 리소스에만 연결할 수 있다는 점에 유의 하세요.
1. 검색 리소스의 **개요** 페이지에서 **인덱스**를 선택 합니다. 
1. **testkb** 인덱스를 선택합니다.
1. **필드** 탭을 선택 합니다. 
1. **질문** 및 **답변** 필드의 **Analyzer** 열을 확인 합니다. 


## <a name="query-matching-and-relevance"></a>쿼리 일치 및 관련성
QnA Maker는 결과를 제공할 때 Azure의 [언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)에 의존합니다. 특수한 순위 재지정 기능은 더 나은 관련성을 제공하는 En-* 언어에서 사용할 수 있습니다.

Azure Search 기능은 지원되는 언어에 동일하며 QnA Maker에는 Azure Search 결과의 상위에 있는 추가 랭커(ranker)가 포함되어 있습니다. 이 ranker 모델에서는 다른 언어에서 아직 사용할 수 없는 일부 특수 의미 체계 및 단어 기반 기능을 en-*에서 사용 합니다. 이러한 기능은 QnA Maker의 ranker 내부 작업의 일부 이므로 사용할 수 없습니다. 

QnA Maker는 생성 시 [기술 자료의 언어를 자동으로 검색](#primary-language-detection) 하 고 그에 따라 분석기를 설정 합니다. 다음 언어로 기술 자료를 만들 수 있습니다. 

|지원되는 언어|
|-----|
|아랍어|
|아르메니아어|
벵골어|
|바스크어|
|불가리아어|
|카탈로니아어|
|중국어 간체|
|중국어 번체|
|크로아티아어|
|체코어|
|덴마크어|
|네덜란드어|
|영어|
|에스토니아어|
|핀란드어|
|프랑스어|
|갈리시아어|
|독일어|
|그리스어|
|구자라트어|
|히브리어|
|힌디어|
|헝가리어|
|아이슬란드어|
|인도네시아어|
|아일랜드어|
|이탈리아어|
|일본어|
|칸나다어|
|한국어|
|라트비아어|
|리투아니아어|
|말라얄람어|
|말레이어|
|노르웨이어|
|폴란드어|
|포르투갈어|
|펀잡어|
|루마니아어|
|러시아어|
|세르비아어 키릴 자모|
|세르비아어 라틴어|
|슬로바키아어|
|슬로베니아어|
|스페인어|
|스웨덴어|
|타밀어|
|텔루구어|
|태국어|
|터키어|
|우크라이나어|
|우르두어|
|베트남어|
