---
title: 의도 예측
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 사용자의 의도를 예측하는 사용자 지정 앱을 만듭니다. 이 앱은 이메일 주소 또는 날짜 등의 발화 텍스트에서 다양한 데이터 요소를 추출하지 않으므로 가장 간단한 형식의 LUIS 앱입니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 20cd3931488f3d3cf4728b3022316b685da3277a
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754263"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>자습서: 사용자 의도를 확인하는 LUIS 앱 빌드

이 자습서에서는 발화(텍스트)에 따라 사용자의 의도를 예측하는 사용자 지정 HR(인적 자원) 앱을 만듭니다. 

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 새 앱 만들기 
> * 의도 만들기
> * 예제 발화 추가
> * 앱 교육
> * 앱 게시
> * 엔드포인트에서 의도 가져오기


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>사용자 의도

이 앱의 목적은 대화, 자연어 텍스트의 의도를 확인하는 것입니다. 

`Are there any new positions in the Seattle office?`

이러한 의도는 **의도**로 분류됩니다. 

이 앱에는 몇 가지 의도가 있습니다. 

|의도|목적|
|--|--|
|ApplyForJob|사용자가 구직 중인지 확인합니다.|
|GetJobInformation|사용자가 일반 직무 또는 특정 직무에 대한 정보를 찾고 있는지 확인합니다.|
|없음|사용자가 앱에서 대답할 수 없는 내용을 질문하고 있는지 확인합니다. 이 의도가 앱 만들기의 일부로 제공된 경우에는 삭제할 수 없습니다. |

## <a name="create-a-new-app"></a>새 앱 만들기

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>직무 정보에 대한 의도 만들기

1. **새 의도 만들기**를 선택합니다. `GetJobInformation`이라는 새 의도 이름을 입력합니다. 이 의도는 사용자가 회사 내 공석에 관한 정보를 원할 때 예측됩니다. 

    ![Language Understanding(LUIS) 새 의도 대화 상자 스크린샷](media/luis-quickstart-intents-only/create-intent.png "Language Understanding(LUIS) 새 의도 대화 상자 스크린샷")

1. **완료**를 선택합니다.

2. 사용자가 요청할 것으로 예상되는 여러 예제 발언을 이 의도에 추가합니다.

    | 예제 발화|
    |--|
    |오늘 게시된 새 자리가 있나요?|
    |시애틀 지사에 빈자리가 있나요?|
    |원격 작업 또는 재택 근무가 가능한 엔지니어 일자리가 있나요?|
    |데이터베이스 관련 업무가 있나요?|
    |탬파 사무실에서 공동 작업을 할 수 있는 일자리를 찾고 있습니다.|
    |샌프란시스코 지사에 인턴 자리가 있나요?|
    |대학생이 할 수 있는 파트 타임 일자리가 있나요?|
    |회계 직무와 관련된 새 상황을 찾고 있습니다.|
    |2개 국어 가능자를 원하는 뉴욕시 소재 일자리를 찾고 있습니다.|
    |회계 직무와 관련된 새로운 일자리를 찾고 있습니다.|
    |새 업무|
    |지난 2일 동안 추가된 엔지니어 일자리를 모두 보여주세요.|
    |오늘 게시된 구인 광고가 있나요?|
    |런던 지사에서 구하는 회계 직무는 무엇인가요?|
    |수석 엔지니어에게 제공되는 자리는 무엇인가요?|
    |직무 목록은 어디에 있나요?|

    [![MyStore 의도에 대한 새 발화 입력 스크린샷](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "MyStore 의도에 대한 새 발화 입력 스크린샷")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    _예제 발언_을 입력하면 LUIS 앱이 이 의도에 대해 예측해야 하는 발언의 종류를 학습하게 됩니다. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>None 의도에 예제 발언 추가 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>테스트하거나 게시하기 전에 앱 학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>엔드포인트에서 쿼리할 앱 게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>엔드포인트에서 의도 예측 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 주소 표시줄의 URL 끝으로 이동하여 `I'm looking for a job with Natural Language Processing`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`로 발화 **쿼리**입니다. 이 발화는 예제 발화 중 하나와 같지 않습니다. 좋은 테스트이며, 상위 채점 의도로 `GetJobInformation`을 반환해야 합니다. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    `verbose=true` querystring 매개 변수는 앱의 쿼리 결과에 **모든 의도**를 포함하라는 의미입니다. 이 앱에 현재 엔터티가 없기 때문에 엔터티 배열이 비어 있습니다. 

    JSON 결과는 최고 채점 의도를 **`topScoringIntent`** 속성으로 식별합니다. 모든 점수는 1에서 0 사이이며, 1에 가까울수록 더 좋은 점수입니다. 

## <a name="create-intent-for-job-applications"></a>구직에 대한 의도 만들기

LUIS 포털로 돌아가서 사용자 발언이 구직과 관련된 것인지 확인하는 새 의도를 만듭니다.

1. 맨 위 오른쪽 메뉴에서 **빌드**를 선택하여 앱 빌드로 돌아갑니다.

1. 왼쪽 메뉴에서 **의도**를 선택하여 의도 목록으로 이동합니다.

1. **새 의도 만들기**를 선택하고 `ApplyForJob` 이름을 입력합니다. 

    ![새 의도를 만드는 LUIS 대화 상자](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. 사용자가 요청해야 하는 이 의도에 다음과 같은 몇 가지 발화를 추가합니다.

    | 예제 발화|
    |--|
    |123456 업무에 대한 지원서를 작성합니다.|
    |제 이력서가 있습니다. 654234 업무 관련|
    |파트 타임 접수원 게시물에 대한 제 이력서입니다.|
    |아트 데스크 직무에 지원하기 위해 이 서류를 제출합니다.|
    |샌디에이고의 연구 및 개발 여름 대학 인턴십에 지원합니다.|
    |구내 식당 임시직에 지원하기 위해 이력서를 제출합니다.|
    |오하이오 콜롬버스의 새 Autocar 팀에서 일하고 싶어서 이력서를 제출합니다.|
    |새 회계 업무에 지원하려고 합니다.|
    |직무 456789 회계 인턴십 지원서를 제출합니다.|
    |567890 업무 및 내 서류|
    |털사 회계 인턴십 지원서를 첨부합니다.|
    |휴일 배달직 지원서|
    |시애틀의 새로운 회계 직무에 제 이력서를 제출해 주세요.|
    |엔지니어링 업무에 이력서를 제출합니다.|
    |여기의 내 이력서는 탬파 게시물 234123에 대한 이력서입니다.|

    [![ApplyForJob 의도에 대한 새 발화 입력 스크린샷](media/luis-quickstart-intents-only/utterance-applyforjob.png "ApplyForJob 의도에 대한 새 발화 입력 스크린샷")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    LUIS는 현재 의도가 정확한지 확신하지 못하므로 레이블의 의도에 빨간색 밑줄이 있습니다. 앱 학습에서 LUIS에게 발화가 정확한 의도임을 알립니다. 

## <a name="train-again"></a>다시 학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>다시 게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>의도 예측 다시 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 새 브라우저 창에서 URL의 마지막에 `Can I submit my resume for job 235986`을 입력합니다. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    결과에는 기존 의도뿐만 아니라 새 의도인 **ApplyForJob**이 포함됩니다. 

## <a name="client-application-next-steps"></a>클라이언트-애플리케이션 다음 단계

LUIS가 JSON 응답은 반환한 후에 이 요청에서 LUIS가 완료됩니다. LUIS는 사용자 발화에 대답을 제공하지 않고, 자연어에 요청되는 정보의 형식만 식별합니다. 대화형 후속 작업은 Azure Bot 같은 클라이언트 애플리케이션을 통해 제공됩니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>관련 정보

* [엔터티의 형식](luis-concept-entity-types.md)
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 HR(인적 자원) 앱을 만들고, 2가지 의도를 만들고, 각 의도에 대한 예제 발화를 추가하고, 없음 의도. 학습됨, 게시됨 및 엔드포인트에서 테스트됨에 예제 발화를 추가했습니다. LUIS 모델을 빌드하는 기본 단계입니다. 

이 앱을 계속 사용하여 [단순 엔터티 및 구 목록을 추가](luis-quickstart-primary-and-secondary-data.md)합니다.

> [!div class="nextstepaction"]
> [이 앱에 미리 빌드된 의도 및 엔터티 추가](luis-tutorial-prebuilt-intents-entities.md)
