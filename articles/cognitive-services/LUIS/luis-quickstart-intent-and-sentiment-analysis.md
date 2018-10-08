---
title: '자습서 9: LUIS에서 긍정, 부정 및 중립을 포함한 감정 분석'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 긍정, 부정 및 중립 감정을 발언에서 추출하는 방법을 보여주는 앱을 만듭니다. 감정은 전체 발화에서 판별됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: ff5a47f977f34535c5ad1fde7e6cac5995e7f7dd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031461"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>자습서 9: 전체 발화의 감정 추출
이 자습서에서는 긍정, 부정 및 중립 감정을 발언에서 추출하는 방법을 보여주는 앱을 만듭니다. 감정은 전체 발화에서 판별됩니다.

감정 분석은 사용자의 발언이 긍정, 부정 또는 중립인지를 확인하는 기능입니다. 

다음 발언에서는 감정의 예제를 보여줍니다.

|데이터|Score|발화|
|:--|:--|:--|
|긍정|0.91 |John W. Smith는 파리에서 훌륭한 프레젠테이션을 했습니다.|
|긍정|0.84 |jill-jones@mycompany.com은 Parker 판매 권유 작업을 훌륭히 해냈습니다.|

감정 분석은 모든 발화에 적용되는 게시 설정입니다. 감정 분석은 전체 발언에 적용되므로 해당 발언에서 감정을 나타내는 단어를 찾아 레이블을 붙일 필요는 없습니다. 

게시 설정이므로, 의도 또는 엔터티 페이지에는 보이지 않고 [대화형 테스트](luis-interactive-test.md#view-sentiment-results) 창이나, 엔드포인트 URL에서 테스트할 때 볼 수 있습니다. 

**이 자습서에서는 다음 방법에 대해 알아봅니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 기존 자습서 앱 사용 
> * 게시 설정으로 감정 분석 추가
> * 학습
> * 게시
> * 엔드포인트에서 발화 감정 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>기존 앱 사용

마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

이전 자습서의 HumanResources 앱이 없으면 다음 단계를 사용합니다.

1.  [앱 JSON 파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `sentiment`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.

## <a name="employeefeedback-intent"></a>EmployeeFeedback 의도 
회사의 구성원에게서 직원 피드백을 캡처하기 위한 새 의도를 추가합니다. 

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **새 의도 만들기**를 선택합니다.

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

## <a name="train"></a>학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>감정 분석을 포함하도록 앱 구성
1. 맨 위 오른쪽 탐색에서 **관리**를 선택한 다음, 왼쪽 메뉴에서 **게시 설정**을 선택합니다.

2. **감정 분석**을 토글하여 이 설정을 사용 설정합니다. 

    ![](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>엔드포인트에서 발화 감정 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `Jill Jones work with the media team on the public portal was amazing`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 이 발언은 레이블이 지정된 발언과 같지 않으므로 유용한 테스트이며 감정 분석이 추출된 `EmployeeFeedback` 의도가 반환되어야 합니다.
    
    ```JSON
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

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 발화 전체에서 감정 값을 추출하기 위해 감정 분석을 게시 설정으로 추가합니다.

> [!div class="nextstepaction"] 
> [HR 앱에서 엔드포인트 발언 검토](luis-tutorial-review-endpoint-utterances.md) 

