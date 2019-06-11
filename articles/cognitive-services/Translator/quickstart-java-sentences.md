---
title: '빠른 시작: 문장 길이 가져오기, Java - Translator Text API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java 및 Translator Text API를 사용하여 문장 길이를 확인하는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 748f8cfa1e105b5c6d7b9b3fc073eac9f38a63dd
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514843"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-java"></a>빠른 시작: Translator Text API를 사용하여 Java를 통해 문장 길이 확인

이 빠른 시작에서는 Java 및 Translator Text API를 사용하여 문장 길이를 확인하는 방법을 알아봅니다.

이 빠른 시작에는Translator Text 리소스와 함께 [Azure Cognitive Services 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)이 필요합니다. 계정이 없는 경우 [평가판](https://azure.microsoft.com/try/cognitive-services/)을 사용하여 구독 키를 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [JDK 7 이상](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Translator Text에 대한 Azure 구독 키

## <a name="initialize-a-project-with-gradle"></a>Gradle을 사용하여 프로젝트 초기화

이 프로젝트의 작업 디렉터리를 만들어 보겠습니다. 명령줄(또는 터미널)에서 이 명령을 실행합니다.

```console
mkdir length-sentence-sample
cd length-sentence-sample
```

다음으로, Gradle 프로젝트를 초기화하려고 합니다. 이 명령은 런타임 시 애플리케이션을 만들고 구성하는 데 사용되는 중요한 `build.gradle.kts`와 같은 Gradle의 필수 빌드 파일을 만듭니다. 작업 디렉터리에서 이 명령을 실행합니다.

```console
gradle init --type basic
```

**DSL**을 선택하라는 메시지가 표시되면 **Kotlin**을 선택합니다.

## <a name="configure-the-build-file"></a>빌드 파일 구성

`build.gradle.kts`를 찾고 즐겨찾는 IDE 또는 텍스트 편집기를 사용하여 엽니다. 그런 다음, 이 빌드 구성에서 복사합니다.

```
plugins {
    java
    application
}
application {
    mainClassName = "LengthSentence"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

이 샘플에는 HTTP 요청의 경우 OkHttp에 대한 종속성이 있고 JSON을 처리하고 구문 분석하기 위해 Gson에 대한 종속성이 있습니다. 빌드 구성에 대해 자세히 알아보려면 [새 Gradle 빌드 만들기](https://guides.gradle.org/creating-new-gradle-builds/)를 참조하세요.

## <a name="create-a-java-file"></a>Java 파일 만들기

샘플 앱의 폴더를 만들어 보겠습니다. 작업 디렉터리에서 다음을 실행합니다.

```console
mkdir -p src/main/java
```

다음으로, 이 폴더에서 `LengthSentence.java`라는 파일을 만듭니다.

## <a name="import-required-libraries"></a>필수 라이브러리 가져오기

`LengthSentence.java`를 열고 다음과 같은 가져오기 문을 추가합니다.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>변수 정의

먼저 프로젝트에 대한 공용 클래스를 만들어야 합니다.

```java
public class LengthSentence {
  // All project code goes here...
}
```

`LengthSentence` 클래스에 이러한 줄을 추가합니다. `api-version`과 함께 입력 언어를 정의할 수 있습니다. 이 샘플에서 이 언어는 영어입니다.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0&language=en";
```

## <a name="create-a-client-and-build-a-request"></a>클라이언트 만들기 및 요청 빌드

이 줄을 `LengthSentence` 클래스에 추가하여 `OkHttpClient`를 인스턴스화합니다.

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

다음으로, POST 요청을 빌드해 보겠습니다. 텍스트를 변경해도 됩니다. 텍스트는 이스케이프되어야 합니다.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"How are you? I am fine. What did you do today?\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>응답을 구문 분석하는 함수 만들기

이 간단한 함수는 Translator Text 서비스의 JSON 응답을 구문 분석하고 꾸밉니다.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>모든 요소 결합

마지막 단계는 요청하고 응답을 받는 것입니다. 프로젝트에 다음과 같은 줄을 추가합니다.

```java
public static void main(String[] args) {
    try {
        LengthSentence lengthSentenceRequest = new LengthSentence();
        String response = lengthSentenceRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>샘플 앱 실행

이제 끝났습니다. 샘플 앱을 실행할 준비가 되었습니다. 명령줄(또는 터미널 세션)에서 작업 디렉터리의 루트로 이동하고 다음을 실행합니다.

```console
gradle build
```

빌드가 완료되면 다음을 실행합니다.

```console
gradle run
```

## <a name="sample-response"></a>샘플 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>다음 단계

번역 및 음역을 포함하여 이 빠른 시작과 다른 빠른 시작의 샘플 코드 그리고 GitHub의 다른 샘플 Translator Text 프로젝트를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 Java 예제 살펴보기](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>참고 항목

* [텍스트 번역](quickstart-java-translate.md)
* [텍스트 음역](quickstart-java-transliterate.md)
* [입력으로 언어 식별](quickstart-java-detect.md)
* [대체 번역 가져오기](quickstart-java-dictionary.md)
* [지원되는 언어 목록 가져오기](quickstart-java-languages.md)
* [입력으로 문장 길이 확인](quickstart-java-sentences.md)
