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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d94b527f1ad84d2b34a1708fd31eed273f8c363a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074517"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>후속 프롬프트를 사용 하 여 대화의 여러 설정 만들기

사용 하 여 후속 프롬프트 및 컨텍스트 관리 라고 여러 결과적으로 _다중 턴_, 다른 질문 중 하나에서 봇을 대 한 합니다.

설명 합니다 [데모 비디오](https://aka.ms/multiturnexample)합니다.

## <a name="what-is-a-multi-turn-conversation"></a>다중 설정 대화 란?

한 번에 몇 가지 유형의 대화를 완료할 수 없습니다. 클라이언트 응용 프로그램 (채팅 봇) 대화를 디자인할 때 사용자 필터링 하거나 올바른 답을 확인 하기 위해 조정 해야 하는 질문을 요청할 수 있습니다. 사용 하 여 사용자를 제공 하 여 질문에 대해이 흐름은 가능한 **후속 프롬프트**합니다.

QnA Maker 답을 반환 합니다 사용자 질문을 요청 _고_ 후속 프롬프트. 이 옵션을 사용 하면 선택 항목으로 추가 질문을 제공할 수 있습니다. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>채팅 봇과 다중 설정 대화 예제

채팅 봇 대화에 질문 하 여 질문을 사용자에 게 최종 응답 확인을 사용 하 여 관리 합니다.

![대화형 흐름 내에서 대화 상태를 대화를 계속 하기 위한 옵션으로 제공 된 답변 내에서 표시 되는 메시지를 제공 하 여 다중 설정 대화 시스템에서 관리 합니다.](../media/conversational-context/conversation-in-bot.png)

위의 이미지에서 사용자의 질문의 답을 반환 하기 전에 구체화 해야 합니다. 기술 자료를 질문 (#1)에 선택 항목 4 개 (2)로 채팅 봇 나오는 네 가지 후속 프롬프트에 있습니다. 

사용자가 선택할 (3)을 선택 하면 선택 항목 (4) 구체화의 다음 목록에 표시 됩니다. 이 계속할 수 (5) 올바르고 최종 응답 (6) 결정 됩니다.

상황에 맞는 대화를 관리 하려면 클라이언트 응용 프로그램을 변경 해야 합니다.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>문서 구조에서 여러 차례 대화 만들기
기술 자료를 만들 때 여러 차례 추출 수 있도록 선택적 확인 상자 표시 됩니다. 문서를 가져올 때이 옵션을 선택 하면 다중 설정 대화 구조에서 암시 합니다. 해당 구조 있으면 QnA Maker를 후속 프롬프트 QnA 쌍을 만듭니다. 다중 턴 구조 Url, PDF, DOCX에서 유추할 수 있습니다. 파일입니다. 

다음 이미지는 Microsoft Surface [PDF 파일](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) 수동으로 사용할 것입니다. 

![! [문서를 가져오는 경우 상황에 맞는 대화 암시 구조에서. 해당 구조 있으면 QnA Maker 만듭니다 후속 프롬프트 QnA 쌍, 문서 가져오기의 일부로.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

PDF 문서를 가져오면 QnA Maker는 대화형 흐름을 만들기 위해 구조에서 후속 프롬프트를 확인 합니다. 

![! [PDF 문서를 가져오면 QnA Maker 결정 대화형 흐름을 만들기 위해 구조에서 후속 프롬프트 합니다. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="filter-questions-and-answers-by-context"></a>질문 및 답변 컨텍스트에서 필터링

1. 질문 및 답변 쌍을 상황에 맞는 대화를 사용 하 여 표시를 줄입니다. 선택 **옵션을 보려면**을 선택한 후 **표시 컨텍스트 (미리 보기)** 합니다. 목록 후속 프롬프트를 사용 하 여 첫 번째 질문 및 답변 쌍을 추가 하기 전 까지는 비어 있게 됩니다. 

    ![질문을 필터링 하 고 상황에 맞는 대화 하 여 쌍에 대답](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>후속 프롬프트도 새로운 QnA 쌍 추가

1. 선택 **QnA 추가 쌍**합니다. 
1. 새 질문 텍스트를 입력할 `Give feedback.` 의 답변을 사용 하 여 `What kind of feedback do you have?`입니다.

1. 에 **답변** 선택이 질문에 대 한 열 **후속 프롬프트 추가**합니다. 
1. 합니다 **후속 프롬프트** 팝업 대화 상자를 사용 하면 기존 질문에 대 한 검색 하거나 새 질문을 입력할 수 있습니다. 이 절차에서는 텍스트 입력 `Feedback on an QnA Maker service`에 대 한 합니다 **텍스트 표시**합니다. 
1. 확인할 **상황에 맞는 전용**합니다. 합니다 **상황에 맞는 전용** 옵션 나타냅니다는이 사용자 텍스트 인식 됩니다 _만_ 이전 질문에 대 한 응답에 지정 된 경우. 이 시나리오에 대 한 프롬프트 텍스트를 아무런 의미가 독립 실행형 질문으로,만 이전 질문의 컨텍스트에서 것이 좋습니다.
1. 에 **응답할 링크** 텍스트 상자에 대 한 답을 입력 `How would you rate QnA Maker?`합니다.
1. 선택 **새로 만들기** 선택한 **저장**합니다. 

    ![새 프롬프트 QnA 만들기](../media/conversational-context/create-child-prompt-from-parent.png)

    새 질문 및 답변 쌍을 만들고 후속 프롬프트로 선택한 질문을 연결 합니다. 합니다 **상황에 맞는** 두 질문에 대 한 열에는 후속 프롬프트 관계를 나타냅니다. 

    ![! [두 질문에 대 한 상황에 맞는 열에는 후속 프롬프트 관계를 나타냅니다.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 선택 **추가 후속 프롬프트** 에 대 한는 `Give feedback` 다른 후속 프롬프트를 추가 하는 질문입니다. 
1. 새 질문을 입력 하 여 만듭니다 `Feedback on an existing feature`에서 답변을 사용 하 여 `Which feature would you like to give feedback on?`입니다.  

1.  확인할 **상황에 맞는 전용**합니다. 합니다 **상황에 맞는 전용** 옵션 나타냅니다는이 사용자 텍스트 인식 됩니다 _만_ 이전 질문에 대 한 응답에 지정 된 경우. 이 시나리오에 대 한 프롬프트 텍스트를 아무런 의미가 독립 실행형 질문으로,만 이전 질문의 컨텍스트에서 것이 좋습니다.
1.  **저장**을 선택합니다. 

    이 새 질문을 생성 및 후속 프롬프트 질문을 질문에 연결 된 `Give feedback` 질문 합니다.
    
    이 시점에서 맨 위 질문에 이전 질문에 연결 하는 두 가지 추가 프롬프트가 `Give feedback`합니다.

    ![! [이 시점에서 맨 위 질문에는 이전 질문의 답변에서 '피드백을 제공 합니다.'에 연결 하는 두 가지 추가 프롬프트가] (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. 선택 **저장 하 고 학습** 새 질문을 사용 하 여 기술 자료를 학습 합니다. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>후속 프롬프트 기존 QnA 쌍 추가

1. 후속 프롬프트로 기존 QnA 쌍을 연결 하려는 경우 질문 및 답변 쌍에 대 한 행을 선택 합니다.
1. 선택 **후속 프롬프트 추가** 해당 행에 있습니다.
1. 팝업 대화 상자에서 검색 상자에 질문 텍스트를 입력 합니다. 일치 항목을 모두 반환 됩니다. 추가 작업으로 원하며 확인 질문을 선택 **상황에 맞는 전용**을 선택한 후 **저장**합니다. 

    Ou는 후속 프롬프트에 추가 되 면 선택 해야 **저장 하 고 학습**합니다.
  
## <a name="add-metadata-to-follow-up-prompts"></a>후속 메시지에 메타 데이터를 추가 합니다. 

기술 자료의 질문 및 답변 쌍에 연결 된 후속 메시지를 표시, 메타 데이터 필터가 먼저 적용 된 다음는 후속 작업에서 반환 됩니다.

1. 두 후속 QnA 쌍에 대 한 메타 데이터를 각각을 추가 합니다.

    |질문|메타데이터 추가|
    |--|--|
    |`Feedback on an QnA Maker service`|"기능": "all"|
    |`Feedback on an existing feature`|"기능": "one"|
    
    ![서비스에서 대화에 대 한 응답에서 필터링 할 수 있도록 후속 프롬프트에 대 한 메타 데이터 추가](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. 저장 하 고 학습 합니다. 

    질문을 보낼 때 `Give feedback` 메타 데이터 필터를 사용 하 여 `Feature` 의 값을 사용 하 여 `all`, 해당 메타 데이터를 사용 하 여 QnA 쌍만 반환 됩니다. 필터를 일치 하지 않으므로 둘 모두 QnA 쌍 반환 되지 않습니다. 

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>테스트는 QnA 집합과 모든 후속 프롬프트

경우에 요청 추가 작업을 사용 하 여 질문을 테스트 합니다 **테스트** 응답 창 후속 프롬프트를 포함 합니다.

![테스트 창에 질문을 테스트할 때 응답 후속 프롬프트를 포함 합니다.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>초기 응답 및 후속 프롬프트를 반환 하는 JSON 요청

빈을 사용 하 여 `context` 개체를 사용자의 질문에 대 한 답변을 요청 하 고 후속 프롬프트를 포함 합니다. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
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
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

합니다 `prompts` 배열에서 텍스트를 제공 합니다 `displayText` 속성 및 `qnaId` 대화 흐름의 다음 표시 된 선택 항목으로이 대답을 표시할 수 있도록 값. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>비 초기 응답 및 후속 프롬프트를 반환 하는 JSON 요청

입력 된 `context` 이전 컨텍스트를 포함 하는 개체입니다.

현재 질문은 다음 JSON 요청에서 `Use Windows Hello to sign in` 이전 질문 했는데 `Accounts and signing in`합니다. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
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
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>클라이언트 응용 프로그램에서 컨텍스트를 보내고 메시지를 표시 합니다. 

클라이언트 응용 프로그램 단추 / 제안 된 작업으로 표시 되는 메시지를 표시 하려면 사용자에 대 한 옵션을 사용 하 여 모든 질문을 표시 합니다.
그러면 클라이언트 응용 프로그램이 다음 사용자 쿼리와 함께 전달 되어야 하는 컨텍스트로 현재 QnA 쌍 및 사용자 쿼리를 저장 합니다. 

이 사용 하 여 [Bot Framework 샘플](https://aka.ms/qnamakermultiturnsample) 에 다중 설정 대화 작업에 종단 간 QnA Maker 봇의를 참조 하세요.


## <a name="prompt-order-supported-in-api"></a>API에서 지원 되는 프롬프트 순서

JSON 응답에서 반환 하는 프롬프트 순서를 편집만 API에 의해 지원 됩니다. 

## <a name="next-steps"></a>다음 단계

상황별 대화에 대 한 자세한 합니다 [대화 상자 샘플](https://aka.ms/qnamakermultiturnsample) 하거나 더 자세한 [개념적 봇 다중 턴 대화에 대 한 디자인](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)합니다.

> [!div class="nextstepaction"]
> [기술 자료를 마이그레이션](../Tutorials/migrate-knowledge-base.md)
