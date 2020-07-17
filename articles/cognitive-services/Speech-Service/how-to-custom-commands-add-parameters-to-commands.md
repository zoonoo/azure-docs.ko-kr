---
title: '방법: 사용자 지정 명령에 간단한 명령 추가 응용 프로그램-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령에 매개 변수를 추가 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d2a14a501ebcf0913804ce39019a3fa4018ca141
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362376"
---
# <a name="add-parameters-to-commands"></a>명령에 매개 변수 추가

이 문서에서는 사용자 지정 명령에 매개 변수를 추가 하는 방법에 대해 알아봅니다. 매개 변수는 작업을 완료 하는 명령에 필요한 정보입니다. 복잡 한 시나리오에서 매개 변수를 사용 하 여 사용자 지정 작업을 트리거하는 조건을 정의할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

> [!div class="checklist"]
> * [방법: 간단한 명령을 사용 하 여 응용 프로그램 만들기](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>TurnOn 명령에 대 한 매개 변수 구성

기존 **TurnOn** 명령을 편집 하 여 여러 장치를 켜고 끌 수 있습니다.

1. 이제 명령이 on 및 off 시나리오를 모두 처리 하므로 명령의 이름을 **Turnonoff**로 바꿉니다.
   1. 왼쪽 창에서 **TurnOn** 명령을 선택 하 고 창의 맨 위에 있는 **새로 만들기 명령** 옆에 있는 줄임표 (...) 단추를 선택 합니다.
   
   1. **이름 바꾸기**를 선택 합니다. 이름 **바꾸기 명령** 창에서 **이름** 을 **turnonoff**로 변경 합니다.

1. 다음으로, 사용자가 장치를 켤 지 여부를 나타내는 새 매개 변수를이 명령에 추가 합니다.
   1. 가운데 창의 맨 위에 있는 **추가** 를 선택 합니다. 드롭다운에서 **매개 변수**를 선택 합니다.
   1. 오른쪽 창의 **매개 변수** 섹션에서 **이름** 상자의 값을 **onoff**로 추가 합니다.
   1. **필수**를 선택 합니다. **필수 매개 변수에 대 한 응답 추가** 창에서 **단순 편집기**를 선택 합니다. **첫 번째 변형**에서 다음을 추가 합니다.
        ```
        On or Off?
        ```
   1. **업데이트**를 선택합니다.

       > [!div class="mx-imgBorder"]
       > ![필수 매개 변수 응답 만들기](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. 이제 매개 변수 속성을 구성 합니다. 명령의 모든 구성 속성에 대 한 설명은 [참조](./custom-commands-references.md)로 이동 합니다. 매개 변수의 나머지 속성은 다음과 같이 구성 합니다.
      

       | 구성      | 제안 값     | 설명                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 이름               | `OnOff`           | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                                                           |
       | 전역          | unchecked       | 응용 프로그램의 모든 명령에이 매개 변수의 값이 전역적으로 적용 되는지 여부를 나타내는 확인란입니다.|
       | 필수           | checked         | 명령을 완료 하기 전에이 매개 변수의 값이 필요한 지 여부를 나타내는 확인란 |
       | 필수 매개 변수에 대 한 응답      |간단한 편집기 >`On or Off?`      | 이 매개 변수 값을 알 수 없는 경우 요청 하는 프롬프트입니다. |
       | 유형               | 문자열          | 숫자, 문자열, 날짜/시간 또는 지리와 같은 매개 변수의 형식입니다.   |
       | 구성      | 내부 카탈로그에서 미리 정의 된 입력 값 허용 | 문자열의 경우 가능한 값 집합에 대 한 입력을 제한 합니다. |
       | 미리 정의 된 입력 값     | `on`, `off`           | 가능한 값 및 해당 별칭 집합         |
       
        
   1. 미리 정의 된 입력 값을 추가 하려면 **미리 정의 된 입력 추가** 를 선택 하 고 **새 항목** 창에서 위의 표에 제공 된 대로 **이름을** 입력 합니다. 이 경우 별칭을 사용 하지 않으므로 비워 둘 수 있습니다. 
    > [!div class="mx-imgBorder"]
        > ![매개 변수 만들기](media/custom-commands/create-on-off-parameter.png)
   1. **저장** 을 선택 하 여 매개 변수의 모든 구성을 저장 합니다.
 
 ### <a name="add-subjectdevice-parameter"></a>주제 장치 매개 변수 추가 

   1. 그런 다음 **추가** 를 다시 선택 하 여이 명령을 사용 하 여 제어할 수 있는 장치의 이름을 나타내는 두 번째 매개 변수를 추가 합니다. 다음 구성을 사용 합니다.
   

       | 설정            | 제안 값       |
       | ------------------ | --------------------- |
       | 이름               | `SubjectDevice`         |
       | 전역          | unchecked             |
       | 필수           | checked               |
       | 필수 매개 변수에 대 한 응답     | 간단한 편집기 >`Which device do you want to control?`    | 
       | 유형               | 문자열                |          |
       | 구성      | 내부 카탈로그에서 미리 정의 된 입력 값 허용 | 
       | 미리 정의 된 입력 값 | `tv`, `fan`               |
       | 별칭 ( `tv` )      | `television`, `telly`     |

   1. **저장**을 선택합니다.

### <a name="modify-example-sentences"></a>예제 문장 수정

매개 변수가 있는 명령의 경우 가능한 모든 조합을 포함 하는 예제 문장을 추가 하는 것이 좋습니다. 예:

* 전체 매개 변수 정보-`turn {OnOff} the {SubjectDevice}`
* 부분 매개 변수 정보-`turn it {OnOff}`
* 매개 변수 정보 없음-`turn something`

다른 수준의 정보를 사용 하는 예제 문장을 사용 하면 사용자 지정 명령 응용 프로그램에서 단일 샷 해상도와 일부 정보를 사용 하는 다중 턴 해상도를 모두 확인할 수 있습니다.

이 점을 염두에 두면 아래에서 제안 하는 매개 변수를 사용 하도록 예제 문장을 편집 합니다.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

**저장**을 선택합니다.

> [!TIP]
> 예제 문장 편집기에서 중괄호를 사용 하 여 매개 변수를 참조 합니다. - `turn {OnOff} the {SubjectDevice}`이전에 만든 매개 변수에서 지원 되는 자동 완성 기능에 대해 tab 키를 사용 합니다.

### <a name="modify-completion-rules-to-include-parameters"></a>매개 변수를 포함 하도록 완료 규칙 수정

기존 완료 규칙 **ConfirmationResponse**을 수정 합니다.

1. **조건** 섹션에서 **조건 추가**를 선택 합니다.
1. **새 조건** 창의 **유형** 목록에서 **필수 매개 변수**를 선택 합니다. 아래 확인 목록에서 **Onoff** 및 하위 **장치**를 모두 선택 합니다.
1. **만들기**를 선택합니다.
1. **작업 섹션에서** 작업을 마우스로 가리키고 편집 단추를 선택 하 여 기존 **음성 응답 보내기** 작업을 편집 합니다. 이번에는 새로 만든 **Onoff** 및 하위 **장치** 매개 변수를 사용 합니다.

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. **저장**을 선택합니다.

### <a name="try-it-out"></a>기능 직접 사용해 보기
1. 오른쪽 창 위에 있는 **학습** 아이콘을 선택 합니다.

1. 교육이 완료 되 면 **테스트**를 선택 합니다. **응용 프로그램** 창이 표시 됩니다.
 몇 가지 상호 작용을 시도 합니다.

    - 입력: tv 끄기
    - 출력: 확인, tv 끄기
    - 입력: tv 끄기
    - 출력: 확인, tv 끄기
    - 입력: 해제
    - 출력: 제어할 장치를 선택 하십시오.
    - 입력: tv
    - 출력: 확인, tv 끄기

## <a name="configure-parameters-for-settemperature-command"></a>SetTemperature 명령에 대 한 매개 변수 구성

사용자가 지시 하는 대로 온도를 설정할 수 있도록 **SetTemperature** 명령을 수정 합니다.

다음 구성을 사용 하 여 새 매개 변수 **온도** 를 추가 합니다.

| 구성      | 제안 값     |
| ------------------ | ----------------|
| 이름               | `Temperature`           |
| 필수           | checked         |
| 필수 매개 변수에 대 한 응답      | 간단한 편집기 >`What temperature would you like?`
| 유형               | Number          |


길이 발언 예제를 다음 값으로 편집 합니다.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

다음 구성에 따라 기존 완료 규칙을 편집 합니다.

| 구성      | 제안 값     |
| ------------------ | ----------------|
| 조건         | 필수 매개 변수 > 온도           |
| 작업           | 음성 응답 보내기 >`Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>기능 직접 사용해 보기

몇 가지 상호 작용으로 변경 내용을 **학습** 하 고 **테스트** 합니다.

- 입력: 온도 설정
- 출력: 원하는 온도는 무엇 인가요?
- 입력: 50도
- 출력: 정상, 온도를 50 각도로 설정

## <a name="configure-parameters-to-the-setalarm-command"></a>SetAlarm 명령에 대 한 매개 변수 구성

다음 구성을 사용 하 여 **DateTime** 이라는 매개 변수를 추가 합니다.

   | 설정                           | 제안 값                     | 
   | --------------------------------- | ----------------------------------------|
   | 이름                              | `DateTime`                               |
   | 필수                          | checked                                 |
   | 필수 매개 변수에 대 한 응답   | 간단한 편집기 >`For what time?`            | 
   | 유형                              | DateTime                                |
   | 날짜 기본값                     | 오늘 날짜를 누락 하는 경우            |
   | 시간 기본값                     | 시간이 누락 된 경우 하루 시작 사용     |


> [!NOTE]
> 이 문서에서는 문자열, 숫자 및 날짜/시간 매개 변수 유형을 주로 사용 했습니다. 지원 되는 모든 매개 변수 형식 및 해당 속성 목록은 [참조](./custom-commands-references.md)로 이동 합니다.


길이 발언 예제를 다음 값으로 편집 합니다.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

다음 구성에 따라 기존 완료 규칙을 편집 합니다.

   | 설정    | 제안 값                               |
   | ---------- | ------------------------------------------------------- |
   | 작업    | 음성 응답 보내기-`Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>기능 직접 사용해 보기

변경 내용을 **학습** 하 고 **테스트** 합니다.
- 입력: 내일 정오에 경보 설정
- 출력: 정상, 2020-05-02 12:00:00에 대해 설정 되는 경보
- 입력: 경보 설정
- 출력: 시간
- 입력: 오후 5 시
- 출력: 정상, 2020-05-01 17:00:00에 대해 설정 되는 경보


## <a name="try-out-all-the-commands"></a>모든 명령을 사용해 보세요.

다른 명령과 관련 된 길이 발언를 사용 하 여 세 개의 명령을 모두 테스트 합니다. 다른 명령 간을 전환할 수 있습니다.

- 입력: 경보 설정
- 출력: 시간에 대 한
- 입력: tv 켜기
- 출력: 양호, tv 켜기
- 입력: 경보 설정
- 출력: 시간에 대 한
- 입력: 오후 5 시
- 출력: 정상, 2020-05-01 17:00:00에 대해 설정 되는 경보

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 명령 매개 변수에 구성 추가](./how-to-custom-commands-add-parameter-configuration.md)
