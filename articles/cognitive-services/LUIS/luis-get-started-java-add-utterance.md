---
title: 변경, 학습 앱, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: Java 빠른 시작에서는 Home Automation 앱에 예제 발언을 추가하여 앱을 학습시킵니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: ce2cf0603e584684edda1b1f14a12b52fbbb928c
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357131"
---
# <a name="quickstart-change-model-using-java"></a>빠른 시작: Java를 사용하여 모델 변경 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks)(Java Development Kit, Standard Edition)
* [Google의 GSON JSON 라이브러리](https://github.com/google/gson)

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>빠른 시작 코드 만들기

1. Java 종속성을 `AddUtterances.java` 파일에 추가합니다.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. `AddUtterances` 클래스를 만듭니다. 이 클래스에는 다음에 나오는 모든 코드 조각이 포함됩니다.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. LUIS 상수를 클래스에 추가합니다. 다음 코드를 복사하고, 제작 키, 애플리케이션 ID 및 버전 ID로 변경합니다.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. AddUtterances 클래스로 LUIS API를 호출하는 메서드를 추가합니다. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. LUIS API의 HTTP 응답에 대한 메서드를 AddUtterances 클래스에 추가합니다.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. AddUtterances 클래스에 예외 처리를 추가합니다. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. AddUtterances 클래스에 main 함수를 추가합니다.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>코드 빌드

종속성이 있는 AddUtterance를 컴파일합니다.

```console
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>코드 실행
인수 없이 `AddUtterance`를 호출하면 LUIS 발화가 학습되지 않고 앱에 추가됩니다.

```console
> java -classpath .;gson-2.8.2.jar AddUtterances
```

이 명령줄은 add utterances API를 호출한 결과를 표시합니다. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작을 완료하면 이 빠른 시작에서 생성된 모든 파일을 제거하세요. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [프로그래밍 방식으로 LUIS 앱 빌드](luis-tutorial-node-import-utterances-csv.md) 
