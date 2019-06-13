---
title: Node.js를 사용하여 Conversation Learner 모델을 만드는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Node.js를 사용하여 Conversation Learner 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: cc071d59a387c8ae4982eacbce6812526f447788
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388777"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Node.js를 사용하여 Conversation Learner 모델 만들기

Conversation Learner는 봇 빌드의 복잡성을 줄여줍니다. 직접 작성한 코드 및 기계 학습을 통해 봇 작성에 필요한 코드 양을 줄이도록 하는 하이브리드 개발 워크플로를 허용합니다. 모델의 고정된 특정 부분(예: 사용자가 로그인되어 있는지 확인 또는 저장소 재고를 확인하기 위한 API 요청 수행)은 여전히 코딩할 수 있습니다. 그러나 도메인 전문가나 개발자가 제공한 예제 대화 상자에서 기타 상태 변경 및 작업 선택 항목을 학습할 수 있습니다.

## <a name="invitation-required"></a>초대 필요

*Project Conversation Learner에 액세스하려면 초대가 필요합니다.*

Project Conversation Learner는 봇에 추가하는 SDK와 SDK가 기계 학습을 위해 액세스하는 클라우드 서비스로 구성됩니다.  현재, Project Conversation Learner 클라우드 서비스에 액세스하려면 초대가 필요합니다.  아직 초대하지 않은 경우 [초대를 요청](https://aka.ms/conversation-learner-request-invite)합니다.  초대를 받지 못한 경우 클라우드 API에 액세스할 수 없습니다.

## <a name="prerequisites"></a>필수 조건

- Node 8.5.0 이상 및 npm 5.3.0 이상. [https://nodejs.org](https://nodejs.org)에서 설치합니다.
  
- LUIS 작성 키:

  1. [https://www.luis.ai](https://www.luis.ai)에 로그인합니다.

  2. 오른쪽 위에 있는 이름을 클릭하고 "설정"을 클릭합니다.

  3. 작성 키가 결과 페이지에 표시됩니다.

  (LUIS 작성 키는 2가지 역할을 제공합니다.  첫째, Conversation Learner 작성 키의 역할을 합니다.  둘째, Conversation Learner는 엔터티 추출을 위해 LUIS를 사용합니다. LUIS 작성 키는 사용자 대신 LUIS 모델을 만드는 데 사용됩니다.)

- Google Chrome 웹 브라우저. [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html)에서 설치합니다.

- git. [https://git-scm.com/downloads](https://git-scm.com/downloads)에서 설치합니다.

- VSCode. [https://code.visualstudio.com/](https://code.visualstudio.com/)에서 설치합니다. 필수는 아니지만 권장됩니다.

## <a name="quick-start"></a>빠른 시작 

1. 다음과 같이 설치한 후 빌드합니다.

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > `npm install` 동안 `gyp ERR! stack Error: Can't find Python executable` 오류가 발생하는 경우 무시해도 됩니다.

2. 다음과 같이 구성합니다.

   `cl-bot-01` 디렉터리에 `.env`라는 파일을 만듭니다.  파일의 내용은 다음과 같습니다.

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. 다음과 같이 봇을 시작합니다.

    ```
    npm start
    ```

    이렇게 하면 `cl-bot-01/src/app.ts`의 비어 있는 일반 봇이 실행됩니다.

3. 브라우저에서 `http://localhost:3978`을 엽니다.

이제 Conversation Learner를 사용하고 있으며 Conversation Learner 모델을 만들고 학습할 수 있습니다.  

> [!NOTE]
> 시작 시, 초대를 통해 Project Conversation Learner를 사용할 수 있습니다.  `http://localhost:3978/ui`에 HTTP `403` 오류가 표시되면 계정이 초대를 받지 못한 것입니다.  [초대를 요청](https://aka.ms/conversation-learner-request-invite)하세요.

## <a name="tutorials-demos-and-switching-between-bots"></a>자습서, 데모 및 봇 간 전환

위의 지침에서는 비어 있는 일반 봇을 시작했습니다.  대신, 자습서나 데모 봇을 실행하려면

1. Conversation Learner 웹 UI가 열려 있는 경우 `http://localhost:3978/ui/home`의 모델 목록으로 돌아갑니다.
    
2. 다른 봇이 실행되고 있는 경우(예: `npm start` 또는 `npm run demo-pizza`) 중지합니다.  UI 프로세스를 중지하거나 웹 브라우저를 닫을 필요는 없습니다.

3. 명령줄에서 데모 봇을 실행합니다(위의 2단계).  데모에는 다음이 포함됩니다.

   ```bash
   npm run tutorial-general
   npm run tutorial-entity-detection
   npm run tutorial-session-callbacks
   npm run tutorial-api-calls
   npm run tutorial-hybrid
   npm run demo-password
   npm run demo-pizza
   npm run demo-storage
   ```

4. Chrome에서 Conversation Learner 웹 UI로 아직 전환하지 않은 경우 `http://localhost:3978/ui/home`을 로드하여 전환합니다. 

5. “자습서 가져오기”를 클릭하고 Conversation Learner UI에서 시작한 데모에 해당하는 데모 모델을 선택합니다.

데모의 원본 파일은 `cl-bot-01/src/demos`에 있습니다.

## <a name="create-a-bot-which-includes-back-end-code"></a>백 엔드 코드를 포함하는 봇 만들기

1. Conversation Learner 웹 UI가 열려 있는 경우 `http://localhost:3978/ui/home`의 모델 목록으로 돌아갑니다.
    
2. 봇이 실행 중인 경우(예: `npm run demo-pizza`) 중지합니다.  UI 프로세스를 중지하거나 웹 브라우저를 닫을 필요는 없습니다.

3. 원하는 경우 `cl-bot-01/src/app.ts`에서 코드를 편집합니다.

4. 다음과 같이 봇을 다시 빌드한 후 다시 시작합니다.

    ```bash    
    npm run build
    npm start
    ```

5. Chrome에서 Conversation Learner 웹 UI로 아직 전환하지 않은 경우 `http://localhost:3978/ui/home`을 로드하여 전환합니다. 

6. Ui에서 새 Conversation Learner 모델을 만들고 학습을 시작합니다.

7. `cl-bot-01/src/app.ts`에서 코드를 변경하려면 2단계부터 시작해서 위 단계를 반복합니다.

## <a name="vscode"></a>VSCode

VSCode에는 각 데모 및 `cl-bot-01/src/app.ts`의 "빈 봇"에 대한 실행 구성이 있습니다.  VSCode에서 `cl-bot-01` 폴더를 엽니다.

## <a name="advanced-configuration"></a>고급 구성

샘플을 구성하기 위해 설정할 수 있는 환경 변수를 표시하는 템플릿 `.env.example` 파일이 있습니다.

`.env` 파일을 프로젝트의 루트에 추가하여 컴퓨터에서 실행 중인 다른 서비스 간 충돌을 피하도록 이러한 포트를 조정할 수 있습니다.

```bash
cp .env.example .env
```

이렇게 하면 봇을 로컬로 실행하고 Conversation Learner 사용을 시작할 수 있도록 하는 표준 구성이 사용됩니다.  (나중에 Bot Framework로 봇을 배포하려면 이 파일을 일부 편집해야 합니다.)

## <a name="support"></a>지원

- [Stack Overflow](https://stackoverflow.com)에 대한 질문에 "microsoft cognitive" 태그 지정
- [사용자 의견 페이지](https://aka.ms/conversation-learner-uservoice)에서 기능 요청
- [GitHub 리포지토리](https://github.com/Microsoft/ConversationLearner-Samples)에서 문제 열기

## <a name="contributing"></a>참여

이 프로젝트에는 [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/)(Microsoft 오픈 소스 준수 사항)이 적용됩니다. 자세한 내용은 [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/)(준수 사항 FAQ)를 참조하거나 [opencode@microsoft.com](mailto:opencode@microsoft.com)에 추가 질문 또는 의견을 알려주세요.

## <a name="source-repositories"></a>소스 리포지토리

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Hello world](./tutorials/01-hello-world.md)
