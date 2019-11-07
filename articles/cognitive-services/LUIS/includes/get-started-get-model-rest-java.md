---
title: Java에서 REST 호출을 사용하여 모델 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: a8129bf057b0e80a5f656f5ab3d578156b18a23c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500717"
---
## <a name="prerequisites"></a>필수 조건

* 시작 키.
* cognitive-services-language-understanding GitHub 리포지토리에서 [TravelAgent 앱](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json)을 가져옵니다.
* 가져온 TravelAgent 앱의 LUIS 애플리케이션 ID입니다. 애플리케이션 ID는 애플리케이션 대시보드에 표시됩니다.
* 발화를 수신하는 애플리케이션 내의 버전 ID입니다. 기본 ID는 "0.1"입니다.
* [JDK SE](https://aka.ms/azure-jdks)(Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) 또는 선호하는 IDE

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>LUIS 키 가져오기

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>프로그래밍 방식으로 모델 변경

Go를 사용하여 기계 학습된 엔터티 [API](https://aka.ms/luis-apim-v3-authoring)를 애플리케이션에 추가합니다. 

1. `lib`라는 하위 디렉터리를 만들고 다음 java 라이브러리에 복사합니다.

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. 이름이 `Model.java`인 새 파일을 만듭니다. 다음 코드를 추가합니다.


    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    
    //javac -cp ":lib/*" Model.java
    //java -cp ":lib/*" Model
    
    public class Model {
    
        public static void main(String[] args) 
        {
            try
            {
    
                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "YOUR-APP-ID";
                
                // Add your endpoint key 
                String Key = "YOUR-KEY";
    
                // Add your endpoint, example is westus.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";
    
                String Utterance = "[{'text': 'go to Seattle today','intentName': 'BookFlight','entityLabels': [{'entityName': 'Location::LocationTo',"
                    + "'startCharIndex': 6,'endCharIndex': 12}]},{'text': 'a barking dog is annoying','intentName': 'None','entityLabels': []}]";
    
                String Version = "1.0";
    
                // Begin endpoint URL string building
                URIBuilder addUtteranceURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/examples");
                URIBuilder trainURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/train");
    
                // create URL from string
                URI addUtterancesURI = addUtteranceURL.build();
                URI trainURI = trainURL.build();
    
                // add utterances POST
                HttpClient addUtterancesClient = HttpClients.createDefault();
                HttpPost addutterancesRequest = new HttpPost(addUtterancesURI);
                addutterancesRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                addutterancesRequest.setHeader("Content-type","application/json");
                HttpResponse addutterancesResponse = addUtterancesClient.execute(addutterancesRequest);
                HttpEntity addutterancesEntity = addutterancesResponse.getEntity();
                if (addutterancesEntity != null) 
                {
                    System.out.println(EntityUtils.toString(addutterancesEntity));
                }
    
                // train POST
                HttpClient trainClient = HttpClients.createDefault();
                HttpPost trainRequest = new HttpPost(trainURI);
                trainRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainRequest.setHeader("Content-type","application/json");
                HttpResponse trainResponse = trainClient.execute(trainRequest);
                HttpEntity trainEntity = trainResponse.getEntity();
                if (trainEntity != null) 
                {
                    System.out.println(EntityUtils.toString(trainEntity));
                }
    
                // training status GET
                HttpClient trainStatusClient = HttpClients.createDefault();
                HttpGet trainStatusRequest = new HttpGet(trainURI);
                trainStatusRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainStatusRequest.setHeader("Content-type","application/json");
                HttpResponse trainStatusResponse = trainStatusClient.execute(trainStatusRequest);
                HttpEntity trainStatusEntity = trainStatusResponse.getEntity();
                if (trainStatusEntity != null) 
                {
                    System.out.println(EntityUtils.toString(trainStatusEntity));
                }            
            }
    
            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }   
    }
    ```
1. 다음 값을 바꿉니다.

    * 시작 키가 있는 `YOUR-KEY`
    * 엔드포인트가 있는 `YOUR-ENDPOINT`(예: `westus2.api.cognitive.microsoft.com`)
    * 앱의 ID가 있는 `YOUR-APP-ID`

1. 파일을 만든 위치와 같은 디렉터리에서 명령 프롬프트에 다음 명령을 입력하여 Java 파일을 컴파일합니다.

    ```console
    javac -cp ":lib/*" Model.java
    ```  

1. 명령 프롬프트에서 다음 텍스트를 입력하여 명령줄에서 Java 애플리케이션을 실행합니다. 

    ```console
    java -cp ":lib/*" Model
    ```

## <a name="luis-keys"></a>LUIS 키

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료하면 파일 시스템에서 파일을 삭제합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 모범 사례](../luis-concept-best-practices.md)