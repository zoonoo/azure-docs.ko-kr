---
title: V3 API 쿼리 문자열 매개 변수
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91309434"
---
V3 API 쿼리 문자열 매개 변수는 다음과 같습니다.

|쿼리 매개 변수|LUIS 포털 이름|유형|버전|기본값|목적|
|--|--|--|--|--|--|
|`log`|로그 저장|boolean|V2 및 V3|false|로그 파일에 쿼리를 저장합니다. 기본값은 false입니다.|
|`query`|-|문자열|V3만|기본값 없음 - GET 요청에 필요|**V2에서** 예측할 발화는 `q` 매개 변수에 있습니다. <br><br>**V3에서** 기능은 `query` 매개 변수로 전달됩니다.|
|`show-all-intents`|모든 의도의 점수 포함|boolean|V3만|false|**prediction.intents** 개체의 해당 점수와 함께 모든 의도를 반환합니다. 의도는 부모 `intents` 개체의 개체로 반환됩니다. 이렇게 하면 `prediction.intents.give` 배열에서 의도를 찾을 필요 없이 프로그래밍 방식으로 액세스할 수 있습니다 V2에서는 배열로 반환되었습니다. |
|`verbose`|추가 엔터티 세부 정보 포함|boolean|V2 및 V3|false|**V2에서는** true로 설정할 경우 예측된 모든 의도가 반환되었습니다. 예측된 모든 의도가 필요한 경우 V3 매개 변수 `show-all-intents`를 사용합니다.<br><br>**V3에서** 이 매개 변수는 엔터티 예측의 엔터티 메타데이터 세부 정보만 제공합니다.  |
|`timezoneOffset`|-|문자열|V2|-|datetimeV2 엔터티에 적용되는 표준 시간대입니다.|
|`datetimeReference`|-|문자열|V3|-|datetimeV2 엔터티에 적용되는 [표준 시간대](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)입니다. V2의 `timezoneOffset`을 대체합니다.|