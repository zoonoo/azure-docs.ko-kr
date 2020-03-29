---
title: 내보내기 & 데이터 삭제 - LUIS
titleSuffix: Azure Cognitive Services
description: 해당 데이터의 보기, 내보내기 및 삭제를 완전히 제어할 수 있습니다. 고객 데이터를 삭제하여 개인 정보 보호 및 규정 준수를 보장합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273354"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Cognitive Services의 LUIS(Language Understanding)에서 고객 데이터 내보내기 및 삭제

고객 데이터를 삭제하여 개인 정보 보호 및 규정 준수를 보장합니다.

## <a name="summary-of-customer-data-request-features"></a>고객 데이터 요청 기능 요약
LUIS(Language Understanding Intelligent Service)는 서비스 작동을 위해 고객 콘텐츠를 보존하지만, LUIS 사용자가 데이터 보기, 내보내기 및 삭제에 대한 모든 권한을 갖습니다. 이 작업은 LUIS 웹 [포털](luis-reference-regions.md) 또는 [LUIS 작성(프로그래밍 방식이라고도 함) API를](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)통해 수행할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

고객 콘텐츠는 Microsoft 지역별 Azure Storage에 암호화되어 저장되며 다음을 포함합니다.

- 등록 시 수집된 사용자 계정 콘텐츠
- 모델을 빌드하는 데 필요한 교육 데이터
- 모델을 개선하는 데 도움이 되는 [활성 학습에](luis-concept-review-endpoint-utterances.md) 사용되는 기록된 사용자 쿼리
  - 사용자는 요청에 `&log=false`를 추가하여 쿼리 로깅을 해제할 수 있습니다. 자세한 내용은 [여기](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)를 참조하세요.

## <a name="deleting-customer-data"></a>고객 데이터 삭제
LUIS 사용자는 LUIS 웹 포털 또는 LUIS 작성(프로그래밍 방식이라고도 함) API를 통해 모든 사용자 콘텐츠를 삭제할 수 있습니다. 다음 표에는 두 작업에 도움이 되는 링크가 나와 있습니다.

| | **사용자 계정** | **애플리케이션** | **발언 예** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **포털** | [링크](luis-concept-data-storage.md#delete-an-account) | [링크](luis-how-to-start-new-app.md#delete-app) | [링크](luis-concept-data-storage.md#utterances-in-an-intent) | [적극적인 학습 발언](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[기록된 발언](luis-concept-data-storage.md#disable-logging-utterances) |
| **Api** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>고객 데이터 내보내기
LUIS 사용자는 포털에서 데이터를 볼 수 있지만 LUIS 작성(프로그래밍 방식이라고도 함) API를 통해 내보내야 합니다. 다음 표에는 LUIS Authoring(프로그래밍 방식이라고도 함) API를 통해 데이터 내보내기를 지원하는 링크가 표시됩니다.

| | **사용자 계정** | **애플리케이션** | **발언(들)** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **Api** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>적극적인 학습의 위치

활성 [학습을](luis-how-to-review-endpoint-utterances.md#enable-active-learning)활성화하기 위해 게시된 LUIS 끝점에서 수신된 사용자의 로깅된 발언은 다음 Azure 지역에 저장됩니다.

* [유럽](#europe)
* [오스트레일리아](#australia)
* [미국](#united-states)

활성 학습 데이터(아래 설명)를 제외하고 LUIS는 [지역 서비스에 대한 데이터 저장소 방법을](https://azuredatacentermap.azurewebsites.net/)따릅니다.

### <a name="europe"></a>유럽

[eu.luis.ai](https://eu.luis.ai) 포털 및 유럽 작성(프로그래밍 API라고도 함)은 Azure의 유럽 지리에서 호스팅됩니다. eu.luis.ai 포털 및 유럽 작성(프로그래밍 API라고도 함)은 다음 Azure 지역에 대한 끝점 배포를 지원합니다.

* 유럽
* 프랑스
* United Kingdom

이러한 Azure 지역에 배포할 때 앱의 최종 사용자로부터 엔드포인트에서 받은 발언은 활성 학습을 위해 Azure의 유럽 지역에 저장됩니다. 활성 학습을 비활성화할 [Disable active learning](luis-how-to-review-endpoint-utterances.md#disable-active-learning)수 있습니다. 저장된 발언을 관리하려면 [발언 삭제를](luis-how-to-review-endpoint-utterances.md#delete-utterance)참조하십시오.

### <a name="australia"></a>오스트레일리아

[au.luis.ai](https://au.luis.ai) 포털 및 오스트레일리아 작성(프로그래밍 API라고도 함)은 Azure의 오스트레일리아 지역에서 호스팅됩니다. au.luis.ai 포털 및 오스트레일리아 작성(프로그래밍 API라고도 함)은 다음 Azure 지역에 대한 끝점 배포를 지원합니다.

* 오스트레일리아

이러한 Azure 지역에 배포할 때 앱의 최종 사용자로부터 엔드포인트에서 받은 발언은 활성 학습을 위해 Azure의 호주 지리에 저장됩니다. 활성 학습을 비활성화할 [Disable active learning](luis-how-to-review-endpoint-utterances.md#disable-active-learning)수 있습니다. 저장된 발언을 관리하려면 [발언 삭제를](luis-how-to-review-endpoint-utterances.md#delete-utterance)참조하십시오.

### <a name="united-states"></a>미국

[luis.ai](https://www.luis.ai) 포털 및 미국 작성(프로그래밍 API라고도 함)은 Azure의 미국 지리에서 호스팅됩니다. luis.ai 포털 및 미국 작성(프로그래밍 방식 API라고도 함)은 다음 Azure 지역에 대한 끝점 배포를 지원합니다.

* 유럽 또는 오스트레일리아 저작 지역에서 지원되지 않는 Azure 지역

이러한 Azure 지역에 배포할 때 앱의 최종 사용자로부터 엔드포인트에서 받은 발언은 활성 학습을 위해 Azure의 미국 지리에 저장됩니다. 활성 학습을 비활성화할 [Disable active learning](luis-how-to-review-endpoint-utterances.md#disable-active-learning)수 있습니다. 저장된 발언을 관리하려면 [발언 삭제를](luis-how-to-review-endpoint-utterances.md#delete-utterance)참조하십시오.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS 지역 참조](./luis-reference-regions.md)
