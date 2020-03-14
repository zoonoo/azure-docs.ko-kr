---
title: 언어 개념-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 여러 언어로 기술 자료 콘텐츠를 지원합니다. 그러나 각 QnA Maker 서비스는 단일 언어에 예약되어야 합니다. 특정 QnA Maker 서비스를 대상으로 하 여 만든 첫 번째 기술 자료는 해당 서비스의 언어를 설정 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221446"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA 작성기에 대한 기술 콘텐츠의 언어 지원

리소스의 첫 번째 기술 자료를 만들 때 서비스에 대 한 언어가 선택 됩니다. 리소스의 모든 추가 기술 자료가 동일한 언어로 되어 있어야 합니다.

언어는 사용자 쿼리에 대 한 응답으로 제공 QnA Maker 결과의 관련성을 결정 합니다.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>리소스의 모든 기술 자료에 대 한 한 가지 언어

QnA Maker를 사용 하 여 첫 번째 기술 자료를 만드는 동안 QnA 서비스에 대 한 언어를 선택할 수 있습니다. QnA Maker 리소스의 모든 기술 자료에 대해 모두 같은 언어로 되어 있어야 합니다. 이 언어를 변경할 수 없습니다.

한 리소스에서 다른 언어로 기술 자료를 만들면 사용자 쿼리에 대 한 응답으로 제공 QnA Maker 결과의 관련성에 부정적인 영향을 줍니다.

[지원 되는 언어](../overview/language-support.md#languages-supported) 목록과 언어의 [일치 및 관련성](#query-matching-and-relevance)을 검토 합니다.

## <a name="select-language-when-creating-first-knowledge-base"></a>첫 번째 기술 자료를 만들 때 언어 선택

언어 선택은 리소스의 첫 번째 기술 자료를 만드는 단계의 일부입니다.

![첫 번째 기술 자료에 대 한 언어를 선택 하 QnA Maker 포털 스크린샷](../media/language-support/select-language-when-creating-knowledge-base.png)

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

## <a name="verify-language"></a>언어 확인

QnA Maker의 서비스 설정 페이지에서 QnA Maker 리소스의 언어를 확인할 수 있습니다.

![QnA Maker 포털 서비스 설정 페이지의 스크린샷](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 마이그레이션](../Tutorials/migrate-knowledge-base.md)
