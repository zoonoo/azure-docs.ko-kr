---
title: QnA Maker에서 만들기, 게시, 답변
titleSuffix: Azure Cognitive Services
description: 이 포털 기반 자습서에서는 프로그래매틱 방식으로 기술 자료를 생성 및 게시한 후 기술 자료의 질문에 답변하는 방법을 안내합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.technology: qna-maker
ms.topic: tutorial
ms.date: 10/29/2018
ms.author: diberry
ms.openlocfilehash: 9853e84ffd2de8a1dfd6d9c4f32f58bb36b16403
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164435"
---
# <a name="tutorial-create-a-knowledge-base-then-answer-question-via-the-qna-maker-portal"></a>자습서: 기술 자료를 만든 후 QnA Maker 포털을 통해 질문의 답변 만들기

이 자습서에서는 기술 자료를 생성 및 게시한 후 기술 자료의 질문에 답변하는 방법을 안내합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
* QnA Maker 포털에서 기술 자료 만들기
* 기술 자료 검토, 저장 및 학습
* 기술 자료 게시
* Curl을 사용하여 기술 자료 쿼리

> [!NOTE] 
> 이 자습서의 프로그래밍 방식 버전은 [**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base)에서 완전한 솔루션으로 제공됩니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 기존의 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 필요합니다. 

## <a name="create-a-knowledge-base"></a>기술 자료 만들기 

1. [QnA Maker](https://www.qnamaker.ai) 포털에 로그인합니다. 

1. 상단 메뉴에서 **기술 자료 만들기**를 선택합니다.

    ![KB 생성 프로세스 1단계](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. 기존 QnA Maker 서비스를 사용할 것이므로 첫 번째 단계는 건너뜁니다. 

1. 다음 단계에서 기존 설정을 선택합니다.  

    |설정|목적|
    |--|--|
    |Microsoft Azure Directory ID|_Microsoft Azure Directory ID_는 Azure Portal과 QnA Maker 포털에 로그인하는 데 사용하는 계정과 연결되어 있습니다. |
    |Azure 구독 이름|QnA Maker 리소스를 만든 청구 계정입니다.|
    |Azure QnA 서비스|기존 QnA Maker 리소스입니다.|

    ![KB 생성 프로세스 2단계](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. 다음 단계에서는 기술 자료 이름 `My Tutorial kb`를 입력합니다.

    ![KB 생성 프로세스 3단계](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. 다음 단계에서는 다음 설정을 사용하여 kb를 채웁니다.  

    |설정 이름|설정 값|목적|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |해당 URL의 FAQ 콘텐츠는 질문 다음에 답변이 오는 형식으로 구성됩니다. QnA Maker는 이러한 형식을 해석하여 질문과 관련 답변을 추출할 수 있습니다.|
    |파일 |_이 자습서에서 사용되지 않음_|질문 및 답변 파일을 업로드합니다. |
    |잡담 개성|친구|일반적인 질문 및 답변에 친숙하고 편안한 개성을 부여합니다. 나중에 이러한 질문 및 답변을 편집할 수 있습니다. |

    ![KB 생성 프로세스 4단계](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. **KB 만들기**를 선택하여 생성 프로세스를 완료합니다.

    ![KB 생성 프로세스 5단계](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>KB 검토, 저장 및 학습

1. 질문 및 답변을 검토합니다. 첫 번째 페이지에는 URL의 질문 및 답변이 있습니다. 

    ![저장 후 학습](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. 표 하단에서 질문 및 답변의 마지막 페이지를 선택합니다. 이 페이지는 잡담 개성의 질문 및 답변을 보여줍니다. 

1. 질문 및 답변 목록 위의 도구 모음에서 기어를 선택합니다. 이는 각 질문 및 답변의 필터를 보여줍니다. 잡담 질문에는 **editorial: chit-chat** 필터가 이미 설정되어 있습니다. 이 필터는 선택된 답변과 함께 클라이언트 애플리케이션으로 반환됩니다. 챗봇과 같은 클라이언트 애플리케이션은 이 필터를 사용하여 추가 처리 또는 사용자와의 상호 작용을 결정할 수 있습니다.

    ![필터 보기](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. 상단 메뉴 모음에서 **저장 및 학습**을 선택합니다.

## <a name="publish-to-get-kb-endpoints"></a>게시하여 KB 엔드포인트 가져오기

상단 메뉴에서 **게시** 단추를 선택합니다. 게시 페이지에 있다면 **취소** 단추 옆에 있는 **게시**를 선택합니다.

![게시](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

KB가 게시되면 엔드포인트가 표시됩니다.

![페이지의 엔드포인트 설정 게시](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

## <a name="use-curl-to-query-for-an-faq-answer"></a>curl을 사용하여 FAQ 답변 쿼리

1. **Curl** 탭을 선택합니다. 

    ![Curl 명령](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. **Curl** 탭의 텍스트를 복사하고 Curl 지원 터미널 또는 명령줄에서 실행합니다. 인증 헤더의 값에 `Endpoint ` 텍스트와 후행 공백이 포함된 후 키가 포함됩니다.

1. `<Your question>`을 `How large can my KB be?`로 바꿉니다. 이는 `How large a knowledge base can I create?`라는 질문과 가깝지만 똑같지는 않습니다. QnA Maker는 자연어 처리를 적용하여 두 질문이 동일한지 확인합니다.     

1. CURL 명령을 실행하고 점수와 답변이 포함된 JSON 응답을 수신합니다. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    QnA Maker는 신뢰도 점수가 42.81%로, 어느 정도 믿을 수 있습니다.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>curl을 사용하여 잡담 답변 쿼리

1. Curl 지원 터미널에서 `How large can my KB be?`를 사용자의 봇 대화 종결 문구(예: `Thank you`)로 바꿉니다.   

1. CURL 명령을 실행하고 점수와 답변이 포함된 JSON 응답을 수신합니다. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    `Thank you`의 질문은 잡담 질문과 정확히 일치하므로 QnA Maker는 신뢰도 점수 100으로, 완전히 신뢰할 수 있습니다. QnA Maker는 또한 모든 관련 질문과 잡담 필터 정보를 포함한 메타데이터 속성도 반환했습니다.  

## <a name="use-curl-to-query-for-the-default-answer"></a>curl을 사용하여 기본 답변 쿼리

QnA Maker가 신뢰할 수 없는 모든 질문에는 기본 답변이 수신됩니다. 이 답변은 Azure Portal에서 구성할 수 있습니다. 

1. Curl 지원 터미널에서 `Thank you`를 `x`로 바꿉니다. 

1. CURL 명령을 실행하고 점수와 답변이 포함된 JSON 응답을 수신합니다. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker가 신뢰할 수 없다는 의미의 0을 반환했을 뿐만 아니라 기본 답변도 반환했습니다. 

## <a name="next-steps"></a>다음 단계

지원 파일 형식에 대한 자세한 내용은 [지원되는 데이터 원본](../Concepts/data-sources-supported.md)을 참조하세요. 

잡담 [개성](../Concepts/best-practices.md#chit-chat)에 대해 자세히 알아보세요.

기본 답변에 대한 자세한 내용은 [일치하는 항목 없음](../Concepts/confidence-score.md#no-match-found)을 참조하세요. 

> [!div class="nextstepaction"]
> [기술 자료 개념](../Concepts/knowledge-base.md)