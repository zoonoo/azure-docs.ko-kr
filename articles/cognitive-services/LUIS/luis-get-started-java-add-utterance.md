---
title: Java를 사용하여 LUIS 앱에 발화를 추가하는 방법을 알아보는 자습서 | Microsoft Docs
description: 이 자습서에서는 Java를 사용하여 LUIS 앱을 호출하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5b9c7b90ca96b23fbabfeb1e1d06e4124e65a0dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266042"
---
# <a name="tutorial-add-utterances-to-app-using-java"></a>자습서: Java를 사용하여 앱에 발화 추가 
이 자습서에서는 Java에서 제작 API를 사용하여 의도에 발화를 추가하는 프로그램을 작성합니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio 콘솔 프로젝트 만들기 
> * LUIS API를 호출하여 발화를 추가하는 메서드 추가 및 앱 학습
> * BookFlight 의도에 대한 발화 예제가 있는 JSON 파일 추가
> * 콘솔 실행 및 발화 학습 상태 확인

자세한 내용은 [의도에 발화 예제 추가](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [학습](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) 및 [학습 상태](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API에 대한 기술 설명서를 참조하세요.

이 문서에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="prerequisites"></a>필수 조건

* 최신 Oracle [**Java/JDK**](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Google의 GSON JSON 라이브러리](https://github.com/google/gson)
* LUIS **[제작 키](luis-concept-keys.md#authoring-key)**. 이 키는 [LUIS](luis-reference-regions.md) 웹 사이트의 계정 설정에서 찾을 수 있습니다.
* 기존 LUIS [**응용 프로그램 ID**](./luis-get-started-create-app.md). 응용 프로그램 ID는 응용 프로그램 대시보드에 표시되어 있습니다. `AddUtterances.java`의 코드를 실행하기 전에 `utterances.json` 파일에 사용된 의도와 엔터티가 있는 LUIS 앱이 있어야 합니다. 이 문서의 코드에서는 의도와 엔터티를 만들지 않습니다. 기존 의도 및 엔터티에 대한 발화만 추가됩니다. 
* 발화를 받는 응용 프로그램 내의 **버전 ID**. 기본 ID는 "0.1"입니다.
* `AddUtterances.java`라는 이름의 새 텍스트 파일을 만듭니다.

> [!NOTE] 
> 완전한 `add-utterances.cs` 파일과 `utterances.json` 예제 파일은 [**LUIS-Samples** Github 리포지토리](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/java)에서 사용할 수 있습니다.


## <a name="write-the-java-code"></a>Java 코드 작성

Java 종속성을 파일에 추가합니다.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=31-34 "Java Dependencies")]

`AddUtterances` 클래스를 만듭니다.

```Java
public class AddUtterances {
    // Insert code here
}
```

이 클래스에는 다음에 나오는 모든 코드 조각이 포함됩니다.

LUIS 상수를 클래스에 추가합니다. 다음 코드를 복사하고, 제작 키, 응용 프로그램 ID 및 버전 ID로 변경합니다.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=41-53 "LUIS-based IDs")]

LUIS API를 호출하는 메서드를 추가합니다. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=55-178 "HTTP request to LUIS")]

LUIS API로부터의 HTTP 응답에 대한 메서드를 추가합니다.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=180-226 "HTTP response from LUIS")]


예외 처리를 추가합니다. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=228-256 "Exception Handling")]

출력/인쇄 처리를 추가합니다.

   [!code-java[Add output handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=258-267 "Add configuration information for adding utterance")]

main 함수를 추가합니다.

   [!code-java[Add main function](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=269-345 "Add main function")]


## <a name="specify-utterances-to-add"></a>추가할 발화 지정
`utterances.json` 파일을 만들고 편집하여 LUIS 앱에 추가하려는 **발화 배열**을 지정합니다. 의도와 엔터티가 이미 LUIS 앱에 **있어야 합니다**.

> [!NOTE]
> `AddUtterances.java`의 코드를 실행하기 전에 `utterances.json` 파일에 사용된 의도와 엔터티가 있는 LUIS 앱이 있어야 합니다. 이 문서의 코드에서는 의도와 엔터티를 만들지 않습니다. 기존 의도 및 엔터티에 대한 발화만 추가됩니다.

`text` 필드에는 발화 텍스트가 포함됩니다. `intentName` 필드는 LUIS 앱의 의도 이름과 일치해야 합니다. `entityLabels` 필드는 필수입니다. 엔터티에 대한 레이블을 지정하지 않으려면 다음 예제와 같이 빈 목록을 제공합니다.

entityLabels 목록이 비어 있지 않으면 `startCharIndex` 및 `endCharIndex`에서 `entityName` 필드에 언급된 엔터티를 표시해야 합니다. 두 인덱스는 모두 0부터 시작하는 계수입니다. 즉 위의 예제에서 6은 시애틀(Seattle)의 "S"를 의미하고 대문자 S 앞에 있는 공백이 아닙니다.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>명령줄에서 발화 추가

종속성이 있는 AddUtterance를 컴파일합니다.

```
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

인수 없이 `AddUtterance`를 호출하면 LUIS 발화가 학습되지 않고 앱에 추가됩니다.
````
> java -classpath .;gson-2.8.2.jar AddUtterances
````

이 샘플에서는 발화 추가 API를 호출한 결과가 포함된 `results.json`을 사용하여 파일을 만듭니다. `response` 필드는 추가된 발화에 대한 형식입니다. `hasError`는 false이며, 이는 발화가 추가되었음을 나타냅니다.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>명령줄에서 발화 추가 및 학습
`-train` 인수로 `add-utterance`를 호출하여 학습 요청을 보냅니다. 

````
> java -classpath .;gson-2.8.2.jar AddUtterances -train
````

> [!NOTE]
> 중복된 발화는 다시 추가되지 않지만 오류가 발생하지 않습니다. `response`에는 원래 발화의 ID가 포함되어 있습니다.

`-train` 인수로 앱을 호출하면 LUIS 앱이 성공적으로 큐에 있다는 학습 요청을 나타내는 `training-results.json` 파일이 만들어집니다. 

성공적인 학습 요청의 결과를 보여 주는 예제는 다음과 같습니다.
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

학습 요청이 큐에 있으면 학습을 완료하는 데 약간의 시간이 걸릴 수 있습니다.

## <a name="get-training-status-from-the-command-line"></a>명령줄에서 학습 상태 확인
`-status` 인수로 앱을 호출하여 학습 상태를 확인하고, 상태 세부 정보를 파일에 기록합니다.

````
> java -classpath .;gson-2.8.2.jar AddUtterances -status
````

## <a name="clean-up-resources"></a>리소스 정리
자습서를 완료하여 더 이상 필요하지 않은 경우 Visual Studio 및 콘솔 응용 프로그램을 제거합니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [프로그래밍 방식으로 LUIS 앱 빌드](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website