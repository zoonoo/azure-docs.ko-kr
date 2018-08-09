---
title: 감정 분석을 반환하는 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서 감정 분석을 LUIS 앱에 추가하여 긍정, 부정 및 중립 감정에 대한 발언을 분석하는 방법을 설명합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: baa449bb9e78a5c6437b0a9528e5d1f10dfa519f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520455"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>자습서: 9.  감정 분석 추가
이 자습서에서는 긍정, 부정 및 중립 감정을 발언에서 추출하는 방법을 보여주는 앱을 만듭니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * 감정 분석 이해
> * HR(인적 자원) 도메인에서 LUIS 앱 사용 
> * 감정 분석 추가
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>시작하기 전에
[미리 작성된 keyPhrase 엔터티](luis-quickstart-intent-and-key-phrase.md) 자습서에서 인적 자원 앱이 없는 경우 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json) Github 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `sentiment`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

## <a name="sentiment-analysis"></a>정서 분석
감정 분석은 사용자의 발언이 긍정, 부정 또는 중립인지를 확인하는 기능입니다. 

다음 발언에서는 감정의 예제를 보여줍니다.

|데이터|Score|발화|
|:--|:--|:--|
|긍정|0.91 |John W. Smith는 파리에서 훌륭한 프레젠테이션을 했습니다.|
|긍정|0.84 |jill-jones@mycompany.com은 Parker 판매 권유 작업을 훌륭히 해냈습니다.|

감정 분석은 모든 발언에 적용되는 앱 설정입니다. 감정 분석은 전체 발언에 적용되므로 해당 발언에서 감정을 나타내는 단어를 찾아 레이블을 붙일 필요는 없습니다. 

## <a name="add-employeefeedback-intent"></a>EmployeeFeedback 의도 추가 
회사의 구성원에게서 직원 피드백을 캡처하기 위한 새 의도를 추가합니다. 

1. 인사 관리 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

    [ ![오른쪽 위의 탐색 모음에서 강조 표시된 빌드가 있는 LUIS 앱의 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. **새 의도 만들기**를 선택합니다.

    [ ![오른쪽 위의 탐색 모음에서 강조 표시된 빌드가 있는 LUIS 앱의 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. 새 의도 이름 `EmployeeFeedback`을 명명합니다.

    ![이름으로 EmployeeFeedback이 있는 의도 대화 상자 새로 만들기](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 개선이 필요한 영역 또는 제대로 작업을 수행하는 직원을 나타내는 여러 발언을 추가합니다.

    이 인적 자원 앱에서 직원은 이름, 이메일, 내선 전화 번호, 휴대폰 번호 및 미국 연방 사회 보장 번호로 목록 엔터티`Employee`에서 정의됩니다. 

    |발언|
    |--|
    |425-555-1212는 동료의 출산 휴가 복귀를 환영하는 멋진 일을 했습니다.|
    |234-56-7891은 슬픔에 빠진 동료를 위로하는 대단한 일을 했습니다.|
    |jill-jones@mycompany.com에는 문서 작업에 대한 모든 필수 청구서가 없었습니다.|
    |john.w.smith@mycompany.com은 한 달 늦게 서명 없는 필수 양식을 제출했습니다.|
    |x23456은 중요한 오프사이트 마케팅 회의에 제 시간에 도착하지 못했습니다.|
    |x12345는 6월 검토 회의에 참석하지 못했습니다.|
    |Jill Jones는 하버드에서 판매 권유에 박차를 가했습니다.|
    |John W. Smith는 스탠포드에서 훌륭한 프레젠테이션을 했습니다.|

    [ ![EmployeeFeedback 의도에서 예제 발언이 포함된 LUIS 앱의 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>감정 분석을 포함하도록 앱 구성
**게시** 페이지에서 감정 분석을 구성합니다. 

1. 오른쪽 맨 위 탐색 창에서 **게시**를 선택합니다.

    ![게시 단추가 확장된 의도 페이지 스크린샷 ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. **감정 분석 사용**을 선택합니다. 

## <a name="publish-app-to-endpoint"></a>엔드포인트에 앱 게시

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-an-utterance"></a>발언을 사용하여 엔드포인트 쿼리

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `Jill Jones work with the media team on the public portal was amazing`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 이 발언은 레이블이 지정된 발언과 같지 않으므로 유용한 테스트이며 감정 분석이 추출된 `EmployeeFeedback` 의도가 반환되어야 합니다.

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

sentimentAnalysis는 0.86점으로 긍정적입니다. 

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
감정 분석이 사용된 이 앱은 자연어 쿼리 의도를 식별하고 전반적인 감정을 점수로 포함하는 추출된 데이터를 반환했습니다. 

이제 챗봇에는 대화의 다음 단계를 확인할 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램은 발언에서 topScoringIntent 결과 및 감정 데이터를 사용하여 다음 단계를 진행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [HR 앱에서 엔드포인트 발언 검토](luis-tutorial-review-endpoint-utterances.md) 

