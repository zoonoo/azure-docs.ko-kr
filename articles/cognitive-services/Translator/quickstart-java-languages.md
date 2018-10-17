---
title: '빠른 시작: 지원되는 언어 가져오기 - Translator Text, Java'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java와 함께 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 64954c06d90ff8110b77874846fe57072faf2f76
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126975"
---
# <a name="quickstart-get-supported-languages-with-java"></a>빠른 시작: Java를 사용하여 지원되는 언어 가져오기

이 빠른 시작에서는 Translator Text API를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.

## <a name="prerequisites"></a>필수 조건

이 코드를 컴파일하고 실행하려면 [JDK 7 또는 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)이 필요합니다. 자주 사용하는 Java IDE를 사용해도 되지만, 텍스트 편집기로도 충분합니다.

Translator Text API를 사용하려면 구독 키도 필요합니다. [Translator Text API에 등록하는 방법](translator-text-how-to-signup.md)을 참조하세요.

## <a name="languages-request"></a>언어 요청

다음 코드는 [Languages](./reference/v3-0-languages.md) 메서드를 사용하여 번역, 음역, 사전 조회에 지원되는 언어 목록과 예제를 가져옵니다.

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
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/* NOTE: To compile and run this code:
1. Save this file as Languages.java.
2. Run:
    javac Languages.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar Languages
*/

public class Languages {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://api.cognitive.microsofttranslator.com";
    static String path = "/languages?api-version=3.0";

    static String output_path = "output.txt";

    public static String GetLanguages () throws Exception {
        URL url = new URL (host + path);

        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void WriteToFile (String data) throws Exception {
        String json = prettify (data);
        Writer outputStream = new BufferedWriter(new OutputStreamWriter(new FileOutputStream(output_path), "UTF-8"));
        outputStream.write(json);
        outputStream.close();
    }

    public static void main(String[] args) {
        try {
            String response = GetLanguages ();
            WriteToFile (response);
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

## <a name="languages-response"></a>언어 응답

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>다음 단계

번역 및 음역을 포함하여 이 빠른 시작과 다른 빠른 시작의 샘플 코드 그리고 GitHub의 다른 샘플 Translator Text 프로젝트를 살펴봅니다.

> [!div class="nextstepaction"]
> [GitHub에서 Java 예제 살펴보기](https://aka.ms/TranslatorGitHub?type=&language=java)
