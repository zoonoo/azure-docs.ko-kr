---
title: '빠른 시작: 기술 자료 만들기 - REST, Go - QnA Maker'
titlesuffix: Azure Cognitive Services
description: 이 Go REST 기반 빠른 시작에서는 Cognitive Services API 계정의 Azure 대시보드에 표시될 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: a1f477dd02e048a3bfb77463c2d9857ee32fb8fb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235312"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>빠른 시작: Go를 사용하여 QnA Maker 기술 자료 만들기

이 빠른 시작에서는 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다. QnA Maker는 [데이터 원본](../Concepts/data-sources-supported.md)에서 반구조화된 콘텐츠(예: FAQ)의 질문과 답변을 자동으로 추출합니다. 기술 자료 모델은 API 요청 본문에 전송된 JSON에 정의됩니다. 

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [KB 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [작업 세부 정보 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>필수 조건

* [Go 1.10.1](https://golang.org/dl/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키를 검색하려면 대시보드의 **리소스 관리** 아래에서 **키**를 선택합니다. 

## <a name="create-a-knowledge-base-go-file"></a>기술 자료 Go 파일 만들기

`create-new-knowledge-base.go`라는 파일을 만듭니다.

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

`create-new-knowledge-base.go`의 맨 위에 프로젝트에 필요한 종속성을 추가하는 다음 줄을 추가합니다.

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>필요한 상수 추가
앞서의 필요한 종속성 뒤에 QnA Maker에 액세스하는 데 필요한 상수를 추가합니다. `subscriptionKey` 변수의 값을 사용자 고유의 QnA Maker 키로 바꿉니다.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>KB 모델 정의 추가
다음 KB 모델 정의를 상수 뒤에 추가합니다. 모델이 정의 뒤에 있는 문자열로 변환됩니다.

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>지원 구조 및 함수 추가

다음으로, 지원하는 다음 함수를 추가합니다.

1. HTTP 요청에 대한 구조를 추가합니다.

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. QnA Maker API에 대한 POST를 처리하는 다음 메서드를 추가합니다. 이 빠른 시작에서 POST는 QnA Maker에 KB 정의를 보내는 데 사용됩니다.

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. QnA Maker API에 대한 GET을 처리하는 다음 메서드를 추가합니다. 이 빠른 시작에서 GET은 만들기 작업의 상태를 확인하는 데 사용됩니다. 

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>KB를 만드는 함수 추가

기술 자료를 만들도록 HTTP POST 요청을 하는 다음 함수를 추가합니다. POST 응답 헤더 필드 **Location**에 _create_ **Operation ID**가 반환된 후 GET 요청의 경로 부분에 사용됩니다. `Ocp-Apim-Subscription-Key`는 인증에 사용되는 QnA Maker 서비스 키입니다. 

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

이 API 호출은 헤더 필드 **Location**에 ID를 포함하는 JSON 응답을 반환합니다. 작업 ID를 사용하여 KB가 성공적으로 만들어졌는지 결정합니다. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-get-status"></a>상태를 가져오는 함수 추가

작업 상태를 확인하도록 HTTP GET 요청을 하는 다음 함수를 추가합니다. `Ocp-Apim-Subscription-Key`는 인증에 사용되는 QnA Maker 서비스 키입니다. 

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>main 함수 추가

다음 함수는 main 함수이며, KB를 만들고 상태 확인을 반복합니다. KB를 만드는 데 시간이 걸릴 수 있으므로 상태가 성공 또는 실패일 때까지 상태를 확인하기 위한 호출을 반복해야 합니다.

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>프로그램 컴파일
파일을 컴파일하는 다음 명령을 실행합니다. 명령 프롬프트에는 빌드 성공에 대한 정보가 반환되지 않습니다.

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>프로그램 실행

프로그램을 실행하려면 명령줄에 다음 명령을 입력합니다. 그러면 KB를 만들기 위한 요청을 QnA Maker API에 보낸 다음, 30초마다 결과를 폴링합니다. 각 응답이 콘솔 창에 출력됩니다.

```bash
go run create-new-knowledge-base
```

기술 자료가 생성되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 볼 수 있습니다. 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)