---
title: 기술 자료 만들기, 교육, 게시 - QnA Maker
titleSuffix: Azure Cognitive Services
description: FAQ 또는 제품 설명서 등, 사용자 고유의 콘텐츠에서 QnA Maker 기술 자료(KB)를 만들 수 있습니다. 이 예제의 QnA Maker 기술 자료는 BitLocker 키 복구에 대한 질문에 답변하기 위해 단순한 FAQ 웹 페이지에서 작성되었습니다.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: cf6b0cd99254d5b034687598c588ce5ef13dbd53
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59272855"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기, 학습 및 게시

FAQ 또는 제품 설명서 등, 사용자 고유의 콘텐츠에서 QnA Maker 기술 자료(KB)를 만들 수 있습니다. 이 예제의 QnA Maker 기술 자료는 BitLocker 키 복구에 대한 질문에 답변하기 위해 단순한 FAQ 웹 페이지에서 작성되었습니다.

## <a name="prerequisite"></a>필수 요소

> [!div class="checklist"]
> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기

1. Azure 자격 증명으로 [QnAMaker.ai](https://QnAMaker.ai) 포털에 로그인합니다.

1. QnA Maker 포털에서 **기술 자료 만들기**를 선택합니다.

   ![새 기술 자료 만들기](../media/qna-maker-create-kb.png)

1. 1단계 - **만들기** 페이지에서 **QnA 서비스 만들기**를 선택합니다. 그러면 구독에서 QnA Maker 서비스를 설정할 수 있는 [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)로 연결됩니다. Azure Portal 시간이 초과되면 사이트에서 **다시 시도**를 선택합니다. 연결한 후에 Azure 대시보드가 나타납니다.

1. Azure에서 새 QnA Maker 서비스를 성공적으로 만든 후 qnamaker.ai/create로 돌아갑니다. 2단계 - 드롭다운 목록에서 QnA 서비스를 선택합니다. 새 QnA 서비스를 만든 경우에는 페이지를 반드시 새로 고침해야 합니다.

   ![QnA 서비스 기술 자료 선택](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. 3단계에서는 기술 자료의 이름을 **내 샘플 QnA KB**로 지정합니다.

1. 기술 자료에 콘텐츠를 추가하려면 세 가지 유형의 데이터 원본을 선택합니다. 4단계 - **KB 입력**의 **URL** 상자에 [BitLocker 복구 FAQ](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL을 추가합니다.

   ![QnA 서비스 기술 자료 선택](../media/qnamaker-quickstart-kb/add-datasources.png)

1. 5단계 - **KB 만들기**를 선택합니다.

1. 기술 자료를 만드는 동안 팝업 창이 나타납니다. 추출 프로세스는 HTML 페이지를 읽고 질문 및 답변을 확인하려면 몇 분 정도 걸립니다.

1. 기술 자료가 성공적으로 만들어지면 **기술 자료** 페이지가 열립니다. 이 페이지에서 기술 자료의 내용을 편집할 수 있습니다.

## <a name="edit-the-knowledge-base"></a>기술 자료 편집

1. QnA Maker 포털의 **편집** 섹션에서 **QnA 쌍을 추가**하여 기술 자료에 새 행을 추가합니다. **질문**에 **Hi**를 입력합니다. **답변**에 **Hello. Ask me BitLocker questions.** 를 입력합니다.

    ![QnA 쌍 추가](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. 오른쪽 위에서 **저장 후 학습**을 선택하여 편집 내용을 저장하고 QnA Maker 모델을 학습합니다. 저장하지 않으면 편집 내용은 보관되지 않습니다.

## <a name="test-the-knowledge-base"></a>기술 자료 테스트

1. QnA Maker 포털의 오른쪽 위에서 **테스트**를 선택하여 변경 내용이 적용되었는지 테스트합니다. 상자에 `hi there`를 입력하고 Enter 키를 선택합니다. 사용자가 만든 답변이 응답으로 표시됩니다.

1. **검사**를 선택하여 응답을 더 자세히 살펴봅니다. 테스트 창은 기술 자료를 게시하기 전에 기술 자료 변경 내용을 테스트하는 데 사용됩니다.

    ![테스트 패널](../media/qnamaker-quickstart-kb/inspect-panel.png)

1. **테스트**를 다시 선택하여 **테스트** 팝업을 닫습니다.

## <a name="publish-the-knowledge-base"></a>기술 자료 게시

기술 자료를 게시하면 기술 자료의 QnA 콘텐츠가 테스트 인덱스에서 Azure Search의 프로덕션 인덱스로 이동합니다.

![게시 프로덕션 테스트 인덱스](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. QnA Maker 포털의 **편집** 옆에 있는 메뉴에서 **게시**를 선택합니다. 그런 다음, 확인하려면 페이지에서 **게시**를 선택합니다.

1. 이제 QnA Maker 서비스가 성공적으로 게시되었습니다. 애플리케이션 또는 봇 코드에서 엔드포인트를 사용할 수 있습니다.

    ![게시](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>봇 만들기

**게시** 페이지에서 게시한 후에는 언제든지 봇을 사용할 수 있습니다. 

* 개별 봇의 여러 Azure 지역 또는 가격 책정 계획에 대한 동일한 기술 자료를 가리키는 여러 봇을 신속하게 만들 수 있습니다. 
* 기술 자료에 봇을 하나만 사용하려면 **Azure Portal에서 모든 봇 보기** 링크를 사용하여 현재 봇 목록을 봅니다. 
* 기술 자료를 변경하고 다시 게시하는 경우 봇에 다른 조치를 취할 필요가 없습니다. 이미 기술 자료와 함께 작동하도록 구성되어 있으며 향후 모든 기술 자료 변경 내용과 호환됩니다. 기술 자료를 게시할 때마다 기술 자료에 연결된 모든 봇이 자동으로 업데이트됩니다.

1. QnA Maker 포털의 **게시** 페이지에서 **봇 만들기**를 선택합니다. 이 단추는 기술 자료가 게시된 후에만 표시됩니다.

    ![QnA Maker 포털에서 [게시] 페이지로 이동하여 기술 자료를 게시합니다. [봇 만들기]를 선택합니다.](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. 새 브라우저 탭에서 Azure Portal의 Azure Bot Service 만들기 페이지가 열립니다. Azure Bot Service를 구성합니다. 이러한 구성 설정에 대한 자세한 내용은 [Azure Bot Service v4로 QnA 봇 만들기](../tutorials/create-qna-bot.md) 자습서를 참조하세요.
    
    * 봇을 만들 때 Azure Portal에서 다음 설정을 변경하지 마세요. 기존 기술 자료에 대한 내용이 미리 채워집니다. 
        * QnA 인증 기
        * 앱 서비스 플랜/위치
        * Azure Storage
    * 봇과 QnA Maker는 웹앱 서비스 _플랜_을 공유할 수 있지만, 웹앱을 공유할 수는 없습니다. 즉, **앱 이름**이 QnA Maker 서비스를 만들 때 사용한 앱 이름과 달라야 합니다. 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기](../How-To/create-knowledge-base.md)
