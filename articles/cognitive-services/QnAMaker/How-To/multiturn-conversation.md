---
title: 다중 설정 대화
titleSuffix: Azure Cognitive Services
description: 다중 차례로 다른 질문 중 하나에서 봇에 라고 여러 결과적으로 관리 하려면 프롬프트 및 컨텍스트를 사용 합니다. 다중 됩니다 백업 하는 기능을 명시 하 고 대화 이전 질문의 상황에 맞는 다음 질문 및 답변에는 영향을 줍니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 3ca166b287858b3e42aeda1421d1733fe24c81ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479674"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>후속 프롬프트를 사용 하 여 대화의 여러 설정 만들기

사용 하 여 후속 프롬프트 및 컨텍스트 관리 라고 여러 결과적으로 _다중 턴_, 다른 질문 중 하나에서 봇을 대 한 합니다.

어떻게 수행 되는지 확인 하려면 다음 데모 비디오를 시청 하세요.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>다중 설정 대화 란?

한 번에 몇 가지 질문에 대답할 수 없습니다. 클라이언트 응용 프로그램 (채팅 봇) 대화를 디자인할 때 사용자 필터링 하거나 올바른 답을 확인 하기 위해 조정 해야 하는 질문을 요청할 수 있습니다. 사용 하 여 사용자를 제공 하 여 질문에 대해이 흐름은 가능한 **후속 프롬프트**합니다.

QnA Maker 답을 반환 합니다 사용자 질문을 요청 _고_ 후속 프롬프트. 이 옵션을 사용 하면 선택 항목으로 추가 질문을 제공할 수 있습니다. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>채팅 봇과 다중 설정 대화 예제

채팅 봇, 사용자와 질문 질문 최종 해답을 확인 하려면 대화를 관리 합니다.

![대화형 흐름 내에서 대화 상태를 대화를 계속 하기 위한 옵션으로 제공 된 답변 내에서 표시 되는 메시지를 제공 하 여 다중 설정 대화 시스템에서 관리 합니다.](../media/conversational-context/conversation-in-bot.png)

위의 이미지에서 사용자 입력 `My account`합니다. 기술 자료에는 3 개의 연결 된 QnA 쌍이 있습니다. 사용자가 답변을 구체화 하는 세 가지 선택 사항 중에서 선택 해야 합니다. 기술 자료 (#1) 질문 채팅 봇의 세 가지 선택 (2)으로 표시 하는 세 가지 후속 프롬프트에 있습니다. 

사용자가 선택할 (3)을 선택 하면 선택 항목 (4) 구체화의 다음 목록에 표시 됩니다. 이 계속할 수 (5) 올바르고 최종 응답 (6) 결정 됩니다.

이전 이미지에 있는 **여러 차례 사용** 프롬프트를 표시 하려면 선택 합니다. 

### <a name="using-multi-turn-in-a-bot"></a>다중 턴을 사용 하 여 봇의

상황에 맞는 대화를 관리 하려면 클라이언트 응용 프로그램을 변경 해야 합니다. 추가 해야 합니다 [봇 코드](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) 표시 되는 메시지를 확인 합니다.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>문서 구조에서 다중 설정 대화 만들기

기술 자료를 만들 때 여러 차례 추출 수 있도록 선택적 확인 상자 표시 됩니다. 

![기술 자료를 만들 때 여러 차례 추출 수 있도록 선택적 확인 상자 표시 됩니다.](../media/conversational-context/enable-multi-turn.png)

문서를 가져올 때이 옵션을 선택 하면 다중 설정 대화 구조에서 암시 합니다. 해당 구조 있으면 QnA Maker를 후속 프롬프트 QnA 쌍을 만듭니다. 

다중 턴 구조 Url, PDF, DOCX에서 유추할 수 있습니다. 파일입니다. 

다음 이미지는 Microsoft Surface [PDF 파일](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) 수동으로 사용할 것입니다. 이 PDF 파일의 크기가, Azure QnA Maker 리소스 필요한 가격 책정 계층 B의 검색 (인덱스 15) 이상. 

![! [문서를 가져오는 경우 상황에 맞는 대화 암시 구조에서. 해당 구조 있으면 QnA Maker 만듭니다 후속 프롬프트 QnA 쌍, 문서 가져오기의 일부로.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

PDF 문서를 가져오면 QnA Maker는 대화형 흐름을 만들기 위해 구조에서 후속 프롬프트를 확인 합니다. 

1. **1 단계**를 선택 **기술 자료 만들기** 위쪽 탐색 모음에서.
1. **2 단계**를 만들거나 기존 QnA 서비스를 사용 합니다. B의 검색 서비스를 사용 하 여 QnA 서비스를 사용 하도록 (15 인덱스) 또는 화면 수동 PDF 파일 보다 작은 계층에 대 한 너무 크기 때문에 더 높은 합니다.
1. **3 단계**에 기술 자료의 이름과 같은 입력 `Surface manual`합니다.
1. **4 단계**, 선택 **Url에서 여러 차례 추출 사용.docx 또는.pdf 파일입니다.** Surface 설명서에 대 한 URL을 선택 합니다.

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. 선택 된 **KB 만들기** 단추입니다. 

    기술 자료를 만든 후 질문 및 답변 쌍의 뷰를 표시 합니다.

## <a name="show-questions-and-answers-with-context"></a>질문 및 답변 컨텍스트를 사용 하 여 표시 합니다.

질문 및 답변 쌍을 상황에 맞는 대화를 사용 하 여 표시를 줄입니다. 

1. 선택 **옵션을 보려면**을 선택한 후 **표시 컨텍스트 (미리 보기)** 합니다. 후속 프롬프트를 포함 하는 질문 및 답변 쌍 목록에 나타납니다. 

    ![질문을 필터링 하 고 상황에 맞는 대화 하 여 쌍에 대답](../media/conversational-context/filter-question-and-answers-by-context.png)

2. 첫 번째 열에 다중 설정 컨텍스트를 표시합니다.

    ![! [PDF 문서를 가져오면 QnA Maker 결정 대화형 흐름을 만들기 위해 구조에서 후속 프롬프트 합니다. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    위의 이미지에서 #1 현재 질문을 의미 하는 열에서 굵게 표시 된 텍스트를 나타냅니다. 부모 질문은 행의 상위 항목입니다. 아래 질문은 연결 된 질문 및 답변 쌍입니다. 이러한 항목은 다른 컨텍스트 항목을 즉시 이동할 수 있도록 선택할 수 있습니다. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>후속 프롬프트 기존 QnA 쌍 추가

원래 질문 `My account` 와 같은 후속 프롬프트를가지고 `Accounts and signing in`합니다. 

!['계정의' 원래 질문을 올바르게 반환 합니다 '계정 및 로그인' 답과 이미 연결 된 후속 프롬프트.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

현재 연결 되지 않은 기존 QnA 쌍에 후속 프롬프트를 추가 합니다. 질문 QnA 쌍에 연결 되지 않은, 때문에 현재 보기 설정을 변경 해야 합니다.

1. 후속 프롬프트로 기존 QnA 쌍에 연결 하려면 질문 및 답변 쌍에 대 한 행을 선택 합니다. 검색할 노출 수동 `Sign out` 목록을 축소 합니다.
1. 에 대 한 행의 `Signout`을 선택 **추가 후속 프롬프트** 에서 합니다 **응답** 열입니다.
1. 에 **후속 프롬프트 (미리 보기)** 팝업 창에서 다음을 입력 합니다.

    |필드|값|
    |--|--|
    |표시 텍스트|`Turn off the device`. 후속 프롬프트에서 표시 하도록 선택 하면 사용자 지정 텍스트입니다.|
    |상황에 맞는 전용|선택 합니다. 이 응답 컨텍스트를 지정 하는 질문 하는 경우에 반환 됩니다.|
    |응답에 대 한 링크|입력 `Use the sign-in screen` 기존 QnA 쌍을 찾을 수 있습니다.|


1.  이상의 일치 항목이 반환 됩니다. 이 응답의 추가 작업으로 선택한 다음 선택 **저장할**합니다. 

    ![답변의 텍스트를 사용 하 여 기존 대답에 대 한 응답 대화 상자에 후속 프롬프트의 링크를 검색 합니다.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 선택 해야 하는 후속 프롬프트를 추가한 후 **저장 하 고 학습** 위쪽 탐색에서 합니다.
  
### <a name="edit-the-display-text"></a>표시 텍스트를 편집 합니다. 

경우 후속 프롬프트를 만들고 기존 QnA 쌍으로 선택 하는 합니다 **응답에 연결할**, 새 입력할 수 있습니다 **텍스트 표시**합니다. 이 텍스트는 기존 질문을 대체 하지 않습니다 하 고 새 대체 질문을 추가 하지 않습니다. 이러한 값을 분리 합니다. 

1. 표시 텍스트를 편집 하려면 검색 하 고에 질문을 선택 합니다 **상황에 맞는** 필드입니다.
1. 질문의 행에서 응답 열의 후속 프롬프트를 선택 합니다. 
1. 편집 하 고 선택 표시 텍스트 선택 **편집**합니다.

    ![을 편집 하려면 원하는 표시 텍스트를 선택한 다음 편집을 선택 합니다.](../media/conversational-context/edit-existing-display-text.png)

1. 합니다 **후속 프롬프트** 팝업을 사용 하면 기존 텍스트를 변경할 수 있습니다. 
1. 완료 되 면 선택 표시 텍스트 편집 **저장할**합니다. 
1. 선택 해야 **저장 하 고 학습** 위쪽 탐색에서 합니다.


<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>후속 프롬프트도 새로운 QnA 쌍 추가

기술 자료에 새 QnA 쌍을 추가 합니다. QnA 쌍으로 후속 프롬프트를 기존 질문에 연결 되어야 합니다.

1. 기술 자료의 도구 모음에서 검색 하 고 선택에 대 한 기존 QnA 쌍 `Accounts and Signing In`합니다. 

1. 에 **답변** 선택이 질문에 대 한 열 **후속 프롬프트 추가**합니다. 
1. 합니다 **후속 프롬프트 (미리 보기)** , 다음 값을 입력 하 여 새 후속 프롬프트를 만듭니다. 

    |텍스트 필드|값|
    |--|--|
    |**텍스트를 표시 합니다.**|`Create a Windows Account`. 후속 프롬프트에서 표시 하도록 선택 하면 사용자 지정 텍스트입니다.|
    |**Context-only**|선택 합니다. 이 응답 컨텍스트를 지정 하는 질문 하는 경우에 반환 됩니다.|
    |**응답에 연결**|답변으로 다음 텍스트를 입력 합니다.<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>이 텍스트를 변환할 저장 하 고 데이터베이스를 학습 하는 경우 |
    |||

    ![새 프롬프트 QnA 만들기](../media/conversational-context/create-child-prompt-from-parent.png)


1. 선택 **새로 만들기** 선택한 **저장**합니다. 

    새 질문 및 답변 쌍을 만들고 후속 프롬프트로 선택한 질문을 연결 합니다. 합니다 **상황에 맞는** 두 질문에 대 한 열에는 후속 프롬프트 관계를 나타냅니다. 

1. 변경 합니다 **옵션을 보려면** 를 [상황에 맞는 표시](#show-questions-and-answers-with-context)합니다.

    새 질문을 연결 하는 방법을 보여 줍니다.

    ![새 후속 프롬프트를 만들기 ](../media/conversational-context/new-qna-follow-up-prompt.png)

    부모 질문의 선택 항목 중 하나로 새 질문을 보여 줍니다.

    ![! [두 질문에 대 한 상황에 맞는 열에는 후속 프롬프트 관계를 나타냅니다.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 선택 해야 하는 후속 프롬프트를 추가한 후 **저장 하 고 학습** 위쪽 탐색에서 합니다.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>테스트 추가 작업 메시지를 표시 하면 다중 설정 사용

경우 프롬프트 추가 작업을 사용 하 여 질문을 테스트 합니다 **테스트** 창 **다중 턴을 사용 하도록 설정**, 질문을 입력 하 고 합니다. 응답은 후속 프롬프트를 포함 합니다.

![테스트 창에 질문을 테스트할 때 응답 후속 프롬프트를 포함 합니다.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

다중 턴을 사용 하지 않는 경우 답변 반환할 수 있지만 후속 프롬프트는 반환 되지 않습니다.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>초기 응답 및 후속 프롬프트를 반환 하는 JSON 요청

빈을 사용 하 여 `context` 개체를 사용자의 질문에 대 한 답변을 요청 하 고 후속 프롬프트를 포함 합니다. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>초기 응답 및 후속 프롬프트를 반환 하는 JSON 응답

이전 섹션에 요청 응답 및 모든 후속 프롬프트를 `Accounts and signing in`입니다. 응답에 있는 확인 정보를 포함 `answers[0].context`, 사용자에 게 표시할 텍스트를 포함 합니다. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` 배열에서 텍스트를 제공 합니다 `displayText` 속성 및 `qnaId` 값 대화의 다음 표시 된 선택 항목으로이 대답을 표시할 수 있도록 흐름을 다음 송신 다음 요청에서 QnA Maker에 선택한 값입니다. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>비 초기 응답 및 후속 프롬프트를 반환 하는 JSON 요청

입력 된 `context` 이전 컨텍스트를 포함 하는 개체입니다.

현재 질문은 다음 JSON 요청에서 `Use Windows Hello to sign in` 이전 질문 했는데 `Accounts and signing in`합니다. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>비 초기 응답 및 후속 프롬프트를 반환 하는 JSON 응답

QnA Maker _GenerateAnswer_ JSON 응답의 후속 프롬프트를 포함 합니다 `context` 첫 번째 항목의 속성을 `answers` 개체:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Id가 QnA 기술 자료를 쿼리 합니다.

초기 질문 응답, 후속 프롬프트 및 관련 `qnaId` 반환 됩니다. ID 설정 했으므로이 후속 프롬프트를 요청 본문에 전달할 수 있습니다. 요청 본문을 포함 하는 경우는 `qnaId`, 및 컨텍스트 개체 (이전 QnA 속성 포함), GenerateAnswer 답변을 찾아보십시오 질문 텍스트의 순위 알고리즘을 사용 하는 대신 ID를 기준으로 정확한 질문에 반환 됩니다. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>클라이언트 응용 프로그램에서 컨텍스트를 보내고 메시지를 표시 합니다. 

기술 자료에서 표시 되는 메시지를 추가 하 고 테스트 창에 흐름을 테스트 합니다. 이제 클라이언트 응용 프로그램에서 이러한 프롬프트를 사용 해야 합니다. Bot Framework에 대 한 프롬프트 시작 되지 않습니다 자동으로 클라이언트 응용 프로그램에 표시 합니다. 에 표시할 수 있습니다 프롬프트 단추 / 제안 된 작업으로 사용자의 쿼리에 대 한 답변의 일부로 클라이언트 응용 프로그램이를 포함 하 여 [Bot Framework 샘플](https://aka.ms/qnamakermultiturnsample) 코드에서. 클라이언트 응용 프로그램은 현재 QnA ID 및 사용자 쿼리를 저장 및 전달에 [GenerateAnswer API의 상황에 맞는 개체](#json-request-to-return-non-initial-answer-and-follow-up-prompts) 다음 사용자 쿼리에 대 한 합니다. 

## <a name="display-order-supported-in-api"></a>API에서 지원 되는 순서를 표시 합니다.

[텍스트 표시 및 표시 순서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)JSON 응답에서 반환 된, 편집 하는 동안 지원 되는 [업데이트 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)합니다. 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>다음 단계

상황별 대화에 대 한 자세한 합니다 [대화 상자 샘플](https://aka.ms/qnamakermultiturnsample) 하거나 더 자세한 [개념적 봇 다중 턴 대화에 대 한 디자인](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)합니다.

> [!div class="nextstepaction"]
> [기술 자료를 마이그레이션](../Tutorials/migrate-knowledge-base.md)
