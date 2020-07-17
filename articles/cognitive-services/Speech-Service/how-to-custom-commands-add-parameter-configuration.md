---
title: '방법: 매개 변수를 외부 엔터티 엔터티로 구성'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 웹 끝점을 통해 노출 되는 카탈로그를 참조 하도록 문자열 매개 변수를 구성 하는 방법을 설명 합니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 5417ff3b53526adbaeba2b9df58694ad495475d3
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308081"
---
# <a name="add-configurations-to-commands-parameters"></a>명령 매개 변수에 구성 추가

이 문서에서는 다음을 포함 하 여 고급 매개 변수 구성에 대해 자세히 알아봅니다.

 - 매개 변수 값이 외부에서 사용자 지정 명령 응용 프로그램에 정의 된 집합에 속할 수 있는 방법
 - 매개 변수 값에 유효성 검사 절을 추가 하는 방법

## <a name="prerequisites"></a>필수 조건

다음 문서의 단계를 완료 해야 합니다.

> [!div class="checklist"]
> * [방법: 간단한 명령을 사용 하 여 응용 프로그램 만들기](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [방법: 명령에 매개 변수 추가](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>매개 변수를 외부 카탈로그 엔터티로 구성

이 섹션에서는 웹 끝점을 통해 호스트 되는 외부 카탈로그를 참조 하도록 문자열 형식 매개 변수를 구성 합니다. 이렇게 하면 사용자 지정 명령 응용 프로그램을 편집 하지 않고도 외부 카탈로그를 독립적으로 업데이트할 수 있습니다. 이 방법은 카탈로그 항목 수가 클 수 있는 경우에 유용 합니다.

**Turnonoff** 명령에서 **gedevice** 매개 변수를 다시 사용 합니다. 이 매개 변수에 대 한 현재 구성에는 **내부 카탈로그의 미리 정의 된 입력이 허용**됩니다. 이는 매개 변수 구성에 정의 된 장치에 대 한 정적 목록을 나타냅니다. 이 콘텐츠를 독립적으로 업데이트할 수 있는 외부 데이터 원본으로 이동 하려고 합니다.

이렇게 하려면 먼저 새 웹 끝점을 추가 합니다. 왼쪽 창에서 **웹 끝점** 섹션으로 이동 하 고 다음 구성을 사용 하 여 새 웹 끝점을 추가 합니다.

| 설정 | 제안 값 |
|----|----|
| Name | `getDevices` |
| URL | `https://aka.ms/speech/cc-sampledevices` |
| 메서드 | GET |


URL에 제안 된 값이 적합 하지 않은 경우 제어 될 수 있는 장치 목록으로 구성 된 json을 반환 하는 간단한 웹 끝점을 구성 하 고 호스트 해야 합니다. 웹 끝점은 다음과 같이 형식이 지정 된 json을 반환 해야 합니다.
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```


그런 다음, 주제 **장치** 매개 변수 설정 페이지로 이동 하 고 속성을 다음과 같이 변경 합니다.

| 설정 | 제안 값 |
| ----| ---- |
| Configuration | 외부 카탈로그의 미리 정의 된 입력 허용 |                               
| 카탈로그 끝점 | getDevices |
| 메서드 | GET |

그런 다음 **저장**을 선택합니다.

> [!IMPORTANT]
> 왼쪽 창의 **웹 끝점** 섹션에 웹 끝점이 설정 되어 있지 않으면 외부 카탈로그의 입력을 허용 하도록 매개 변수를 구성 하는 옵션이 표시 되지 않습니다.

### <a name="try-it-out"></a>기능 직접 사용해 보기

학습 **을 선택 하** 고 학습 완료를 기다립니다. 교육이 완료 되 면 **테스트** 를 선택 하 고 몇 가지 상호 작용을 시도 합니다.

* 입력: 켜기
* 출력: 제어할 장치를 선택 하십시오.
* 입력: 광원
* 출력: 정상, 표시등 켜기

> [!NOTE]
> 이제 웹 끝점에서 호스트 되는 모든 장치를 제어 하는 방법을 확인할 수 있습니다. 새 변경을 테스트 하 고 응용 프로그램을 다시 게시 하기 위해 응용 프로그램을 학습 해야 합니다.

## <a name="add-validation-to-parameters"></a>매개 변수에 유효성 검사 추가

**유효성 검사** 는 매개 변수 값에 대 한 제약 조건을 구성할 수 있도록 하는 특정 매개 변수 형식에 적용할 수 있는 구문이 며, 값이 제약 조건에 포함 되지 않는 경우에는 수정 메시지를 표시 합니다. 유효성 검사 구문을 확장 하는 매개 변수 형식의 전체 목록을 보려면 [참조](./custom-commands-references.md) 로 이동 하세요.

**SetTemperature** 명령을 사용 하 여 유효성 검사를 테스트 합니다. 다음 단계를 사용 하 여 **온도** 매개 변수에 대 한 유효성 검사를 추가 합니다.

1. 왼쪽 창에서 **SetTemperature** 명령을 선택 합니다.
1. 가운데 창에서 **온도** 를 선택 합니다.
1. 오른쪽 창에 있는 **유효성 검사 추가** 를 선택 합니다.
1. **새 유효성 검사** 창에서 다음과 같이 유효성 검사를 구성 하 고 **만들기**를 선택 합니다.


    | 매개 변수 구성 | 제안 값 | Description |
    | ---- | ---- | ---- |
    | 최소값 | `60` | Number 매개 변수의 경우이 매개 변수에서 가정할 수 있는 최소 값입니다. |
    | 최대값 | `80` | Number 매개 변수의 경우이 매개 변수에서 가정할 수 있는 최대 값입니다. |
    | 실패 응답 |  단순 편집기 > 첫 번째 변형 >`Sorry, I can only set temperature between 60 and 80 degrees` | 유효성 검사에 실패 하는 경우 새 값을 요청 하는 메시지 표시 |

    > [!div class="mx-imgBorder"]
    > ![범위 유효성 검사 추가](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>기능 직접 사용해 보기

1. 오른쪽 창 위에 있는 **학습** 아이콘을 선택 합니다.

1. 학습이 완료 되 면 **테스트** 를 선택 하 고 몇 가지 상호 작용을 시도 합니다.

    - Input: 온도를 72도로 설정 합니다.
    - 출력: 정상, 온도를 72 각도로 설정
    - Input: 온도를 45도로 설정 합니다.
    - 출력: 죄송 합니다. 60 ~ 80도 사이의 온도를 설정할 수 있습니다.
    - Input: 대신 72도로 설정
    - 출력: 정상, 온도를 72 각도로 설정

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 상호 작용 규칙 추가](./how-to-custom-commands-add-interaction-rules.md)
