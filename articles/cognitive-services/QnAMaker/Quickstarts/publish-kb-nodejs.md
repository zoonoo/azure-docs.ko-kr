---
title: '빠른 시작: 기술 자료 게시 - REST, Node.js - QnA Maker'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 KB(기술 자료)를 프로그래밍 방식으로 게시하는 방법을 안내합니다. 게시는 최신 버전의 기술 자료를 전용 Azure Search 인덱스에 푸시하고, 응용 프로그램 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: diberry
ms.openlocfilehash: c70b90a6e465c72193f63afd7ab9106579e2c634
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886613"
---
# <a name="quickstart-publish-a-qna-maker-knowledge-base-in-nodejs"></a>빠른 시작: Node.js에서 QnA Maker 기술 자료 게시

이 빠른 시작에서는 KB(기술 자료)를 프로그래밍 방식으로 게시하는 방법을 안내합니다. 게시는 최신 버전의 기술 자료를 전용 Azure Search 인덱스에 푸시하고, 응용 프로그램 또는 챗봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [게시](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 이 API는 요청 본문에 어떤 정보도 요구하지 않습니다.

## <a name="prerequisites"></a>필수 조건

* [Node.js 6+](https://nodejs.org/en/download/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키를 검색하려면 대시보드의 **리소스 관리** 아래에서 **키**를 선택합니다. 
* QnA Maker KB(기술 자료) ID는 아래와 같이 kbid 쿼리 문자열 매개 변수의 URL에 있습니다.

    ![QnA Maker 기술 자료 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

아직 기술 자료가 없는 경우 샘플을 만들어서 빠른 시작: [새 기술 자료 만들기](create-new-kb-nodejs.md)에서 사용하면 됩니다.

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>기술 자료 만들기 Node.js 파일

`publish-knowledge-base.js`라는 파일을 만듭니다.

## <a name="add-required-dependencies"></a>필요한 종속성 추가

`publish-knowledge-base.js`의 맨 위에 프로젝트에 필요한 종속성을 추가하는 다음 줄을 추가합니다.

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>필요한 상수 추가

앞서의 필요한 종속성 뒤에 QnA Maker에 액세스하는 데 필요한 상수를 추가합니다. `subscriptionKey` 변수의 값을 사용자 고유의 QnA Maker 키로 바꿉니다. 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>기술 자료 ID 추가

앞서의 상수 뒤에 기술 자료 ID를 추가하고 경로에 이 ID를 추가합니다.

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-supporting-functions"></a>지원하는 함수 추가

다음으로, 지원하는 다음 함수를 추가합니다.

1. JSON을 읽을 수 있는 형식으로 출력하는 다음 함수를 추가합니다.

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=25-28 "Add supporting functions, step 1")]

2. HTTP 응답을 관리하여 만들기 작업 상태를 가져오는 다음 함수를 추가합니다.

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=30-52 "Add supporting functions, step 2")]

## <a name="add-the-publishkb-function-and-main-function"></a>publish_kb 함수 및 main 함수 추가

다음 코드에서는 KB를 게시하도록 QnA Maker API에 HTTPS 요청을 하고 응답을 받습니다.

[!code-nodejs[Add POST request to publish KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=54-71 "Add POST request to publish KB")]

[!code-nodejs[Add the publish_kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=73-91 "Add the publish_kb function and main function")]

## <a name="add-the-main-function"></a>main 함수 추가

요청과 응답을 관리하는 다음 함수를 추가합니다.

[!code-nodejs[Add the main function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base/publish-knowledge-base.js?range=94-97 "Add the main function")]

## <a name="run-the-program"></a>프로그램 실행

프로그램을 빌드하고 실행합니다. 그러면 자동으로 KB를 게시하기 위한 요청을 QnA Maker API에 보낸 다음, 응답이 콘솔 창에 출력됩니다.

기술 자료가 게시되면 클라이언트 응용 프로그램 또는 챗봇을 사용하여 엔드포인트에서 쿼리할 수 있습니다. 

```bash
node publish-knowledge-base.js
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)