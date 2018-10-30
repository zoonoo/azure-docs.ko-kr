---
title: 기술 자료 만들기, 교육, 게시 - QnA Maker
titleSuffix: Azure Cognitive Services
description: FAQ 또는 제품 설명서 등, 사용자 고유의 콘텐츠에서 QnA Maker 기술 자료(KB)를 만들 수 있습니다. 이 예제에서는 BitLocker 키 복구에 대한 질문에 답변하기 위해 단순한 FAQ 웹 페이지에서 QnA Maker KB를 만듭니다.
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: c57edd897797d4352706283072aa19444948436b
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49644788"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기, 학습 및 게시

FAQ 또는 제품 설명서 등, 사용자 고유의 콘텐츠에서 QnA Maker 기술 자료(KB)를 만들 수 있습니다. 이 예제에서는 BitLocker 키 복구에 대한 질문에 답변하기 위해 단순한 FAQ 웹 페이지에서 QnA Maker KB를 만듭니다.

## <a name="prerequisite"></a>필수 요소

> [!div class="checklist"]
> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-qna-maker-knowledge-base"></a>QnA Maker 기술 자료 만들기

1. Azure 자격 증명으로 QnAMaker.ai에 로그인합니다.

2. QnA Maker 웹 사이트에서 **기술 자료 만들기**를 선택합니다.

   ![새 KB 만들기](../media/qna-maker-create-kb.png)

3. 1단계 - **만들기** 페이지에서 **QnA 서비스 만들기**를 선택합니다. 그러면 구독에서 QnA Maker 서비스를 설정할 수 있는 [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)로 연결됩니다. Azure Portal 시간이 초과되면 사이트에서 **다시 시도**를 선택합니다. 연결한 후에 Azure 대시보드가 나타납니다.

4. Azure에서 새 QnA Maker 서비스를 성공적으로 만든 후 qnamaker.ai/create로 돌아갑니다. 2단계 - 드롭다운 목록에서 QnA 서비스를 선택합니다. 새 QnA 서비스를 만든 경우에는 페이지를 반드시 새로 고침해야 합니다.

   ![QnA 서비스 KB 선택](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. 3단계 - KB **내 샘플 QnA KB**에 이름을 지정합니다.

6. KB에 콘텐츠를 추가하려면 세 가지 유형의 데이터 원본을 추가합니다. 4단계 - **KB 입력**의 **URL** 상자에 [BitLocker 복구 FAQ](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL을 추가합니다.

   ![QnA 서비스 KB 선택](../media/qnamaker-quickstart-kb/add-datasources.png)

7. 5단계 - **KB 만들기**를 선택합니다.

8. KB를 만드는 동안 팝업 창이 나타납니다. 추출 프로세스는 HTML 페이지를 읽고 질문 및 답변을 확인하려면 몇 분 정도 걸립니다.

9. KB가 성공적으로 만들어지면 **기술 자료** 페이지가 열립니다. 이 페이지에서 KB의 내용을 편집할 수 있습니다.

10. 오른쪽 위에서 **QnA 쌍 추가**를 선택하여 KB의 **Editorial**에 새 행을 추가합니다. **질문**에 **Hi**를 입력합니다. **답변**에 **Hello. Ask me bitlocker questions.** 를 입력합니다.

   ![QnA 쌍 추가](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. 오른쪽 위에서 **저장 후 학습**을 선택하여 편집 내용을 저장하고 QnA Maker 모델을 학습합니다. 저장하지 않으면 편집 내용은 보관되지 않습니다.

   ![저장 후 학습](../media/qnamaker-quickstart-kb/add-qna-pair2.png)

12. 오른쪽 위에서 **테스트**를 선택하여 변경 내용이 적용되었는지 테스트합니다. 상자에 **hi there**를 입력하고 Enter 키를 선택합니다. 사용자가 만든 답변이 응답으로 표시됩니다.

13. **검사**를 선택하여 응답을 더 자세히 살펴봅니다. 테스트 창을 사용하여 KB에 적용한 변경 사항을 게시하기 전에 테스트합니다.

   ![테스트 패널](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. **테스트**를 다시 선택하여 **테스트** 팝업을 닫습니다.

15. **편집** 옆 메뉴에서 **게시**를 선택합니다. 그런 다음, 확인하려면 페이지에서 **게시**를 선택합니다.

16. 이제 QnA Maker 서비스가 성공적으로 게시되었습니다. 응용 프로그램 또는 봇 코드에서 엔드포인트를 사용할 수 있습니다.

   ![게시](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 만들기](../How-To/create-knowledge-base.md)
