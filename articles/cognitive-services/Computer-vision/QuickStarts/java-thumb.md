---
title: '빠른 시작: 썸네일 생성 - REST, Java'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java와 함께 Computer Vision API를 사용하여 이미지에서 썸네일을 생성합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: a1057fa04d5834b0bebb98d9d66a35a21f1a4558
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325494"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-java"></a>빠른 시작: Computer Vision REST API 및 Java를 사용하여 썸네일 생성

이 빠른 시작에서는 Computer Vision REST API를 사용하여 이미지에서 썸네일을 생성합니다. 높이와 너비를 지정합니다. 입력 이미지의 가로 세로 비율과 다를 수 있습니다. Computer Vision은 스마트 자르기를 사용하여 관심 영역을 지능적으로 식별하고 해당 영역을 기반으로 자르기 좌표를 생성합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Java&trade; 플랫폼, Standard Edition Development Kit 7 또는 8](https://aka.ms/azure-jdks)(JDK 7 또는 8)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 키 및 서비스 엔드포인트 URL에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="create-and-run-the-sample-application"></a>애플리케이션 예제 만들기 및 실행

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 즐겨 찾는 IDE 또는 편집기에서 새 Java 프로젝트를 만듭니다. 옵션을 사용할 수 있는 경우 명령줄 애플리케이션 템플릿에서 Java 프로젝트를 만듭니다.
1. Java 프로젝트로 다음 라이브러리를 가져옵니다. Maven을 사용하는 경우 각 라이브러리에 대한 Maven 좌표가 제공됩니다.
   - [Apache HTTP 클라이언트](https://hc.apache.org/downloads.cgi)(org.apache.httpcomponents:httpclient:4.5.X)
   - [Apache HTTP 코어](https://hc.apache.org/downloads.cgi)(org.apache.httpcomponents:httpcore:4.4.X)
   - [JSON 라이브러리](https://github.com/stleary/JSON-java)(org.json:json:20180130)
1. 주 클래스에 다음 `import` 문을 추가합니다. 

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

1. 아래에 있는 나머지 샘플 코드를 가져오기 아래에 추가합니다(필요한 경우 클래스 이름으로 변경).
1. 환경 변수에 Computer Vision 구독 키와 엔드포인트를 추가합니다.
1. 필요에 따라 `imageToAnalyze` 값을 고유한 이미지의 URL로 바꿉니다.
1. 저장한 다음, Java 프로젝트를 빌드합니다.
1. IDE를 사용하는 경우 `GenerateThumbnail`을 실행합니다. 그렇지 않으면 명령줄(아래 명령)에서 실행합니다.

```java
/**
 * This sample uses the following libraries (create a "lib" folder to place them in): 
 * Apache HTTP client:
 * org.apache.httpcomponents:httpclient:4.5.X 
 * Apache HTTP core:
 * org.apache.httpcomponents:httpccore:4.4.X 
 * JSON library:
 * org.json:json:20180130
 *
 * To build/run from the command line: 
 *     javac GenerateThumbnail.java -cp .;lib\*
 *     java -cp .;lib\* GenerateThumbnail
 */

public class GenerateThumbnail {

    // Add your Computer Vision subscription key and endpoint to your environment
    // variables. Then, close and then re-open your command shell or project for the
    // changes to take effect.
    private static String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    private static String endpoint = System.getenv("COMPUTER_VISION_ENDPOINT");
    // The endpoint path
    private static final String uriBase = endpoint + "vision/v3.0/generateThumbnail";
    // It's optional if you'd like to use your own image instead of this one.
    private static final String imageToAnalyze = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

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
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            System.out.println("status" + response.getStatusLine().getStatusCode());

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
            e.printStackTrace();
        }
    }

    // Displays the given input stream as an image.
    public static void displayImage(InputStream inputStream) {
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

성공적인 응답이 썸네일에 대한 이미지 데이터를 나타내는 이진 데이터로 반환됩니다. 요청이 성공하면 응답으로 이진 데이터에서 썸네일이 생성되고 애플리케이션 예제에서 만든 별도 창에 표시됩니다. 요청이 실패하면 응답이 콘솔 창에 표시됩니다. 실패된 요청에 대한 응답은 무엇이 잘못되었는지 확인할 수 있도록 오류 코드 및 메시지를 포함합니다.

## <a name="next-steps"></a>다음 단계

Computer Vision을 사용하여 OCR(광학 문자 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지의 시각적 기능을 검색, 분류, 태그 지정 및 설명하는 Java Swing 애플리케이션을 살펴봅니다.

> [!div class="nextstepaction"]
> [Computer Vision API Java 자습서](../Tutorials/java-tutorial.md)

* Computer Vision API를 사용하여 신속하게 실험하려면 [API 테스트 콘솔 열기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)를 사용하세요.
