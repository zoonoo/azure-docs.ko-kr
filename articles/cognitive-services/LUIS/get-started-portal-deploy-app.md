---
title: '빠른 시작: LUIS 포털에서 앱 배포'
titleSuffix: Language Understanding - Azure Cognitive Services
description: 앱이 챗봇 같은 클라이언트 애플리케이션으로 발화 예측을 반환할 준비가 된 후에 예측 엔드포인트에 LUIS 앱을 배포하는 방법을 알아봅니다. 이 빠른 시작에서는 예측 엔드포인트 리소스를 만들고, 앱에 리소스를 할당하고, 앱을 학습시키고, 앱을 게시하여 애플리케이션을 배포하는 방법을 배웁니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: db2b543fa8e5429cc8d50d7789b03239173f563d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154507"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>빠른 시작: LUIS 포털에서 앱 배포

LUIS 앱이 챗봇 같은 클라이언트 애플리케이션으로 발화 예측을 반환할 준비가 되면 예측 엔드포인트에 앱을 배포해야 합니다.

이 빠른 시작에서는 애플리케이션을 배포하는 것에 대해 알아봅니다. 예측 엔드포인트 리소스를 만들고, 앱에 리소스를 할당하고, 앱을 학습시키고, 앱을 게시합니다.

## <a name="prerequisites"></a>필수 조건

* [Azure 구독](https://azure.microsoft.com/free)을 가져옵니다.
* [이전 포털 빠른 시작](get-started-portal-build-app.md)을 완료하거나 [앱을 다운로드하고 가져옵니다](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>엔드포인트 리소스 만들기

예측 엔드포인트 리소스를 Azure Portal에서 만듭니다. 이 리소스는 엔드포인트 예측 쿼리에만 사용해야 합니다. 앱을 변경하는 작업에는 이 리소스를 사용하지 마세요.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.

1. 왼쪽 위 패널에 있는 녹색 **+** 기호를 선택합니다. Marketplace에서 `Cognitive Services`를 검색하고 선택합니다.

1. 다음 설정으로 구독을 구성합니다.

   |설정|값|목적|
   |--|--|--|
   |Name|`my-cognitive-service-resource`|Azure 리소스의 이름입니다. LUIS 포털에서 앱에 리소스를 할당할 때 이 이름이 필요합니다.|
   |구독|사용자의 구독|계정과 연결된 구독 중 하나를 선택합니다.|
   |위치|**미국 서부**|이 리소스의 Azure 지역입니다.|
   |가격 책정 계층 |**S0**|이 리소스의 기본 가격 책정 계층입니다.|
   |리소스 그룹|`my-cognitive-service-resource-group`|모든 인식 서비스 리소스에 대한 새 리소스 그룹을 만듭니다. 리소스 사용을 마친 후에는 리소스 그룹을 삭제하여 구독을 정리할 수 있습니다. |
   | | | |

   ![Azure API 선택](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. **만들기**를 선택하여 Azure 리소스를 만듭니다.

   다음 섹션에서는 LUIS 포털에서 이 새 리소스를 LUIS 앱에 연결하는 방법을 보여줍니다.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>LUIS 포털에서 LUIS 앱에 리소스 키 할당

LUIS에 대한 새 리소스를 만들 때마다 LUIS 앱에 리소스를 할당해야 합니다. 리소스가 할당되면 새 리소스를 만들지 않는 이상 이 단계를 다시 수행할 필요가 없습니다. 앱의 영역을 확장하거나 더 많은 예측 쿼리를 지원하기 위해 새 리소스를 만들어야 하는 경우가 있습니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고, 앱 목록에서 **myEnglishApp** 앱을 선택합니다.

1. 오른쪽 상단 메뉴에서 **관리**를 선택한 다음, **키 및 엔드포인트**를 선택합니다.

1. LUIS를 추가하려면 **리소스 할당 +** 을 선택합니다.

   [![앱에 리소스 할당](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. 테넌트, 구독 및 리소스 이름을 선택합니다. **리소스 할당**을 선택합니다.

   ![앱에 리소스 할당](./media/get-started-portal-deploy-app/assign-resource.png)

1. 표에서 새 행을 찾고 엔드포인트 URL을 복사합니다. 예측을 위한 LUIS API 엔드포인트에 `HTTP GET`을 요청할 수 있도록 올바르게 구성되어 있습니다.

## <a name="train-and-publish-the-app"></a>앱 학습 및 게시

테스트할 준비가 되면 앱을 학습시킵니다. 학습된 현재 버전을 쿼리 예측 엔드포인트 런타임의 클라이언트 애플리케이션에서 사용할 수 있게 하려면 앱을 게시합니다.

1. 앱이 학습되지 않은 경우 오른쪽 위 메뉴에서 **학습**을 선택합니다.

1. 상단 메뉴에서 **게시**를 선택합니다. 기본 환경 설정을 수락하고, **게시**를 선택합니다.

1. 브라우저 창의 맨 위에 녹색 성공 알림 표시줄이 나타나면 **엔드포인트 목록 참조**를 선택합니다.

   ![브라우저의 앱 게시 성공 알림 표시줄](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. **키 및 엔드포인트 설정** 페이지에서 맨 아래를 보면 할당된 리소스 및 해당 엔드포인트 URL의 목록이 있습니다.

1. 새 리소스 이름과 연결된 엔드포인트 URL을 선택합니다. 그러면 예측 엔드포인트 런타임에 `GET`을 요청하도록 올바르게 구성된 URL이 포함된 웹 브라우저가 열립니다.

1. URL 끝부분의 `q=`는 **쿼리**를 의미하는 약어이며 사용자의 발화가 GET 요청에 추가되는 위치입니다. 이전 빠른 시작의 끝부분에 사용한 것과 동일한 사용자 발화를 `q=` 뒤에 입력합니다.

    ```Is there a form named hrf-234098```

    브라우저에 클라이언트 애플리케이션이 받게 될 JSON과 동일한 응답이 표시됩니다.

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    이 응답은 이전 자습서의 기본 테스트 창보다 더 자세한 정보를 제공합니다. 이와 동일한 수준의 정보를 테스트 창에 표시하려면 앱을 게시해야 합니다. 앱을 게시한 후 테스트 창에서 **게시된 버전과 비교**를 선택합니다. 게시된 테스트 창의 **JSON 보기 표시**를 사용하여 이전 단계와 동일한 JSON을 볼 수 있습니다. 이 방법으로 현재 작업 중인 앱과 엔드포인트에 게시된 앱을 비교할 수 있습니다.

    [![현재 편집 중인 앱 버전과 게시된 앱 버전 비교](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 마쳤으면 위쪽 탐색 메뉴에서 **내 앱**을 선택합니다. 목록에서 앱의 확인란을 선택한 다음, 목록 위의 상황에 맞는 도구 모음에서 **삭제**를 선택합니다.

[![내 앱 목록에서 앱 삭제](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [일반적인 의도 및 엔터티 식별](luis-tutorial-prebuilt-intents-entities.md)
