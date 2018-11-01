---
title: Java 빠른 시작 - 의도 예측 - LUIS
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 사용 가능한 공용 LUIS 앱을 통해 대화형 텍스트에서 사용자의 의도를 판단합니다. Java를 사용하여 공용 앱의 HTTP 예측 엔드포인트에 사용자의 의도를 텍스트로 보냅니다. 엔드포인트에서 LUIS는 공용 앱의 모델을 적용하여 자연어 텍스트의 의미를 분석하고 전반적인 의도를 판단하여 앱의 주체 도메인에 적절한 데이터를 추출합니다.
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c492a0d0a9d90a79b0e05d4e7de6f5ef0796184a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417535"
---
# <a name="quickstart-get-intent-using-java"></a>빠른 시작: Java를 사용하여 의도 가져오기

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>필수 조건

* [JDK SE](https://aka.ms/azure-jdks)(Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>브라우저를 사용하여 의도 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>프로그래밍 방식으로 의도 가져오기 

Java를 사용하여 이전 단계의 브라우저 창에서 본 것과 동일한 결과에 액세스할 수 있습니다. 

1. 다음 코드를 복사하여 `LuisGetRequest.java` 파일에 클래스를 만듭니다.

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. `YOUR-KEY` 변수 값을 LUIS 키로 바꿉니다.

3. `javac -cp ":lib/*" LuisGetRequest.java`를 사용하여 Java 프로그램을 컴파일합니다. 

4. `java -cp ":lib/*" LuisGetRequest.java`키를 눌러 응용 프로그램을 실행합니다. 브라우저 창에서 앞서 본 것과 동일한 JSON을 표시합니다.

    ![콘솔 창에서는 LUIS의 JSON 결과를 표시합니다.](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>LUIS 키

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

Java 파일을 삭제합니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [발언 추가](luis-get-started-java-add-utterance.md)