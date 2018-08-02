---
title: Language Understanding에서 미리 빌드된 의도 및 엔터티를 추가하여 일반적인 데이터 추출 - Azure | Microsoft Docs
description: 미리 빌드된 의도 및 엔터티를 사용하여 다양한 형식의 엔터티 데이터를 추출하는 방법에 대해 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 0ec6f002b35b1224118b62accda1f69e7be22fb8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358525"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>자습서: 2. 미리 작성된 의도 및 엔터티 추가
미리 빌드된 의도 및 엔터티를 인사 관리 자습서 앱에 추가하여 의도 예측 및 데이터 추출을 빠르게 수행합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
* 미리 빌드된 의도 추가 
* 미리 빌드된 엔터티 datetimeV2 및 number 추가
* 학습 및 게시
* LUIS 쿼리 및 예측 응답 받기

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>시작하기 전에
이전 엔터티 자습서의 [인사 관리](luis-quickstart-intents-only.md) 앱이 없으면 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) Github 리포지토리의 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app).

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `prebuilts`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

## <a name="add-prebuilt-intents"></a>미리 빌드된 의도 추가
LUIS는 일반적인 사용자 의도에 도움이 되도록 여러 가지 미리 빌드된 의도를 제공합니다.  

1. 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

2. **Add prebuilt domain intent**(미리 빌드된 도메인 의도 추가)를 선택합니다. 

    [ ![미리 빌드된 도메인 의도 추가 단추가 강조 표시된 의도 페이지의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. `Utilities`를 검색합니다. 

    [ ![검색 상자에 Utilities가 입력된 미리 빌드된 의도 대화 상자의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 다음 의도를 선택하고 **완료**를 선택합니다. 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>미리 빌드된 엔터티 추가
LUIS는 일반적인 데이터 추출을 위해 여러 가지 미리 빌드된 엔터티를 제공합니다. 

1. 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다.

    [ ![왼쪽 탐색에 엔터티가 강조 표시된 의도 목록의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. **미리 빌드된 엔터티 관리** 단추를 선택합니다.

    [ ![미리 빌드된 엔터티 관리가 강조 표시된 엔터티 목록의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. 미리 빌드된 엔터티 목록에서 **number** 및 **datetimeV2**를 선택하고 **완료**를 선택합니다.

    ![number가 선택된 미리 빌드된 엔터티 대화 상자의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>앱 학습 및 게시

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>엔드포인트에 앱 게시

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-endpoint-with-an-utterance"></a>발화를 사용하여 끝점 쿼리

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `I want to cancel on March 3`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`로 발화 **쿼리**입니다. 

    결과에서는 Utilities.Cancel 의도를 예측하고 March 3 날짜와 숫자 3을 추출했습니다. 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    3월 3일이 과거 또는 미래에 있을 때 발언이 언급되지 않았기 때문에 3월 3일에는 두 가지 값이 있습니다. 가정하거나 필요할 경우 설명을 요청하는 것은 LUIS 호출 응용 프로그램에 달렸습니다. 

    미리 빌드된 의도 및 엔터티를 쉽고 빠르게 추가하면 클라이언트 응용 프로그램에서 대화 관리를 추가하고 일반적인 데이터 형식을 추출할 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱에 정규식 엔터티 추가](luis-quickstart-intents-regex-entity.md)

