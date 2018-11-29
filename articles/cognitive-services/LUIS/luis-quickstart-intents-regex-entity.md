---
title: '자습서 3: 정규식 일치 데이터 - 적절한 형식의 데이터 추출'
titleSuffix: Azure Cognitive Services
description: 정규식 엔터티를 사용하여 발언에서 일관적인 형식의 데이터를 추출합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 5e8345deb629d293d9673819893181e652d5dbb9
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423420"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>자습서 3: 적절한 형식의 데이터 추출
이 자습서에서는 **정규식** 엔터티를 사용하여 발언에서 일관적인 형식의 데이터를 추출하도록 인사 관리 앱을 수정합니다.

엔터티의 목적은 발언에 포함된 중요한 데이터를 추출하는 것입니다. 이 앱이 정규식 엔터티를 사용하는 것은 발언에서 형식이 지정된 HR(인사 관리) 양식 번호를 추출하는 것입니다. 발언의 의도는 언제나 기계 학습을 사용하여 확인되지만, 이 특정 엔터티 형식에는 기계 학습이 적용되지 않습니다. 

**다음은 발언 예제입니다.**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
다음과 같은 경우 정규식은 이러한 종류의 데이터에 적합한 선택입니다.

* 데이터 형식이 적절합니다.

**이 자습서에서는 다음 방법에 대해 알아봅니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 기존 자습서 앱 사용
> * FindForm 의도 추가
> * 정규식 엔터티 추가 
> * 학습
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>기존 앱 사용
마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

이전 자습서의 HumanResources 앱이 없으면 다음 단계를 사용합니다.

1. [앱 JSON 파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `regex`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다. 

## <a name="findform-intent"></a>FindForm 의도

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **새 의도 만들기**를 선택합니다. 

3. 팝업 대화 상자에서 `FindForm`을 입력하고 **완료**를 선택합니다. 

    ![검색 상자에서 유틸리티가 입력된 새 의도 만들기 대화 상자의 스크린샷](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. 의도에 발화 예제를 추가합니다.

    |발화 예제|
    |--|
    |hrf-123456에 대한 URL은 무엇인가요?|
    |hrf-345678은 어디에 있나요?|
    |hrf-456098은 언제 업데이트되었나요?|
    |지난주 John Smith가 hrf-234639를 업데이트했나요?|
    |hrf-345123 버전의 수는 몇 개인가요?|
    |hrf-123456 양식에 대한 권한을 부여해야 하는 사람은 누구인가요?|
    |hrf-345678에서 로그오프해야 사용자의 수는 얼마인가요?|
    |hrf-234123의 날짜는?|
    |hrf-546234의 작성자는?|
    |hrf-456234의 제목은?|

    [ ![새 발화가 강조 표시된 의도 페이지의 스크린샷](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    응용 프로그램에는 이전 자습서에서 추가된 미리 작성된 번호 엔터티가 있으므로 각 양식 번호에 태그가 지정됩니다. 이는 클라이언트 응용 프로그램에 대해 충분하지만 번호에 숫자 형식의 레이블이 지정되지 않습니다. 적절한 이름으로 새 엔터티를 만들면 LUIS에서 반환할 때 클라이언트 응용 프로그램에서 해당 엔터티를 적절히 처리할 수 있습니다.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>정규식 엔터티 
양식 번호와 일치하는 정규식 엔터티는 `hrf-[0-9]{6}`입니다. 이 정규식은 `hrf-` 리터럴 문자와 일치하지만 대/소문자 및 문화권 변형을 무시합니다. 6자리 숫자에 대해 0에서 9까지의 숫자와 정확하게 일치합니다.

HRF는 `human resources form`을 의미합니다.

발화가 의도에 추가되면 LUIS가 해당 발화를 토큰화합니다. 이러한 발화에 대한 토큰화에서는 하이픈의 앞뒤에 공백이 추가됩니다(예: `Where is HRF - 123456?`). 정규식은 토큰화되기 전에 원시 양식으로 발화에 적용됩니다. _원시_ 양식에 적용되므로 정규식에서 단어 경계를 처리할 필요가 없습니다. 

다음 단계에서는 정규식 엔터티를 만들어 LUIS에 HRF 번호 형식을 알려줍니다.

1. 왼쪽 패널에서 **엔터티**를 선택합니다.

2. [엔터티] 페이지에서 **새 엔터티 만들기** 단추를 선택합니다. 

3. 팝업 대화 상자에서 `HRF-number`라는 새 엔터티 이름을 입력하고, 엔터티 형식으로 **RegEx**를 선택하고, **Regex** 값으로 `hrf-[0-9]{6}`을 입력한 다음, **완료**를 선택합니다.

    ![새 엔터티 속성이 설정된 팝업 대화 상자의 스크린샷](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. 왼쪽 메뉴에서 **의도**를 선택한 다음, **FindForm** 의도를 선택하여 발화에 레이블이 지정된 정규식을 확인합니다. 

    [![기존 엔터티 및 정규식 패턴이 사용된 발화 레이블 지정의 스크린샷](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    엔터티는 기계 학습 엔터티가 아니므로 레이블이 발화에 적용되고 만들어지는 즉시 LUIS 웹 사이트에 표시됩니다.

## <a name="train"></a>학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `When were HRF-123456 and hrf-234567 published in the last year?`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발화 **쿼리**를 나타내는 `q`입니다. 이 발화는 레이블이 지정된 발화와 같지 않으므로 유용한 테스트이며, 두 양식 번호(`HRF-123456` 및 `hrf-234567`)가 있는 `FindForm` 의도가 반환되어야 합니다.

    ```JSON
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    발화의 숫자는 두 번, 즉 한 번은 새로운 `hrf-number` 엔터티로, 그리고 한 번은 미리 작성된 `number` 엔터티로 반환됩니다. 발화에는 이 예제와 같이 둘 이상의 엔터티 및 둘 이상의 동일한 형식의 엔터티가 있을 수 있습니다. LUIS는 정규식 엔터티를 사용하여 명명된 데이터를 추출합니다. 이 데이터는 프로그래밍 방식으로 JSON 응답을 받는 클라이언트 응용 프로그램에 더 유용합니다.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 새 의도를 만들고, 예제 발언을 추가하고, 발언에서 적절한 형식의 데이터를 추출하는 정규식 엔터티를 만들었습니다. 교육 및 앱 게시를 마친 후 엔드포인트 쿼리가 의도를 식별하고 추출된 데이터를 반환했습니다.

> [!div class="nextstepaction"]
> [목록 엔터티에 대해 알아보기](luis-quickstart-intent-and-list-entity.md)

