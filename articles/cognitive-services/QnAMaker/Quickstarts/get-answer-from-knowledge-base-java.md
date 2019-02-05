---
title: '빠른 시작: 기술 자료에서 답변 찾기 - REST, Java - QnA Maker'
titlesuffix: Azure Cognitive Services
description: 이 Java REST 기반 빠른 시작은 기술 자료에서 프로그래밍 방식으로 답변을 가져오는 방법을 안내합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 34630b2d2852da37bdcb3e5f097b2d3f6ebc6f1a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217410"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Java를 사용하여 기술 자료에서 질문에 대한 답변 얻기

이 빠른 시작에서는 게시된 QnA Maker 기술 자료에서 프로그래밍 방식으로 답변을 가져오는 방법을 안내합니다. QnA Maker는 [데이터 원본](../Concepts/data-sources-supported.md)에서 반구조화된 콘텐츠(예: FAQ)의 질문과 답변을 자동으로 추출합니다. JSON 형식의 질문이 API 요청의 본문에 전송됩니다. 

## <a name="prerequisites"></a>필수 조건

* [JDK SE](https://aka.ms/azure-jdks)(Java Development Kit, Standard Edition)
* 이 샘플에서는 HTTP 구성 요소의 Apache [HTTP 클라이언트](http://hc.apache.org/httpcomponents-client-ga/)를 사용합니다. 프로젝트에 다음 Apache HTTP 클라이언트 라이브러리를 추가해야 합니다. 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키를 검색하려면 QnA Maker 리소스에 대한 Azure 대시보드의 **리소스 관리** 아래에서 **키**를 선택합니다. 
* **게시** 페이지 설정. 게시된 기술 자료가 없는 경우 빈 기술 자료를 만든 다음, **설정** 페이지에서 기술 자료를 가져온 후 게시합니다. [이 기본 기술 자료](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)를 다운로드하고 사용할 수 있습니다. 

    게시 페이지 설정에는 POST 경로 값, 호스트 값 및 EndpointKey 값이 포함됩니다. 

    ![게시 설정](../media/qnamaker-quickstart-get-answer/publish-settings.png)

이 빠른 시작용 코드는 [https://github.com/Azure-Samples/cognitive-services-qnamaker-java](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/get-answer) 리포지토리에 있습니다. 

## <a name="create-a-java-file"></a>Java 파일 만들기

VSCode를 열고 `GetAnswer.java`라는 새 파일을 만든 후 다음 클래스를 추가합니다.

```Java
public class GetAnswer {

    public static void main(String[] args) 
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

이 빠른 시작에서는 HTTP 요청에 대해 Apache 클래스를 사용합니다. `GetAnswer.java` 파일 맨 위의 GetAnswer 클래스 위에 프로젝트에 필요한 종속성을 추가합니다.

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>필요한 상수 추가

`GetAnswer.java` 클래스의 맨 위에서 QnA Maker에 액세스하기 위한 필수 상수를 추가합니다. 이러한 값은 기술 자료를 게시한 후 **게시** 페이지에 표시됩니다.  

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>질문을 보내기 위한 POST 요청 추가

다음 코드에서는 기술 자료로 질문을 보내기 위한 QnA Maker API에 대한 HTTPS 요청을 수행한 후 답변을 수신합니다.

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

`Authorization` 헤더의 값에는 문자열 `EndpointKey `가 포함됩니다. 

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

명령줄에서 프로그램을 빌드하고 실행합니다. 자동으로 해당 요청이 QnA Maker API로 전송되고, 응답이 콘솔 창에 출력됩니다.

1. 파일을 빌드합니다.

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. 다음과 같이 파일을 실행합니다.

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
