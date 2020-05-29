---
title: '빠른 시작: 사용자 지정 명령 미리 보기 앱 만들기-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 호스트 된 사용자 지정 명령 미리 보기 응용 프로그램을 만들고 테스트 합니다. 응용 프로그램에서 길이 발언를 처리 합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142353"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>빠른 시작: 사용자 지정 명령 미리 보기 앱 만들기

이 빠른 시작에서는 사용자 지정 명령 응용 프로그램을 만들고 테스트 하는 방법을 배웁니다.
응용 프로그램은 "tv 켜기"와 같은 길이 발언를 처리 하 고 "확인, tv 켜기"와 같은 간단한 메시지를 사용 하 여 회신 합니다.

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech 리소스 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > 지금은 사용자 지정 명령은 westus, westus2 및 neur 지역의 음성 구독만 지원 합니다.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>사용자 지정 명령을 위한 Speech Studio로 이동

1. 웹 브라우저에서 [Speech Studio](https://speech.microsoft.com/)로 이동 합니다.
1. 자격 증명을 입력하여 포털에 로그인합니다.

   기본 보기는 음성 구독의 목록입니다.
    > [!NOTE]
    > 구독 선택 페이지가 표시 되지 않으면 화면 위쪽의 설정 메뉴에서 **음성 리소스** 를 선택 하 여 가져올 수 있습니다.

1. 음성 구독을 선택한 다음, **스튜디오로 이동**을 선택 합니다.
1. **사용자 지정 명령**을 선택 합니다.

     기본 보기는 선택한 구독에 있는 사용자 지정 명령 응용 프로그램의 목록입니다.

## <a name="create-a-custom-commands-project"></a>사용자 지정 명령 프로젝트 만들기

1. **새 프로젝트** 를 선택 하 여 프로젝트를 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![프로젝트 만들기](media/custom-speech-commands/create-new-project.png)

1. **이름** 상자에 프로젝트 이름을 입력 합니다.
1. **언어** 목록에서 언어를 선택 합니다.
1. **LUIS authoring 리소스** 목록에서 제작 리소스를 선택 합니다. 유효한 제작 리소스가 없는 경우 create **NEW LUIS authoring resource**를 선택 하 여 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![리소스 그룹 만들기](media/custom-speech-commands/create-new-resource.png)

   1. **리소스 이름** 상자에 리소스의 이름을 입력 합니다.
   1. **리소스 그룹** 목록에서 리소스 그룹을 선택 합니다.
   1. **위치** 목록에서 위치를 선택 합니다.
   1. **가격 책정 계층** 목록에서 계층을 선택 합니다.

      > [!NOTE]
      > **리소스 그룹 상자에** 리소스 그룹 이름을 입력 하 여 리소스 그룹을 만들 수 있습니다. **만들기**를 선택 하면 리소스 그룹이 생성 됩니다.

1. **만들기**를 선택합니다.
1. 프로젝트를 만든 후 프로젝트를 선택 합니다.

    이제 새 사용자 지정 명령 응용 프로그램의 개요가 표시 됩니다.

## <a name="update-luis-resources-optional"></a>LUIS 리소스 업데이트 (옵션)

**새 프로젝트** 창에서 선택한 제작 리소스를 업데이트 하 고 예측 리소스를 설정할 수 있습니다. 예측 리소스는 사용자 지정 명령 응용 프로그램이 게시 될 때 인식에 사용 됩니다. 개발 및 테스트 단계 중에는 예측 리소스가 필요 하지 않습니다.

1. 왼쪽 창에서 **설정** 을 선택 하 고 가운데 창에서 **LUIS resources** 를 선택 합니다.
1. 예측 리소스를 선택 하거나 **새 리소스 만들기**를 선택 하 여 하나를 만듭니다.
1. **저장**을 선택합니다.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS 리소스 설정](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> 제작 리소스는 매월 1000 예측 끝점 요청만 지원 하므로 사용자 지정 명령 응용 프로그램을 게시 하기 전에 LUIS 예측 리소스를 설정 해야 합니다.


## <a name="create-a-command"></a>명령 만들기

단일 utterance를 사용 하 여 메시지에 응답 하는 간단한 명령을 만들어 보겠습니다 `turn on the tv` `Ok, turning on the tv` .

1. 왼쪽 창의 맨 위에 있는 **새 명령** 을 선택 하 여 명령을 만듭니다. **새 명령** 창이 열립니다.
1. **이름** 상자에 **TurnOn**을 입력 합니다.
1. **만들기**를 선택합니다.

중간 창에는 명령의 속성이 나열 됩니다.


| 구성            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **예제 문장** | 사용자가 명령을 트리거할 수 있는 길이 발언의 예입니다.                                                                 |
| **매개 변수**       | 명령을 완료 하는 데 필요한 정보입니다.                                                                                |
| **완료 규칙** | 명령을 수행 하기 위해 수행 되는 작업입니다. 예를 들어 사용자에 게 응답 하거나 다른 웹 서비스와 통신 합니다. |
| **상호 작용 규칙**   | 보다 구체적 이거나 복잡 한 상황을 처리 하는 추가 규칙                                                              |


> [!div class="mx-imgBorder"]
> ![명령 만들기](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>예제 문장 추가

**예제 문장** 섹션부터 살펴보겠습니다. 사용자가 말할 수 있는 작업의 예를 제공 합니다.

1. 가운데 창에서 **예제 문장을** 선택 합니다. 
1. 오른쪽 창에 예를 추가 합니다.

    ```
    turn on the tv
    ```

1. 창 위쪽에서 **저장** 을 선택 합니다.

지금은 매개 변수가 없으므로 **완료 규칙** 섹션으로 이동할 수 있습니다.

### <a name="add-a-completion-rule"></a>완료 규칙 추가

이제 다음 구성이 포함 된 완료 규칙을 추가 합니다. 이 규칙은 사용자에 게 처리 작업을 수행 하 고 있음을 알려 줍니다.


| 설정    | 제안 값                          | 설명                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **이름**  | **ConfirmationResponse**                  | 규칙의 용도를 설명 하는 이름입니다.          |
| **조건** | 없음                                     | 규칙을 실행할 수 있는 시기를 결정 하는 조건입니다.    |
| **actions**    | **음성 응답 보내기-> 확인, tv 켜기** | 규칙 조건이 true 인 경우 수행할 동작입니다. |

1. 가운데 창의 맨 위에 있는 **추가** 를 선택 하 여 새 완료 규칙을 만듭니다.
1. **이름** 상자에 이름을 입력합니다.
1. 작업을 추가 합니다.
   1. **작업 섹션에서** **작업 추가** 를 선택 하 여 작업을 만듭니다.
   1. **작업 편집** 창의 **유형** 목록에서 **음성 응답 보내기**를 선택 합니다.
   1. **응답**아래에서 **단순 편집기**를 선택 합니다. **첫 번째 변형** 상자에서 **확인을**입력 하 고 tv를 켭니다.

   > [!div class="mx-imgBorder"]
   > ![응답 만들기](media/custom-speech-commands/create-speech-response-action.png)

1. **저장** 을 선택 하 여 규칙을 저장 합니다.
1. **완료 규칙** 섹션으로 돌아가서 **저장** 을 선택 하 여 모든 변경 내용을 저장 합니다. 

> [!div class="mx-imgBorder"]
> ![완료 규칙 만들기](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>기능 직접 사용해 보기

테스트 채팅 패널을 사용 하 여 동작을 테스트 합니다.
1. 오른쪽 창 맨 위에서 **학습** 을 선택 합니다.
1. 학습을 완료 한 후 **테스트**를 선택 합니다. 새 **테스트 응용 프로그램** 창이 나타납니다.
    - **Tv 켜기** 를 입력 합니다.
    - 예상 응답: **양호, tv** 켜기


> [!div class="mx-imgBorder"]
> ![동작 테스트](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 미리 보기 응용 프로그램 만들기](./quickstart-custom-speech-commands-create-parameters.md)
