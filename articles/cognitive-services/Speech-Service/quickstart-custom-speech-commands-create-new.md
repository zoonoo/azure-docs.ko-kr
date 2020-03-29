---
title: '빠른 시작: 사용자 지정 명령 만들기(미리 보기) - 음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 호스팅된 사용자 지정 명령 응용 프로그램을 만들고 테스트합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155590"
---
# <a name="quickstart-create-a-custom-command-preview"></a>빠른 시작: 사용자 지정 명령 만들기(미리 보기)

이 문서에서는 호스팅된 사용자 지정 명령 응용 프로그램을 만들고 테스트하는 방법을 알아봅니다.
응용 프로그램은 "TV를 켜기"와 같은 발언을 인식하고 간단한 메시지 "좋아, TV를 켜기"로 응답합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 음성 구독입니다.

음성 구독이 없는 경우 [음성 스튜디오로](https://speech.microsoft.com/) 이동하여 **음성 리소스 만들기를**선택하여 음성 구독을 만들 수 있습니다.

  > [!div class="mx-imgBorder"]
  > [ ![프로젝트 만들기](media/custom-speech-commands/create-new-subscription.png) ](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > 미리 보기 중에는 westus2 리전만 지원됩니다.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>사용자 지정 명령에 대한 음성 스튜디오로 이동

1. 웹 브라우저를 열고 음성 [스튜디오로](https://speech.microsoft.com/) 이동
1. 포털에 로그인할 자격 증명을 입력합니다.

   - 기본 보기는 음성 구독 목록입니다.
     > [!NOTE]
     > 선택한 구독 페이지가 표시되지 않으면 상단 표시줄의 설정 메뉴에서 "음성 리소스"를 선택하여 탐색할 수 있습니다.

1. 음성 구독을 선택한 다음 **스튜디오로 이동을** 선택합니다.
1. **사용자 지정 명령 선택(미리 보기)**

기본 보기는 만든 사용자 지정 명령 응용 프로그램의 목록입니다.

## <a name="create-a-custom-commands-project"></a>사용자 지정 명령 프로젝트 만들기

1. **새 프로젝트를** 선택하여 새 프로젝트를 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![프로젝트 만들기](media/custom-speech-commands/create-new-project.png)

1. 프로젝트 이름과 언어를 입력합니다.
1. 작성 리소스를 선택합니다. 유효한 작성 리소스가 없는 경우 새 리소스 만들기를 선택하여 **작성합니다.**

   > [!div class="mx-imgBorder"]
   > ![리소스 그룹 만들기](media/custom-speech-commands/create-new-resource.png)

   1. 리소스 이름, 그룹, 위치 및 가격 책정 계층을 입력합니다.

         > [!NOTE]
         > 원하는 리소스 그룹 이름을 "리소스 그룹" 필드에 입력하여 리소스 그룹을 만들 수 있습니다. **만들기를** 선택하면 리소스 그룹이 만들어집니다.

1. **만들기를** 클릭하여 프로젝트를 만듭니다.
1. 일단 생성되면 프로젝트를 선택합니다.

이제 뷰가 사용자 지정 명령 응용 프로그램의 개요가 되어야 합니다.

## <a name="update-luis-resources-optional"></a>LUIS 리소스 업데이트(선택 사항)

새 프로젝트 창에서 설정된 작성 리소스를 업데이트하고 런타임 동안 입력을 인식하는 데 사용되는 예측 리소스를 설정할 수 있습니다.

> [!NOTE]
> 응용 프로그램이 작성 리소스에서 제공하는 1,000개 이상의 요청을 초과하여 예측을 요청하기 전에 예측 리소스를 설정해야 합니다.

> [!div class="mx-imgBorder"]
> ![LUIS 리소스 설정](media/custom-speech-commands/set-luis-resources.png)

1. 왼쪽 창에서 **설정을** 선택한 다음 가운데 창에서 LUIS **리소스를** 선택하여 LUIS 리소스 창으로 이동합니다.
1. 예측 리소스를 선택하거나 새 리소스 만들기를 선택하여 **생성합니다.**
1. **저장** 선택

## <a name="create-a-new-command"></a>새 명령 만들기

이제 명령을 만들 수 있습니다. 단일 발언을 `turn on the tv`하고 메시지로 `Ok, turning on the TV`응답하는 예제를 사용해 보겠습니다.

1. 명령 옆에 있는 아이콘을 `+` 선택하여 새 명령을 만들고 이름을 지정합니다.`TurnOn`
1. **저장** 선택

> [!div class="mx-imgBorder"]
> ![명령 만들기](media/custom-speech-commands/create-add-command.png)

명령은 다음 의 집합입니다.

| 그룹            | 설명                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 샘플 문장 | 사용자가 이 명령을 트리거하기 위해 말할 수 있는 예시 발언                                                                 |
| 매개 변수       | 명령을 완료하는 데 필요한 정보                                                                                |
| 완료 규칙 | 명령을 이행하기 위해 취해야 할 조치입니다. 예를 들어, 사용자에게 응답하거나 다른 웹 서비스와 통신하기 |
| 고급 규칙   | 더 구체적이거나 복잡한 상황을 처리하는 추가 규칙                                                              |

### <a name="add-a-sample-sentence"></a>샘플 문장 추가

샘플 문장으로 시작하여 사용자가 말할 수있는 예제를 제공 해 보겠습니다.

```
turn on the tv
```

지금은 매개 변수가 없으므로 완료 규칙으로 이동할 수 있습니다.

### <a name="add-a-completion-rule"></a>완료 규칙 추가

이제 완료 규칙을 추가하여 작업이 수행되고 있음을 나타내는 사용자에게 응답합니다.

1. 완료 규칙 옆의 아이콘을 `+` 선택하여 새 완료 규칙 만들기
1. 규칙 이름 입력
1. 작업 추가
   1. 작업 옆의 `+` 아이콘을 선택하여 새 음성 응답 작업을 만들고`SpeechResponse`
   1. 응답을 입력합니다.

   > [!NOTE]
   > 일반 텍스트는 대시로 시작해야 합니다. 자세한 내용은 [여기를](https://aka.ms/sc-lg-format) 참조하십시오.

   > [!div class="mx-imgBorder"]
   > ![음성 응답 만들기](media/custom-speech-commands/create-speech-response-action.png)

1. **저장을** 클릭하여 규칙을 저장합니다.

> [!div class="mx-imgBorder"]
> ![완료 규칙 만들기](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 설정    | 제안 값                          | 설명                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 규칙 이름  | "확인 응답"                   | 규칙의 목적을 설명하는 이름          |
| 조건 | None                                     | 규칙을 실행할 수 있는 시기를 결정하는 조건    |
| 동작    | 음성 응답 "- 좋아, TV를 켜기" | 규칙 조건이 true일 때 취할 작업 |

## <a name="try-it-out"></a>체험

테스트 채팅 패널을 사용하여 동작을 테스트합니다.

> [!div class="mx-imgBorder"]
> ![웹 채팅으로 테스트](media/custom-speech-commands/create-basic-test-chat.png)

- 입력: "TV 켜기"
- 예상 응답 : "좋아, TV를 켜기"

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: 매개 변수를 사용하여 사용자 지정 명령 만들기(미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
