---
title: Java에서 REST 호출을 사용하여 의도 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838495"
---
## <a name="prerequisites"></a>필수 조건

* [JDK SE](https://aka.ms/azure-jdks)(Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) 또는 선호하는 IDE
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>프로그래밍 방식으로 의도 가져오기

Java를 사용하여 이전 단계의 브라우저 창에서 본 것과 동일한 결과에 액세스할 수 있습니다. 프로젝트에 Apache 라이브러리를 꼭 추가해야 합니다.

1. 다음 코드를 복사하여 `LuisGetRequest.java` 파일에 클래스를 만듭니다.

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. `YOUR-KEY` 변수 값을 LUIS 키로 바꿉니다.

3. 명령줄 `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`에서 파일 경로를 바꾸고 java 프로그램을 컴파일합니다.

4. 명령줄 `java -cp .;<FILE_PATH>\* LuisGetRequest.java`에서 파일 경로를 바꾸고 애플리케이션을 실행합니다. 브라우저 창에서 앞서 본 것과 동일한 JSON을 표시합니다.

    ![콘솔 창에서는 LUIS의 JSON 결과를 표시합니다.](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>LUIS 키

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 마치면 Visual Studio를 닫고 프로젝트 디렉터리를 파일 시스템에서 제거하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Java로 발언 및 학습 추가](../luis-get-started-java-add-utterance.md)