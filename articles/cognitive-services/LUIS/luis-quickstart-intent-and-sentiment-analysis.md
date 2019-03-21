---
title: 정서 분석
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 발언에서 긍정, 부정 및 중립적 감정을 추출하는 방법을 보여주는 앱을 만듭니다. 감정은 전체 발화에서 판별됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0e61f6a914c33842f4f42b2e1e4206b370a11dd4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099038"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>자습서:  발언의 감정 가져오기

이 자습서에서는 발언에서 긍정, 부정 및 중립적 감정을 확인하는 방법을 보여주는 앱을 만듭니다. 감정은 전체 발화에서 판별됩니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 새 앱 만들기
> * 게시 설정으로 감정 분석 추가
> * 앱 교육
> * 앱 게시
> * 엔드포인트에서 발화 감정 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>감정 분석은 게시 설정

다음 발언에서는 감정의 예제를 보여줍니다.

|데이터|Score|발화|
|:--|:--|:--|
|긍정|0.91 |John W. Smith는 파리에서 훌륭한 프레젠테이션을 했습니다.|
|긍정|0.84 |시애틀 엔지니어들은 Parker 판매 권유 작업을 훌륭히 해냈습니다.|

감정 분석은 모든 발화에 적용되는 게시 설정입니다. 발언에서 감정을 나타내는 단어를 찾아서 표시하지 않아도 됩니다. 

게시 설정이므로, 의도 또는 엔터티 페이지에는 보이지 않고 [대화형 테스트](luis-interactive-test.md#view-sentiment-results) 창이나, 엔드포인트 URL에서 테스트할 때 볼 수 있습니다. 


## <a name="create-a-new-app"></a>새 앱 만들기

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>PersonName 미리 빌드된 엔터티 추가 


1. 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다.

1. **미리 빌드된 엔터티 추가** 단추를 선택합니다.

1. 미리 빌드된 엔터티 목록에서 다음 엔터티를 선택하고 **완료**를 선택합니다.

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![number가 선택된 미리 빌드된 엔터티 대화 상자의 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>직원 피드백을 확인하는 의도 만들기

회사의 구성원에게서 직원 피드백을 캡처하기 위한 새 의도를 추가합니다. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **새 의도 만들기**를 선택합니다.

3. 새 의도 이름 `EmployeeFeedback`을 명명합니다.

    ![이름으로 EmployeeFeedback이 있는 의도 대화 상자 새로 만들기](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 개선이 필요한 영역 또는 제대로 작업을 수행하는 직원을 나타내는 여러 발언을 추가합니다.

    |발언|
    |--|
    |John Smith는 동료의 출산 휴가 복귀를 환영하는 멋진 일을 했습니다.|
    |Jill Jones는 슬픔에 빠진 동료를 위로하는 대단한 일을 했습니다.|
    |Bob Barnes는 서류 작업에 필요한 청구서를 모두 갖고 있지는 않았습니다.|
    |Todd Thomas는 필수 양식을 한 달 늦게 서명이 빠진 채로 제출했습니다.|
    |Katherine Kelly는 중요한 현장 마케팅 회의에 제때 도착하지 못했습니다.|
    |Denise Dillard는 6월 검토 회의에 불참했습니다.|
    |Mark Mathews는 하버드에서 판매 권유에 박차를 가했습니다.|
    |Walter Williams는 스탠포드에서 훌륭한 프레젠테이션을 했습니다.|

    [![EmployeeFeedback 의도에서 예제 발화가 포함된 LUIS 앱의 스크린샷](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>None 의도에 예제 발언 추가 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>의도에 대한 변경 내용을 테스트할 수 있도록 앱 학습시키기 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>감정 분석을 포함하도록 앱 구성

1. 맨 위 오른쪽 탐색에서 **관리**를 선택한 다음, 왼쪽 메뉴에서 **게시 설정**을 선택합니다.

1. **감정 분석**을 선택하여 이 설정을 사용합니다. 

    ![게시 설정으로 감정 분석 켜기](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>학습된 모델을 엔드포인트에서 쿼리할 수 있도록 앱 게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>엔드포인트에서 발언의 감정 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 주소의 URL 끝으로 이동하고 `Jill Jones work with the media team on the public portal was amazing`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 이 발언은 레이블이 지정된 발언과 같지 않으므로 유용한 테스트이며 감정 분석이 추출된 `EmployeeFeedback` 의도가 반환되어야 합니다.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
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

    sentimentAnalysis는 점수 86%로 긍정적입니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>관련 정보

* 감정 분석은 Cognitive Service [Text Analytics](../Text-Analytics/index.yml)에서 제공합니다. 기능은 Text Analytics [지원 언어](luis-language-support.md##languages-supported)로 제한됩니다.
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 발화 전체에서 감정 값을 추출하기 위해 감정 분석을 게시 설정으로 추가합니다.

> [!div class="nextstepaction"] 
> [HR 앱에서 엔드포인트 발언 검토](luis-tutorial-review-endpoint-utterances.md) 

