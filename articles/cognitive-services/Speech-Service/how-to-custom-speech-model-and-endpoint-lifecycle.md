---
title: Custom Speech-Speech service의 모델 및 끝점 수명 주기
titleSuffix: Azure Cognitive Services
description: Custom Speech는 적응을 위한 기본 모델을 제공 하 고 데이터에서 사용자 지정 모델을 만들 수 있습니다. 이 문서에서는 모델 및 이러한 모델을 사용 하는 끝점에 대 한 타임 라인을 설명 합니다.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555497"
---
# <a name="model-and-endpoint-lifecycle"></a>모델 및 끝점 수명 주기

Custom Speech는 *기본 모델과* *사용자 지정 모델* 을 모두 사용 합니다. 각 언어에는 하나 이상의 기본 모델이 있습니다. 일반적으로 새 음성 모델을 일반 음성 서비스에 릴리스할 때 새 기본 모델로 Custom Speech 서비스에도 가져옵니다. 6 개월 마다 업데이트 됩니다. 이전 모델은 일반적으로 최신 모델의 정확도가 높기 때문에 일반적으로 시간이 지남에 따라 더 유용 하지 않게 됩니다.

반면, 사용자 지정 모델은 특정 고객 시나리오의 데이터를 사용 하 여 선택한 기본 모델을 적용 하 여 생성 됩니다. 사용자의 요구를 충족 하는 경우에는 오랜 시간 동안 특정 사용자 지정 모델을 계속 사용할 수 있습니다. 그러나 최신 기본 모델로 정기적으로 업데이트 하 고 추가 데이터를 사용 하 여 시간에 따라 다시 학습 하는 것이 좋습니다. 

모델 수명 주기와 관련 된 다른 주요 용어는 다음과 같습니다.

* **적응**: 기본 모델을 사용 하 고 텍스트 데이터 및/또는 오디오 데이터를 사용 하 여 도메인/시나리오로 사용자 지정 합니다.
* **디코딩**: 모델을 사용 하 고 음성 인식을 수행 합니다 (오디오를 텍스트로 디코딩).
* **끝점**: 지정 된 사용자 *만* 액세스할 수 있는 기본 모델 또는 사용자 지정 모델의 사용자별 배포입니다.

### <a name="expiration-timeline"></a>만료 타임 라인

새 모델 및 새로운 기능을 사용할 수 있게 되 고 이전에는 더 작은 모델을 사용할 수 없게 되 면 모델 및 끝점 만료에 대해 다음 타임 라인을 참조 하세요.

**기본 모델** 

* 적응: 1 년 동안 사용할 수 있습니다. 모델을 가져온 후에는 1 년 동안 사용자 지정 모델을 만드는 데 사용할 수 있습니다. 1 년이 지난 후 새로운 기본 모델 버전에서 새 사용자 지정 모델을 만들어야 합니다.  
* 디코딩: 가져온 후 2 년 동안 사용할 수 있습니다. 따라서이 모델을 사용 하 여 끝점을 만들고 2 년 동안 일괄 처리 기록을 사용할 수 있습니다. 
* 끝점: 디코딩과 동일한 타임 라인에서 사용할 수 있습니다.

**사용자 지정 모델**

* 디코딩: 모델을 만든 후 2 년 동안 사용할 수 있습니다. 따라서 두 연도 (배치/실시간/테스트)에 대해 사용자 지정 모델을 만든 후 사용할 수 있습니다. 2 년 후에는 기본 모델이 일반적으로 적응에 사용 되지 않기 때문에 *모델을 다시 학습 합니다* .  
* 끝점: 디코딩과 동일한 타임 라인에서 사용할 수 있습니다.

기본 모델이 나 사용자 지정 모델이 만료 되 면 항상 *최신 기본 모델 버전* 으로 대체 됩니다. 따라서 구현은 중단 되지 않지만 사용자 지정 모델이 만료에 도달 하면 *특정 데이터* 에 대해 정확 하지 않을 수 있습니다. Speech Studio의 Custom Speech 영역에 있는 다음 위치에서 모델에 대 한 만료를 확인할 수 있습니다.

* 모델 학습 요약
* 모델 학습 세부 정보
* 배포 요약
* 배포 세부 정보

모델 학습 요약의 예는 다음과 같습니다.

![모델 학습 요약 ](media/custom-speech/custom-speech-model-training-with-expiry.png) 및 모델 학습 세부 정보 페이지에서 다음을 수행 합니다.

![모델 학습 세부 정보](media/custom-speech/custom-speech-model-details-with-expiry.png)

[`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) JSON 응답의 속성 아래에 있는 및 사용자 지정 speech api를 통해 만료 날짜를 확인할 수도 있습니다 `deprecationDates` .

GetModel API 호출의 만료 데이터 예제는 다음과 같습니다. "DEPRECATIONDATES"에는 다음이 표시 됩니다. 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
음성 스튜디오의 배포 섹션에 있는 끝점에서 사용 하는 모델을 변경 하거나 사용자 지정 speech API를 통해 사용자 지정 음성 끝점에서 모델을 업그레이드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [모델 학습 및 배포](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>추가 리소스

* [데이터 준비 및 테스트](./how-to-custom-speech-test-and-train.md)
* [데이터 검사](how-to-custom-speech-inspect-data.md)