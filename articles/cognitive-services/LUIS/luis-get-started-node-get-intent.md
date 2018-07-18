---
title: Node.js를 사용하여 LUIS(Language Understanding Intelligent Services) 앱을 호출하는 방법을 알아보는 자습서 | Microsoft Docs
description: 이 자습서에서는 Node.js를 사용하여 LUIS 앱을 호출하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5e9df272be3c48602d38f551a791376c043bf409
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263655"
---
# <a name="tutorial-call-a-luis-endpoint-using-nodejs"></a>자습서: Node.js를 사용하여 LUIS 엔드포인트 호출
발언을 LUIS 엔드포인트로 전달하고 의도와 엔터티를 다시 가져옵니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * LUIS 구독 만들기 및 나중에 사용할 키 값 복사
> * 공개 IoT 앱 샘플에서 브라우저의 LUIS 엔드포인트 결과 보기
> * HTTP 엔드포인트에 대한 HTTPS 호출을 수행하는 Visual Studio C# 콘솔 앱 만들기

이 아티클에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="create-luis-subscription-key"></a>LUIS 구독 키 만들기
이 연습에서 사용하는 LUIS 앱 샘플을 호출하기 위해 Cognitive Services API 키가 필요합니다. 

API 키를 가져오려면 다음 단계를 수행합니다. 

1. 먼저 Azure Portal에서 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만들어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

2. Azure Portal ( https://portal.azure.com ) 에 로그인합니다. 

3. [Azure를 사용하여 구독 키 만들기](./luis-how-to-azure-subscription.md)의 단계에 따라 키를 가져옵니다.

4. [LUIS](luis-reference-regions.md) 웹 사이트로 돌아가서 Azure 계정을 사용하여 로그인합니다. 

    [![](media/luis-get-started-node-get-intent/app-list.png "앱 목록 스크린샷")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>LUIS에서 반환하는 결과에 대한 이해

LUIS 앱에서 무엇을 반환하는지 파악하기 위해 LUIS 앱 샘플의 URL을 브라우저 창에 붙여넣을 수 있습니다. 샘플 앱은 사용자가 라이트를 켜는지 여부를 검색하는 IoT 앱입니다.

1. 샘플 앱의 엔드포인트는 `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` 형식으로입니다. URL을 복사하고 `subscription-key` 필드의 값을 구독 키의 값으로 대체합니다.
2. 브라우저 창에 URL을 붙여넣고 Enter 키를 누릅니다. LUIS에서 `HomeAutomation.TurnOn` 의도 및 `bedroom` 값이 있는 `HomeAutomation.Room` 엔터티를 검색한다고 나타내는 JSON 결과가 브라우저에 표시됩니다.

    ![JSON 결과에서 TurnOn 의도 검색](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. URL에서 `q=` 매개 변수 값을 `turn off the living room light`로 변경하고 Enter 키를 누릅니다. 이제 결과에는 LUIS에서 `HomeAutomation.TurnOff` 의도 및 `living room` 값이 있는 `HomeAutomation.Room` 엔터티를 검색했다고 표시됩니다. 

    ![JSON 결과에서 TurnOff 의도 검색](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-nodejs"></a>Node.js에서 엔드포인트 API를 사용하여 LUIS 결과 사용

Node.js를 사용하여 이전 단계의 브라우저 창에서 본 것과 동일한 결과에 액세스할 수 있습니다.

1. 다음 코드 조각을 복사합니다.

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/endpoint-api-samples/node/call-endpoint.js)]

2. 코드 주석에 설명된 대로 `LUIS_APP_ID` 환경 변수를 설정합니다. 

3. `LUIS_SUBSCRIPTION_KEY` 환경 변수를 Cognitive Services 등록 키로 설정합니다.

4. 코드를 실행합니다. 브라우저 창에서 앞서 본 것과 동일한 값을 표시합니다.
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-Node.js-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>리소스 정리
이 자습서에서 만든 두 개의 리소스는 LUIS 구독 키와 C# 프로젝트입니다. Azure Portal에서 LUIS 구독 키를 삭제합니다. Visual Studio 프로젝트를 닫고 파일 시스템에서 해당 디렉터리를 제거합니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [발언 추가](luis-get-started-node-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website