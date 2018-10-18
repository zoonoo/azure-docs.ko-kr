---
title: '빠른 시작: 썸네일 생성 - REST, Java - Computer Vision'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java와 함께 Computer Vision API를 사용하여 이미지에서 썸네일을 생성합니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: a29fd161e788a0c10e5fcb5cd5cd735e1f2cf1b4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343580"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-java-in-computer-vision"></a>빠른 시작: Computer Vision에서 REST API 및 Java를 사용하여 썸네일 생성

이 빠른 시작에서는 Computer Vision의 REST API를 사용하여 이미지에서 썸네일을 생성합니다. [썸네일 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) 메서드를 사용하여 이미지의 썸네일을 생성할 수 있습니다. 높이와 너비를 지정합니다. 입력 이미지의 가로 세로 비율과 다를 수 있습니다. Computer Vision은 스마트 자르기를 사용하여 관심 영역을 지능적으로 식별하고 해당 영역을 기반으로 자르기 좌표를 생성합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

- [Java&trade; 플랫폼, Standard Edition Development Kit 7 또는 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)(JDK 7 또는 8)이 설치되어 있어야 합니다.
- Computer Vision에 대한 구독 키가 있어야 합니다. 구독 키를 가져오려면 [구독 키 얻기](../Vision-API-How-to-Topics/HowToSubscribe.md)를 참조하세요.

## <a name="create-and-run-the-sample-application"></a>응용 프로그램 예제 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만듭니다. 옵션을 사용할 수 있는 경우 명령줄 응용 프로그램 템플릿에서 Java 프로젝트를 만듭니다.
1. Java 프로젝트로 다음 라이브러리를 가져옵니다. Maven을 사용하는 경우 각 라이브러리에 대한 Maven 좌표가 제공됩니다.
   - [Apache HTTP 클라이언트](https://hc.apache.org/downloads.cgi)(org.apache.httpcomponents:httpclient:4.5.5)
   - [Apache HTTP 코어](https://hc.apache.org/downloads.cgi)(org.apache.httpcomponents:httpcore:4.4.9)
   - [JSON 라이브러리](https://github.com/stleary/JSON-java)(org.json:json:20180130)
1. 다음 `import` 명령문을 프로젝트에 대한 `Main` 공용 클래스를 포함하는 파일에 추가합니다.  

   ```java
   import java.awt.*;
   import javax.swing.*;
   import java.net.URI;
   import java.io.InputStream;
   import javax.imageio.ImageIO;
   import java.awt.image.BufferedImage;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. `Main` 공용 클래스를 다음 코드로 바꾼 다음, 필요한 코드에 다음 변경 내용을 만듭니다.
   1. `subscriptionKey`의 값을 구독 키로 바꿉니다.
   1. 필요한 경우 `uriBase`의 값을 구독 키를 가져온 Azure 지역의 [썸네일 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) 메서드에 대한 엔드포인트 URL로 바꿉니다.
   1. 필요에 따라 `imageToAnalyze`의 값을 썸네일을 생성하려는 다른 이미지의 URL로 바꿉니다.
1. 저장한 다음, Java 프로젝트를 빌드합니다.
1. IDE를 사용하는 경우 `Main`을 실행합니다. 그렇지 않은 경우 명령 프롬프트 창을 연 다음, `java` 명령을 사용하여 컴파일된 클래스를 실행합니다. 예: `java Main`.

```java
// This sample uses the following libraries:
//  - Apache HTTP client (org.apache.httpcomponents:httpclient:4.5.5)
//  - Apache HTTP core (org.apache.httpcomponents:httpccore:4.4.9)
//  - JSON library (org.json:json:20180130).


public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the West Central US region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API method.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200) {
                // Display the thumbnail.
                System.out.println("\nDisplaying thumbnail.\n");
                displayImage(entity.getContent());
            } else {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("Error:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }

    // Displays the given input stream as an image.
    private static void displayImage(InputStream inputStream) {
        try {
            BufferedImage bufferedImage = ImageIO.read(inputStream);

            ImageIcon imageIcon = new ImageIcon(bufferedImage);

            JLabel jLabel = new JLabel();
            jLabel.setIcon(imageIcon);

            JFrame jFrame = new JFrame();
            jFrame.setLayout(new FlowLayout());
            jFrame.setSize(100, 150);

            jFrame.add(jLabel);
            jFrame.setVisible(true);
            jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="examine-the-response"></a>응답 검사

성공적인 응답이 썸네일에 대한 이미지 데이터를 나타내는 이진 데이터로 반환됩니다. 요청이 성공하면 응답으로 이진 데이터에서 썸네일이 생성되고 응용 프로그램 예제에서 만든 별도 창에 표시됩니다. 요청이 실패하면 응답이 콘솔 창에 표시됩니다. 실패된 요청에 대한 응답은 무엇이 잘못되었는지 확인할 수 있도록 오류 코드 및 메시지를 포함합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 컴파일된 클래스 및 가져온 라이브러리를 포함하여 Java 프로젝트를 삭제합니다.

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하는 Java Swing 응용 프로그램을 탐색합니다. 이 프로그램은 OCR(광학 문자 인식)을 수행하고, 스마트하게 자른 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 특징을 감지하고, 분류하고, 태그를 지정하고, 설명합니다. Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.

> [!div class="nextstepaction"]
> [Computer Vision API Java 자습서](../Tutorials/java-tutorial.md)
