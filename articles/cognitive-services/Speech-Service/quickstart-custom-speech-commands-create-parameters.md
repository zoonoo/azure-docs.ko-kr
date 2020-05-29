---
title: '빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 미리 보기 앱 만들기-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램에 매개 변수를 추가 하 여 여러 장치를 켜고 끌 수 있게 합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 5461ef9680ab89c8cc9cc2e1166366abb04a6eab
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142285"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 미리 보기 응용 프로그램 만들기

[이전 문서](./quickstart-custom-speech-commands-create-new.md)에서는 매개 변수 없이 간단한 사용자 지정 명령 응용 프로그램을 만들었습니다.

이 문서에서는 여러 장치를 켜고 끌 수 있도록 매개 변수를 사용 하 여 해당 응용 프로그램을 확장 합니다.

## <a name="create-parameters"></a>매개 변수 만들기

1. [이전 문서](./quickstart-custom-speech-commands-create-new.md)에서 만든 프로젝트를 엽니다.

   여러 장치를 설정 및 해제 하는 데 사용할 수 있도록 기존 명령을 편집 합니다.
1. 이제 명령이 on 및 off를 모두 처리 하기 때문에이 파일의 이름을 **Turnonoff**로 바꿉니다.
   1. 왼쪽 창에서 **TurnOn** 명령을 선택 하 고 창의 맨 위에 있는 **새로 만들기 명령** 옆에 있는 줄임표 (**...**) 단추를 선택 합니다.
   
   1. **이름 바꾸기**를 선택 합니다. 이름 **바꾸기 명령** 창에서 **이름** 을 원래 대로 변경 **합니다.** **저장**을 선택합니다.

1. 사용자가 장치를 켤 지 여부를 나타내는 매개 변수를 만듭니다.
   1. 가운데 창의 위쪽에서 **추가** 를 선택 합니다. 드롭다운 목록에서 **매개 변수**를 선택 합니다.
   1. 오른쪽 창의 **매개 변수** 섹션에서 **이름** 상자에 값을 추가 합니다.
   1. **필수**를 선택 합니다. **필수 매개 변수에 대 한 응답 추가** 창에서 **단순 편집기**를 선택 합니다. **첫 번째 변형** 상자에 다음 텍스트를 입력 합니다.
        ```
        On or Off?
        ```
   1. **업데이트**를 선택합니다.

       > [!div class="mx-imgBorder"]
       > ![필수 매개 변수 응답 만들기](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. 매개 변수의 나머지 속성은 다음과 같이 구성 합니다.
       

    | Configuration      | 제안 값     | 설명                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **이름**               | **마이크**           | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                                                  |
    | **전역**          | 선택 취소       | 매개 변수의 값이 응용 프로그램의 모든 명령에 전역적으로 적용 되는지 여부를 나타내는 확인란입니다.|
    | **필수**           | 선택함         | 매개 변수의 값이 필수 인지 여부를 나타내는 확인란입니다.  |
    | **필수 매개 변수에 대 한 응답**      |**단순 편집기-> 설정 또는 해제**      | 매개 변수를 알 수 없는 경우 매개 변수 값을 요청 하는 메시지입니다. |
    | **형식**               | **String**          | 매개 변수의 유형입니다. 예를 들어 숫자, 문자열, 날짜 시간, 지리입니다.   |
    | **구성**      | **내부 카탈로그에서 미리 정의 된 입력 값 허용** | 문자열의 경우이 설정은 입력을 가능한 값 집합으로 제한 합니다. |
    | **미리 정의 된 입력 값**     | **켜기**, **끄기**             | 사용할 수 있는 값 및 해당 별칭의 집합입니다.         |
       


    > [!div class="mx-imgBorder"]
    > ![매개 변수 만들기](media/custom-speech-commands/create-on-off-parameter.png)

1. **저장**을 선택하여 설정을 저장합니다.

 1. **추가** 를 다시 선택 하 여 두 번째 매개 변수를 추가 합니다. 이 매개 변수는 장치의 이름을 나타냅니다. 다음 설정을 사용합니다.
   

       | 설정            | 제안 값       | 설명                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **이름**               | **주제 장치**         | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                                                     |
       | **전역**          | 선택 취소             | 매개 변수의 값이 응용 프로그램의 모든 명령에 전역적으로 적용 되는지 여부를 나타내는 확인란입니다. |
       | **필수**           | 선택함               | 매개 변수의 값이 필수 인지 여부를 나타내는 확인란입니다.          |
       | **간단한 편집기**      | **어떤 장치 입니까?**    | 매개 변수를 알 수 없는 경우 매개 변수 값을 요청 하는 메시지입니다.                                       |
       | **형식**               | **String**                | 매개 변수의 유형입니다. 예를 들어 숫자, 문자열, 날짜 시간, 지리입니다.                                                |
       | **구성**      | **내부 카탈로그에서 미리 정의 된 입력 값 허용** | 문자열의 경우이 설정은 입력을 가능한 값 집합으로 제한 합니다.       |
       | **미리 정의 된 입력 값** | **tv**, **팬**               | 사용할 수 있는 값 및 해당 별칭의 집합입니다.                               |
       | **별칭** (tv)      | **텔레비전**, **고** 대     | 미리 정의 된 각 입력 값에 대 한 선택적 별칭입니다.                                 |

## <a name="add-example-sentences"></a>예제 문장 추가

매개 변수가 있는 명령의 경우 가능한 모든 조합을 포함 하는 예제 문장을 추가 하는 것이 좋습니다. 예를 들어:

- 전체 매개 변수 정보:`turn {OnOff} the {SubjectDevice}`
- 부분 매개 변수 정보:`turn it {OnOff}`
- 매개 변수 정보 없음:`turn something`

정보 양이 다른 예제 문장을 사용 하면 사용자 지정 명령 응용 프로그램에서 단일 샷 해상도와 부분 정보를 포함 하는 다중 턴 해상도를 모두 확인할 수 있습니다.

이 점을 염두에 두면 여기에서 제안 하는 매개 변수를 사용 하도록 예제 문장을 편집 합니다.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> 예제 문장 편집기에서 중괄호를 사용 하 여 매개 변수를 참조 `turn {OnOff} the {SubjectDevice}` 합니다.
>
> 이전에 만든 매개 변수에 의해 정의 된 자동 완성에 대해 tab 키를 사용 합니다.

## <a name="add-parameters-to-completion-rules"></a>완료 규칙에 매개 변수 추가

[이전 빠른](./quickstart-custom-speech-commands-create-new.md)시작에서 만든 완료 규칙을 수정 합니다.

1. **조건** 섹션에서 **조건 추가**를 선택 합니다.
1. **새 조건** 창의 **유형** 목록에서 **필수 매개 변수**를 선택 합니다. 목록에서 **Onoff** 및- **device 장치**를 모두 선택 합니다.
1. **만들기**를 선택합니다.
1. **작업 섹션에서** 작업을 마우스로 가리키고 편집 단추를 선택 하 여 기존 **음성 응답 보내기** 작업을 편집 합니다. 이번에는 새 `OnOff` 및 매개 변수를 사용 합니다 `SubjectDevice` .

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>기능 직접 사용해 보기
1. 오른쪽 창 맨 위에서 **학습** 을 선택 합니다.

1. 교육이 완료 되 면 **테스트**를 선택 합니다.
    
    **응용 프로그램** 창이 표시 됩니다.

1. 몇 가지 상호 작용을 시도 합니다.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: 사용자 지정 음성 (미리 보기)으로 사용자 지정 명령 사용](./quickstart-custom-speech-commands-select-custom-voice.md)
