---
title: 멀티 턴 대화 - QnA 메이커
description: 프롬프트와 컨텍스트를 사용하여 봇이 한 질문에서 다른 질문으로 이동하기 위해 다중 턴(다중 턴)을 관리합니다. 멀티 턴은 이전 질문의 맥락이 다음 질문과 대답에 영향을 미치는 앞뒤로 대화를 나눌 수 있는 기능입니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: abdde09fbb1f6b066772366c5cea933824cb5864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221444"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>추가 작업 프롬프트를 사용하여 여러 대화 설정 만들기

후속 프롬프트와 컨텍스트를 사용하여 봇이 한 질문에서 다른 질문으로 _멀티턴으로_알려진 여러 턴을 관리합니다.

멀티턴의 작동 방식을 보려면 다음 데모 비디오를 참조하십시오.

[![QnA 메이커의 멀티 턴 대화](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>멀티 턴 대화란 무엇입니까?

일부 질문은 한 턴에 대답할 수 없습니다. 클라이언트 응용 프로그램(챗봇) 대화를 디자인할 때 사용자는 정답을 결정하기 위해 필터링하거나 구체화해야 하는 질문을 할 수 있습니다. *사용자에게 후속 프롬프트를*표시하여 질문을 통해 이러한 흐름을 가능하게 합니다.

사용자가 질문을 하면 QnA Maker는 _답변과_ 후속 프롬프트를 반환합니다. 이 응답을 통해 후속 질문을 선택 항목으로 제시할 수 있습니다.

> [!CAUTION]
> 다중 회전 프롬프트는 FAQ 문서에서 추출되지 않습니다. 다중 회전 추출이 필요한 경우 QnA 쌍을 FAQ로 지정하는 물음표를 제거합니다.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>채팅 봇으로 멀티 턴 대화 예

멀티 턴을 사용하면 챗봇은 사용자와의 대화를 관리하여 다음 이미지와 같이 최종 답변을 결정합니다.

![대화를 통해 사용자를 안내하는 프롬프트가 있는 다중 턴 대화 상자](../media/conversational-context/conversation-in-bot.png)

앞의 이미지에서 사용자가 **내 계정을**입력하여 대화를 시작했습니다. 기술 자료에는 세 개의 연결된 질문 및 답변 쌍이 있습니다. 답변을 구체화하기 위해 사용자는 기술 자료의 세 가지 선택 사항 중 하나를 선택합니다. 질문 (#1)에는 세 가지 후속 프롬프트가 있으며 채팅 봇에는 세 가지 옵션 (#2)으로 표시됩니다.

사용자가 옵션(#3)을 선택하면 다음 구체화 옵션 목록(#4)이 표시됩니다. 이 순서는 사용자가 정답(#6)을 결정할 때까지(#5) 계속됩니다.


### <a name="use-multi-turn-in-a-bot"></a>봇에서 멀티 턴 사용

KB를 게시한 후 **봇 만들기** 단추를 선택하여 QnA Maker 봇을 Azure 봇 서비스에 배포할 수 있습니다. 봇에 대해 활성화한 채팅 클라이언트에 프롬프트가 표시됩니다.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>문서 구조에서 다중 전환 대화 만들기

기술 자료작성을 하면 **KB 채우기** 섹션에 **URL, .pdf 또는 .docx 파일에서 다중 턴 추출 활성화 확인란이** 표시됩니다.

![멀티 턴 추출을 활성화하기 위한 확인란](../media/conversational-context/enable-multi-turn.png)

이 옵션을 선택하면 QnA Maker가 문서 구조에 있는 계층 구조를 추출합니다. 계층 구조는 후속 프롬프트로 변환되고 계층의 루트는 부모 QnA 역할을 합니다. 일부 문서에서는 계층 구조의 루트에 답변으로 사용할 수 있는 콘텐츠가 없는 경우 이러한 계층 구조를 추출하기 위해 대체 답변 텍스트로 사용할 '기본 답변 텍스트'를 제공할 수 있습니다.

멀티 턴 구조는 URL, PDF 파일 또는 DOCX 파일에서만 유추할 수 있습니다. 구조의 예를 들어 Microsoft Surface [사용자 매뉴얼 PDF 파일의](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)이미지를 봅니다.

![! [사용자 설명서의 구조 예] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>나만의 멀티턴 문서 작성

다중 턴 문서를 작성하는 경우 다음 지침을 염두에 두십시오.

* 머리글과 하위 머리글을 사용하여 계층구조를 나타냅니다. 예를 들어 h1은 부모 QnA와 h2를 나타내기 위해 프롬프트로 취해야 하는 QnA를 나타내기 위해 할 수 있습니다. 작은 제목 크기를 사용하여 후속 계층을 나타냅니다. 문서의 구조를 암시하는 스타일, 색상 또는 기타 메커니즘을 사용하지 마십시오.

* 제목의 첫 번째 문자는 대문자로 만들어야 합니다.

* 물음표로 제목을 끝내지 `?`마십시오.

* [샘플 문서를](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) 예로 사용하여 고유한 다중 회전 문서를 만들 수 있습니다.

### <a name="adding-files-to-a-multi-turn-kb"></a>멀티 턴 KB에 파일 추가

계층적 문서를 추가할 때 QnA Maker는 구조에서 후속 프롬프트를 결정하여 대화 흐름을 만듭니다.

1. QnA Maker에서 **URL, .pdf 또는 .docx 파일에서 다중 턴 추출을 사용하도록 설정하여** 만든 기존 기술 기반을 선택합니다. 사용.
1. **설정** 페이지로 이동하여 추가할 파일 또는 URL을 선택합니다.
1. 기술 기반을 **저장하고 교육합니다.**

> [!Caution]
> 내보낸 TSV 또는 XLS 다중 턴 기술 자료 파일을 새 기술 또는 빈 기술 자료에 대한 데이터 원본으로 사용하는 지원은 지원되지 않습니다. 내보낸 다중 턴 프롬프트를 기술 자료에 추가하려면 QnA Maker 포털의 **설정** 페이지에서 해당 파일 형식을 **가져와야** 합니다.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>API 만들기를 사용하여 다중 턴 프롬프트를 사용하여 기술 자료 만들기

[QnA Maker Create API를](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)사용하여 다중 턴 프롬프트가 있는 지식 사례를 만들 수 있습니다. 프롬프트가 `context` 속성의 `prompts` 배열에 추가됩니다.

## <a name="show-questions-and-answers-with-context"></a>문맥으로 질문과 답변 표시

표시된 질문과 대답 쌍을 상황에 맞는 대화만 으로 줄입니다.

**옵션 보기를**선택한 다음 **컨텍스트 표시를**선택합니다. 목록에는 후속 프롬프트가 포함된 질문 및 답변 쌍이 표시됩니다.

![문맥 대화별로 질문과 답변 쌍을 필터링](../media/conversational-context/filter-question-and-answers-by-context.png)

다중 회전 컨텍스트가 첫 번째 열에 표시됩니다.

![! ["컨텍스트(미리 보기)" 열] (.. /media/conversational-context/Surface-manual-pdf-후속 프롬프트.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

앞의 이미지에서 **#1** 현재 질문을 나타내는 열에 굵은 텍스트를 나타냅니다. 상위 질문은 행의 맨 위 항목입니다. 그 아래에 있는 질문은 연결된 질문과 대답 쌍입니다. 이러한 항목은 선택할 수 있으므로 다른 컨텍스트 항목으로 즉시 이동하여 이동할 수 있습니다.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>후속 프롬프트로 기존 질문 및 답변 쌍을 추가

원래 질문인 **내 계정에는** **계정 및 로그인과**같은 후속 메시지가 표시됩니다.

!["계정 및 로그인" 답변 및 후속 조치 프롬프트](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

현재 연결되지 않은 기존 질문 및 답변 쌍에 후속 프롬프트를 추가합니다. 질문이 질문 및 답변 쌍에 연결되어 있지 않으므로 현재 보기 설정을 변경해야 합니다.

1. 기존 질문 및 답변 쌍을 후속 프롬프트로 연결하려면 질문 및 답변 쌍의 행을 선택합니다. Surface 설명서의 경우 **로그아웃을** 검색하여 목록을 줄입니다.
1. **[표고]** 행에서 **답** 열에서 **후속 추가 프롬프트를**선택합니다.
1. **후속 프롬프트** 팝업 창의 필드에 다음 값을 입력합니다.

    |필드|값|
    |--|--|
    |표시 텍스트|**장치를 끕니다.** 후속 프롬프트에 표시할 사용자 지정 텍스트입니다.|
    |컨텍스트 전용| 이 확인란을 선택합니다. 질문이 컨텍스트를 지정하는 경우에만 답변이 반환됩니다.|
    |답변 링크|**로그인 화면 사용을 입력하여** 기존 질문 및 답변 쌍을 찾습니다.|


1.  한 경기가 반환됩니다. 이 답변을 후속 으로 선택한 다음 **저장을**선택합니다.

    !["후속 조치 프롬프트(미리 보기)" 페이지](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 후속 프롬프트를 추가한 후 상단 탐색에서 **저장 및 학습을** 선택합니다.

### <a name="edit-the-display-text"></a>표시 텍스트 편집

후속 프롬프트가 만들어지고 기존 질문 및 답변 쌍이 **답변 링크로**입력되면 새 **[표시 텍스트]를**입력할 수 있습니다. 이 텍스트는 기존 질문을 대체하지 않으며 새 대체 질문을 추가하지 않습니다. 이러한 값과 는 별개입니다.

1. 표시 텍스트를 편집하려면 **컨텍스트** 필드에서 질문을 검색하고 선택합니다.
1. 해당 질문의 행에서 답변 열에서 후속 메시지를 선택합니다.
1. 편집할 텍스트 표시를 선택한 다음 **편집을**선택합니다.

    ![표시 텍스트에 대한 편집 명령](../media/conversational-context/edit-existing-display-text.png)

1. 후속 **프롬프트** 팝업 창에서 기존 표시 텍스트를 변경합니다.
1. 표시 텍스트 편집이 완료되면 **저장을**선택합니다.
1. 상단 탐색 모음에서 **저장 및 기차.**


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>후속 프롬프트로 새 질문 및 답변 쌍 추가

기술 자료에 새 질문 및 답변 쌍을 추가할 때 각 쌍은 후속 프롬프트로 기존 질문에 연결되어야 합니다.

1. 기술 자료 도구 모음에서 계정 및 로그인에 대한 기존 질문 및 답변 쌍을 검색하고 **선택합니다.**

1. 이 질문에 대한 **답변** 열에서 **후속 조치 추가 프롬프트를**선택합니다.
1. **후속 프롬프트(PREVIEW)에서**다음 값을 입력하여 새 후속 프롬프트를 만듭니다.

    |필드|값|
    |--|--|
    |표시 텍스트|*Windows 계정 만들기*. 후속 프롬프트에 표시할 사용자 지정 텍스트입니다.|
    |컨텍스트 전용|이 확인란을 선택합니다. 이 대답은 질문이 컨텍스트를 지정하는 경우에만 반환됩니다.|
    |답변 링크|다음 텍스트를 답으로 입력합니다.<br>*새 전자 메일 또는 기존 전자 메일 계정으로 Windows 계정을 [만듭니다.](https://account.microsoft.com/) *<br>데이터베이스를 저장하고 학습하면 이 텍스트가 변환됩니다. |
    |||

    ![새 프롬프트 질문 및 답변 만들기](../media/conversational-context/create-child-prompt-from-parent.png)


1. **새 수만들기를**선택한 다음 **에 저장을**선택합니다.

    이 작업은 새 질문 및 답변 쌍을 만들고 선택한 질문을 후속 프롬프트로 연결합니다. 두 질문에 대한 **컨텍스트** 열은 후속 프롬프트 관계를 나타냅니다.

1. **옵션 보기를**선택한 다음 [**컨텍스트 표시(미리 보기)를**](#show-questions-and-answers-with-context)선택합니다.

    새 질문은 어떻게 연결되는지 보여줍니다.

    ![새 후속 조치 프롬프트 만들기](../media/conversational-context/new-qna-follow-up-prompt.png)

    상위 질문은 새 질문을 선택 항목 중 하나로 표시합니다.

    ![! [두 질문에 대한 컨텍스트 열은 후속 프롬프트 관계를 나타냅니다.] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 후속 프롬프트를 추가한 후 상단 탐색 모음에서 **저장 및 학습을** 선택합니다.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>후속 프롬프트 테스트 중 멀티 턴 사용

**테스트** 창에서 후속 프롬프트로 문제를 테스트할 때 **다중 회전 활성화를**선택한 다음 질문을 입력합니다. 응답에는 후속 프롬프트가 포함됩니다.

![응답에는 후속 프롬프트가 포함됩니다.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

다중 턴을 활성화하지 않으면 답이 반환되지만 후속 프롬프트는 반환되지 않습니다.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>초기 답변 및 후속 메시지를 반환하는 JSON 요청

빈 `context` 개체를 사용하여 사용자의 질문에 대한 답변을 요청하고 후속 프롬프트를 포함합니다.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>초기 답변 및 후속 프롬프트를 반환하는 JSON 응답

앞의 섹션에서는 에 대한 답변 및 후속 **메시지(에 로그인)를**요청했습니다. 응답에는 *응답[0].context에*있는 프롬프트 정보와 사용자에게 표시할 텍스트가 포함됩니다.

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
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
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

배열은 `prompts` `displayText` 속성및 `qnaId` 값에 텍스트를 제공합니다. 이러한 답변을 대화 흐름에서 다음에 표시된 선택 항목으로 표시한 다음 다음 요청에서 선택한 `qnaId` 답변을 QnA Maker로 다시 보낼 수 있습니다.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>초기 응답이 아닌 응답 및 후속 메시지를 반환하는 JSON 요청

이전 `context` 컨텍스트를 포함하도록 개체를 채웁니다.

다음 JSON 요청에서 현재 질문은 *Windows Hello를 사용하여 로그인하고* 이전 질문은 *계정 및 로그인입니다.*

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>초기응답이 아닌 응답 및 후속 프롬프트를 반환하는 JSON 응답

QnA Maker _생성 응답_ JSON 응답에는 개체의 `context` 첫 번째 항목의 `answers` 속성에 대한 후속 프롬프트가 포함됩니다.

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>QnA Maker ID로 기술 자료 쿼리

멀티 턴 기능을 사용하여 사용자 지정 응용 프로그램을 빌드하는 경우 초기 질문의 응답에서 후속 프롬프트와 관련 `qnaId` 메시지가 반환됩니다. 이제 ID가 있으므로 후속 프롬프트의 요청 본문에 전달할 수 있습니다. 요청 본문에 `qnaId`는 의 문을 포함 하는 경우 (이전 QnA Maker 속성을 포함 하는 컨텍스트 개체), 생성 Answer 는 질문 텍스트에 의해 답을 찾기 위해 순위 알고리즘을 사용 하는 대신 ID로 정확한 질문을 반환 합니다.


## <a name="display-order-is-supported-in-the-update-api"></a>디스플레이 순서는 업데이트 API에서 지원됩니다.

JSON 응답에서 반환되는 [표시 텍스트 및 표시 순서는](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto) [Update API에서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)편집할 수 있도록 지원됩니다.

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>업데이트 API를 사용하면 다중 턴 프롬프트 추가 또는 삭제

[QnA 메이커 업데이트 API를](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)사용하여 다중 턴 프롬프트를 추가하거나 삭제할 수 있습니다.  프롬프트는 `context` 속성의 `promptsToAdd` 배열과 배열에 `promptsToDelete` 추가됩니다.

## <a name="export-knowledge-base-for-version-control"></a>버전 제어를 위한 내보내기 기술 자료

QnA Maker는 내보낸 파일에 다중 회전 대화 단계를 포함하여 버전 제어를 지원합니다.

## <a name="next-steps"></a>다음 단계

이 [대화 상자 샘플에서](https://aka.ms/qnamakermultiturnsample) 컨텍스트 대화에 대해 자세히 알아보거나 [다중 턴 대화를 위한 개념식 봇 디자인에](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)대해 자세히 알아보십시오.

> [!div class="nextstepaction"]
> [기술 자료 마이그레이션](../Tutorials/migrate-knowledge-base.md)
