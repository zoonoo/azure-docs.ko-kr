---
title: V3 API 쿼리 문자열 매개 변수
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610791"
---
V3 API 쿼리 문자열 매개 변수는 다음과 같습니다.

|쿼리 매개 변수|LUIS 포털 이름|Type|Version|기본값|목적|
|--|--|--|--|--|--|
|`log`|로그 저장|boolean|V2 & V3|false|로그 파일에 쿼리를 저장 합니다. 기본값은 False입니다.|
|`query`|-|문자열|V3만|기본값 없음-GET 요청에 필요 합니다.|**V2에서**예측할 utterance는 `q` 매개 변수입니다. <br><br>**V3에서**기능은 `query` 매개 변수로 전달 됩니다.|
|`show-all-intents`|모든 의도에 대 한 점수 포함|boolean|V3만|false|**예측과** 개체의 해당 점수를 사용 하 여 모든 의도를 반환 합니다. 의도는 부모 개체에서 개체로 반환 됩니다 `intents` . 이렇게 하면 배열에서 의도를 찾을 필요 없이 프로그래밍 방식으로 액세스할 수 `prediction.intents.give` 있습니다. V 2에서는 배열에이 반환 되었습니다. |
|`verbose`|추가 엔터티 정보 포함|boolean|V2 & V3|false|V 2 **에서**true로 설정 하면 예측 된 모든 것이 반환 됩니다. 모든 예측 의도를 필요로 하는 경우의 V3 매개 변수를 사용 `show-all-intents` 합니다.<br><br>**V3에서**이 매개 변수는 엔터티 예측의 엔터티 메타 데이터 정보만 제공 합니다.  |
|`timezoneOffset`|-|문자열|V2|-|DatetimeV2 엔터티에 적용 되는 표준 시간대입니다.|
|`datetimeReference`|-|문자열|V3|-|DatetimeV2 엔터티에 적용 되는 [표준 시간대](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) 입니다. `timezoneOffset`V2에서 대체 합니다.|