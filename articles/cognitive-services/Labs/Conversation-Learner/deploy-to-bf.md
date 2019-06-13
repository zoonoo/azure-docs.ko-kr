---
title: Conversation Learner 봇을 배포하는 방법 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 봇을 배포하는 방법을 알아봅니다.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 5522f762f3893f1d67cd3755b1e022f0118cc004
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385312"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Conversation Learner 봇을 배포하는 방법

이 문서에서는 로컬 또는 Azure에 Conversation Learner 봇을 배포하는 방법을 설명합니다.

## <a name="prerequisite-determine-the-model-id"></a>필수 조건: 모델 ID 확인 

Conversation Learner UI 외부에서 봇을 실행하려면 봇에서 사용할 Conversation Learner 모델 ID(예: Conversation Learner 클라우드의 Machine Learning 모델 ID)를 설정해야 합니다.  반면, Conversation Learner UI를 통해 봇을 실행하는 경우 UI가 모델 ID를 선택합니다.  

다음은 모델 ID를 가져오는 방법입니다.

1. 봇과 Conversation Learner UI를 시작합니다.  전체 지침은 빠른 시작 가이드를 참조하세요. 요약하면 다음과 같습니다.

    명령 창에서 다음을 실행합니다.

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    다른 명령 창에서 다음을 실행합니다.

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. 브라우저에서 `http://localhost:5050`을 엽니다. 

3. ID를 가져올 Conversation Learner 모델을 클릭합니다.

4. 왼쪽의 탐색 모음에서 “설정”을 클릭합니다.

5. “모델 ID” GUID가 페이지 맨 위에 표시됩니다.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>옵션 1: 로컬에서 실행할 Conversation Learner 봇 배포

이 옵션은 로컬 컴퓨터에 봇을 배포하고 Bot Framework 에뮬레이터를 사용하여 봇에 액세스하는 방법을 보여 줍니다.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Conversation Learner UI 외부에서 액세스할 수 있도록 봇 구성

로컬에서 봇을 실행하는 경우 애플리케이션 ID를 봇의 `.env` 파일에 추가합니다.

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

그런 다음, 봇을 시작합니다.

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

이제 봇이 로컬에서 실행되고 있습니다.  Bot Framework 에뮬레이터를 사용하여 봇에 액세스할 수 있습니다.

### <a name="download-and-install-the-emulator"></a>에뮬레이터 다운로드 및 설치

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>봇에 에뮬레이터 연결

1. 에뮬레이터 왼쪽 위에 있는 “엔드포인트 URL 입력” 상자에 `http://127.0.0.1:3978/api/messages`를 입력합니다.  다른 필드는 비워 두고 “연결”을 클릭합니다.

2. 이제 봇과 대화하고 있습니다.

## <a name="option-2-deploy-to-azure"></a>옵션 2: Deploy to Azure

다른 봇을 게시하는 것과 동일한 방식으로 Conversation Learner 봇을 게시합니다. 상위 수준에서 코드를 호스트된 웹 사이트에 업로드하고, 적절한 구성 값을 설정한 다음, 다양한 채널에 봇을 등록합니다. 이 비디오에는 Azure Bot Service를 사용하여 봇을 게시하는 방법을 보여 주는 자세한 지침이 있습니다.

봇이 배포 되 고 실행에 연결할 수 있습니다 다른 채널 Facebook, Teams, Skype 등과 같은 합니다. Azure 봇 채널 등록을 사용합니다. 해당 프로세스에 대한 문서는 다음을 참조하세요. https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

다음은 Azure에 Conversation Learner 봇을 배포하기 위한 단계별 지침입니다.  이러한 지침에서는 Azure DevOps Services, GitHub, BitBucket 또는 OneDrive와 같은 클라우드 기반 원본에서 봇 원본을 사용할 수 있다고 가정하고 지속적인 배포에 맞게 봇을 구성합니다.

1. https://portal.azure.com에서 Azure Portal에 로그인합니다.

2. 새로운 “웹앱 봇” 리소스를 만듭니다. 

    1. 봇에 이름을 지정합니다.
    2. “봇 템플릿”을 클릭하고 “Node.js”, “기본”을 차례로 선택한 다음, “선택” 단추를 클릭합니다.
    3. “만들기”를 클릭하여 웹앱 봇을 만듭니다.
    4. 웹앱 봇 리소스가 생성될 때까지 기다립니다.

3. Azure Portal에서 방금 만든 웹앱 봇 리소스를 편집합니다.

   1. 왼쪽에서 “애플리케이션 설정” 탐색 항목을 클릭합니다.
   1. “앱 설정” 섹션까지 아래로 스크롤합니다.
   2. 다음 설정을 추가합니다.

       환경 변수 | 값
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | Conversation Learner UI에서 모델 “설정” 아래에 있는 애플리케이션 ID GUID
       LUIS_AUTHORING_KEY               | 이 모델에 대한 LUIS 작성 키
       LUIS_SUBSCRIPTION_KEY            | 필수 변수는 아니지만 작성 할당량이 소진되지 않도록 하려면 게시된 봇에 사용하는 것이 좋습니다.
    
   4. 페이지 맨 위에서 “저장”을 클릭합니다.
   5. 왼쪽에서 “빌드” 탐색 항목을 엽니다.
   6. “지속적인 배포 구성”을 클릭합니다. 
   7. 배포 아래에서 “설정” 아이콘을 클릭합니다.
   8. “필수 설정”을 클릭합니다.
   9. 봇 코드를 사용할 수 있는 소스를 선택하고 소스를 구성합니다.
