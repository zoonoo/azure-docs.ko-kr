---
title: 언어 지원 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 기술 자료에 대한 QnA Maker에서 지원되는 자연 언어인 문화권의 목록입니다. 동일한 기술 자료에서 언어를 혼합하지 마세요.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/04/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3bdc3a91e87f1bbd06ef0181d0042ee618f519c9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861445"
---
# <a name="language-and-region-support-for-qna-maker"></a>QnA Maker에 대한 언어 및 지역 지원

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

## <a name="query-matching-and-relevance"></a>쿼리 일치 및 관련성
QnA Maker는 결과를 제공할 때 Azure의 [언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)에 의존합니다. 특수한 순위 재지정 기능은 더 나은 관련성을 제공하는 En-* 언어에서 사용할 수 있습니다.

Azure Search 기능은 지원되는 언어에 동일하며 QnA Maker에는 Azure Search 결과의 상위에 있는 추가 랭커(ranker)가 포함되어 있습니다. 이 순위매기기 모델에서는 일부 특수한 의미 체계 및 단어 기반 기능을 en-*에서 사용하며, 아직 다른 언어에는 해당 기능이 제공되지 않습니다. 이러한 기능은 랭커의 내부 작업에 속하므로 제공하지 않습니다. 

QnA Maker는 기술 자료를 만드는 동안에 해당 언어를 자동으로 검색하고 그에 따라 분석기를 설정합니다. 다음 언어로 기술 자료를 만들 수 있습니다. QnA Maker가 언어를 처리하는 방법에 대한 자세한 내용을 보려면 [이 내용](../How-To/language-knowledge-base.md)을 읽어보세요.


> [!Tip]
> 일단 설정된 언어 분석기는 변경할 수 없습니다. 또한 언어 분석기는 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)의 모든 기술 자료에 적용됩니다. 기술 자료를 다른 언어로 만들려는 경우 별도 QnA Maker 서비스에서 만들어야 합니다.

|지원되는 언어|
|-----|
|아랍어|
|아르메니아어|.
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
