---
title: LUIS에서 Cortana 미리 빌드된 앱 사용 | Microsoft Docs
description: LUIS(Language Understanding Intelligent Service)에서 미리 빌드된 응용 프로그램인 Cortana 개인 비서를 사용합니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: c7249cb8d8cff29f419412025c69e3b2b76b49d1
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110660"
---
# <a name="cortana-prebuilt-app"></a>Cortana 미리 빌드된 앱

> [!IMPORTANT]
> Cortana 미리 빌드된 앱 대신 [미리 빌드된 도메인](./luis-how-to-use-prebuilt-domains.md)을 사용하는 것이 좋습니다. 예를 들어 **builtin.intent.calendar.create _calendar_entry** 대신 **Calendar** 미리 빌드된 도메인의 **Calendar.Add**를 사용합니다.
> 미리 빌드된 도메인은 다음과 같은 장점을 제공합니다. 
> * 서로 잘 작동하도록 설계된 미리 작성되고 미리 학습된 의도와 엔터티의 패키지를 제공합니다. 미리 빌드된 도메인을 앱에 직접 통합할 수 있습니다. 예를 들어 피트니스 추적기를 빌드하는 경우 **피트니스** 도메인을 추가하고 체중 및 식사 계획, 남은 시간 또는 거리, 피트니스 활동 메모 저장 등 피트니스 활동을 추적하기 위한 전체 의도 및 엔터티 집합을 사용할 수 있습니다.
> * 미리 빌드된 도메인 의도를 사용자 지정할 수 있습니다. 예를 들어 호텔 검토를 제공하려는 경우 **장소** 도메인에서 **Places.GetReviews** 의도를 학습하고 사용자 지정하여 호텔 검토 요청을 인식할 수 있습니다.
> * 미리 빌드된 도메인을 확장할 수 있습니다. 예를 들어 식당을 검색하는 봇에서 **장소** 미리 빌드된 도메인을 사용하려고 하며 요리 유형을 가져오는 의도가 필요한 경우 **Places.GetCuisine** 의도를 빌드하고 학습할 수 있습니다.

사용자 고유의 응용 프로그램을 빌드할 수 있도록 허용할 뿐 아니라 LUIS는 미리 빌드된 앱인 Microsoft Cortana 개인 비서의 의도 및 엔터티를 제공합니다. 이 공개적으로 사용 가능한 LUIS 앱의 동작은 변경할 수 없습니다. 이 응용 프로그램의 의도 및 엔터티는 편집하거나 다른 LUIS 앱에 통합할 수 없습니다. 클라이언트 응용 프로그램이 이 미리 빌드된 응용 프로그램과 사용자 고유의 LUIS 응용 프로그램 둘 다에 액세스할 수 있게 하려면 클라이언트 응용 프로그램이 LUIS 앱을 둘 다 참조해야 합니다.

미리 빌드된 개인 비서 앱은 영어, 프랑스어, 이탈리아어, 스페인어 및 중국어 문화권(로캘)에서 사용할 수 있습니다.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>Cortana 미리 빌드된 앱에 대한 끝점 가져오기

다음 끝점을 사용하여 Cortana 미리 빌드된 앱에 액세스할 수 있습니다. 

| 언어 | 끝점|
|--------| ------------------|
| 영어| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    중국어| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    프랑스어| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    스페인어| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    이탈리아어| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> 끝점 URL은 [앱 - 개인 비서 응용 프로그램 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) API에서도 사용할 수 있습니다.

## <a name="try-out-the-personal-assistant-app"></a>개인 비서 앱 체험하기
끝점을 호출하려면 끝점 키 인수와 쿼리 문자열을 끝점에 추가할 수 있습니다. 

예를 들어 해석하려는 발언이 “create an appointment for team meeting”인 경우 해당 발언을 끝점 URL에 추가할 수 있습니다. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

URL을 웹 브라우저에 붙여넣고 `{YOUR-SUBSCRIPTION-KEY}` 필드에 끝점 키를 대체할 수 있습니다.

브라우저에서 Cortana 미리 빌드된 앱이 `create an appointment for team meeting` 발언에 대해 `builtin.intent.calendar.create_calendar_entry`를 의도로, `builtin.calendar.title`을 엔터티 형식으로 식별하는 것을 확인할 수 있습니다.

![Cortana 미리 빌드된 앱 사용](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Cortana 미리 빌드된 앱 참조](./luis-reference-cortana-prebuilt.md)

