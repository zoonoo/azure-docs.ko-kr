---
title: 언어 지원 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 기술 자료에 대한 QnA Maker에서 지원되는 자연 언어인 문화권의 목록입니다. 동일한 기술 자료에서 언어를 혼합하지 마세요.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650905"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker 리소스 및 기술 자료에 대 한 언어 지원

리소스의 첫 번째 기술 자료를 만들 때 서비스에 대 한 언어가 선택 됩니다. 리소스의 모든 추가 기술 자료가 동일한 언어로 되어 있어야 합니다. 

언어는 사용자 쿼리에 대 한 응답으로 제공 QnA Maker 결과의 관련성을 결정 합니다. QnA Maker 리소스 및 해당 리소스 내의 모든 기술 자료는 단일 언어를 지원 합니다. 단일 언어는 쿼리에 가장 적합 한 답변을 제공 하는 데 필요 합니다.

## <a name="single-language-per-resource"></a>리소스 당 단일 언어

다음을 살펴보세요.

* QnA Maker 서비스 및 모든 기술 자료는 한 가지 언어만 지원 합니다.
* 서비스의 첫 번째 기술 자료가 생성 될 때 언어가 명시적으로 설정 됩니다.
* 언어는 기술 자료가 만들어질 때 추가 된 파일 및 Url에서 결정 됩니다.
* 서비스의 다른 기술 자료에 대 한 언어를 변경할 수 없습니다.
* 언어는 Cognitive Search 서비스 (ranker #1) 및 QnA Maker 서비스 (ranker #2)에서 쿼리에 대 한 최상의 답을 생성 하는 데 사용 됩니다.

## <a name="supporting-multiple-languages"></a>여러 언어 지원

여러 언어를 포함 하는 기술 자료 시스템을 지원 해야 하는 경우 다음을 수행할 수 있습니다.

* 질문을 기술 자료에 보내기 전에 [Translator 서비스](../../translator/translator-info-overview.md) 를 사용 하 여 질문을 단일 언어로 번역 합니다. 이를 통해 단일 언어의 품질 및 대체 질문과 대답의 품질에 집중할 수 있습니다.
* 모든 언어에 대해 QnA Maker 리소스 및 해당 리소스 내에 기술 자료를 만듭니다. 이렇게 하면 별도의 대체 질문과 대답 텍스트를 관리할 수 있습니다 .이 텍스트는 각 언어에 대해 더 미묘한. 이는 훨씬 더 많은 유연성을 제공 하지만 모든 언어에서 질문이 나 답변이 변경 될 때 훨씬 높은 유지 관리 비용이 필요 합니다.

QnA Maker에 대해 [지원 되는 언어](../overview/language-support.md) 를 검토 합니다.

### <a name="support-each-language-with-a-qna-maker-resource"></a>QnA Maker 리소스를 사용 하 여 각 언어 지원

* 모든 언어에 대 한 QnA Maker 리소스 만들기
* 해당 언어에 대 한 파일 및 Url만 추가
* 리소스에 대 한 명명 규칙을 사용 하 여 언어를 식별 합니다. 예는 `qna-maker-fr` 프랑스어 문서용 모든 기술 자료에 대 한 것입니다.


## <a name="languages-supported"></a>지원되는 언어

다음 목록에는 QnA Maker 리소스에 대해 지원 되는 언어가 나와 있습니다. 

|언어|
|--|
|아랍어|
|아르메니아어|
|벵골어|
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

## <a name="query-matching-and-relevance"></a>쿼리 일치 및 관련성
QnA Maker은 결과를 제공 하기 위해 [Azure Cognitive Search 언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support) 에 의존 합니다.

Azure Cognitive Search 기능은 지원 되는 언어에 대해 동일 하지만 QnA Maker에는 Azure Search 결과 위에 추가 ranker 있습니다. 이 ranker 모델에서는 다음과 같은 언어에서 특수 한 의미 체계 및 단어 기반 기능을 사용 합니다.

|추가 ranker 있는 언어|
|--|
|중국어|
|체코어|
|네덜란드어|
|영어|
|프랑스어|
|독일어|
|헝가리어|
|이탈리아어|
|일본어|
|한국어|
|폴란드어|
|포르투갈어|
|스페인어|
|스웨덴어|

이 추가 순위는 QnA Maker의 ranker에 대 한 내부 작업입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [언어 선택](../how-to/language-knowledge-base.md)
