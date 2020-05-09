---
title: '빠른 시작: 매개 변수 (미리 보기)-음성 서비스를 사용 하 여 사용자 지정 명령 만들기'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램에 매개 변수를 추가 합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853585"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 응용 프로그램 만들기 (미리 보기)

[이전 문서](./quickstart-custom-speech-commands-create-new.md)에서는 매개 변수 없이 간단한 사용자 지정 명령 응용 프로그램을 만들었습니다.

이 문서에서는 여러 장치를 켜고 끄는 작업을 처리할 수 있도록이 응용 프로그램을 확장 하 여 매개 변수를 사용 합니다.

## <a name="create-parameters"></a>Create Parameters

1. [이전에 만든](./quickstart-custom-speech-commands-create-new.md) 프로젝트를 엽니다.
1. 기존 명령을 편집 하 여 여러 장치를 켜고 끌 수 있습니다.
1. 이제 명령이 on 및 off를 처리 하므로 명령의 이름을로 `TurnOnOff`바꿉니다.
   - 왼쪽 창에서 `TurnOn` 명령을 선택 하 고 창 위쪽의 옆 `...` `+ New command` 에 있는 아이콘을 클릭 합니다.
   
   - 아이콘 `Rename` 을 선택 합니다. 이름 **바꾸기 명령** 팝업에서 **이름** 을로 `TurOnOff`변경 합니다. 다음으로 **저장**을 선택합니다.

1. 다음으로, 사용자가 장치를 켤 지 여부를 나타내는 새 매개 변수를 만듭니다.
   - 가운데 `+ Add` 창의 맨 위에 있는 아이콘을 선택 합니다. 드롭다운에서 **매개 변수**를 선택 합니다.
   - 가장 오른쪽 창에서 **매개 변수** 구성 섹션을 볼 수 있습니다.
   - **이름**에 값을 추가 합니다.
   - **필수** 확인란을 선택 합니다. **필수 매개 변수에 대 한 응답 추가** 창에서 **단순 편집기** 를 선택 하 고 **첫 번째 변형**에를 추가 합니다.
        ```
        On or Off?
        ```
   - **업데이트**를 선택합니다.

       > [!div class="mx-imgBorder"]
       > ![필수 매개 변수 응답 만들기](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - 그런 다음 매개 변수의 나머지 속성을 다음과 같이 구성 하 고 구성 모든 구성을 매개 `Save` 변수에 저장 하도록 선택 합니다.
       

       | Configuration      | 제안 값     | Description                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 속성               | 마이크           | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                                                           |
       | 전역          | unchecked       | 응용 프로그램의 모든 명령에이 매개 변수의 값이 전역적으로 적용 되는지 여부를 나타내는 확인란입니다.|
       | 필수           | checked         | 명령을 완료 하기 전에이 매개 변수의 값이 필요한 지 여부를 나타내는 확인란 |
       | 필수 매개 변수에 대 한 응답      |단순 편집기-> 설정 또는 해제      | 이 매개 변수 값을 알 수 없는 경우 요청 하는 프롬프트입니다. |
       | 형식               | String          | 숫자, 문자열, 날짜/시간 또는 지리와 같은 매개 변수의 형식입니다.   |
       | Configuration      | 내부 카탈로그에서 미리 정의 된 입력 값 허용 | 문자열의 경우 가능한 값 집합에 대 한 입력을 제한 합니다. |
       | 미리 정의 된 입력 값     | on, off             | 가능한 값 및 해당 별칭 집합         |
       
        > [!div class="mx-imgBorder"]
        > ![매개 변수 만들기](media/custom-speech-commands/create-on-off-parameter.png)

   - 다음으로 `+ Add` 아이콘을 다시 선택 하 여 다음 구성의 장치 이름을 나타내는 두 번째 매개 변수를 추가 합니다.
   

       | 설정            | 제안 값       | Description                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | 속성               | 주제 장치         | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                                                     |
       | 전역          | unchecked             | 응용 프로그램의 모든 명령에이 매개 변수의 값이 전역적으로 적용 되는지 여부를 나타내는 확인란입니다. |
       | 필수           | checked               | 명령을 완료 하기 전에이 매개 변수의 값이 필요한 지 여부를 나타내는 확인란          |
       | 간단한 편집기      | 어떤 장치 입니까?    | 이 매개 변수 값을 알 수 없는 경우 요청 하는 프롬프트입니다.                                       |
       | 형식               | String                | 숫자, 문자열, 날짜/시간 또는 지리와 같은 매개 변수의 형식입니다.                                                |
       | Configuration      | 내부 카탈로그에서 미리 정의 된 입력 값 허용 | 문자열의 경우 문자열 목록에서 가능한 값 집합에 대 한 입력을 제한 합니다.       |
       | 미리 정의 된 입력 값 | tv, 팬               | 가능한 값 및 해당 별칭 집합                               |
       | 별칭 (tv)      | 텔레비전, 고 대     | 미리 정의 된 입력 값의 가능한 각 값에 대 한 선택적 별칭                                 |

## <a name="add-example-sentences"></a>예제 문장 추가

매개 변수가 있는 명령을 사용 하 여 가능한 모든 조합을 포함 하는 예제 문장을 추가 하는 것이 좋습니다. 예를 들어:

1. 전체 매개 변수 정보-`turn {OnOff} the {SubjectDevice}`
1. 부분 매개 변수 정보-`turn it {OnOff}`
1. 매개 변수 정보 없음-`turn something`

다른 수준의 정보를 사용 하는 예제 문장을 사용 하면 사용자 지정 명령 응용 프로그램에서 단일 샷 해상도와 일부 정보를 사용 하는 다중 턴 해상도를 모두 확인할 수 있습니다.

이 점을 염두에 두면 아래에서 제안 하는 매개 변수를 사용 하도록 예제 문장을 편집 합니다.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> 예제 문장 편집기에서 중괄호를 사용 하 여 매개 변수를 참조 합니다. - `turn {OnOff} the {SubjectDevice}`이전에 만든 매개 변수에서 지원 되는 자동 완성 기능에 대해 tab 키를 사용 합니다.

## <a name="add-parameters-to-completion-rules"></a>완료 규칙에 매개 변수 추가

[이전 빠른](./quickstart-custom-speech-commands-create-new.md)시작에서 만든 완료 규칙을 수정 합니다.

1. **조건** 섹션에서 **+ 조건 추가** 를 선택 하 여 새 조건을 추가 합니다.
1. 새 팝업 **새 조건**에서 `Required parameters` **유형** 드롭다운에서를 선택 합니다. 아래 검사 목록에서 및 `OnOff` `SubjectDevice`를 모두 확인 합니다.
1. **만들기**를 클릭 합니다.
1. **작업 섹션에서** 작업을 마우스로 가리키고 편집 아이콘을 클릭 하 여 기존 음성 응답 보내기 작업을 편집 합니다. 이번에는 새로 만든 `OnOff` 및 `SubjectDevice` 매개 변수를 사용 하겠습니다.

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>기능 직접 사용해 보기
1. 오른쪽 `Train` 창 위에 있는 아이콘을 선택 합니다.

1. 학습을 완료 한 후를 `Test`선택 합니다.
    - 새 **테스트 응용 프로그램** 창이 표시 됩니다.
    - 몇 가지 상호 작용을 시도 합니다.

        - 입력: tv 끄기
        - 출력: 확인, tv 끄기        
        - 입력: tv 끄기
        - 출력: 확인, tv 끄기
        - 입력: 해제
        - 출력: 어떤 장치 입니까?
        - 입력: tv
        - 출력: 확인, tv 끄기

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: 사용자 지정 음성 (미리 보기)으로 사용자 지정 명령 사용](./quickstart-custom-speech-commands-select-custom-voice.md)
