---
title: '빠른 시작: 기술 자료 업데이트 - REST, Node.js - QnA Maker'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 기존 QnA Maker KB(기술 자료)를 프로그래밍 방식으로 업데이트하는 방법을 안내합니다.  이 JSON을 사용하면 데이터 원본을 새로 추가, 변경 또는 삭제하여 KB를 업데이트할 수 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 1b92589fe01f171b732c04057dc290f0f32cb31a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647819"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-nodejs"></a>빠른 시작: Node.js를 사용하여 QnA Maker 기술 자료 업데이트

이 빠른 시작에서는 기존 QnA Maker KB(기술 자료)를 프로그래밍 방식으로 업데이트하는 방법을 안내합니다.  이 JSON을 사용하면 데이터 원본을 새로 추가, 변경 또는 삭제하여 KB를 업데이트할 수 있습니다.

이 API는 QnA Maker 포털에서 편집한 다음, **저장 및 학습** 단추를 사용하는 것과 같습니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [업데이트](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) - 기술 자료 모델은 API 요청 본문에 전송된 JSON에 정의됩니다. 
* [작업 세부 정보 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>필수 조건

* [Node.js 6+](https://nodejs.org/en/download/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키를 검색하려면 대시보드의 **리소스 관리** 아래에서 **키**를 선택합니다. 
* QnA Maker KB(기술 자료) ID는 아래와 같이 kbid 쿼리 문자열 매개 변수의 URL에 있습니다.

    ![QnA Maker 기술 자료 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

아직 기술 자료가 없는 경우 샘플을 만들어서 빠른 시작: [새 기술 자료 만들기](create-new-kb-nodejs.md)에서 사용하면 됩니다.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>기술 자료 만들기 Node.js 파일

`update-knowledge-base.js`라는 파일을 만듭니다.

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

`update-knowledge-base.js`의 맨 위에 프로젝트에 필요한 종속성을 추가하는 다음 줄을 추가합니다.

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>필요한 상수 추가
앞서의 필요한 종속성 뒤에 QnA Maker에 액세스하는 데 필요한 상수를 추가합니다. `subscriptionKey` 변수의 값을 사용자 고유의 QnA Maker 키로 바꿉니다. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>기술 자료 ID 추가

앞서의 상수 뒤에 기술 자료 ID를 추가하고 경로에 이 ID를 추가합니다.

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>KB 업데이트 모델 정의 추가

다음 KB 업데이트 정의를 상수 뒤에 추가합니다. 업데이트 정의에는 다음 세 개의 섹션이 있습니다.

* 추가
* update
* delete

각 섹션은 API에 대한 동일한 단일 요청에 사용할 수 있습니다. 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>지원하는 함수 추가

다음으로, 지원하는 다음 함수를 추가합니다.

1. JSON을 읽을 수 있는 형식으로 출력하는 다음 함수를 추가합니다.

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. HTTP 응답을 관리하여 만들기 작업 상태를 가져오는 다음 함수를 추가합니다.

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>KB를 업데이트하기 위한 PATCH 요청 추가

기술 자료를 업데이트하도록 HTTP PATCH 요청을 하는 다음 함수를 추가합니다. `Ocp-Apim-Subscription-Key`는 인증에 사용되는 QnA Maker 서비스 키입니다.

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

다음 API 호출은 작업 ID를 포함한 JSON 응답을 반환합니다. 작업 ID는 작업이 완료되지 않은 경우 상태를 요청하는 데 필요합니다.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>작업 상태를 확인하기 위한 GET 요청 추가

작업의 상태를 확인합니다.
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

다음 API 호출은 작업 상태를 포함한 JSON 응답을 반환합니다. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

성공하거나 실패할 때까지 호출을 반복합니다. 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>update_kb 메서드 추가

다음 메서드는 KB를 업데이트하고 상태 확인을 반복합니다. KB를 만드는 데 시간이 걸릴 수 있으므로 상태가 성공 또는 실패일 때까지 상태를 확인하기 위한 호출을 반복해야 합니다.

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>프로그램 실행

프로그램을 실행하려면 명령줄에 다음 명령을 입력합니다. 그러면 KB를 업데이트하기 위한 요청을 QnA Maker API에 보낸 다음, 30초마다 결과를 폴링합니다. 각 응답이 콘솔 창에 출력됩니다.

```bash
node update-knowledge-base.js
```

기술 자료가 업데이트되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 해당 기술 자료를 볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)