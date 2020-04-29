---
title: '빠른 시작: 사용자 지정 명령 만들기 (미리 보기)-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 호스트 된 사용자 지정 명령 응용 프로그램을 만들고 테스트 합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "76155590"
---
# <a name="quickstart-create-a-custom-command-preview"></a>빠른 시작: 사용자 지정 명령 만들기 (미리 보기)

이 문서에서는 호스트 된 사용자 지정 명령 응용 프로그램을 만들고 테스트 하는 방법을 배웁니다.
응용 프로그램은 "tv 켜기"와 같은 utterance을 인식 하 고 간단한 메시지 "확인, tv 켜기"를 사용 하 여 응답 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 음성 구독.

음성 구독이 없는 경우 speech [Studio](https://speech.microsoft.com/) 로 이동 하 여 음성 **리소스 만들기**를 선택 하 여 만들 수 있습니다.

  > [!div class="mx-imgBorder"]
  > [![프로젝트](media/custom-speech-commands/create-new-subscription.png) 만들기](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > 미리 보기 중에는 westus2 지역만 지원 됩니다.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>사용자 지정 명령을 위한 Speech Studio로 이동

1. 웹 브라우저를 열고 [Speech Studio](https://speech.microsoft.com/) 로 이동 합니다.
1. 자격 증명을 입력 하 여 포털에 로그인 합니다.

   - 기본 보기는 음성 구독 목록입니다.
     > [!NOTE]
     > 구독 선택 페이지가 표시 되지 않는 경우 위쪽 표시줄의 설정 메뉴에서 "음성 리소스"를 선택 하 여 탐색할 수 있습니다.

1. 음성 구독을 선택한 다음, **스튜디오로 이동** 을 선택 합니다.
1. **사용자 지정 명령 선택 (미리 보기)**

기본 보기는 사용자가 만든 사용자 지정 명령 응용 프로그램의 목록입니다.

## <a name="create-a-custom-commands-project"></a>사용자 지정 명령 프로젝트 만들기

1. 새 **프로젝트를 선택 하** 여 새 프로젝트 만들기

   > [!div class="mx-imgBorder"]
   > ![프로젝트 만들기](media/custom-speech-commands/create-new-project.png)

1. 프로젝트 이름 및 언어를 입력 합니다.
1. 제작 리소스를 선택 합니다. 유효한 제작 리소스가 없는 경우 **새 리소스 만들기**를 선택 하 여 하나를 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![리소스 그룹 만들기](media/custom-speech-commands/create-new-resource.png)

   1. 리소스 이름, 그룹, 위치 및 가격 책정 계층을 입력 합니다.

         > [!NOTE]
         > "리소스 그룹" 필드에 원하는 리소스 그룹 이름을 입력 하 여 리소스 그룹을 만들 수 있습니다. **만들기** 를 선택 하면 리소스 그룹이 생성 됩니다.

1. **만들기** 를 클릭 하 여 프로젝트를 만듭니다.
1. 만든 후 프로젝트를 선택 합니다.

이제 사용자 지정 명령 응용 프로그램의 개요가 표시 됩니다.

## <a name="update-luis-resources-optional"></a>LUIS 리소스 업데이트 (옵션)

새 프로젝트 창에서 제작 리소스 집합을 업데이트 하 고 런타임 중에 입력을 인식 하는 데 사용 되는 예측 리소스를 설정할 수 있습니다.

> [!NOTE]
> 응용 프로그램이 제작 리소스에서 제공 하는 1000 요청을 초과 하 여 예측을 요청 하기 전에 예측 리소스를 설정 해야 합니다.

> [!div class="mx-imgBorder"]
> ![LUIS 리소스 설정](media/custom-speech-commands/set-luis-resources.png)

1. 왼쪽 창에서 **설정** 을 선택 하 여 LUIS 리소스 창으로 이동한 다음 가운데 창에서 **리소스를 LUIS** 합니다.
1. 예측 리소스를 선택 하거나 **새 리소스 만들기** 를 선택 하 여 하나를 만듭니다.
1. **저장**을 선택합니다.

## <a name="create-a-new-command"></a>새 명령 만들기

이제 명령을 만들 수 있습니다. 단일 utterance `turn on the tv`를 사용 하 여 메시지 `Ok, turning on the TV`에 응답 하는 예제를 살펴보겠습니다.

1. 명령 옆에 있는 `+` 아이콘을 선택 하 고 이름을 지정 하 여 새 명령을 만듭니다.`TurnOn`
1. **저장**을 선택합니다.

> [!div class="mx-imgBorder"]
> ![명령 만들기](media/custom-speech-commands/create-add-command.png)

명령은 다음과 같은 집합입니다.

| 그룹            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 샘플 문장 | 예 길이 발언 사용자가이 명령을 트리거할 수 있습니다.                                                                 |
| 매개 변수       | 명령을 완료 하는 데 필요한 정보                                                                                |
| 완료 규칙 | 명령을 수행 하기 위해 수행할 작업입니다. 예를 들어 사용자에 게 응답 하거나 다른 웹 서비스와 통신 하는 경우 |
| 고급 규칙   | 보다 구체적인 또는 복잡 한 상황을 처리 하는 추가 규칙                                                              |

### <a name="add-a-sample-sentence"></a>샘플 문장 추가

샘플 문장부터 시작 하 여 사용자가 말할 수 있는 작업의 예를 제공 하겠습니다.

```
turn on the tv
```

지금은 완료 규칙으로 이동할 수 있도록 매개 변수가 없습니다.

### <a name="add-a-completion-rule"></a>완료 규칙 추가

이제 작업이 수행 중임을 나타내는 사용자에 게 응답 하는 완료 규칙을 추가 합니다.

1. 완료 규칙 옆의 `+` 아이콘을 선택 하 여 새 완료 규칙을 만듭니다.
1. 규칙 이름 입력
1. 작업 추가
   1. 작업 옆의 `+` 아이콘을 선택 하 여 새 음성 응답 작업을 만들고`SpeechResponse`
   1. 응답 입력

   > [!NOTE]
   > 일반 텍스트는 대시로 시작 해야 합니다. 자세한 내용은 [여기](https://aka.ms/sc-lg-format) 를 참조 하세요.

   > [!div class="mx-imgBorder"]
   > ![음성 응답 만들기](media/custom-speech-commands/create-speech-response-action.png)

1. **저장** 을 클릭 하 여 규칙을 저장 합니다.

> [!div class="mx-imgBorder"]
> ![완료 규칙 만들기](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 설정    | 제안 값                          | Description                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 규칙 이름  | "ConfirmationResponse"                   | 규칙의 용도를 설명 하는 이름입니다.          |
| 조건 | 없음                                     | 규칙을 실행할 수 있는 시기를 결정 하는 조건    |
| 작업    | SpeechResponse "-Ok, TV 켜기" | 규칙 조건이 참인 경우 수행할 동작입니다. |

## <a name="try-it-out"></a>기능 직접 사용해 보기

테스트 채팅 패널을 사용 하 여 동작을 테스트 합니다.

> [!div class="mx-imgBorder"]
> ![웹 채팅으로 테스트](media/custom-speech-commands/create-basic-test-chat.png)

- "Tv 켜기"를 입력 합니다.
- 예상 응답: "확인, tv 켜기"

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
