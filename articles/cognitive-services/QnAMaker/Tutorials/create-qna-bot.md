---
title: QnA 봇 - Azure Bot Service - QnA Maker
titleSuffix: Azure Cognitive Services
description: 기존 기술 자료의 게시 페이지에서 QnA 채팅 봇을 만듭니다. 이 봇 Bot Framework SDK v4를 사용합니다. 봇 빌드 위해 어떠한 코드도 작성할 필요가 없습니다, 모든 코드를 제공 됩니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.openlocfilehash: 85b0004288a06a834b61f6e3d50017d35d66ce86
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59263879"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>자습서: Azure를 사용 하 여 QnA Bot 만들기 Bot 서비스 v4

QnA 채팅 봇 만들기의 **게시** 기존 기술 자료에 대 한 페이지입니다. 이 봇 Bot Framework SDK v4를 사용합니다. 봇 빌드 위해 어떠한 코드도 작성할 필요가 없습니다, 모든 코드를 제공 됩니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Azure Bot Service는 기존 기술 자료에서 만들기
> * 봇과 채팅하면서 코드가 작동 중인지 확인 

## <a name="prerequisites"></a>필수 조건

이 자습서에 대해 게시된 기술 자료가 있어야 합니다. 단계에 따라 하나 없으면 [만들고 기술 자료에서 답변](create-publish-query-in-portal.md) 질문 및 답변을 사용 하 여 QnA Maker 기술 자료를 만드는 자습서입니다.

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>QnA 봇 만들기

봇 기술 자료에 대 한 클라이언트 응용 프로그램을 만듭니다. 

1. QnA Maker 포털에서로 이동 합니다 **게시** 페이지 및 기술 자료를 게시 합니다. 선택 **봇 만들기**합니다. 

    ![QnA Maker 포털에서 [게시] 페이지로 이동하여 기술 자료를 게시합니다. [봇 만들기]를 선택합니다.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    봇 만들기 구성을 사용 하 여 Azure portal이 열립니다.

1.  봇 만들기에 대 한 설정을 입력 합니다.

    |설정|값|목적|
    |--|--|--|
    |봇 이름|`my-tutorial-kb-bot`|봇에 대 한 Azure 리소스 이름입니다.|
    |구독|용도 참조 하세요.|QnA Maker 리소스를 만드는 데 사용한 것과 동일한 구독을 선택 합니다.|
    |리소스 그룹|`my-tutorial-rg`|봇와 관련 된 모든 Azure 리소스에 사용 되는 리소스 그룹입니다.|
    |Location|`west us`|봇의 Azure 리소스 위치입니다.|
    |가격 책정 계층 |`F0`|Azure bot service에 대 한 무료 계층입니다.|
    |앱 이름|`my-tutorial-kb-bot-app`|봇을을 지원 하도록 웹 앱입니다. QnA Maker 서비스 이미 사용 하는 동일한 앱 이름을 하지 해야 수 있습니다. 다른 리소스를 사용 하 여 QnA Maker의 웹 앱을 공유 하는 것은 지원 되지 않습니다.|
    |SDK 언어|C#|Bot framework SDK 사용 하는 기본 프로그래밍 언어입니다. 중에서 선택 C# 또는 Node.js.|
    |QnA 인증 기|**변경 하지 마세요**|이 값은 채워집니다.|
    |앱 서비스 플랜/위치|**변경 하지 마세요**|이 자습서에서는 위치 중요 하지 않습니다.|
    |Azure Storage|**변경 하지 마세요**|대화 데이터는 Azure Storage 테이블에 저장 됩니다.|
    |Application Insights|**변경 하지 마세요**|로깅은 Application Insights로 전송 됩니다.|
    |Microsoft 앱 ID|**변경 하지 마세요**|Active directory 사용자 및 암호가 필요 합니다.|

    ![이러한 설정을 사용 하 여 기술 자료 봇을 만듭니다.](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    몇 분 봇 만들기 프로세스 알림 성공을 보고할 때까지 기다립니다.

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>봇과 채팅

1. Azure portal에서 알림에서 새 봇 리소스를 엽니다. 

    ![Azure portal에서 알림에서 새 봇 리소스를 엽니다.](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. **Bot 관리**를 선택 **테스트 웹 채팅에** 입력: `How large can my KB be?`합니다. 봇에 사용 하 여 응답 합니다. 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![새 기술 자료 bot을 테스트 합니다.](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    Azure Bot에 대 한 자세한 내용은 참조 하세요. [응답할 QnA Maker를 사용 하 여 질문](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>QnA Maker 봇을 관련

* QnA Maker 포털에 사용 되는 QnA Maker 도움말 봇, 수를 [bot 샘플](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot)합니다.
    ![QnA Maker 도움말 봇 아이콘은 빨간색 로봇](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [의료 분쟁 야 기자가](https://docs.microsoft.com/HealthBot/qna_model_howto) 중 하나로 QnA Maker를 사용 하 여 해당 [언어 모델](https://docs.microsoft.com/HealthBot/qna_model_howto)합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 봇 작업이 완료되면 Azure Portal에서 해당 봇을 제거합니다. 

봇의 리소스에 대 한 새 리소스 그룹을 만든 경우 리소스 그룹을 삭제 합니다. 

새 리소스 그룹을 만들지 않은 경우에 봇과 연결 된 리소스를 찾을 해야 합니다. 가장 쉬운 방법은 봇 및 봇 앱의 이름으로 검색 됩니다. 봇 리소스에는 다음이 포함 됩니다.

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
