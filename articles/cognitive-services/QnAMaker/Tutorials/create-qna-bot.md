---
title: Azure Bot Service로 QnA 봇 만들기 - Azure Cognitive Services | Microsoft Docs
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: f2721dfa9a0922ee4a5af1eb66fd4506feb94d28
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35383142"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>Azure Bot Service로 QnA 봇 만들기
이 자습서에서는 Azure Portal에서 Azure Bot Service를 사용하여 QnA 봇을 빌드하는 과정을 안내합니다.

## <a name="prerequisite"></a>필수 요소
빌드하기 전에 [기술 자료 만들기]()의 단계에 따라 질문과 답변이 있는 QnA Maker 서비스를 만듭니다.

봇은 QnAMakerDialog를 통해 사용자가 만든 기술 자료의 질문에 응답합니다.

## <a name="create-a-qna-bot"></a>QnA 봇 만들기
1. [Azure Portal](https://portal.azure.com)의 메뉴 블레이드에서 새 리소스 **만들기**를 선택한 다음, **모두 표시**를 선택합니다.

    ![봇 서비스 만들기](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 검색 상자에서 **웹앱 봇**을 검색합니다.

    ![봇 서비스 선택](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. **봇 서비스 블레이드**에서 필요한 정보를 제공하고 **만들기**를 선택합니다. 이렇게 하면 봇 서비스가 만들어지고 QnAMakerDialog를 통해 Azure에 배포됩니다.

    - **앱 이름**을 봇 이름으로 설정합니다. 이름은 봇이 클라우드에 배포될 때 하위 도메인으로 사용됩니다(예: mynotesbot.azurewebsites.net).
    - 구독, 리소스 그룹, App Service 계획 및 위치를 선택합니다.
    - 봇 템플릿 필드에 대해 **질문 및 답변**(Node.js 또는 C#) 템플릿을 선택합니다.
    - 법적 고지 사항에 대한 확인 확인란을 선택합니다. 법적 고지 사항의 조건은 확인란 아래에 있습니다.

        ![봇 서비스 선택](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. 봇 서비스가 배포되었는지 확인합니다.

    - **알림**(Azure Portal의 위쪽 가장자리에 있는 벨 아이콘)을 선택합니다. 알림이 **배포가 시작됨**에서 **배포 성공**으로 변경됩니다.
    - 알림이 **배포 성공**으로 변경된 후 해당 알림에서 **리소스로 이동**을 선택합니다.

## <a name="chat-with-the-bot"></a>봇과 채팅
**리소스로 이동**을 선택하면 봇의 리소스 블레이드로 이동합니다.

봇이 등록되면 **웹 채팅에서 테스트**를 클릭하여 [웹 채팅] 창을 엽니다. 웹 채팅에 “hello”를 입력합니다.

![QnA 봇 웹 채팅](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

봇에서 “앱 설정에서 QnAKnowledgebaseId 및 QnASubscriptionKey를 설정하세요. https://aka.ms/qnaabssetup에서 가져오는 방법을 알아보세요.”라는 응답이 표시됩니다. 이 응답은 QnA 봇이 메시지를 수신했지만 아직 이와 연결된 QnA Maker 기술 자료가 없음을 확인합니다. 다음 단계에서 연결하면 됩니다.

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>QnA Maker 기술 자료를 봇에 연결

1. **응용 프로그램 설정**을 열고 **QnAKnowledgebaseId**, **QnAAuthKey** 및 **QnAEndpointHostName** 필드를 편집하여 QnA Maker 기술 자료의 값을 포함합니다.

    ![앱 설정](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. https://qnamaker.ai에 있는 기술 자료의 [설정] 탭에서 기술 자료 ID, 호스트 URL 및 끝점 키를 가져옵니다.
    - [QnA Maker](https://qnamaker.ai)에 로그인
    - 기술 자료로 이동
    - **설정** 탭 클릭
    - 아직 게시하지 않은 경우 기술 자료 **게시**

    ![QnA Maker 값](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> 기술 자료의 미리 보기 버전을 QnA 봇에 연결하려면 **Ocp-Apim-Subscription-Key**의 값을 **QnAAuthKey**로 설정합니다. **QnAEndpointHostName**을 비워 둡니다.

## <a name="test-the-bot"></a>봇 테스트
Azure Portal에서 **웹 채팅에서 테스트**를 클릭하여 봇을 테스트합니다. 

![QnA Maker 봇](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

이제 QnA 봇이 기술 자료를 기반으로 응답합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker 및 LUIS 통합](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>참고 항목

- [Manage your knowledge base](https://qnamaker.ai)(기술 자료 관리)
- [Enable your bot in different channels](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)(다양한 채널에서 봇 사용)
