---
title: 사용자 지정 명령 미리 보기에서 유효성 검사 추가-음성 서비스
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명령 미리 보기 앱에서 명령 매개 변수에 유효성 검사를 추가 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: nitinme
ms.openlocfilehash: eb011510a9f636aea9910a4be445cd094acf0c21
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509579"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>사용자 지정 명령 미리 보기 응용 프로그램에서 명령 매개 변수에 유효성 검사 추가

이 문서에서는 매개 변수 및 수정 프롬프트에 유효성 검사를 추가 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 문서의 단계를 완료 합니다.

> [!div class="checklist"]
 
> * [빠른 시작: 사용자 지정 명령 미리 보기 앱 만들기](./quickstart-custom-speech-commands-create-new.md)
> * [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 미리 보기 앱 만들기](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature 명령 만들기

유효성 검사를 시연 하려면 사용자가 온도를 설정할 수 있는 새 명령을 만듭니다.

1. [Speech Studio](https://speech.microsoft.com/)에서 사용자가 만든 사용자 지정 명령 미리 보기 앱을 엽니다.
1. 새 **SetTemperature** 명령을 만듭니다.
1. 다음 구성이 포함 된 온도 매개 변수를 추가 합니다.

   | 매개 변수 구성           | 제안 값    |설명                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **이름**              | **기온**                       | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                |
   | **필수**          | 선택                           | 명령을 완료 하기 전에이 매개 변수의 값이 필요한 지 여부를 나타내는 확인란 |
   | **필수 매개 변수에 대 한 응답**     | **간단한 편집기-원하는 온도를 > 하 시겠습니까?**  | 이 매개 변수 값을 알 수 없는 경우 요청 하는 프롬프트입니다. |
   | **형식**              | **Number**                            | 숫자, 문자열, 날짜/시간 또는 지리와 같은 매개 변수의 유형입니다.   |

1. 온도 매개 변수에 대 한 유효성 검사를 추가 합니다.

    1. 온도 매개 변수의 **매개 변수** 구성 페이지에서 **유효성 검사 섹션의 유효성 검사 추가** 를 **선택 합니다.**

    1. **새 유효성 검사** 팝업 창에서 다음과 같이 유효성 검사를 구성 합니다.
  
       | 매개 변수 구성         | 제안 값                                          | 설명                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **최소값**        | **60**               | Number 매개 변수의 경우이 매개 변수에서 가정할 수 있는 최소 값입니다. |
       | **최 댓 값**        | **80**               | Number 매개 변수의 경우이 매개 변수에서 가정할 수 있는 최대 값입니다. |
       | **실패 응답-간단한 편집기**| **첫 번째 변형-60 ~ 80도만 설정할 수 있습니다.**      | 유효성 검사에 실패 하는 경우 새 값을 요청 하는 메시지 표시                                       |

       > [!div class="mx-imgBorder"]
       > ![범위 유효성 검사 추가](media/custom-speech-commands/validations-add-temperature.png)

1. **만들기**를 선택합니다.

1. 몇 가지 예제 문장을 추가 합니다.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 다음 구성이 포함 된 완료 규칙을 추가 합니다. 이 규칙은 결과를 확인 합니다.

   | 설정    | 제안 값                                           |설명                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | 확인 메시지                                      |규칙의 용도를 설명 하는 이름입니다. |
   | **조건** | **필수 매개 변수-온도**                       |규칙을 실행할 수 있는 시기를 결정 하는 조건    |   
   | **actions**    | **음성 응답 보내기-정상, 온도를 {온도}도로 설정** | 규칙 조건이 참인 경우 수행할 동작입니다. |

> [!TIP]
> 이 예제에서는 음성 응답을 사용 하 여 결과를 확인 합니다. 클라이언트 작업을 사용 하 여 명령을 완료 하는 방법에 대 한 예제 [는 방법: 음성 SDK를 사용 하 여 클라이언트에서 명령](./how-to-custom-speech-commands-fulfill-sdk.md)처리를 참조 하세요.

## <a name="try-it-out"></a>기능 직접 사용해 보기

1. **학습**을 선택합니다.

1. 학습을 완료 한 후 **테스트**를 선택 하 고 다음 상호 작용을 시도 합니다.

    - Input: 온도를 72도로 설정 합니다.
    - 출력: 정상, 온도를 72 각도로 설정
    - Input: 온도를 45도로 설정 합니다.
    - 출력: 죄송 합니다. 60 ~ 80도 까지만 설정할 수 있습니다.
    - Input: 대신 72도로 설정
    - 출력: 정상, 온도를 72 각도로 설정

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명령 미리 보기 앱에서 명령에 확인 추가](./how-to-custom-speech-commands-confirmations.md)
