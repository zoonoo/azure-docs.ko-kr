---
title: QnA 봇 - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Azure Portal에서 Azure Bot Service v3를 사용하여 QnA 봇을 빌드하는 과정을 안내합니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: qna-maker`
ms.topic: article
ms.date: 10/25/2018
ms.author: tulasim
ms.openlocfilehash: f5587e14a0250e7312f1c95598b481bd052931c3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094834"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>자습서: Azure Bot Service v3로 QnA 봇 만들기

이 자습서에서는 코드를 작성하지 않고 [Azure Portal](https://portal.azure.com)에서 Azure Bot Service v3를 사용하여 QnA 봇을 빌드하는 과정을 안내합니다. 게시된 KB(기술 자료)를 봇에 연결하는 것은 봇 응용 프로그램 설정을 변경하는 것만큼 간단합니다. 

> [!Note] 
> 이 항목은 Bot SDK 버전 3에 대한 내용을 다룹니다. 버전 4는 [여기](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)에서 찾을 수 있습니다. 

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * QnA Maker 템플릿을 사용하여 Azure Bot Service 만들기
> * 봇과 채팅하면서 코드가 작동 중인지 확인 
> * 게시된 KB를 봇에 연결
> * 문제가 있는 봇 테스트

이 문서에서는 무료 QnA Maker [서비스](../how-to/set-up-qnamaker-service-azure.md)를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에 대해 게시된 기술 자료가 있어야 합니다. 이러한 기술 자료가 없으면 [기술 자료 만들기](../How-To/create-knowledge-base.md)의 단계에 따라 질문과 답변이 있는 QnA Maker 서비스를 만듭니다.

## <a name="create-a-qna-bot"></a>QnA 봇 만들기

1. Azure Portal에서 **리소스 만들기**를 선택합니다.

    ![봇 서비스 만들기](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 검색 상자에서 **웹앱 봇**을 검색합니다.

    ![봇 서비스 선택](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. **Bot Service**에서 필수 정보를 제공합니다.

    - **앱 이름**을 봇 이름으로 설정합니다. 이름은 봇이 클라우드에 배포될 때 하위 도메인으로 사용됩니다(예: mynotesbot.azurewebsites.net).
    - 구독, 리소스 그룹, App Service 계획 및 위치를 선택합니다.

4. v3 템플릿을 사용하려면 **SDK v3**의 SDK 버전과 **C#** 또는 **Node.js**의 SDK 언어를 선택하세요.

    ![봇 sdk 설정](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. 봇 템플릿 필드에 대한 **질문 및 답변**을 선택한 다음, **선택**을 선택하여 템플릿 설정을 저장합니다.

    ![Bot 서비스 템플릿 선택 저장](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. 설정을 검토한 다음, **만들기**를 선택합니다. 이렇게 하면 봇 서비스가 만들어지고 Azure에 배포됩니다.

    ![봇 만들기](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. Bot Service가 배포되었는지 확인합니다.

    - **알림**(Azure Portal의 위쪽 가장자리에 있는 벨 아이콘)을 선택합니다. 알림이 **배포가 시작됨**에서 **배포 성공**으로 변경됩니다.
    - 알림이 **배포 성공**으로 변경된 후 해당 알림에서 **리소스로 이동**을 선택합니다.

## <a name="chat-with-the-bot"></a>봇과 채팅

**리소스로 이동**을 선택하면 봇의 리소스로 이동됩니다.

**웹 채팅에서 테스트**를 선택하여 웹 채팅 창을 엽니다. 웹 채팅에서 "hi"를 입력합니다.

![QnA 봇 웹 채팅](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

봇에서 “앱 설정에서 QnAKnowledgebaseId 및 QnASubscriptionKey를 설정하세요. 이 응답은 QnA 봇이 메시지를 수신했지만 아직 이와 연결된 QnA Maker 기술 자료가 없음을 확인합니다. 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>QnA Maker 기술 자료를 봇에 연결

1. **응용 프로그램 설정**을 열고 **QnAKnowledgebaseId**, **QnAAuthKey** 및 **QnAEndpointHostName** 필드를 편집하여 QnA Maker 기술 자료의 값을 포함합니다.

    ![앱 설정](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. QnA Maker 포털에 있는 기술 자료의 설정 탭에서 기술 자료 ID, 호스트 URL 및 엔드포인트 키를 가져옵니다.

    - [QnA Maker](https://qnamaker.ai)에 로그인
    - 기술 자료로 이동
    - **설정** 탭을 선택합니다.
    - 아직 게시하지 않은 경우 기술 자료 **게시**

    ![QnA Maker 값](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> 기술 자료의 미리 보기 버전을 QnA 봇에 연결하려면 **Ocp-Apim-Subscription-Key**의 값을 **QnAAuthKey**로 설정합니다. **QnAEndpointHostName**을 비워 둡니다.

## <a name="test-the-bot"></a>봇 테스트

Azure Portal에서 **웹 채팅에서 테스트**를 선택하여 봇을 테스트합니다. 

![QnA Maker 봇](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

QnA 봇이 기술 자료를 기준으로 응답합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 봇 작업이 완료되면 Azure Portal에서 해당 봇을 제거합니다. 봇 서비스는 다음을 포함합니다.

* App Service 플랜
* 검색 서비스
* Cognitive Service
* App service
* 필요에 따라 Application Insights 서비스 및 Application Insights 데이터용 저장소를 포함할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [개념: 기술 자료](../concepts/knowledge-base.md)

## <a name="see-also"></a>참고 항목

- [Manage your knowledge base](https://qnamaker.ai)(기술 자료 관리)
- [Enable your bot in different channels](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)(다양한 채널에서 봇 사용)
