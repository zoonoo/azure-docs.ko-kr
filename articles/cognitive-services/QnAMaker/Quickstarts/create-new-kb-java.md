---
title: '빠른 시작: 기술 자료 만들기 - REST, Java - QnA Maker'
titlesuffix: Azure Cognitive Services
description: 이 Java REST 기반 빠른 시작에서는 Cognitive Services API 계정의 Azure 대시보드에 표시될 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: diberry
ms.openlocfilehash: 85d5fa1cc036737497e276413985e0828e3b51f6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788713"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>빠른 시작: Java를 사용하여 QnA Maker 기술 자료 만들기

이 빠른 시작에서는 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다. QnA Maker는 [데이터 원본](../Concepts/data-sources-supported.md)에서 반구조화된 콘텐츠(예: FAQ)의 질문과 답변을 자동으로 추출합니다. 기술 자료 모델은 API 요청 본문에 전송된 JSON에 정의되어 있습니다.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="create-a-knowledge-base-file"></a>기술 자료 파일 만들기

`CreateKB.java` 파일 만들기

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

`CreateKB.java`의 맨 위에 프로젝트에 필요한 종속성을 추가하는 다음 줄을 추가합니다.

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=1-5 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>필요한 상수 추가
앞부분의 필수 종속성 뒤에서, QnA Maker에 액세스하는 데 필요한 `CreateKB` 클래스에 필수 상수를 추가합니다. `subscriptionKey` 변수의 값을 사용자 고유의 QnA Maker 키로 바꿉니다. 클래스를 종료하기 위해 마지막 중괄호를 추가할 필요가 없습니다. 이 빠른 시작의 마지막에 나오는 최종 코드 조각입니다.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=26-34 "Add the required constants")]


## <a name="add-the-kb-model-definition-classes"></a>KB 모델 정의 클래스 추가
상수 뒤에서, 모델 정의 개체를 JSON으로 직렬화하는 다음 클래스 및 함수를 `CreateKB` 클래스 내부에 추가합니다.

[!code-java[Add the KB model definition classes](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=36-80 "Add the KB model definition classes")]

## <a name="add-supporting-functions"></a>지원하는 함수 추가

`CreateKB` 함수 내부에 다음 지원 함수를 추가합니다.

1. JSON을 읽을 수 있는 형식으로 출력하는 다음 함수를 추가합니다.

    [!code-java[Add the PrettyPrint function](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=82-87 "Add the KB model definition classes")]

2. HTTP 응답을 관리하는 다음 클래스를 추가합니다.

    [!code-java[Add class to manage the HTTP response](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=89-97 "Add class to manage the HTTP response")]

3. QnA Maker API에 대한 POST 요청을 만드는 다음 메서드를 추가합니다. `Ocp-Apim-Subscription-Key`는 인증에 사용되는 QnA Maker 서비스 키입니다.

    [!code-java[Add POST method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=99-121 "Add POST method")]

4. QnA Maker API에 대한 GET 요청을 만드는 다음 메서드를 추가합니다.

    [!code-java[Add GET method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=123-137 "Add GET method")]

## <a name="add-a-method-to-create-the-kb"></a>KB를 만드는 메서드 추가
Post 메서드를 호출하여 KB를 만드는 다음 메서드를 추가합니다.

[!code-java[Add CreateKB method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=139-144 "Add CreateKB method")]

다음 API 호출은 작업 ID를 포함한 JSON 응답을 반환합니다. 작업 ID를 사용하여 KB가 성공적으로 만들어졌는지 결정합니다.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>상태를 가져오는 메서드 추가
만들기 상태를 확인하는 다음 메서드를 추가합니다.

[!code-java[Add GetStatus method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=146-150 "Add GetStatus method")]

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

## <a name="add-a-main-method"></a>main 메서드 추가
main 메서드는 KB를 만든 후 상태를 폴링합니다. _create_ **Operation ID**는 POST 응답 헤더 필드 **Location**에 반환된 후 GET 요청의 경로로 사용됩니다. **`while` 루프는 완료되지 않으면 상태를 재시도합니다.

[!code-java[Add main method](~/samples-qnamaker-java/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java?range=152-191 "Add main method")]

## <a name="compile-and-run-the-program"></a>프로그램 컴파일 및 실행

1. gson 라이브러리가 `./libs` 디렉터리에 있는지 확인합니다. 명령줄에서 `CreateKB.java` 파일을 컴파일합니다.

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. 프로그램을 실행하려면 명령줄에 다음 명령을 입력합니다. 그러면 KB를 만들기 위한 요청을 QnA Maker API에 보낸 다음, 30초마다 결과를 폴링합니다. 각 응답이 콘솔 창에 출력됩니다.

    ```base
    java -cp ",;libs/*" CreateKB
    ```

기술 자료가 생성되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 볼 수 있습니다.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)
