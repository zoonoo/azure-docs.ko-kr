---
title: Go에서 REST 호출을 사용하여 의도 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: b36e967e960ec6a2b39409ea7be5f3f98f69b9c8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838532"
---
## <a name="prerequisites"></a>필수 조건

* [Go](https://golang.org/) 프로그래밍 언어  
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>프로그래밍 방식으로 의도 가져오기

Go를 사용하여 이전 단계의 브라우저 창에서 본 것과 동일한 결과에 액세스할 수 있습니다. 

1. 이름이 `endpoint.go`인 새 파일을 만듭니다. 다음 코드를 추가합니다.
    
   [!code-go[Go code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/go/endpoint.go?range=36-98)]

2. 파일을 만든 위치와 같은 디렉터리에서 명령 프롬프트에 `go build endpoint.go`를 입력하여 Go 파일을 컴파일합니다. 명령 프롬프트에는 빌드 성공에 대한 정보가 반환되지 않습니다.

3. 명령 프롬프트에서 다음 텍스트를 입력하여 명령줄에서 Go 애플리케이션을 실행합니다. 

    ```CMD
    go run endpoint.go -appID df67dcdb-c37d-46af-88e1-8b97951ca1c2 -endpointKey <add-your-key> -region westus
    ```
    
    `<add-your-key>`를 키 값으로 대체합니다.  
    
    명령 프롬프트 응답은 다음과 같습니다. 
    
    ```CMD
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    region has value westus
    utterance has value turn on the bedroom light
    response
    {
        "query": "turn on the bedroom light",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.809439957
        },
        "entities": [
            {
            "entity": "bedroom",
            "type": "HomeAutomation.Room",
            "startIndex": 12,
            "endIndex": 18,
            "score": 0.8065475
            }
        ]
    }
    ```


## <a name="luis-keys"></a>LUIS 키

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 마치면 Visual Studio를 닫고 프로젝트 디렉터리를 파일 시스템에서 제거하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Go로 발언 및 학습 추가](../luis-get-started-go-add-utterance.md)