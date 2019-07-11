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
ms.openlocfilehash: a543b17633b99bea63d72f46ba856a8b4593d16a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439549"
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

## <a name="primary-language-detection"></a>기본 언어 감지

QnA Maker 리소스 및 첫 번째 문서 또는 URL 첫 번째 기술 자료에 추가할 때 해당 리소스에 대해 만들어진 모든 기술 자료에 대 한 검색에 사용 된 기본 언어 설정 됩니다. 언어를 변경할 수 없습니다. 

사용자는 여러 언어를 지원할 계획을 하는 경우 각 언어에 대 한 새 QnA Maker 리소스가 해야 합니다. 에 대해 알아봅니다 하는 방법 [QnA Maker 언어 기반 기술 자료 만들기](../how-to/language-knowledge-base.md)합니다.  

다음 단계를 사용 하 여 기본 언어를 확인 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  
1. 에 대 한 확인 하 고 QnA Maker 리소스의 일부로 만든 Azure Search 리소스를 선택 합니다. Azure Search 리소스 QnA Maker 리소스와 동일한 이름으로 시작 됩니다 이름과 유형을 갖습니다 **Search 서비스**합니다. 
1. **개요** 검색 리소스 선택 페이지 **인덱스**합니다. 
1. **testkb** 인덱스를 선택합니다.
1. 선택 된 **필드** 탭 합니다. 
1. 보기는 **분석기** 열에는 **질문** 및 **응답** 필드입니다. 


## <a name="query-matching-and-relevance"></a>쿼리 일치 및 관련성
QnA Maker는 결과를 제공할 때 Azure의 [언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)에 의존합니다. 특수한 순위 재지정 기능은 더 나은 관련성을 제공하는 En-* 언어에서 사용할 수 있습니다.

Azure Search 기능은 지원되는 언어에 동일하며 QnA Maker에는 Azure Search 결과의 상위에 있는 추가 랭커(ranker)가 포함되어 있습니다. 이 우선 순위 모델 en-몇 가지 특별 한 의미 체계 및 word 기반 기능 사용 *를 아직 사용할 수 없는 다른 언어에 대 한 합니다. 에서는 없도록 이러한 기능을 사용할 수 QnA Maker의 우선 순위의 내부 작업의 일부인 것 처럼 합니다. 

QnA Maker [기술 자료의 언어를 자동으로 검색](#primary-language-detection) 를 만드는 동안 및 분석기를 적절 하 게 설정 합니다. 다음 언어로 기술 자료를 만들 수 있습니다. 

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
