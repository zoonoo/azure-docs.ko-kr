---
title: '빠른 시작: 기술 자료 업데이트 - REST, Java - QnA Maker'
titleSuffix: Azure Cognitive Services
description: 이 REST 기반 빠른 시작에서는 QnA Maker KB(기술 자료) 샘플을 프로그래밍 방식으로 업데이트하는 방법을 안내합니다. KB를 업데이트하는 데 사용하는 JSON 정의를 통해 질문 및 답변 쌍을 추가, 변경 또는 삭제할 수 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: b7aaf57def84feec95cc6a157ac6dd33a17b2fc0
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645858"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-java"></a>빠른 시작: Java를 사용하여 QnA Maker 기술 자료 업데이트

이 빠른 시작에서는 기존 QnA Maker KB(기술 자료)를 프로그래밍 방식으로 업데이트하는 방법을 안내합니다.  이 JSON을 사용하면 데이터 원본을 새로 추가, 변경 또는 삭제하여 KB를 업데이트할 수 있습니다.

이 API는 QnA Maker 포털에서 편집한 다음, **저장 및 학습** 단추를 사용하는 것과 같습니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [업데이트](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) - 기술 자료 모델은 API 요청 본문에 전송된 JSON에 정의됩니다. 
* [작업 세부 정보 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

아직 기술 자료가 없는 경우 샘플을 만들어서 빠른 시작: [새 기술 자료 만들기](create-new-kb-java.md)에서 사용하면 됩니다.

1. 즐겨 찾는 IDE에서 새 Java 프로젝트를 만듭니다.
1. 아래 제공된 코드를 추가합니다.
1. `key` 값을 유효한 구독 키로 바꿉니다.
1. `kb` 값을 기술 자료 ID로 바꿉니다. 이 값은 [QnA Maker 기술 자료](https://www.qnamaker.ai/Home/MyServices) 중 한 곳으로 이동하여 찾을 수 있습니다. 업데이트할 기술 자료를 선택합니다. 해당 페이지가 열리면 다음 예제와 같이 URL에서 'kdid='을 찾습니다. 해당 값을 코드 샘플에 사용합니다.

    ![QnA Maker 기술 자료 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

1. [apache httpclient](https://hc.apache.org/downloads.cgi) 라이브러리가 필요합니다. 예를 들어, 바이너리 HttpClient 4.x.x.tar.gz를 다운로드하여 프로젝트로 가져오는 데 필요한 모든 jar 파일을 찾습니다.
1. [GSON](https://github.com/google/gson) 라이브러리도 필요 합니다. .jar 파일을 수동으로 [만들어서](https://stackoverflow.com/questions/5258159/how-to-make-an-executable-jar-file) 가져오거나 원하는 프로젝트 관리 도구(예: Maven)에 종속성을 추가합니다.
1. 프로그램을 실행합니다.

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/**
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/**
 * Java does not natively support HTTP PATCH requests; Apache HttpClient is required.
 * HttpClient: https://hc.apache.org/downloads.cgi
 * Maven info:
 *    <dependency>
 *      <groupId>org.apache.httpcomponents</groupId>
 *      <artifactId>httpclient</artifactId>
 *      <version>4.5.5</version>
 *    </dependency>
 */
import org.apache.commons.logging.LogFactory;
import org.apache.http.client.methods.HttpPatch;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.entity.ByteArrayEntity;
import org.apache.http.Header;
import org.apache.http.HttpEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;

/**
 * NOTE: To compile and run this code:
 * 1. Save this file as UpdateKB.java.
 * 2. Run:
 * javac UpdateKB.java -cp .;gson-2.8.1.jar;httpclient-4.5.5.jar;httpcore-4.4.9.jar;
 * commons-logging-1.2.jar -encoding UTF-8
 * 3. Run:
 * java -cp .;gson-2.8.1.jar;httpclient-4.5.5.jar;httpcore-4.4.9.jar;commons-logging
 * -1.2.jar UpdateKB
 */

public class UpdateKB {

    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace this with a valid subscription key.
    static String subscriptionKey = "ADD KEY HERE";

    // Replace this with a valid knowledge base ID.
    static String kb = "ADD ID HERE";

    // Represents the various elements used to create HTTP request URIs
    // for QnA Maker operations.
    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/";

    // We'll serialize these classes into JSON for our request to the server.
    // For the JSON request schema, see: 
    // https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/
    // operations/5ac266295b4ccd1554da7600
    public static class Request {
        Add add;
        Delete delete;
        Update update;
    }

    public static class Add {
        Question[] qnaList;
        String[] urls;
        File[] files;
    }

    public static class Delete {
        Integer[] ids;
    }

    public static class Update {
        String name;
        Question[] qnaList;
        String[] urls;
        File[] files;
    }

    public static class Question {
        Integer id;
        String answer;
        String source;
        String[] questions;
        Metadata[] metadata;
    }

    public static class Metadata {
        String name;
        String value;
    }

    public static class File {
        String fileName;
        String fileUri;
    }

    // This class contains the headers and body of the HTTP response.
    public static class Response {
        Map<String, List<String>> Headers;
        String Response;

        /**
         * Headers and response (body) of the HTTP request
         * @param headers A map containing the requested response
         * @param response The body of the requested response
         */
        public Response(Map<String, List<String>> headers, String response) {
            this.Headers = headers;
            this.Response = response;
        }
    }

    /**
     * Formats and indents JSON for display.
     * @param json_text The raw JSON input
     * @return String A string containing formatted and indented JSON.
     */
    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    /**
     * Sends a GET HTTP request.
     * @param url The string URL request
     * @return A Response object with the header and body of the response
     */
    public static Response Get (URL url) throws Exception {
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

        return new Response (connection.getHeaderFields(), response.toString());
    }

   /**
     * Sends an HTTP PATCH request. We use Apache HttpClient, as HttpsURLConnection 
     * does not support PATCH.
     * @param url The string request
     * @param content The body of the request
     * @return Response Returns a Response object with the headers and body
     */
    public static Response Patch (URL url, String content) throws Exception {
        HttpPatch patch = new HttpPatch(url.toString());
        // HttpPatch implements HttpMessage, which includes addHeader.
        // See: <a href="https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/
        // apache/http/client/methods/HttpPatch.html">HttpPatch</a>
        // See: <a href="https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/
        // HttpMessage.html">HttpMessage</a>
        patch.addHeader("Content-Type", "application/json");
        // Note: Adding the Content-Length header causes the exception:
        // "Content-Length header already present."
        patch.addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        // HttpPatch implements HttpEntityEnclosingRequest, which includes setEntity.
        // See: <a href="https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/
        // HttpEntityEnclosingRequest.html">HttpEntityEnclosingRequest</a>
        HttpEntity entity = new ByteArrayEntity(content.getBytes("UTF-8"));
        patch.setEntity(entity);

        CloseableHttpClient httpClient = HttpClients.createDefault();
        CloseableHttpResponse response = httpClient.execute(patch);

        // CloseableHttpResponse implements HttpMessage, which includes getAllHeaders.
        // See: <a href="https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/
        // client/methods/CloseableHttpResponse.html">CloseableHttpResponse</a>
        // Header implements NameValuePair.
        // See: <a href="https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/Header.html">
        // Header</a>
        // See: <a href="https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/
        // NameValuePair.html">NameValuePair</a>
        Map<String, List<String>> headers = new HashMap<String, List<String>>();
        for (Header header : response.getAllHeaders()) {
            List<String> list = new ArrayList<String>() {
                {
                    add(header.getValue());
                }
            };
            headers.put(header.getName(), list);
        }

        // CloseableHttpResponse implements HttpResponse, which includes getEntity.
        // See: <a href="https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/
        // HttpResponse.html">HttpResponse</a>
        // HttpEntity implements getContent, which returns an InputStream.
        // See: <a href="https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/
        // HttpEntity.html">HttpEntity</a>
        StringBuilder output = new StringBuilder ();
        BufferedReader reader = new BufferedReader(new InputStreamReader(response.getEntity()
                                                                        .getContent(), "UTF-8"));
        String line;
        while ((line = reader.readLine()) != null) {
            output.append(line);
        }
        reader.close();

        return new Response (headers, output.toString());
    }

    /**
     * Sends the request to update the knowledge base.
     * @param kb The ID for the existing knowledge base
     * @param req The data source for the updated knowledge base
     * @return Reponse Returns the response from a PATCH request
     */
    public static Response UpdateKB (String kb, Request req) throws Exception {
        URL url = new URL(host + service + method + kb);
        System.out.println("Calling " + url.toString() + ".");
        String content = new Gson().toJson(req);
        return Patch(url, content);
    }

    /** 
     * Checks on the status of the request to update the knowledge base.
     * @param operation The QnA Maker operation to check
     * @return The Response object that represents the HTTP response
     */
    public static Response GetStatus (String operation) throws Exception {
        URL url = new URL(host + service + operation);
        System.out.println("Calling " + url.toString() + ".");
        return Get(url);
    }

    /**
     * Compiles the details of the request.
     * @return The Request object with request details.
     */
    public static Request GetRequest () {
        Request req = new Request ();

        Question q = new Question();
        q.id = 0;
        q.answer = "You can use our REST APIs to manage your Knowledge Base."
        + "See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/"
        + "58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa";
        q.source = "Custom Editorial";
        q.questions = new String[]{"How do I programmatically update my Knowledge Base?"};

        Metadata md = new Metadata();
        md.name = "category";
        md.value = "api";
        q.metadata = new Metadata[]{md};

        req.add = new Add ();
        req.add.qnaList = new Question[]{q};
        req.add.urls = new String[]{"https://docs.microsoft.com/azure/cognitive-services"
        + "/qnamaker/faqs", "https://docs.microsoft.com/bot-framework/resources-bot-"
        + "framework-faq"};

        return req;
    }

    public static void main(String[] args) {
        try {
            // Send the request to update the knowledge base.
            Response response = UpdateKB(kb, GetRequest ());
            String operation = response.Headers.get("Location").get(0);
            System.out.println(PrettyPrint(response.Response));
            // Loop until the request is completed.
            Boolean done = false;
            while (true != done) {
                // Check on the status of the request.
                response = GetStatus (operation);
                System.out.println (PrettyPrint (response.Response));
                Type type = new TypeToken<Map<String, String>>(){}.getType();
                Map<String, String> fields = new Gson().fromJson(response.Response, type);
                String state = fields.get ("operationState");
                // If the request is still running, the server tells us how long to wait
                // before checking the status again.
                if (state.equals("Running") || state.equals("NotStarted")) {
                    String wait = response.Headers.get("Retry-After").get(0);
                    System.out.println("Waiting " + wait + " seconds...");
                    Thread.sleep(Integer.parseInt(wait) * 1000);
                }
                else {
                    done = true;
                }
            }
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

## <a name="understand-what-qna-maker-returns"></a>QnA Maker에서 반환되는 내용 이해하기

성공한 응답은 다음 예제와 같이 JSON으로 반환됩니다. 결과가 약간 다를 수도 있습니다. 최종 호출이 "성공" 상태를 반환하면 기술 자료가 업데이트된 것입니다. 문제를 해결하려면 QnA Maker API의 [기술 자료 업데이트](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) 응답 코드를 참조하세요.

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

기술 자료가 업데이트되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 볼 수 있습니다. 사용자 지정 요청에 따라 기술 자료가 변경된 것을 볼 수 있습니다.

기술 자료의 다른 요소를 수정하려면 QnA Maker [JSON 스키마](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600)를 참조하여 `getRequest()` 메서드의 `req` 개체를 수정합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)