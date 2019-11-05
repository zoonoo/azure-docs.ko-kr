---
title: '방법: 사용자 지정 명령 매개 변수에 유효성 검사 추가 (미리 보기)'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 매개 변수에 유효성 검사를 추가 합니다.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 64e092405686caca7baeaf58f19d577a3f80e169
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500871"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>방법: 사용자 지정 명령 매개 변수에 유효성 검사 추가 (미리 보기)

이 문서에서는 매개 변수에 유효성 검사를 추가 하 고 수정 하 라는 메시지를 표시 하는 방법을 배웁니다.

## <a name="prerequisites"></a>필수 조건

다음 문서의 단계를 완료 해야 합니다.

- [빠른 시작: 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-new.md)
- [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature 명령 만들기

유효성 검사를 보여 주기 위해 사용자가 온도를 설정할 수 있도록 하는 새 명령을 만들어 보겠습니다.

1. [Speech Studio](https://speech.microsoft.com/) 에서 이전에 만든 사용자 지정 명령 응용 프로그램 열기
1. 새 명령 만들기 **SetTemperature**
1. 대상 온도에 대 한 매개 변수 추가
1. 온도 매개 변수에 대 한 유효성 검사 추가
   > [!div class="mx-imgBorder"]
   > 범위 유효성 검사를 추가 ![](media/custom-speech-commands/validations-add-temperature.png)

   | 설정           | 제안 값                                          | 설명                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Name              | 온도                                              | 명령 매개 변수에 대 한 설명이 포함 된 이름입니다.                                                    |
   | 필수          | true                                                     | 명령을 완료 하기 전에이 매개 변수의 값이 필요한 지 여부를 나타내는 확인란 |
   | 응답 템플릿 | "원하는 온도는 무엇 인가요?"                       | 이 매개 변수 값을 알 수 없는 경우 요청 하는 프롬프트입니다.                              |
   | 형식              | Number                                                   | 숫자, 문자열 또는 날짜 시간과 같은 매개 변수의 형식입니다.                                      |
   | 유효성 검사        | Min Value: 60, Max Value: 80                             | 숫자 매개 변수의 경우 매개 변수에 사용할 수 있는 값의 범위입니다.                              |
   | 응답 템플릿 | "죄송 합니다. 60 ~ 80도로만 설정할 수 있습니다."        | 유효성 검사에 실패 하는 경우 업데이트 된 값을 요청 하는 메시지 표시                                       |

1. 몇 가지 샘플 문장 추가

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 결과를 확인 하려면 완료 규칙을 추가 합니다.

   | 설정    | 제안 값                                         | 설명                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | 규칙 이름  | 확인 메시지                                    | 규칙의 용도를 설명 하는 이름입니다.          |
   | 조건 | 필수 매개 변수-온도                        | 규칙을 실행할 수 있는 시기를 결정 하는 조건    |
   | 작업    | SpeechResponse-"확인, {온도}도로 설정" | 규칙 조건이 참인 경우 수행할 동작입니다. |

> [!TIP]
> 이 예제에서는 음성 응답을 사용 하 여 결과를 확인 합니다. 클라이언트 작업을 사용 하 여 명령을 완료 하는 방법에 대 한 예제 [는 방법: 음성 SDK를 사용 하 여 클라이언트에서 명령 수행 (미리 보기)](./how-to-custom-speech-commands-fulfill-sdk.md) 을 참조 하세요.

## <a name="try-it-out"></a>사용해보십시오

테스트 패널을 선택 하 고 몇 가지 상호 작용을 시도 합니다.

- Input: 온도를 72도로 설정 합니다.
- 출력: "확인, 72도로 설정"

- Input: 온도를 45도로 설정 합니다.
- 출력: "죄송 합니다. 60 ~ 80도로만 설정할 수 있습니다."
- Input: 대신 72도로 설정
- 출력: "확인, 72도로 설정"
