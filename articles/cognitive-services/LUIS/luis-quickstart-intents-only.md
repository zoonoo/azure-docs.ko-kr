---
title: '자습서 1: 사용자 지정 LUIS 앱에서 의도 찾기'
titleSuffix: Azure Cognitive Services
description: 사용자의 의도를 예측하는 사용자 지정 앱을 만듭니다. 이 앱은 이메일 주소 또는 날짜 등의 발화 텍스트에서 다양한 데이터 요소를 추출하지 않으므로 가장 간단한 형식의 LUIS 앱입니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 30c9f572d77caacbeecf5f15d74fd8517e9fa883
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426862"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>자습서 1: 사용자 지정 앱을 빌드하여 사용자 의도 확인

이 자습서에서는 발화(텍스트)에 따라 사용자의 의도를 예측하는 사용자 지정 HR(인적 자원) 앱을 만듭니다. 마치면 클라우드에서 실행되는 LUIS 엔드포인트를 갖게 됩니다.

앱의 목적은 대화, 자연어 텍스트의 의도를 확인하는 것입니다. 이러한 의도는 **의도**로 분류됩니다. 이 앱에는 몇 가지 의도가 있습니다. 첫 번째 의도 **`GetJobInformation`** 은 사용자가 회사 내에서 제공하는 업무에 관한 정보가 필요한 시점을 식별합니다. 두 번째 의도인 **`None`** 은 이 앱의 _도메인_(범위) 외부에 있는 사용자의 발화에 사용됩니다. 나중에 세 번째 의도인 **`ApplyForJob`** 은 작업에 적용하는 방법에 대한 발화에 추가됩니다. 누군가가 이 작업에 지원할 때 작업 정보는 이미 알고 있으므로 이 세 번째 의도는 `GetJobInformation`과 다릅니다. 그러나 단어 선택에 따라 둘 다 작업에 대한 것이므로 의도를 결정하는 작업은 까다로울 수 있습니다.

LUIS가 JSON 응답은 반환한 후에 이 요청에서 LUIS가 완료됩니다. LUIS는 사용자 발화에 대답을 제공하지 않고, 자연어에 요청되는 정보의 형식만 식별합니다. 

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 새 앱 만들기 
> * 의도 만들기
> * 예제 발화 추가
> * 앱 교육
> * 앱 게시
> * 엔드포인트에서 의도 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>새 앱 만들기

1. [https://www.luis.ai](https://www.luis.ai)의 URL을 사용하여 LUIS 포털에 로그인합니다. 

2. **새 앱 만들기**를 선택합니다.  

    [![](media/luis-quickstart-intents-only/app-list.png "Language Understanding(LUIS) 내 앱 페이지 스크린샷")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. 팝업 대화 상자에 `HumanResources` 이름을 입력하고 기본 문화권인 **영어**를 유지합니다. 설명을 비워둡니다.

    ![LUIS 새 앱](./media/luis-quickstart-intents-only/create-app.png)

    다음으로, 앱에 **없음** 의도가 있는 **의도** 페이지가 표시됩니다.

## <a name="getjobinformation-intent"></a>GetJobInformation 의도

1. **새 의도 만들기**를 선택합니다. `GetJobInformation`이라는 새 의도 이름을 입력합니다. 이 의도는 사용자가 회사 내 공석에 관한 정보를 원할 때마다 예측됩니다.

    ![](media/luis-quickstart-intents-only/create-intent.png "Language Understanding(LUIS) 새 의도 대화 상자 스크린샷")

2. _예제 발화_를 입력하여 이 의도에 대해 예측되어야 하는 발화의 종류에 대해 LUIS를 훈련하게 됩니다. 사용자가 요청해야 하는 이 의도에 다음과 같은 몇 가지 예제 발화를 추가합니다.

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

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>None 의도 
클라이언트 응용 프로그램은 발화가 응용 프로그램의 주체 도메인의 외부에 있는지를 알아야 합니다. LUIS가 발화에 대해 **없음** 의도를 반환하는 경우 클라이언트 응용 프로그램에서는 사용자가 대화를 종료하려는지 물어볼 수 있습니다. 사용자가 종료하지 않으려고 하는 경우 클라이언트 응용 프로그램에서 대화를 계속하기 위해 더 많은 지침을 제공할 수도 있습니다. 

주체 도메인 외부에 있는 이러한 예제 발화는 **없음** 의도로 그룹화됩니다. 이 의도를 비워 두지 마세요. 

1. 왼쪽 패널에서 **의도**를 선택합니다.

2. **없음** 의도를 선택합니다. 사용자가 입력할 수 있지만 인적 자원 앱과 관련이 없는 세 개의 발화를 추가합니다. 앱이 구인 게시에 관한 것이면 일부 **없음** 발화는 다음과 같습니다.

    | 예제 발화|
    |--|
    |개가 짖어서 시끄럽다|
    |피자 주문해 줘|
    |바다의 펭귄|


## <a name="train"></a>학습 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>의도 알기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소 표시줄의 URL 끝으로 이동하여 `I'm looking for a job with Natural Language Processing`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`로 발화 **쿼리**입니다. 이 발화는 예제 발화 중 하나와 같지 않습니다. 좋은 테스트이며, 상위 채점 의도로 `GetJobInformation`을 반환해야 합니다. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
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

    결과에는 현재 2가지인 앱의 **모든 의도**가 포함됩니다. 이 앱에 현재 엔터티가 없기 때문에 엔터티 배열이 비어 있습니다. 

    JSON 결과는 최고 채점 의도를 **`topScoringIntent`** 속성으로 식별합니다. 모든 점수는 1에서 0 사이이며, 1에 가까울수록 더 좋은 점수입니다. 

## <a name="applyforjob-intent"></a>ApplyForJob 의도
LUIS 웹 사이트로 돌아가서 새 의도를 만들고 사용자 발화를 작업에 적용했는지를 확인합니다.

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

## <a name="train-again"></a>다시 학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>다시 게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>다시 의도 알기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 새 브라우저 창에서 URL의 마지막에 `Can I submit my resume for job 235986`을 입력합니다. 

    ```JSON
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

    결과에는 기존 의도뿐만 아니라 새 의도인 **ApplyForJob**이 포함됩니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 HR(인적 자원) 앱을 만들고, 2가지 의도를 만들고, 각 의도에 대한 예제 발화를 추가하고, 없음 의도. 학습됨, 게시됨 및 엔드포인트에서 테스트됨에 예제 발화를 추가했습니다. LUIS 모델을 빌드하는 기본 단계입니다. 

> [!div class="nextstepaction"]
> [이 앱에 미리 빌드된 의도 및 엔터티 추가](luis-tutorial-prebuilt-intents-entities.md)
