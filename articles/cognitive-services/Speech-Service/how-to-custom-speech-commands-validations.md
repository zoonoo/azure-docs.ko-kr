---
title: '방법: 사용자 지정 명령 매개 변수에 유효성 검사를 추가(미리 보기)'
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
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156457"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>방법: 사용자 지정 명령 매개 변수에 유효성 검사를 추가(미리 보기)

이 문서에서는 매개 변수에 유효성 검사를 추가하고 수정하라는 메시지를 표시하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 문서의 단계를 완료해야 합니다.

- [빠른 시작: 사용자 지정 명령 만들기(미리 보기)](./quickstart-custom-speech-commands-create-new.md)
- [빠른 시작: 매개 변수를 사용하여 사용자 지정 명령 만들기(미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature 명령 만들기

유효성 검사를 보여 주기 위해 사용자가 온도를 설정할 수 있도록 하는 새 명령을 만들어 보겠습니다.

1. [음성 스튜디오에서](https://speech.microsoft.com/) 이전에 만든 사용자 지정 명령 응용 프로그램을 엽니다.
1. 새 명령 **설정 만들기온도**
1. 대상 온도에 대한 매개변수 추가
1. 온도 매개변수에 대한 유효성 검사 추가
   > [!div class="mx-imgBorder"]
   > ![범위 유효성 검사 추가](media/custom-speech-commands/validations-add-temperature.png)

   | 설정           | 제안 값                                          | 설명                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | 이름              | 온도                                              | 명령 매개 변수에 대한 설명 이름                                                    |
   | 필수          | true                                                     | 명령을 완료하기 전에 이 매개 변수의 값이 필요한지 여부를 나타내는 확인란 |
   | 응답 템플릿 | "- 어떤 온도를 원하십니까?"                     | 알 수 없는 경우 이 매개 변수의 값을 묻는 프롬프트                              |
   | Type              | Number                                                   | 숫자, 문자열 또는 날짜 시간과 같은 매개 변수 유형                                      |
   | 유효성 검사        | 최소 값: 60, 최대 값: 80                             | Number 매개 변수의 경우 매개 변수에 대해 허용된 값 범위                             |
   | 응답 템플릿 | "- 죄송합니다, 나는 단지 60 ~ 80도 사이에 설정할 수 있습니다"      | 유효성 검사가 실패할 경우 업데이트된 값을 묻는 프롬프트                                       |

1. 일부 샘플 문장 추가

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 완료 규칙을 추가하여 결과 확인

   | 설정    | 제안 값                                           | 설명                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | 규칙 이름  | 확인 메시지                                      | 규칙의 목적을 설명하는 이름          |
   | 조건 | 필수 매개변수 - 온도                          | 규칙을 실행할 수 있는 시기를 결정하는 조건    |
   | 동작    | SpeechResponse - "- 좋아, {온도} 각도로 설정" | 규칙 조건이 true일 때 취할 작업 |

> [!TIP]
> 이 예제에서는 음성 응답을 사용하여 결과를 확인합니다. 클라이언트 작업으로 명령을 완료하는 예제는 [음성 SDK(미리 보기)를 사용하여 클라이언트에서 명령을 이행하는 방법(예:](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>체험

테스트 패널을 선택하고 몇 가지 상호 작용을 시도합니다.

- 입력: 온도를 72도로 설정
- 출력 : "좋아, 72도로 설정"

- 입력: 온도를 45도로 설정
- 출력 : "죄송합니다, 나는 단지 60 ~ 80도 사이에 설정할 수 있습니다"
- 입력 : 대신 72도 확인
- 출력 : "좋아, 72도로 설정"

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 사용자 지정 명령에 확인 추가(미리 보기)](./how-to-custom-speech-commands-confirmations.md)
