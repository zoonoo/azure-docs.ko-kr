---
title: 언어 지원 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 기술 자료에 대한 QnA Maker에서 지원되는 자연 언어인 문화권의 목록입니다. 동일한 기술 자료에서 언어를 혼합하지 마세요.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: dc910c01c401468a3dae392a6318344bee25efb7
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375643"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker 리소스 및 기술 자료에 대 한 언어 지원

이 문서에서는 QnA Maker 리소스 및 기술 자료에 대 한 언어 지원 옵션을 설명 합니다. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

리소스의 첫 번째 기술 자료를 만들 때 서비스에 대 한 언어가 선택 됩니다. 리소스의 모든 추가 기술 자료가 동일한 언어로 되어 있어야 합니다. 

언어는 사용자 쿼리에 대 한 응답으로 제공 QnA Maker 결과의 관련성을 결정 합니다. QnA Maker 리소스 및 해당 리소스 내의 모든 기술 자료는 단일 언어를 지원 합니다. 단일 언어는 쿼리에 가장 적합 한 답변을 제공 하는 데 필요 합니다.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

관리 QnA Maker에서 개별 기술 자료 수준에서 언어 설정을 선택할 수 있습니다. 이 설정은 서비스의 기술 자료 에서만 사용 하도록 설정할 수 있습니다. 설정 되 면 서비스에 대 한 언어 설정을 변경할 수 없습니다. 

기술 자료를 기반으로 하는 언어 설정을 선택 하는 경우 서비스 자체에서 다른 언어의 기술 자료를 만들 수 있습니다. 

---

## <a name="single-language-per-resource"></a>리소스 당 단일 언어

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

다음을 살펴보세요.

* QnA Maker 서비스 및 모든 기술 자료는 한 가지 언어만 지원 합니다.
* 서비스의 첫 번째 기술 자료가 생성 될 때 언어는 명시적으로 설정 됩니다.
* 언어는 기술 자료가 만들어질 때 추가 된 파일 및 Url에서 결정 됩니다.
* 서비스의 다른 기술 자료에 대 한 언어를 변경할 수 없습니다.
* 언어는 Cognitive Search 서비스 (ranker #1) 및 QnA Maker 서비스 (ranker #2)에서 쿼리에 대 한 최상의 답을 생성 하는 데 사용 됩니다.

# <a name="qnamaker-managed-preview"></a>[QnAMaker 관리 (미리 보기)](#tab/v2)
![QnA Maker 관리 되는 언어 설정](../media/language-support/language-setting-managed.png)

**기술 자료 별 언어 설정 사용 확인란을 선택 하지 않은** 경우 다음 사항을 고려 하십시오. 
* QnA Maker 서비스와 모든 해당 기술 자료는 한 가지 언어만 지원 합니다.
* 서비스의 첫 번째 기술 자료가 생성 될 때 언어가 명시적으로 설정 됩니다.
* 언어는 기술 자료가 만들어질 때 추가 된 파일 및 Url에서 결정 됩니다.
* 서비스의 다른 기술 자료에 대 한 언어를 변경할 수 없습니다.
* 언어는 Cognitive Search 서비스 (ranker #1) 및 QnA Maker 서비스 (ranker #2)에서 쿼리에 대 한 최상의 답을 생성 하는 데 사용 됩니다.

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>하나의 QnA Maker 리소스에서 여러 언어 지원

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)
이 기능은 현재 GA (일반 공급) 안정적인 릴리스에서 지원 되지 않습니다. 이 기능을 테스트 하려면 관리 QnA Maker를 확인 하세요. 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)
* 서비스에서 첫 번째 기술 자료를 만들 때 기술 자료 당 언어 설정을 사용 하도록 선택할 수 있습니다. 한 서비스 내에서 다른 언어에 속하는 기술 자료를 만들려면이 확인란을 선택 합니다.
* 첫 번째 기술 자료가 만들어진 후에는 서비스에 대 한 언어 설정 옵션을 수정할 수 없습니다.
* 모든 기술 자료에 특정 한 언어 설정을 사용 하도록 설정 하는 경우 서비스에 대해 하나의 테스트 인덱스를 사용 하는 대신 기술 자료 당 하나의 테스트 인덱스를 갖게 됩니다. 

![QnA Maker 관리 되는 언어 설정](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>한 기술 자료에서 여러 언어 지원

여러 언어를 포함 하는 기술 자료 시스템을 지원 해야 하는 경우 다음을 수행할 수 있습니다.

* 질문을 기술 자료에 보내기 전에 [Translator 서비스](../../translator/translator-info-overview.md) 를 사용 하 여 질문을 단일 언어로 번역 합니다. 이를 통해 단일 언어의 품질 및 대체 질문과 대답의 품질에 집중할 수 있습니다.
* 모든 언어에 대해 QnA Maker 리소스 및 해당 리소스 내에 기술 자료를 만듭니다. 이렇게 하면 별도의 대체 질문과 대답 텍스트를 관리할 수 있습니다 .이 텍스트는 각 언어에 대해 더 미묘한. 이는 훨씬 더 많은 유연성을 제공 하지만 모든 언어에서 질문이 나 답변이 변경 될 때 훨씬 높은 유지 관리 비용이 필요 합니다.


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
