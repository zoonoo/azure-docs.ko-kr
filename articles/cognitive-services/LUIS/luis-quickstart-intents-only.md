---
title: 두 가지 의도로 간단한 앱 만들기 - Azure | Microsoft Docs
description: 이 빠른 시작에서는 엔터티 없이 두 의도를 사용하여 간단한 LUIS 앱을 들어 사용자 발화를 식별하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 3f23ade2b0256c72c344e2a619227a79e3c79a47
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160118"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>자습서: 1. 사용자 지정 도메인으로 앱 빌드
이 자습서에서는 **의도**를 사용하여 사용자가 앱에 제출한 발화(텍스트)를 기초로 사용자의 _의도_를 판단하는 방법을 보여 주는 앱을 만듭니다. 마치면 클라우드에서 실행되는 LUIS 엔드포인트를 갖게 됩니다.

이 앱은 발화에서 데이터를 추출하지 않는 가장 간단한 형식의 LUIS 앱입니다. 오직 사용자의 발화 의도만 판단합니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * HR(인사 관리) 도메인에 대한 새 앱 만들기 
> * GetJobInformation 의도 추가
> * GetJobInformation 의도에 예제 발화 추가 
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인
> * ApplyForJob 의도 추가
> * ApplyForJob 의도에 발화 예제 추가 
> * 엔드포인트 학습, 게시 및 다시 쿼리 

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="purpose-of-the-app"></a>앱의 용도
이 앱에는 몇 가지 의도가 있습니다. 첫 번째 의도 **`GetJobInformation`** 은 사용자가 회사 내에서 제공하는 업무에 관한 정보가 필요한 시점을 식별합니다. 두 번째 의도 **`None`** 은 모든 다른 발화의 형식을 식별합니다. 이 빠른 시작의 뒷부분에 나오는 세 번째 의도 `ApplyForJob`이 추가됩니다. 

## <a name="create-a-new-app"></a>새 앱 만들기
1. [LUIS](luis-reference-regions.md#luis-website) 웹 사이트에 로그인합니다. LUIS 엔드포인트를 게시해야 하는 [지역](luis-reference-regions.md#publishing-regions)에 로그인해야 합니다.

2. [LUIS](luis-reference-regions.md#luis-website) 웹 사이트에서 **새 앱 만들기**를 선택합니다.  

    [![](media/luis-quickstart-intents-only/app-list.png "My Apps 페이지 스크린샷")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. 팝업 대화 상자에서 `HumanResources`이라는 이름을 입력합니다. 이 앱에서는 회사의 인사 부서에 대 한 질문을 다룹니다. 이런 유형의 부서에서는 충원이 필요한 회사 내 자리 등, 채용 관련 문제를 처리합니다.

    ![LUIS 새 앱](./media/luis-quickstart-intents-only/create-app.png)

4. 해당 프로세스가 완료되면 앱에 **없음** 의도가 있는 **의도** 페이지가 표시됩니다. 

## <a name="create-getjobinformation-intention"></a>GetJobInformation 의도 만들기
1. **새 의도 만들기**를 선택합니다. `GetJobInformation`이라는 새 의도 이름을 입력합니다. 이 의도는 사용자가 회사 내 공석에 관한 정보를 원할 때마다 예측됩니다.

    ![](media/luis-quickstart-intents-only/create-intent.png "새 의도 대화 상자의 스크린샷")

    의도를 만들면 식별하려는 정보의 범주를 만들게 됩니다. 범주 이름을 지정하면 LUIS 쿼리 결과를 사용하는 다른 응용 프로그램에서 해당 범주 이름을 사용하여 적절한 대답을 찾습니다. LUIS는 이러한 질문에 답변하지 않으며, 자연어로 요청되는 정보의 유형만 식별합니다. 

2. 사용자가 요청해야 하는 이 의도에 다음 7개 발화를 추가합니다.

    | 예제 발화|
    |--|
    |오늘 게시된 새 자리가 있나요?|
    |수석 엔지니어에게 제공되는 자리는 무엇인가요?|
    |데이터베이스 관련 업무가 있나요?|
    |회계 직무와 관련된 새 상황을 찾고 있습니다.|
    |직무 목록은 어디에 있나요?|
    |새 업무|
    |시애틀 지사에 빈자리가 있나요?|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "MyStore 의도에 대한 새 발화 입력 스크린샷")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

3. LUIS 앱에는 현재 **없음** 의도에 대한 발화가 없습니다. 앱이 응답하지 않으면 발화가 필요합니다. 비워 두면 안됩니다. 왼쪽 패널에서 **의도**를 선택합니다. 

4. **없음** 의도를 선택합니다. 사용자가 입력할 수 있지만 앱과 관련이 없는 세 개의 발화를 추가합니다. 앱이 구인 게시에 관한 것일 때 유효한 **없음** 발화는 다음과 같습니다.

    | 예제 발화|
    |--|
    |개가 짖어서 시끄럽다|
    |피자 주문해 줘|
    |바다의 펭귄|

    챗봇과 같은 LUIS 호출 응용 프로그램에서 LUIS가 발화에 대해 **없음** 의도를 반환하는 경우, 봇에서 사용자가 대화를 종료하려는지 물어볼 수 있습니다. 사용자가 종료하지 않으려고 하는 경우, 챗봇에서 대화를 계속하기 위해 더 많은 지침을 제공할 수도 있습니다. 

## <a name="train-and-publish-the-app"></a>앱 학습 및 게시

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>엔드포인트에 앱 게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="query-endpoint-for-getjobinformation-intent"></a>GetJobInformation 의도의 쿼리 엔드포인트

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `I'm looking for a job with Natual Language Processing`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`로 발화 **쿼리**입니다. 이 발화는 4단계의 에제 발화와 전혀 다르므로 테스트에 적합하며 최고 점수 의도로 `GetJobInformation` 의도를 반환하게 됩니다. 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

## <a name="create-applyforjob-intention"></a>ApplyForJob 의도 만들기
LUIS 웹 사이트에 대한 브라우저 탭으로 돌아가 업무에 지원하는 새 의도를 만듭니다.

1. 맨 위 오른쪽 메뉴에서 **빌드**를 선택하여 앱 빌드로 돌아갑니다.

2. 왼쪽 메뉴에서 **의도**를 선택합니다.

3. **새 의도 만들기**를 선택하고 `ApplyForJob` 이름을 입력합니다. 

    ![새 의도를 만드는 LUIS 대화 상자](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. 사용자가 요청해야 하는 이 의도에 다음과 같은 몇 가지 발화를 추가합니다.

    | 예제 발화|
    |--|
    |새 회계 업무에 지원하려고 합니다.|
    |123456 업무에 대한 지원서를 작성합니다.|
    |엔지니어링 업무에 이력서를 제출합니다.|
    |제 이력서가 있습니다. 654234 업무 관련|
    |567890 업무 및 내 서류|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "ApplyForJob 의도에 대한 새 발화 입력 스크린샷")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    LUIS는 현재 의도가 정확한지 확신하지 못하므로 레이블의 의도에 빨간색 밑줄이 있습니다. 앱 학습에서 LUIS에게 발화가 정확한 의도임을 알립니다. 

    [앱을 학습하고 다시 게시합니다](#train-and-publish-the-app). 

## <a name="query-endpoint-for-applyforjob-intent"></a>ApplyForJob 의도에 대한 쿼리 엔드포인트

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 새 브라우저 창에서 URL의 마지막에 `Can I submit my resume for job 235986`을 입력합니다. 

    ```
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
몇 가지 의도만 있는 이 앱은 의도는 같으나 사용한 단어가 다른 자연어 쿼리를 식별했습니다. 

JSON 결과는 최고 점수 매기기 의도를 식별합니다. 모든 점수는 1에서 0 사이이며, 1에 가까울수록 더 좋은 점수입니다. `GetJobInformation` 및 `None` 의도의 점수는 0에 훨씬 가깝습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇 같은 호출 응용 프로그램은 topScoringIntent 결과를 취하고, 정보를 찾아(LUIS에 저장되지 않음) 질문에 답하거나 대화를 종료할 수 있습니다. 이것은 봇 또는 호출 응용 프로그에 대한 프로그램 방식의 옵션입니다. LUIS는 이러한 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [이 앱에 미리 빌드된 의도 및 엔터티 추가](luis-tutorial-prebuilt-intents-entities.md)
