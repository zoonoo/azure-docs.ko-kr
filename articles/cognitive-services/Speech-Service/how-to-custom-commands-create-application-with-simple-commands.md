---
title: '방법: 간단한 명령을 사용 하 여 응용 프로그램 만들기-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 간단한 명령을 사용 하 여 호스트 된 사용자 지정 명령 응용 프로그램을 만들고 테스트 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 6552d13ab68d8028cd95b18f2d0895d53bba462c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090963"
---
# <a name="create-application-with-simple-commands"></a>간단한 명령으로 애플리케이션 만들기

이 문서에서는 다음 방법을 설명합니다.
 - 빈 응용 프로그램 만들기
 - LUIS 리소스 업데이트
 - 사용자 지정 명령 응용 프로그램에 몇 가지 기본 명령 추가

## <a name="create-empty-application"></a>빈 응용 프로그램 만들기
빈 사용자 지정 명령 응용 프로그램을 만듭니다. 자세한 내용은 [빠른](quickstart-custom-commands-application.md)시작을 참조 하세요. 이번에는 프로젝트를 가져오는 대신 빈 프로젝트를 만듭니다.

1. **이름** 상자에 프로젝트 이름 `Smart-Room-Lite` (또는 원하는 다른 항목)을 입력 합니다.
1. **언어** 목록에서 **영어 (미국)** 를 선택 합니다.
1. 선택한 LUIS 리소스를 선택 하거나 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![프로젝트 만들기](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>LUIS 리소스 업데이트 (옵션)

**새 프로젝트** 창에서 선택한 제작 리소스를 업데이트 하 고 예측 리소스를 설정할 수 있습니다. 예측 리소스는 사용자 지정 명령 응용 프로그램이 게시 될 때 인식에 사용 됩니다. 개발 및 테스트 단계 중에는 예측 리소스가 필요 하지 않습니다.

## <a name="add-turnon-command"></a>TurnOn 명령 추가

방금 만든 빈 **스마트 공간-Lite** 사용자 지정 명령 응용 프로그램에서 utterance를 처리 하 고 메시지에 응답 하는 간단한 명령을 추가 합니다 `turn on the tv` `Ok, turning the tv on` .

1. 왼쪽 창의 맨 위에 있는 **새 명령** 을 선택 하 여 새 명령을 만듭니다. **새 명령** 창이 열립니다.
1. **이름** 필드의 값을 **TurnOn**로 지정 합니다.
1. **만들기**를 선택합니다.

가운데 창에는 명령의 다른 속성이 나열 됩니다. 명령의 다음 속성을 구성 합니다. 명령의 모든 구성 속성에 대 한 설명은 [참조](./custom-commands-references.md)로 이동 합니다.

| 구성            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **예제 문장** | 예 길이 발언 사용자가이 명령을 트리거할 수 있습니다.                                                                 |
| **매개 변수**       | 명령을 완료 하는 데 필요한 정보                                                                                |
| **완료 규칙** | 명령을 수행 하기 위해 수행할 작업입니다. 예를 들어 사용자에 게 응답 하거나 다른 웹 서비스와 통신 합니다. |
| **상호 작용 규칙**   | 보다 구체적인 또는 복잡 한 상황을 처리 하는 추가 규칙                                                              |


> [!div class="mx-imgBorder"]
> ![명령 만들기](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>예제 문장 추가

**예제 문장** 섹션을 시작 하 고 사용자가 말할 수 있는 작업의 예를 제공 하겠습니다.

1. 가운데 창에서 **예제 문장** 섹션을 선택 합니다.
1. 가장 오른쪽 창에서 예제를 추가 합니다.

    ```
    turn on the tv
    ```

1.  창 위쪽에서 **저장** 을 선택 합니다.

지금은 매개 변수가 없으므로 **완료 규칙** 섹션으로 이동할 수 있습니다.

### <a name="add-a-completion-rule"></a>완료 규칙 추가

다음에는 명령에 완료 규칙이 있어야 합니다. 이 규칙은 사용자에 게 처리 작업을 수행 하 고 있음을 알려 줍니다. 규칙 및 완료 규칙에 대 한 자세한 내용을 보려면 [참조](./custom-commands-references.md)로 이동 하세요.

1. 기본 완료 규칙 **완료** 를 선택 하 고 다음과 같이 편집 합니다.

    
    | 설정    | 제안 값                          | 설명                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **이름**       | ConfirmationResponse                  | 규칙의 용도를 설명 하는 이름입니다.          |
    | **조건** | None                                     | 규칙을 실행할 수 있는 시기를 결정 하는 조건    |
    | **actions**    | 첫 번째 변형 > 간단한 편집기 > 음성 응답 보내기 >`Ok, turning the tv on` | 규칙 조건이 참인 경우 수행할 동작입니다. |
    



   > [!div class="mx-imgBorder"]
   > ![음성 응답 만들기](media/custom-commands/create-speech-response-action.png)

1. **저장** 을 선택 하 여 작업을 저장 합니다.
1. **완료 규칙** 섹션으로 돌아가서 **저장** 을 선택 하 여 모든 변경 내용을 저장 합니다. 

    > [!NOTE]
    > 명령과 함께 제공 되는 기본 완료 규칙을 사용할 필요는 없습니다. 필요한 경우 기존 기본 완료 규칙을 삭제 하 고 사용자 고유의 규칙을 추가할 수 있습니다.

### <a name="try-it-out"></a>사용해 보기

테스트 채팅 패널을 사용 하 여 동작 테스트
1. 오른쪽 창 위에 있는 **학습** 아이콘을 선택 합니다.
1. 학습이 완료 되 면 **테스트**를 선택 합니다. 음성/텍스트를 통해 다음 utterance을 사용해 보세요.
    - 입력: tv 켜기
    - 출력: 양호, tv 켜기


> [!div class="mx-imgBorder"]
> ![웹 채팅으로 테스트](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> 테스트 패널에서이 음성/텍스트 입력이 처리 된 방법에 대 한 정보를 **자세히 설정** 을 선택할 수 있습니다.  

## <a name="add-settemperature-command"></a>SetTemperature 명령 추가

이제 단일 utterance,를 사용 하 고 메시지에 응답 하는 명령 **SetTemperature** 를 하나 더 추가 `set the temperature to 40 degrees` `Ok, setting temperature to 40 degrees` 합니다.

**TurnOn** 명령에 대해 설명 된 단계에 따라 예제 문장 "**온도를 40 각도로 설정**합니다."를 사용 하 여 새 명령을 만듭니다.

그런 다음 기존 **완료** 완료 규칙을 다음과 같이 편집 합니다.

| 설정    | 제안 값                          |
| ---------- | ---------------------------------------- |
| Name  | ConfirmationResponse                  |
| 조건 | None                                     |
| 작업    | 첫 번째 변형 > 간단한 편집기 > 음성 응답 보내기 >`Ok, setting temperature to 40 degrees` |

**저장** 을 선택 하 여 명령에 대 한 모든 변경 내용을 저장 합니다.

## <a name="add-setalarm-command"></a>SetAlarm 추가 명령
예제 문장 "**내일 오전 9 시에 경보 설정**"을 사용 하 여 새 명령 **setalarm** 을 만듭니다. 그런 다음 기존 **완료** 완료 규칙을 다음과 같이 편집 합니다.

| 설정    | 제안 값                          |
| ---------- | ---------------------------------------- |
| 규칙 이름  | ConfirmationResponse                  |
| 조건 | None                                     |
| 작업    | 첫 번째 변형 > 간단한 편집기 > 음성 응답 보내기 >`Ok, setting an alarm for 9 am tomorrow` |

**저장** 을 선택 하 여 명령에 대 한 모든 변경 내용을 저장 합니다.

## <a name="try-it-out"></a>사용해 보기

테스트 채팅 패널을 사용 하 여 동작 테스트
1. **학습**을 선택합니다. 학습 성공 후 **테스트** 를 선택 하 고 다음을 시도 합니다.
    - 입력: 온도를 40도로 설정 합니다.
    - 예상 응답: 정상, 온도를 40 각도로 설정
    - Tv 켜기를 입력 합니다.
    - 예상 응답: 정상, tv 켜기
    - 입력: 내일 오전 9 시에 경보 설정
    - 예상 응답: Ok, 내일 오전 9 시의 경보 설정

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 명령에 매개 변수 추가](./how-to-custom-commands-add-parameters-to-commands.md)
