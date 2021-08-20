---
title: MVAD 입력 매개 변수
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 5b4cea9dea4bb8ee6d1f32ec0e21fd87eb55da95
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215247"
---
### <a name="input-parameters"></a>입력 매개 변수

#### <a name="required-parameters"></a>필수 매개 변수

다음 세 가지 매개 변수는 학습 및 유추 API 요청에 필요합니다.

* `source` - SAS(공유 액세스 서명)가 있는 Azure Blob Storage에 있는 zip 파일에 대한 링크입니다.
* `startTime` - 학습 또는 유추에 사용되는 데이터의 시작 시간입니다. 데이터의 가장 이른 실제 타임스탬프보다 이전인 경우 가장 이른 실제 타임스탬프가 시작점으로 사용됩니다.
* `endTime` - 학습 또는 유추에 사용되는 데이터의 종료 시간이며, `startTime`보다 이후이거나 같아야 합니다. `endTime`이 데이터의 최신 실제 타임스탬프보다 이후인 경우 최신 실제 타임스탬프가 끝점으로 사용됩니다. `endTime`이 `startTime`과 같으면 스트리밍 시나리오에서 자주 사용되는 단일 데이터 요소를 유추하는 것입니다.

#### <a name="optional-parameters-for-training-api"></a>학습 API에 대한 선택적 매개 변수

학습 API에 대한 다른 매개 변수는 선택 사항입니다.

* `slidingWindow` - 변칙을 결정하는 데 사용되는 데이터 요소의 수입니다. 28에서 2,880 사이의 정수입니다. 기본값은 300입니다. `slidingWindow`가 모델 학습에 대해 `k`인 경우 유효한 결과를 얻으려면 유추하는 동안 원본 파일에서 `k`개 이상의 요소에 액세스할 수 있어야 합니다.

    MVAD는 데이터 요소의 세그먼트를 사용하여 다음 데이터 요소가 변칙인지 여부를 결정합니다. 세그먼트의 길이는 `slidingWindow`입니다.
    `slidingWindow` 값을 선택하는 경우 다음 두 가지 사항에 주의하세요.
    1. 데이터의 속성: 주기적인지 여부와 샘플링 속도입니다. 데이터가 주기적인 경우 1~3 주기의 길이를 `slidingWindow`로 설정할 수 있습니다. 데이터가 분 수준 또는 초 수준과 같이 짧은 빈도(작은 세분성)인 경우 `slidingWindow`를 상대적으로 높은 값으로 설정할 수 있습니다.
    1. 학습/유추 시간과 잠재적인 성능 영향 간의 균형입니다. `slidingWindow`가 클수록 학습/유추 시간이 길어질 수 있습니다. `slidingWindow`가 클수록 정확도가 향상된다고 **보장하지는 않습니다**. `slidingWindow`가 작은 경우 모델을 최적의 솔루션으로 수렴하기가 어려울 수 있습니다. 예를 들어 `slidingWindow`에 두 개의 요소만 있는 경우 변칙을 검색하기 어렵습니다.

* `alignMode` - 타임스탬프에서 여러 변수(시계열)를 정렬하는 방법입니다. 이 매개 변수에는 `Inner` 및 `Outer`의 두 가지 옵션이 있으며, 기본값은 `Outer`입니다.

    이 매개 변수는 변수의 타임스탬프 시퀀스 간에 잘못 정렬되는 경우에 매우 중요합니다. 모델에서 추가로 처리하기 전에 변수를 동일한 타임스탬프 시퀀스에 정렬해야 합니다.

    `Inner`는 모델에서 **모든 변수** 의 값이 있는 타임스탬프(즉, 모든 변수의 교집합)에 대해서만 검색 결과를 보고함을 의미합니다. `Outer`는 모델에서 **변수** 의 값이 있는 타임스탬프(즉, 모든 변수의 합집합)에 대한 검색 결과를 보고함을 의미합니다.

    다양한 `alignModel` 값을 설명하는 예는 다음과 같습니다.

    *Variable-1*

    |timestamp | 값|
    ----------| -----|
    |2020-11-01| 1  
    |2020-11-02| 2  
    |2020-11-04| 4  
    |2020-11-05| 5

    *Variable-2*

    timestamp | 값  
    --------- | -
    2020-11-01| 1  
    2020-11-02| 2  
    2020-11-03| 3  
    2020-11-04| 4

    *두 변수의 `Inner` 조인*

    timestamp | Variable-1 | Variable-2
    ----------| - | -
    2020-11-01| 1 | 1
    2020-11-02| 2 | 2
    2020-11-04| 4 | 4

    *두 변수의 `Outer` 조인*

    timestamp | Variable-1 | Variable-2
    --------- | - | -
    2020-11-01| 1 | 1
    2020-11-02| 2 | 2
    2020-11-03| `nan` | 3
    2020-11-04| 4 | 4
    2020-11-05| 5 | `nan`

* `fillNAMethod` - `nan`을 병합된 테이블에 채우는 방법입니다. 병합된 테이블에 누락된 값이 있을 수 있으며, 적절하게 처리해야 합니다. 이를 채우기 위한 몇 가지 메서드를 제공합니다. 옵션은 `Linear`, `Previous`, `Subsequent`, `Zero` 및 `Fixed`이며 기본값은 `Linear`입니다.

    | 옵션     | 메서드                                                                                           |
    | ---------- | -------------------------------------------------------------------------------------------------|
    | `Linear`     | `nan` 값을 선형 보간으로 채웁니다.                                                           |
    | `Previous`   | 마지막 유효한 값을 전파하여 간격을 채웁니다. 예제: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
    | `Subsequent` | 다음 유효한 값을 사용하여 간격을 채웁니다. 예제: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
    | `Zero`       | `nan` 값을 0으로 채웁니다.                                                                           |
    | `Fixed`      | `nan` 값을 `paddingValue`에 제공해야 하는 지정된 유효한 값으로 채웁니다.          |

* `paddingValue` - 패딩 값은 `fillNAMethod` 메서드가 `Fixed`일 때 `nan`을 채우는 데 사용되며, 이 경우 반드시 제공해야 합니다. 다른 경우에는 선택 사항입니다.

* `displayName` - 모델을 식별하는 데 사용되는 선택적 매개 변수입니다. 예를 들어 모델 및 해당 입력 데이터에 대한 매개 변수, 데이터 원본 및 기타 메타 데이터를 표시하는 데 사용할 수 있습니다. 기본값은 빈 문자열입니다.
