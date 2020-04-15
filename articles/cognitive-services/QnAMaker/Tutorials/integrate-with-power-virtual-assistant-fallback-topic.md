---
title: '자습서: Power Virtual Agent와 통합 - QnA Maker'
description: 이 자습서에서는 활성 학습을 통해 기술 자료의 품질을 향상시킵니다. 기존 질문을 삭제하거나 변경하지 않고 검토, 수락, 거부 또는 추가합니다.
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398739"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>자습서: Power Virtual Agent에 기술 자료 추가
기술 자료에서 대답을 제공하는 [Power Virtual Agent](https://powervirtualagents.microsoft.com/) 봇을 만들고 확장합니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Power Virtual Agent 만들기
> * 시스템 대체 항목 만들기
> * Power Automate 흐름 항목에 QnA Maker를 작업으로 추가
> * Power Automate 솔루션 만들기
> * 솔루션에 Power Automate 흐름 추가
> * Power Virtual Agent 게시
> * Power Virtual Agent 테스트, QnA Maker 기술 자료에서 대답 받기

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>기술 자료와 Power Virtual Agent 통합

[Power Virtual Agents](https://powervirtualagents.microsoft.com/)를 사용하면 데이터 과학자 또는 개발자 없이 팀에서 코드를 사용하지 않는 단계별 그래픽 인터페이스를 사용하여 강력한 봇을 쉽게 만들 수 있습니다.

작업을 수행하여 사용자 질문에 대답하기 위해 Power Virtual Agent는 일련의 항목(주제 영역)을 사용하여 만들어집니다. 대답을 찾을 수 없는 경우 시스템 대체에서 대답을 반환할 수 있습니다.

질문을 항목의 작업 또는 **시스템 대체** 항목 경로의 일부로 기술 자료에 보내도록 에이전트를 구성합니다. 두 경우에서 모두 동일한 작업 메커니즘을 사용하여 기술 자료에 연결하고 대답을 반환합니다.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate에서 GenerateAnswer 작업에 연결

에이전트를 기술 자료에 연결하려면 Power Automate를 사용하여 작업을 만듭니다. Power Automate는 QnA Maker의 GenerateAnswer API에 연결되는 **흐름** 프로세스를 제공합니다.

**흐름**이 설계되어 저장되면 Power Automate **솔루션**에서 사용할 수 있습니다.  GenerateAnswer 흐름이 솔루션에 추가되면 해당 솔루션을 에이전트의 작업으로 사용합니다.

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>에이전트를 기술 자료에 연결하는 프로세스 단계

다음 단계는 Power Virtual Agent를 QnA Maker 기술 자료에 연결하는 목표와 단계의 관계를 이해하는 데 도움이 되는 개요로 제공됩니다.

QnA Maker에서 Power Virtual Agent를 사용하는 단계는 다음과 같습니다.
* [QnA Maker](https://www.qnamaker.ai/) 포털에서
    * 기술 자료 빌드 및 게시
    * 기술 자료 ID, 런타임 엔드포인트 키 및 런타임 엔드포인트 호스트를 포함한 기술 자료 세부 정보 복사
* [Power Virtual Agent](https://powerva.microsoft.com/) 포털에서
    * 에이전트 항목 빌드
    * 작업(Power Automate 흐름에 대한) 호출
* [Power Automate](https://us.flow.microsoft.com/) 포털에서
    * 커넥터를 사용하여 [QnA Maker의 GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)에 흐름 빌드
        * QnA Maker에서 게시한 기술 자료 정보
            * 기술 자료 ID
            * QnA Maker 리소스 엔드포인트 호스트
            * QnA Maker 리소스 엔드포인트 키
        * 입력 - 사용자 쿼리
        * 출력 - 기술 자료 대답
    * 솔루션 만들기 및 흐름 추가
* Power Virtual Agent에 반환
    * 솔루션의 출력을 항목에 대한 메시지로 선택

## <a name="create-and-publish-a-knowledge-base"></a>기술 자료 만들기 및 게시

1. [빠른 시작](../Quickstarts/create-publish-knowledge-base.md)에 따라 기술 자료를 만듭니다. 봇을 만들기 위해 마지막 섹션을 완료하지 마세요. 이 자습서는 빠른 시작의 Bot Framework 봇 대신 Power Virtual Agent를 사용하여 봇을 만들므로 빠른 시작의 마지막 섹션을 대체합니다.

    > [!div class="mx-imgBorder"]
    > ![[QnA Maker](https://www.qnamaker.ai/) 포털의 **설정** 페이지에 있는 게시된 기술 자료 설정을 입력합니다.](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    이 정보는 QnA Maker GenerateAnswer 연결을 구성하는 [Power Automate 단계](#create-power-automate-flow-to-connect-to-your-knowledge-base)에서 필요합니다.

1. QnA Maker 포털의 **설정** 페이지에서 엔드포인트 키, 엔드포인트 호스트 및 기술 자료 ID를 찾습니다.

## <a name="create-power-virtual-agent"></a>Power Virtual Agent 만들기

1. 학교 또는 회사 이메일 계정으로 Power Virtual Agent에 [로그인](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409)합니다.
1. 첫 번째 봇인 경우 에이전트의 **홈** 페이지에 있어야 합니다. 첫 번째 Power Virtual Agent가 아닌 경우 오른쪽 위의 탐색 영역에서 봇을 선택하고 **+ 새 봇**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![[QnA Maker](https://www.qnamaker.ai/) 포털의 **설정** 페이지에 있는 게시된 기술 자료 설정을 입력합니다.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>봇에 제공되는 몇 가지 항목

에이전트는 항목 컬렉션을 사용하여 주제 영역의 질문에 대답합니다. 이 자습서에서 에이전트는 **사용자 항목** 및 **시스템 항목**으로 구분되어 사용자에게 제공되는 많은 항목이 있습니다.

> [!div class="mx-imgBorder"]
> ![에이전트는 항목 컬렉션을 사용하여 주제 영역의 질문에 대답합니다. 이 자습서에서 에이전트는 **사용자 항목** 및 **시스템 항목**으로 구분되어 사용자에게 제공되는 많은 항목이 있습니다.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>Power Virtual Agent의 시스템 대체 항목 만들기

에이전트에서 모든 항목의 기술 자료에 연결할 수 있지만 이 자습서에서는 시스템 **대체** 항목을 사용합니다. 대체 항목은 에이전트에서 대답을 찾을 수 없을 때 사용됩니다. 에이전트는 사용자의 텍스트를 QnA Maker의 GenerateAnswer API에 전달하고, 기술 자료에서 대답을 받고, 이를 사용자에게 메시지로 다시 표시합니다.

1. [Power Virtual Agents](https://powerva.microsoft.com/#/) 포털에서 탐색 영역의 오른쪽 위 모서리에 있는 **설정** 페이지를 선택합니다. 이 페이지의 아이콘은 기어 모양입니다. **시스템 대체**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![시스템 대체에 대한 Power Virtual Agent 메뉴 항목](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. **설정** 팝업 창에서 **+ 추가**를 선택하여 시스템 대체 항목을 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![[설정] 창에서 대체 항목을 추가합니다.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. 항목이 추가되면 **대체 항목으로 이동**을 선택하여 대체 항목을 제작 캔버스에 작성합니다.

    > [!TIP]
    > 대체 항목으로 돌아가야 하는 경우 **항목** 섹션에서  **시스템** 항목의 일부로 사용할 수 있습니다.

## <a name="use-authoring-canvas-to-add-an-action"></a>제작 캔버스를 사용하여 작업 추가

Power Virtual Agents 제작 캔버스를 사용하여 대체 항목을 기술 자료에 연결합니다. 항목은 **인식할 수 없는 사용자 텍스트**로 시작합니다. 해당 텍스트를 QnA Maker에 전달하는 작업을 추가한 다음, 대답을 메시지로 표시합니다. 대답을 표시하는 마지막 단계는 이 자습서의 뒷부분에서 [별도의 단계](#add-solutions-flow-to-power-virtual-agent)로 처리됩니다.

이 섹션에서는 대체 항목 대화 흐름을 만듭니다.

1. 새 대체 작업에는 이미 대화 흐름 요소가 있을 수 있습니다. [옵션] 메뉴를 선택하여 **에스컬레이션** 항목을 삭제합니다.

    > [!div class="mx-imgBorder"]
    > ![트리거 구를 사용하여 대체 작업을 시작합니다.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. **메시지** 상자에서 흐르는 **+** 커넥터를 선택한 다음, **작업 호출**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![작업 호출](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. **흐름 만들기**를 선택합니다. 이 프로세스의 경우 다른 브라우저 기반 포털인 **Power Automate**로 이동합니다.

    > [!div class="mx-imgBorder"]
    > ![작업 호출](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>기술 자료에 연결하는 Power Automate 흐름 만들기

다음 절차에서는 아래 작업을 수행하는 **Power Automate** 흐름을 만듭니다.
* 들어오는 사용자 텍스트 가져오기
* QnA Maker에 보내기
* 변수에 QnA Maker 최상위 대답 할당
* 변수(최상위 대답)를 응답으로 에이전트에 다시 보내기

1. **Power Automate**에서 **흐름 템플릿**이 시작됩니다. **Power Virtual Agents** 흐름 항목에서 **편집**을 선택하여 에이전트에서 기술 자료로 들어오는 입력 변수를 구성합니다. 텍스트 기반 입력 변수는 에이전트에서 사용자가 제출한 텍스트 질문입니다.

    > [!div class="mx-imgBorder"]
    > ![입력 변수를 텍스트 문자열로 구성합니다.](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. 텍스트 입력을 추가하고, `IncomingUserQuestion`의 설명을 사용하여 `InputText` 변수의 이름을 지정합니다. 이름을 이 방법으로 지정하면 입력 텍스트를 나중에 만드는 출력 텍스트와 구별할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![텍스트 입력을 추가하고, 'UserQuestion'의 설명을 사용하여 'InputText' 변수의 이름을 지정합니다.](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. **Power Virtual Agents** 상자에서 이동하는 **+** 커넥터를 선택하여 새 단계를 흐름에 삽입한 다음(**Power Virtual Agent**에 값을 반환하기 전에), **작업 추가**를 선택합니다.

1. `Qna`를 검색하여 **QnA Maker** 작업을 찾은 다음, **대답 생성**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > !['Qna'를 검색하여 **QnA Maker** 작업을 찾은 다음, **대답 생성**을 선택합니다.](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    QnA Maker에 필요한 세 가지 연결 설정이 Power Virtual Agent의 작업 및 질문 설정에 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![QnA Maker에 대한 연결 설정이 작업에 표시됩니다.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. 기술 자료 ID, 엔드포인트 호스트 및 엔드포인트 키를 사용하여 작업을 구성합니다. 이러한 정보는 QnA Maker 포털에서 기술 자료에 대한 **설정**페이지에 있습니다.

    > [!div class="mx-imgBorder"]
    > ![[QnA Maker](https://www.qnamaker.ai/) 포털의 **설정** 페이지에 있는 게시된 기술 자료 설정을 입력합니다.](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. **질문**을 구성하려면 텍스트 상자를 선택한 다음, 목록에서 `InputText`를 선택합니다.

1. 새 단계를 흐름에 삽입하려면 **대답 생성** 작업 상자에서 이동하는 **+** 커넥터를 선택한 다음, **작업 추가**를 선택합니다.

1. GenerateAnswer에서 반환된 대답 텍스트를 캡처하는 변수를 추가하려면 `Initialize variable` 작업을 검색하여 선택합니다.

    변수 이름을 `OutgoingQnAAnswer`로 설정하고, 형식을 **문자열**로 선택합니다. **값**은 설정하지 마세요.

    > [!div class="mx-imgBorder"]
    > ![변수 이름을 'QnAAnswer'로 설정하고, 형식을 **문자열**로 선택합니다.](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. 새 단계를 흐름에 삽입하려면 **변수 초기화** 작업 상자에서 이동하는 **+** 커넥터를 선택한 다음, **작업 추가**를 선택합니다.

1. 전체 기술 자료 JSON 응답을 변수로 설정하려면 `Apply to each` 작업을 검색하여 선택합니다. GenerateAnswer `answers`를 선택합니다.

1. 최상위 대답만 반환하려면 동일한 **각각에 적용** 상자에서 **작업 추가**를 선택합니다. **변수 설정**을 검색하여 선택합니다.

    **변수 설정** 상자에서 **이름** 텍스트 상자를 선택한 다음, 목록에서 **OutgoingQnAAnswer**를 선택합니다.

    **값** 텍스트 상자를 선택한 다음, 목록에서 **Answers Answer**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![변수에 대한 이름과 값을 설정합니다.](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. 변수(및 해당 값)를 반환하려면 **Power Virtual Agent에 값 반환** 흐름 항목을 선택한 다음, **편집**, **출력 추가**를 차례로 선택합니다. **텍스트** 출력 형식을 선택한 다음, `FinalAnswer`의 **제목**을 입력합니다. **값** 텍스트 상자를 선택한 다음, `OutgoingQnAAnswer` 변수를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![반환 값을 설정합니다.](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. **저장**을 선택하여 흐름을 저장합니다.

## <a name="create-solution-and-add-flow"></a>솔루션 만들기 및 흐름 추가

Power Virtual Agent에서 흐름을 찾아서 연결하려면 해당 흐름이 Power Automate 솔루션에 포함되어 있어야 합니다.

1. 여전히 Power Automate 포털에 있는 동안 왼쪽 탐색 영역에서 **솔루션**을 선택합니다.

1. **+ 새 솔루션**을 선택합니다.

1. 표시 이름을 입력합니다. 솔루션 목록에는 조직 또는 학교의 모든 솔루션이 포함됩니다. 이메일을 접두사로 솔루션 이름(`jondoe-power-virtual-agent-qnamaker-fallback`)에 사용하는 것처럼 솔루션만 필터링하는 데 도움이 되는 명명 규칙을 선택합니다.

1. 선택 목록에서 게시자를 선택합니다.

1. 이름 및 버전에 대한 기본값을 적용합니다.

1. **만들기**를 선택하여 프로세스를 완료합니다.

## <a name="add-flow-to-solution"></a>솔루션에 흐름 추가

1. 솔루션 목록에서 방금 만든 솔루션을 선택합니다. 목록의 위쪽에 있습니다. 그렇지 않은 경우 솔루션 이름의 일부인 이메일 이름으로 검색합니다.

1. 솔루션에서 **+ 기존 항목 추가**를 선택한 다음, 목록에서 **흐름**을 선택합니다.

1. 흐름을 찾은 다음, **추가**를 선택하여 프로세스를 완료합니다. 흐름이 많은 경우 **수정됨** 열을 보고 가장 최근의 흐름을 찾습니다.

## <a name="add-solutions-flow-to-power-virtual-agent"></a>Power Virtual Agent에 솔루션의 흐름 추가

1. Power Virtual Agent를 사용하여 브라우저 탭으로 돌아갑니다. 제작 캔버스는 계속 열려 있어야 합니다.

1. **메시지** 작업 상자 아래에서 **+** 커넥터를 선택하여 새 단계를 흐름에 삽입한 다음, **작업 호출**을 선택합니다.

1. 새 작업에서 **UnrecognizedTriggerPhrase**의 입력 값을 선택합니다. 이렇게 하면 텍스트가 에이전트에서 흐름으로 전달됩니다.

    > [!div class="mx-imgBorder"]
    > ![새 작업에서 **UnrecognizedTriggerPhrase**의 입력 값을 선택합니다.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. **작업** 상자 아래에서 **+** 커넥터를 선택하여 새 단계를 흐름에 삽입한 다음, **메시지 표시**를 선택합니다.

1. 메시지 텍스트(`Your answer is:`)를 입력하고, 내부 도구 모음의 함수를 사용하여 `FinalAnswer`를 컨텍스트 변수로 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Power Automate 흐름에서 메시지 텍스트와 'FinalAnswer'를 입력합니다.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. 상황에 맞는 도구 모음에서 **저장**을 선택하여 항목에 대한 제작 캔버스 세부 정보를 저장합니다.

최종 캔버스는 아래와 같습니다.

> [!div class="mx-imgBorder"]
> ![최종 에이전트 캔버스](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Power Virtual Agent 테스트

1. 테스트 창에서 **항목 간 추적**을 전환합니다. 이렇게 하면 단일 항목 외에도 항목 간의 진행 상황을 볼 수 있습니다.

1. 아래에 제공된 순서대로 사용자 텍스트를 입력하여 에이전트를 테스트합니다. 제작 캔버스에서 녹색 확인 표시를 사용하여 성공한 단계를 보고합니다.

|질문 순서|테스트 질문|목적|
|--|--|--|
|1|안녕하세요.|대화 시작|
|2|시간 저장|샘플 항목 - 사용자 쪽에 대한 추가 작업 없이 구성됩니다.|
|3|예|`Did that answer your question?`에 대한 회신에서|
|4|최고|`Please rate your experience.`에 대한 회신에서|
|5|예|`Can I help with anything else?`에 대한 회신에서|
|6|기술 자료란?|이 질문은 대답하기 위해 텍스트를 기술 자료에 보낸 다음, 대답이 표시되는 대체 작업을 트리거합니다. |

> [!div class="mx-imgBorder"]
> ![최종 에이전트 캔버스](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>봇 게시

학교 또는 조직의 모든 구성원이 에이전트를 사용할 수 있도록 하려면 해당 에이전트를 게시해야 합니다.

1. 왼쪽 탐색 영역에서 **게시**를 선택한 다음, 페이지에서 **게시**를 선택합니다.

1. **게시** 단추 아래에 링크로 제공된 데모 웹 사이트에서 봇을 사용해 봅니다.

    봇이 있는 새 웹 페이지가 열립니다. 봇에 동일한 테스트 질문(`What is a knowledge base?`)을 합니다.

    > [!div class="mx-imgBorder"]
    > ![최종 에이전트 캔버스](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>봇 공유

데모 웹 사이트를 공유하려면 해당 웹 사이트를 채널로 구성합니다.

1. 왼쪽 탐색 영역에서 **관리**, **채널**을 차례로 선택합니다.

1. 채널 목록에서 **데모 웹 사이트**를 선택합니다.

1. 링크를 복사하고 **저장**을 선택합니다. 데모 웹 사이트에 대한 링크를 학교 또는 조직 구성원에게 보내는 이메일에 붙여넣습니다.

## <a name="clean-up-resources"></a>리소스 정리

기술 자료 작업이 완료되면 Azure Portal에서 QnA Maker 리소스를 제거합니다.

## <a name="next-step"></a>다음 단계

[기술 자료에 대한 분석 가져오기](../How-To/get-analytics-knowledge-base.md)

다음에 대해 자세히 알아봅니다.
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker 커넥터](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) 및 [커넥터 설정](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)