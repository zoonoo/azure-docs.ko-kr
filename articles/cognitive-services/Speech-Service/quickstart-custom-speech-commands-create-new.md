---
title: '빠른 시작: 사용자 지정 명령 만들기 (미리 보기)-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 호스트 된 사용자 지정 명령 응용 프로그램을 만들고 테스트 합니다.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 5c90c91791af8a9a16039e9650765bd24433ff38
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815823"
---
# <a name="quickstart-create-a-custom-command-preview"></a>빠른 시작: 사용자 지정 명령 만들기 (미리 보기)

이 문서에서는 호스트 된 사용자 지정 명령 응용 프로그램을 만들고 테스트 하는 방법을 배웁니다.
응용 프로그램은 "tv 켜기"와 같은 utterance을 인식 하 고 간단한 메시지 "확인, tv 켜기"를 사용 하 여 응답 합니다.

## <a name="prerequisites"></a>전제 조건

- 음성 구독. [음성 서비스를 무료로 사용해 보세요](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > 미리 보기 중에는 구독 키에 대해 westus2 지역만 지원 됩니다.

- LUIS ( [Language Understanding](https://www.luis.ai/home) ) 제작 키:
  1. 웹 브라우저를 열고 [Azure Portal](https://portal.azure.com) 로 이동 합니다.
  1. 리소스 만들기 선택
  1. [Language Understanding](https://aka.ms/sc-luis-all) 를 검색 하 고 선택 합니다.
  1. 만들기 옵션에서 제작을 선택 합니다.
  1. 리소스를 배포한 후 리소스로 이동 하 여 빠른 시작 또는 키 섹션에서 키를 복사 합니다.

      > [!div class="mx-imgBorder"]
      > 제작 리소스](media/custom-speech-commands/resources-lu-authoring.png) 만들기 ![

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
   > ![새 프로젝트 만들기](media/custom-speech-commands/create-new-project.png)

1. 프로젝트 이름 및 언어를 입력 하 고 **다음** 을 선택 하 여 계속 진행 합니다.
1. LUIS authoring key 입력
1. 만든 후 프로젝트를 선택 합니다.

이제 사용자 지정 명령 응용 프로그램의 개요가 표시 됩니다.

## <a name="create-a-new-command"></a>새 명령 만들기

이제 명령을 만들 수 있습니다. 단일 utterance를 사용 하 고 `turn on the tv``Ok, turning on the TV`메시지를 사용 하 여 응답 하는 예를 살펴보겠습니다.

1. 명령 옆에 있는 `+` 아이콘을 선택 하 고 이름을 지정 하 여 새 명령을 만듭니다 `TurnOn`
1. **저장**을 선택합니다.

> [!div class="mx-imgBorder"]
> 명령](media/custom-speech-commands/create-add-command.png) ![만듭니다.

명령은 다음과 같은 집합입니다.

| 그룹            | 설명                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 샘플 문장 | 예 길이 발언 사용자가이 명령을 트리거할 수 있습니다.                                                                 |
| parameters       | 명령을 완료 하는 데 필요한 정보                                                                                |
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

> [!div class="mx-imgBorder"]
> ![완료 규칙을 만듭니다](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 설정    | 제안 값                        | 설명                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| 규칙 이름  | "ConfirmationResponse"                 | 규칙의 용도를 설명 하는 이름입니다.          |
| 조건 | 없음                                   | 규칙을 실행할 수 있는 시기를 결정 하는 조건    |
| 작업    | SpeechResponse "확인, TV 켜기" | 규칙 조건이 참인 경우 수행할 동작입니다. |

## <a name="try-it-out"></a>사용해 보기

테스트 채팅 패널을 사용 하 여 동작을 테스트 합니다.

> [!div class="mx-imgBorder"]
> webchat](media/custom-speech-commands/create-basic-test-chat.png)를 사용 하 여 테스트 ![

- "Tv 켜기"를 입력 합니다.
- 예상 응답: "확인, tv 켜기"

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
