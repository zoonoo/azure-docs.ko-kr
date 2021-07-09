---
title: 데이터 내보내기 및 삭제 - LUIS
titleSuffix: Azure Cognitive Services
description: 데이터 보기, 내보내기 및 삭제 작업을 완전히 제어할 수 있습니다. 개인 정보 보호 및 규정 준수를 위해 고객 데이터를 삭제합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/08/2020
ms.openlocfilehash: a7a7bbb8b911f86b81cdeafe3fe57e1fdf820681
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228285"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Cognitive Services의 LUIS(Language Understanding)에서 고객 데이터 내보내기 및 삭제

개인 정보 보호 및 규정 준수를 위해 고객 데이터를 삭제합니다.

## <a name="summary-of-customer-data-request-features"></a>고객 데이터 요청 기능 요약
LUIS(Language Understanding Intelligent Service)는 서비스 작동을 위해 고객 콘텐츠를 보존하지만, LUIS 사용자가 데이터 보기, 내보내기 및 삭제에 대한 모든 권한을 갖습니다. 이 작업은 LUIS 웹 [포털](luis-reference-regions.md) 또는 [LUIS 작성(프로그래매틱이라고도 함) API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)를 통해 수행할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

고객 콘텐츠는 Microsoft 지역별 Azure Storage에 암호화되어 저장되며 다음을 포함합니다.

- 등록 시 수집된 사용자 계정 콘텐츠
- 모델을 빌드하는 데 필요한 학습 데이터
- 모델을 개선하기 위해 [활성 학습](luis-concept-review-endpoint-utterances.md)에서 사용되어 기록된 사용자 쿼리
  - 사용자는 요청에 `&log=false`를 추가하여 쿼리 로깅을 해제할 수 있습니다. 자세한 내용은 [여기](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)를 참조하세요.

## <a name="deleting-customer-data"></a>고객 데이터 삭제
LUIS 사용자는 LUIS 웹 포털 또는 LUIS 작성(프로그래매틱이라고도 함) API를 통해 모든 사용자 콘텐츠를 삭제할 모든 권한이 있습니다. 다음 표에는 두 작업에 도움이 되는 링크가 나와 있습니다.

| | **사용자 계정** | **애플리케이션** | **예제 발화** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **포털** | [링크](luis-concept-data-storage.md#delete-an-account) | [링크](luis-how-to-start-new-app.md#delete-app) | [링크](luis-concept-data-storage.md#utterances-in-an-intent) | [활성 학습 발화](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[기록된 발화](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>고객 데이터 내보내기
LUIS 사용자는 포털에서 데이터를 보기 위한 모든 권한이 있지만 LUIS 작성(프로그래매틱이라고도 함) API를 통해 내보내야 합니다. 다음 표에는 LUIS 작성(프로그래매틱이라고도 함) API를 통한 데이터 내보내기에 도움이 되는 링크가 나와 있습니다.

| | **사용자 계정** | **애플리케이션** | **발언** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **API** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>활성 학습 위치

[활성 학습](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning)을 사용하도록 설정하기 위해 게시된 LUIS 엔드포인트에서 받은 기록된 사용자 발화는 다음 Azure 지역에 저장됩니다.

* [유럽](#europe)
* [오스트레일리아](#australia)
* [미국](#united-states)

활성 학습 데이터(아래에 자세히 설명)를 제외하고 LUIS는 [지역 서비스에 대한 데이터 스토리지 사례](https://azuredatacentermap.azurewebsites.net/)를 따릅니다.

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>유럽

유럽 작성(프로그래매틱 API라고도 함) 리소스는 Azure의 유럽 지역에서 호스트되며 다음 Azure 지역에 대한 엔드포인트 배포를 지원합니다.

* 유럽
* 프랑스
* 영국

이러한 Azure 지역에 배포하는 경우 앱 최종 사용자의 엔드포인트에서 받은 발화는 활성 학습을 위해 Azure의 유럽 지역에 저장됩니다.

### <a name="australia"></a>오스트레일리아

오스트레일리아 작성(프로그래매틱 API라고도 함) 리소스는 Azure의 오스트레일리아 지역에서 호스트되며 다음 Azure 지역에 대한 엔드포인트 배포를 지원합니다.

* 오스트레일리아

이러한 Azure 지역에 배포하는 경우 앱 최종 사용자의 엔드포인트에서 받은 발화는 활성 학습을 위해 Azure의 오스트레일리아 지역에 저장됩니다.

### <a name="united-states"></a>미국

미국 작성(프로그래매틱 API라고도 함) 리소스는 Azure의 미국 지역에서 호스트되며 다음 Azure 지역에 대한 엔드포인트 배포를 지원합니다.

* 유럽 또는 오스트레일리아 작성 지역에서 지원하지 않는 Azure 지역

이러한 Azure 지역에 배포하는 경우 앱 최종 사용자의 엔드포인트에서 받은 발화는 활성 학습을 위해 Azure의 미국 지역에 저장됩니다.

### <a name="switzerland-north"></a>스위스 북부

스위스 북부 작성(프로그래매틱 API라고도 함) 리소스는 Azure의 스위스 지역에서 호스트되며 다음 Azure 지역에 대한 엔드포인트 배포를 지원합니다.

* 스위스 

이러한 Azure 지역에 배포하는 경우 앱 최종 사용자의 엔드포인트에서 받은 발화는 활성 학습을 위해 Azure의 스위스 지역에 저장됩니다.

## <a name="disable-active-learning"></a>활성 학습 사용 안 함

활성 학습을 사용하지 않도록 설정하려면 [활성 학습 사용 안 함](luis-how-to-review-endpoint-utterances.md#disable-active-learning)을 참조하세요. 저장된 발화를 관리하려면 [발화 삭제](luis-how-to-review-endpoint-utterances.md#delete-utterance)를 참조하세요.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS 지역 참조](./luis-reference-regions.md)
