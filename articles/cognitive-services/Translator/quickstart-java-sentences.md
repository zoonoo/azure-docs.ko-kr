---
title: '빠른 시작: 문장 길이 가져오기 - Translator Text, Java'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Cognitive Services에서 Java와 함께 Translator Text API를 사용하여 텍스트에서 문장 길이를 찾습니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: d0459b04bbd0dbbda3dab80623a39742a6f843b2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122625"
---
# <a name="quickstart-get-sentence-lengths-with-java"></a>빠른 시작: Java를 사용하여 문장 길이 가져오기

이 빠른 시작에서는 Translator Text API를 사용하여 텍스트에서 문장 길이를 찾습니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 컴파일하고 실행하려면 [JDK 7 또는 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)이 필요합니다. 자주 사용하는 Java IDE를 사용해도 되지만, 텍스트 편집기로도 충분합니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="breaksentence-request"></a>BreakSentence 요청

다음 코드는 [BreakSentence](./reference/v3-0-break-sentence.md) 메서드를 사용하여 원본 텍스트를 문장으로 분해합니다.

1. 원하는 코드 편집기에서 Java 프로젝트를 새로 만듭니다.
2. 아래 제공된 코드를 추가합니다.
3. `subscriptionKey` 값을 구독에 유효한 액세스 키로 바꿉니다.
4. 프로그램을 실행합니다.

```java
import java.io.*;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/* NOTE: To compile and run this code:
1. Save this file as BreakSentences.java.
2. Run:
    javac BreakSentences.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar BreakSentences
*/

public class BreakSentences {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsofttranslator.com";
    static String path = "/breaksentence?api-version=3.0";

    static String text = "How are you? I am fine. What did you do today?";

    public static class RequestBody {
        String Text;

        public RequestBody(String text) {
            this.Text = text;
        }
    }

    public static String Post (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setRequestProperty("X-ClientTraceId", java.util.UUID.randomUUID().toString());
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String BreakSentences () throws Exception {
        URL url = new URL (host + path);

        List<RequestBody> objList = new ArrayList<RequestBody>();
        objList.add(new RequestBody(text));
        String content = new Gson().toJson(objList);

        return Post(url, content);
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            String response = BreakSentences ();
            System.out.println (prettify (response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

## <a name="breaksentence-response"></a>BreakSentence 응답

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
