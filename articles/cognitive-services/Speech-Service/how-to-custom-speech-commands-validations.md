---
title: '방법: 사용자 지정 명령 매개 변수에 유효성 검사 추가'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령의 매개 변수에 유효성 검사를 추가 하는 방법을 설명 합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857203"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>방법: 사용자 지정 명령 매개 변수에 유효성 검사 추가 (미리 보기)

이 문서에서는 매개 변수에 유효성 검사를 추가 하 고 수정 메시지를 표시 합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 문서의 단계를 완료 해야 합니다.

> [!div class="checklist"]
> * [빠른 시작: 사용자 지정 명령 만들기](./quickstart-custom-speech-commands-create-new.md)
> * [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature 명령 만들기

유효성 검사를 보여 주기 위해 사용자가 온도를 설정할 수 있도록 하는 새 명령을 만들어 보겠습니다.

1. [Speech Studio](https://speech.microsoft.com/) 에서 이전에 만든 사용자 지정 명령 응용 프로그램 열기
1. 새 명령 만들기`SetTemperature`
1. 대상 온도에 대 한 매개 변수를 추가 합니다.

   | 매개 변수 구성           | 제안 값    |Description                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | 속성              | 온도                       | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                |
   | 필수          | checked                           | 명령을 완료 하기 전에이 매개 변수의 값이 필요한 지 여부를 나타내는 확인란 |
   | 필수 매개 변수에 대 한 응답     | 간단한 편집기-원하는 온도를 > 하 시겠습니까?  | 이 매개 변수 값을 알 수 없는 경우 요청 하는 프롬프트입니다. |
   | 형식              | 숫자                            | 숫자, 문자열, 날짜/시간 또는 지리와 같은 매개 변수의 유형입니다.   |

1. 온도 매개 변수에 대 한 유효성 검사를 추가 합니다.

    - 매개 변수 **구성 페이지의 매개 변수에서** 유효성 `Add a validation` 검사 섹션을 선택 합니다. `Temperature`
    - 다음과 같이 **새 유효성 검사** 팝업의 값을 입력 하 고 **만들기**를 선택 합니다.

  
       | 매개 변수 구성         | 제안 값                                          | 설명                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | 최소값        | 60               | Number 매개 변수의 경우이 매개 변수에서 가정할 수 있는 최소 값입니다. |
       | 최대값        | 80               | Number 매개 변수의 경우이 매개 변수에서 가정할 수 있는 최대 값입니다. |
       | 실패 응답-간단한 편집기| 첫 번째 변형-60 ~ 80도만 설정할 수 있습니다.      | 유효성 검사에 실패 하는 경우 새 값을 요청 하는 메시지 표시                                       |

       > [!div class="mx-imgBorder"]
       > ![범위 유효성 검사 추가](media/custom-speech-commands/validations-add-temperature.png)

1. 몇 가지 예제 문장 추가

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 결과를 확인 하려면 완료 규칙을 추가 합니다.

   | 설정    | 제안 값                                           |Description                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | 속성       | 확인 메시지                                      |규칙의 용도를 설명 하는 이름입니다. |
   | 조건 | 필수 매개 변수-`Temperature`                       |규칙을 실행할 수 있는 시기를 결정 하는 조건    |   
   | 동작    | 음성 응답 보내기-`Ok, setting temperature to {Temperature} degrees` | 규칙 조건이 참인 경우 수행할 동작입니다. |

> [!TIP]
> 이 예제에서는 음성 응답을 사용 하 여 결과를 확인 합니다. 클라이언트 작업을 사용 하 여 명령을 완료 하는 방법에 대 한 예제 [는 방법: 음성 SDK를 사용 하 여 클라이언트에서 명령 처리를](./how-to-custom-speech-commands-fulfill-sdk.md) 참조 하세요.


## <a name="try-it-out"></a>기능 직접 사용해 보기
1. 오른쪽 `Train` 창 위에 있는 아이콘을 선택 합니다.

1. 학습을 완료 하 고 몇 `Test` 가지 상호 작용을 선택 하 고 시도 합니다.

    - Input: 온도를 72도로 설정 합니다.
    - 출력: 정상, 온도를 72 각도로 설정
    - Input: 온도를 45도로 설정 합니다.
    - 출력: 죄송 합니다. 60 ~ 80도 까지만 설정할 수 있습니다.
    - Input: 대신 72도로 설정
    - 출력: 정상, 온도를 72 각도로 설정

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 사용자 지정 명령에 확인 추가 (미리 보기)](./how-to-custom-speech-commands-confirmations.md)
