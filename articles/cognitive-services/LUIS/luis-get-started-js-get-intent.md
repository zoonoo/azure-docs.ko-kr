---
title: Node.js를 사용하여 LUIS(Language Understanding) 앱을 호출하는 방법을 알아보는 자습서 | Microsoft Docs
description: 이 자습서에서는 Node.js를 사용하여 LUIS 앱을 호출하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 62c6816e753ee2c29aee0b8f68dec0ebd9f4be44
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265407"
---
# <a name="tutorial-call-a-luis-endpoint-using-javascript"></a>자습서: JavaScript를 사용하여 LUIS 엔드포인트 호출
발화를 LUIS 엔드포인트로 전달하고 의도와 엔터티를 다시 가져옵니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * LUIS 구독 만들기 및 나중에 사용할 키 값 복사
> * 공개 IoT 앱 샘플에서 브라우저의 LUIS 엔드포인트 결과 보기
> * LUIS 엔드포인트에 대한 HTTPS 호출을 수행하는 Visual Studio C# 콘솔 앱 만들기

이 문서에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="create-luis-subscription-key"></a>LUIS 구독 키 만들기
이 연습에서 사용하는 LUIS 앱 샘플을 호출하기 위해 Cognitive Services API 키가 필요합니다. 

API 키를 가져오려면 다음 단계를 수행합니다. 

1. 먼저 Azure Portal에서 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만들어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

2. Azure Portal ( https://portal.azure.com ) 에 로그인합니다. 

3. [Azure를 사용하여 구독 키 만들기](./luis-how-to-azure-subscription.md)의 단계에 따라 키를 가져옵니다.

4. [LUIS](luis-reference-regions.md) 웹 사이트로 돌아가서 Azure 계정을 사용하여 로그인합니다. 

    [![](media/luis-get-started-node-get-intent/app-list.png "앱 목록 만들기 스크린샷")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>LUIS에서 반환하는 결과에 대한 이해

LUIS 앱에서 무엇을 반환하는지 파악하기 위해 LUIS 앱 샘플의 URL을 브라우저 창에 붙여넣을 수 있습니다. 샘플 앱은 사용자가 조명을 켜거나 끄려는지 여부를 검색하는 IoT 앱입니다.

1. 샘플 앱의 엔드포인트는 `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` 형식입니다. URL을 복사하고 구독 키를 `subscription-key` 필드의 값으로 바꿉니다.

2. URL을 브라우저 창에 붙여넣고 Enter 키를 누릅니다. LUIS에서 `HomeAutomation.TurnOn` 의도 및 `bedroom` 값이 있는 `HomeAutomation.Room` 엔터티를 검색한다고 나타내는 JSON 결과가 브라우저에 표시됩니다.

    ![JSON 결과에서 TurnOn 의도 검색](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)

3. URL의 `q=` 매개 변수 값을 `turn off the living room light`로 변경하고 Enter 키를 누릅니다. 이제 결과에는 LUIS에서 `HomeAutomation.TurnOff` 의도 및 `living room` 값이 있는 `HomeAutomation.Room` 엔터티를 검색했다고 표시됩니다. 

    ![JSON 결과에서 TurnOff 의도 검색](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-javascript"></a>JavaScript에서 엔드포인트 API를 통해 LUIS 결과 사용 

JavaScript를 사용하여 이전 단계의 브라우저 창에 표시된 것과 동일한 결과에 액세스할 수 있습니다. 
1. 다음 코드를 복사하여 HTML 파일에 저장합니다.

   [!code-javascript[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/javascript/call-endpoint.html)]
2. `"YOUR SUBSCRIPTION KEY"`를 이 코드 줄(`xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","YOUR SUBSCRIPTION KEY");`)의 구독 키로 바꿉니다.

3. 웹 브라우저를 사용하여 저장된 파일을 엽니다.  `Detected the following intent: TurnOn`이라고 말하는 경고 창이 표시됩니다.

![TurnOn이라고 말하는 팝업](./media/luis-get-started-node-get-intent/popup-turn-on.png)

## <a name="clean-up-resources"></a>리소스 정리
이 자습서에서 만든 두 개의 리소스는 LUIS 구독 키와 C# 프로젝트입니다. Azure Portal에서 LUIS 구독 키를 삭제합니다. Visual Studio 프로젝트를 닫고 파일 시스템에서 해당 디렉터리를 제거합니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [발언 추가](luis-get-started-javascript-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website