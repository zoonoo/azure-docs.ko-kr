---
title: 데이터 내보내기 및 삭제
titleSuffix: Azure Cognitive Services
description: 개인 정보 보호 및 규정 준수 하도록 고객 데이터를 삭제 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: eb125133138c6de173fdeb90024a9e5d961a929d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895159"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Cognitive Services의 LUIS(Language Understanding)에서 고객 데이터 내보내기 및 삭제

개인 정보 보호 및 규정 준수 하도록 고객 데이터를 삭제 합니다. 

## <a name="summary-of-customer-data-request-features"></a>고객 데이터 요청 기능 요약
LUIS(Language Understanding Intelligent Service)는 서비스 작동을 위해 고객 콘텐츠를 보존하지만, LUIS 사용자가 데이터 보기, 내보내기 및 삭제에 대한 모든 권한을 갖습니다. LUIS 웹을 통해이 작업을 수행할 수 있습니다 [포털](luis-reference-regions.md) 또는 [LUIS 제작 (라고도 하며 프로그래밍 방식으로) Api](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

고객 콘텐츠는 Microsoft 지역별 Azure Storage에 암호화되어 저장되며 다음을 포함합니다.

- 등록 시 수집된 사용자 계정 콘텐츠
- 모델을 빌드하는 데 필요한 학습 데이터
- 사용 되는 사용자 쿼리를 기록 [활성 학습](luis-concept-review-endpoint-utterances.md) 모델을 개선 하기 위해
  - 사용자는 요청에 `&log=false`를 추가하여 쿼리 로깅을 해제할 수 있습니다. 자세한 내용은 [여기](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)를 참조하세요.

## <a name="deleting-customer-data"></a>고객 데이터 삭제
LUIS 사용자 콘텐츠, LUIS 웹 포털 또는 LUIS (프로그래밍 라고도 함)을 작성 하는 Api를 통해 모든 사용자를 삭제 하려면 모든 권한이 있습니다. 다음 표에는 두 작업에 도움이 되는 링크가 나와 있습니다.

| | **사용자 계정** | **애플리케이션** | **예제 Utterance(s)** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **포털** | [링크](luis-concept-data-storage.md#delete-an-account) | [링크](luis-how-to-start-new-app.md#delete-app) | [링크](luis-concept-data-storage.md#utterances-in-an-intent) | [활성 학습 길이 발언](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[기록된 길이 발언](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>고객 데이터 내보내기
LUIS 사용자 포털에서 데이터를 보려는 모든 권한이 있 LUIS 제작 (라고도 하며 프로그래밍 방식으로) Api를 통해 내보낼 수 있어야 합니다. 다음 표에서 데이터 내보내기 LUIS 제작 (라고도 하며 프로그래밍 방식으로) Api 통해 지원 되는 링크를 표시 합니다.

| | **사용자 계정** | **애플리케이션** | **발언** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **API** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>활성 학습의 위치

사용할 수 있도록 [활성 학습](luis-how-to-review-endpoint-utterances.md#enable-active-learning), 게시 된 LUIS 끝점에서 수신 하는 사용자의 기록된 표현, 다음과 같은 Azure 지역에 저장 됩니다.

* [유럽](#europe)
* [오스트레일리아](#australia)
* [미국](#united-states)

활성 학습 데이터 (아래 세부 내용)를 제외 하 고 LUIS 따릅니다 합니다 [지역 서비스에 대 한 데이터 저장소 사례](http://azuredatacentermap.azurewebsites.net/)합니다. 

### <a name="europe"></a>유럽

합니다 [eu.luis.ai](https://eu.luis.ai) Azure의 유럽 지역에서 호스팅되는 포털 및 유럽 (프로그래밍 방식으로 Api 라고도 함)를 작성 합니다. Eu.luis.ai 포털 및 유럽 (프로그래밍 방식으로 Api 라고도 함)를 작성 다음과 같은 Azure 지역에는 끝점의 배포를 지원 합니다.

* 유럽
* 프랑스
* 영국

이러한 Azure 지역에 배포할 때 앱의 최종 사용자의 끝점에서 받은 대상이 눈에 띄도록 활성 학습에 대 한 Azure의 유럽 지역에 저장 됩니다. 활성 학습을 사용 하지 않도록 설정을 참조 하세요 [활성 학습을 사용 하지 않도록 설정](luis-how-to-review-endpoint-utterances.md#disable-active-learning)합니다. 저장된 길이 발언을 관리 하려면 참조 [utterance 삭제](luis-how-to-review-endpoint-utterances.md#delete-utterance)합니다. 

### <a name="australia"></a>오스트레일리아

합니다 [au.luis.ai](https://au.luis.ai) 포털 및 오스트레일리아 제작 (프로그래밍 방식으로 Api 라고도 함) Azure의 오스트레일리아 지역에서 호스팅됩니다. Au.luis.ai 포털 및 오스트레일리아 제작 (프로그래밍 방식으로 Api 라고도 함) 다음과 같은 Azure 지역에는 끝점의 배포를 지원합니다.

* 오스트레일리아

이러한 Azure 지역에 배포할 때 앱의 최종 사용자의 끝점에서 받은 대상이 눈에 띄도록 활성 학습을 위한 Azure 오스트레일리아의 지리적 위치에 저장 됩니다. 활성 학습을 사용 하지 않도록 설정을 참조 하세요 [활성 학습을 사용 하지 않도록 설정](luis-how-to-review-endpoint-utterances.md#disable-active-learning)합니다. 저장된 길이 발언을 관리 하려면 참조 [utterance 삭제](luis-how-to-review-endpoint-utterances.md#delete-utterance)합니다. 

### <a name="united-states"></a>미국

합니다 [luis.ai](https://www.luis.ai) 포털 및 United States 제작 (프로그래밍 방식으로 Api 라고도 함) Azure의 미국 지역에서 호스팅됩니다. Luis.ai 포털 및 United States (프로그래밍 방식으로 Api 라고도 함)를 작성 다음과 같은 Azure 지역에는 끝점의 배포를 지원 합니다.

* Azure 지리 유럽 또는 호주 지역 제작에서 지원 되지 않습니다

이러한 Azure 지역에 배포할 때 앱의 최종 사용자의 끝점에서 받은 대상이 눈에 띄도록 활성 학습에 대 한 Azure의 미국 지리적 위치에 저장 됩니다. 활성 학습을 사용 하지 않도록 설정을 참조 하세요 [활성 학습을 사용 하지 않도록 설정](luis-how-to-review-endpoint-utterances.md#disable-active-learning)합니다. 저장된 길이 발언을 관리 하려면 참조 [utterance 삭제](luis-how-to-review-endpoint-utterances.md#delete-utterance)합니다. 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS 지역 참조](./luis-reference-regions.md)
