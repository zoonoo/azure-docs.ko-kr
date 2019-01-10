---
title: '자습서: 이미지 작업 수행 - Java'
titlesuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 Computer Vision API를 사용하는 기본 Java Swing 앱을 살펴봅니다. OCR를 수행하고, 썸네일을 만들고, 이미지의 시각적 기능으로 작업합니다.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.author: kefre
ms.custom: seodec18
ms.date: 09/21/2017
ms.openlocfilehash: ef1180a8e8c833648e3b61705abf354cb4dfaed3
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117489"
---
# <a name="tutorial-computer-vision-api-java"></a>자습서: Computer Vision API Java

이 자습서에서는 Azure Cognitive Services Computer Vision REST API의 기능을 보여 줍니다.

Computer Vision REST API를 사용하는 Java Swing 애플리케이션을 탐색하여 OCR(광학 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지에서 얼굴을 포함한 시각적 기능을 감지, 분류, 태그 지정 및 설명합니다. 이 예제에서는 분석 또는 처리할 이미지 URL을 제출할 수 있습니다. 이 오픈 소스 예제는 Java에서 사용자 고유의 앱을 빌드하여 Computer Vision REST API를 사용하기 위한 템플릿으로 사용할 수 있습니다.

이 자습서에서는 Computer Vision을 사용하여 다음을 수행하는 방법을 설명합니다.

> [!div class="checklist"]
> * 이미지 분석
> * 이미지에서 자연 또는 인공 랜드마크 식별
> * 이미지에서 유명인 식별
> * 이미지에서 고품질 썸네일 만들기
> * 이미지에서 인쇄 텍스트 읽기
> * 이미지에서 필기 텍스트 읽기

Java Swing 양식 애플리케이션은 이미 작성되어 있지만 기능이 없습니다. 이 자습서에서는 Computer Vision REST API 관련 코드를 추가하여 애플리케이션의 기능을 완성합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="platform-requirements"></a>플랫폼 요구 사항

이 자습서는 NetBeans IDE를 사용하여 개발되었습니다. 특히 [여기에서 다운로드](https://netbeans.org/downloads/index.html)할 수 있는 NetBeans의 **Java SE** 버전.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Computer Vision API 구독 및 구독 키 가져오기 

예제를 만들기 전에 먼저 Azure Cognitive Services의 일부인 Computer Vision API를 구독해야 합니다. 구독 및 키 관리에 대한 자세한 내용은 [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요. 기본 및 보조 키는 모두 이 자습서에서 사용할 수 있습니다. 

## <a name="acquire-the-incomplete-tutorial-project"></a>완료되지 않은 자습서 프로젝트 획득

### <a name="download-the-tutorial-project"></a>자습서 프로젝트 다운로드

1. [Cognitive Services Java Computer Vision 자습서](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial) 리포지토리로 이동합니다.
1. **복제 또는 다운로드** 단추를 클릭합니다.
1. **ZIP 파일 다운로드**를 클릭하여 자습서 프로젝트의 .zip 파일을 다운로드합니다.

NetBeans는 .zip 파일에서 프로젝트를 가져오므로 .zip 파일의 콘텐츠를 추출할 필요가 없습니다.

### <a name="import-the-tutorial-project"></a>자습서 프로젝트 가져오기

**cognitive-services-java-computer-vision-tutorial-master.zip** 파일을 NetBeans로 가져옵니다.

1. NetBeans에서 **파일** > **프로젝트 가져오기** > **ZIP에서...** 를 클릭합니다. **ZIP에서 프로젝트 가져오기** 대화 상자가 표시됩니다.
1. **ZIP 파일:** 필드에서 **찾아보기** 단추를 클릭하여 **cognitive-services-java-computer-vision-tutorial-master.zip** 파일을 찾은 후 **열기**를 클릭합니다.
1. **ZIP에서 프로젝트 가져오기** 대화 상자에서 **가져오기**를 클릭합니다.
1. **프로젝트** 패널에서 **ComputerVision** > **소스 패키지** > **&lt;기본 패키지&gt;** 를 확장합니다. 
   일부 NetBeans 버전에서는 **소스 패키지** > **&lt;기본 패키지&gt;** 대신 **src**를 사용합니다. 이 경우 **src**를 확장합니다.
1. **MainFrame.java**를 두 번 클릭하여 파일을 NetBeans 편집기로 로드합니다. **MainFrame.java** 파일의 **디자인** 탭이 표시됩니다.
1. **소스** 탭을 클릭하여 Java 소스 코드를 표시합니다.

### <a name="build-and-run-the-tutorial-project"></a>자습서 프로젝트 빌드 및 실행

1. **F6** 키를 눌러 자습서 애플리케이션을 빌드 및 실행합니다.

    자습서 애플리케이션에서 Tab 키를 클릭하여 해당 기능의 창을 표시합니다. 단추에는 빈 메서드가 있으므로 아무 작업도 수행하지 않습니다.

    창 아래쪽에 **구독 키** 및 **구독 지역** 필드가 있습니다. 이러한 필드에 유효한 구독 키 및 해당 구독 키의 올바른 지역을 입력해야 합니다. 구독 키를 얻으려면 [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요. 해당 링크의 평가판에서 구독 키를 얻은 경우 기본 지역 **westcentralus**가 구독 키의 올바른 지역입니다.

1. 자습서 애플리케이션을 종료합니다.

## <a name="add-the-tutorial-code-to-the-project"></a>프로젝트에 자습서 코드 추가

Java Swing 애플리케이션은 6개의 탭으로 설치됩니다. 각 탭은 Computer Vision의 서로 다른 함수(분석, OCR 등)를 보여줍니다. 6개의 자습서 섹션이 서로 독립적이므로 한 섹션을 추가할 수도 있고, 6개 섹션을 모두 추가할 수도 있고, 하위 집합을 추가할 수도 있습니다. 또한 섹션을 순서에 관계없이 추가할 수 있습니다.

### <a name="analyze-an-image"></a>이미지 분석

Computer Vision의 [분석] 기능은 이미지를 검사하여 2,000개가 넘는 인식 가능한 물체, 생물, 풍경 및 동작을 분석합니다. 분석이 완료되면 [분석]에서 설명 태그, 색 분석, 캡션 등으로 이미지를 설명하는 JSON 개체를 반환합니다.

자습서 애플리케이션의 [분석] 기능을 완성하려면 다음 단계를 수행합니다.

#### <a name="add-the-event-handler-code-for-the-form-button"></a>양식 단추에 대한 이벤트 처리기 코드 추가

**analyzeImageButtonActionPerformed** 이벤트 처리기 메서드는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **AnalyzeImage** 메서드를 호출하여 이미지를 분석합니다. **AnalyzeImage**가 반환되면 메서드는 **응답** 텍스트 영역에 형식 지정된 JSON 응답을 표시하고, **JSONObject**에서 첫 번째 캡션을 추출하고, 캡션 및 캡션이 올바른 신뢰도 수준을 표시합니다.

다음 코드를 복사하여 **analyzeImageButtonActionPerformed** 메서드에 붙여넣습니다.

> [!NOTE]
> NetBeans에서는 메서드 정의 줄(```private void```) 또는 해당 메서드의 닫는 중괄호에 붙여넣을 수 없습니다. 코드를 복사하려면 메서드 정의와 닫는 중괄호 사이의 줄을 복사하고 메서드의 콘텐츠에 붙여넣습니다.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**AnalyzeImage** 메서드는 REST API 호출을 래핑하여 이미지를 분석합니다. 메서드는 이미지를 설명하는 **JSONObject**를 반환하거나 오류가 있는 경우에는 **null**을 반환합니다.

**AnalyzeImage** 메서드를 복사하여 **analyzeImageButtonActionPerformed** 메서드의 바로 밑에 붙여넣습니다.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-application"></a>애플리케이션 실행

**F6** 키를 눌러 애플리케이션을 실행합니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. 분석할 이미지에 대한 URL을 입력한 다음, **이미지 분석** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="recognize-a-landmark"></a>랜드마크 인식

Computer Vision의 [랜드마크] 기능은 산이나 유명한 건물과 같은 자연 및 인공 랜드마크에 대한 이미지를 분석합니다. 분석이 완료되면 [랜드마크]에서 이미지에 있는 랜드마크를 식별하는 JSON 개체를 반환합니다.

자습서 애플리케이션의 [랜드마크] 기능을 완성하려면 다음 단계를 수행합니다.

#### <a name="add-the-event-handler-code-for-the-form-button"></a>양식 단추에 대한 이벤트 처리기 코드 추가

**landmarkImageButtonActionPerformed** 이벤트 처리기 메서드는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **LandmarkImage** 메서드를 호출하여 이미지를 분석합니다. **LandmarkImage**가 반환되면 메서드는 **응답** 텍스트 영역에 형식 지정된 JSON 응답을 표시하고, **JSONObject**에서 첫 번째 랜드마크 이름을 추출하고, 랜드마크가 올바르게 식별된 신뢰도 수준과 함께 이름을 창에 표시합니다.

다음 코드를 복사하여 **landmarkImageButtonActionPerformed** 메서드에 붙여넣습니다.

> [!NOTE]
> NetBeans에서는 메서드 정의 줄(```private void```) 또는 해당 메서드의 닫는 중괄호에 붙여넣을 수 없습니다. 코드를 복사하려면 메서드 정의와 닫는 중괄호 사이의 줄을 복사하고 메서드의 콘텐츠에 붙여넣습니다.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**LandmarkImage** 메서드는 REST API 호출을 래핑하여 이미지를 분석합니다. 메서드는 이미지에 있는 랜드마크를 설명하는 **JSONObject**를 반환하거나 오류가 있는 경우에는 **null**을 반환합니다.

**LandmarkImage** 메서드를 복사하여 **landmarkImageButtonActionPerformed** 메서드의 바로 밑에 붙여넣습니다.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>애플리케이션 실행

**F6** 키를 눌러 애플리케이션을 실행합니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. **랜드마크** 탭을 클릭하고, 랜드마크 이미지의 URL을 입력한 다음, **이미지 분석** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="recognize-celebrities"></a>유명 인사 인식

Computer Vision의 [유명 인사] 기능은 유명 인사에 대한 이미지를 분석합니다. 분석이 완료되면 [유명 인사]에서 이미지에 있는 유명 인사를 식별하는 JSON 개체를 반환합니다.

자습서 애플리케이션의 [유명 인사] 기능을 완성하려면 다음 단계를 수행합니다.

#### <a name="add-the-event-handler-code-for-the-form-button"></a>양식 단추에 대한 이벤트 처리기 코드 추가

**celebritiesImageButtonActionPerformed** 이벤트 처리기 메서드는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **CelebritiesImage** 메서드를 호출하여 이미지를 분석합니다. **CelebritiesImage**가 반환되면 메서드는 **응답** 텍스트 영역에 형식 지정된 JSON 응답을 표시하고, **JSONObject**에서 첫 번째 유명인 이름을 추출하고, 유명인이 올바르게 식별된 신뢰도 수준과 함께 이름을 창에 표시합니다.

다음 코드를 복사하여 **celebritiesImageButtonActionPerformed** 메서드에 붙여넣습니다.

> [!NOTE]
> NetBeans에서는 메서드 정의 줄(```private void```) 또는 해당 메서드의 닫는 중괄호에 붙여넣을 수 없습니다. 코드를 복사하려면 메서드 정의와 닫는 중괄호 사이의 줄을 복사하고 메서드의 콘텐츠에 붙여넣습니다.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**CelebritiesImage** 메서드는 REST API 호출을 래핑하여 이미지를 분석합니다. 메서드는 이미지에 있는 유명인을 설명하는 **JSONObject**를 반환하거나 오류가 있는 경우에는 **null**을 반환합니다.

**CelebritiesImage** 메서드를 복사하여 **celebritiesImageButtonActionPerformed** 메서드의 바로 밑에 붙여넣습니다.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>애플리케이션 실행

**F6** 키를 눌러 애플리케이션을 실행합니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. **유명인** 탭을 클릭하고, 유명인 이미지의 URL을 입력한 다음, **이미지 분석** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="intelligently-generate-a-thumbnail"></a>지능적으로 썸네일 생성

Computer Vision의 썸네일 기능은 이미지에서 썸네일을 생성합니다. **스마트 자르기** 기능을 사용하면 썸네일 기능에서 이미지의 관심 영역을 식별하고 이 영역에서 썸네일을 가운데에 맞춰 미적으로 더욱 만족스러운 썸네일 이미지를 생성할 수 있습니다.

자습서 애플리케이션의 [썸네일] 기능을 완성하려면 다음 단계를 수행합니다.

#### <a name="add-the-event-handler-code-for-the-form-button"></a>양식 단추에 대한 이벤트 처리기 코드 추가

**thumbnailImageButtonActionPerformed** 이벤트 처리기 메서드는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **getThumbnailImage** 메서드를 호출하여 썸네일을 만듭니다. **getThumbnailImage**가 반환되면 메서드는 생성된 썸네일을 표시합니다.

다음 코드를 복사하여 **thumbnailImageButtonActionPerformed** 메서드에 붙여넣습니다.

> [!NOTE]
> NetBeans에서는 메서드 정의 줄(```private void```) 또는 해당 메서드의 닫는 중괄호에 붙여넣을 수 없습니다. 코드를 복사하려면 메서드 정의와 닫는 중괄호 사이의 줄을 복사하고 메서드의 콘텐츠에 붙여넣습니다.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**getThumbnailImage** 메서드는 REST API 호출을 래핑하여 이미지를 분석합니다. 메서드는 썸네일이 포함된 **BufferedImage**를 반환하거나 오류가 있는 경우에는 **null**을 반환합니다. 오류 메시지는 **jsonError** 문자열 배열의 첫 번째 요소로 반환됩니다.

**getThumbnailImage** 메서드를 복사하여 **thumbnailImageButtonActionPerformed** 메서드의 바로 밑에 붙여넣습니다.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-application"></a>애플리케이션 실행

**F6** 키를 눌러 애플리케이션을 실행합니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. **썸네일** 탭을 클릭하고, 이미지의 URL을 입력한 다음, **썸네일 생성** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="read-printed-text-ocr"></a>인쇄된 텍스트 읽기(OCR)

Computer Vision의 OCR(광학 인식) 기능은 인쇄된 텍스트의 이미지를 분석합니다. 분석이 완료되면 OCR에서 텍스트와 이미지 내 텍스트 위치가 포함된 JSON 개체를 반환합니다.

자습서 애플리케이션의 OCR 기능을 완성하려면 다음 단계를 수행합니다.

#### <a name="add-the-event-handler-code-for-the-form-button"></a>양식 단추에 대한 이벤트 처리기 코드 추가

**ocrImageButtonActionPerformed** 이벤트 처리기 메서드는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **OcrImage** 메서드를 호출하여 이미지를 분석합니다. **OcrImage**가 반환되면 메서드는 **응답** 텍스트 영역에 검색된 텍스트를 형식 지정된 JSON으로 표시합니다.

다음 코드를 복사하여 **ocrImageButtonActionPerformed** 메서드에 붙여넣습니다.

> [!NOTE]
> NetBeans에서는 메서드 정의 줄(```private void```) 또는 해당 메서드의 닫는 중괄호에 붙여넣을 수 없습니다. 코드를 복사하려면 메서드 정의와 닫는 중괄호 사이의 줄을 복사하고 메서드의 콘텐츠에 붙여넣습니다.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**OcrImage** 메서드는 REST API 호출을 래핑하여 이미지를 분석합니다. 메서드는 호출에서 반환된 JSON 데이터의 **JSONObject**를 반환하거나 오류가 있는 경우에는 **null**을 반환합니다.

**OcrImage** 메서드를 복사하여 **ocrImageButtonActionPerformed** 메서드의 바로 밑에 붙여넣습니다.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>애플리케이션 실행

**F6** 키를 눌러 애플리케이션을 실행합니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. **OCR** 탭을 클릭하고, 인쇄 텍스트 이미지의 URL을 입력한 다음, **이미지 읽기** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="read-handwritten-text-handwriting-recognition"></a>필기 텍스트 읽기(필기 인식)

Computer Vision의 [필기 인식] 기능은 손으로 쓴 텍스트의 이미지를 분석합니다. 분석이 완료되면 [필기 인식]에서 텍스트와 이미지 내 텍스트 위치가 포함된 JSON 개체를 반환합니다.

자습서 애플리케이션의 [필기 인식] 기능을 완성하려면 다음 단계를 수행합니다.

#### <a name="add-the-event-handler-code-for-the-form-button"></a>양식 단추에 대한 이벤트 처리기 코드 추가

**handwritingImageButtonActionPerformed** 이벤트 처리기 메서드는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **HandwritingImage** 메서드를 호출하여 이미지를 분석합니다. **HandwritingImage**가 반환되면 메서드는 **응답** 텍스트 영역에 검색된 텍스트를 형식 지정된 JSON으로 표시합니다.

다음 코드를 복사하여 **handwritingImageButtonActionPerformed** 메서드에 붙여넣습니다.

> [!NOTE]
> NetBeans에서는 메서드 정의 줄(```private void```) 또는 해당 메서드의 닫는 중괄호에 붙여넣을 수 없습니다. 코드를 복사하려면 메서드 정의와 닫는 중괄호 사이의 줄을 복사하고 메서드의 콘텐츠에 붙여넣습니다.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**HandwritingImage** 메서드는 이미지를 분석하는 데 필요한 두 개의 REST API 호출을 래핑합니다. 필기 인식은 시간이 오래 걸리는 프로세스이므로 2단계 프로세스가 사용됩니다. 첫 번째 호출에서 처리할 이미지를 제출합니다. 처리가 완료되면 두 번째 호출에서 감지된 텍스트를 검색합니다.

텍스트가 검색된 후 **HandwritingImage** 메서드는 텍스트 및 텍스트 위치를 설명하는 **JSONObject**를 반환하거나 오류가 있는 경우에는 **null**을 반환합니다.

**HandwritingImage** 메서드를 복사하여 **handwritingImageButtonActionPerformed** 메서드의 바로 밑에 붙여넣습니다.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>애플리케이션 실행

애플리케이션을 실행하려면 **F6** 키를 누릅니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. **필기 텍스트 읽기** 탭을 클릭하고, 필기 텍스트 이미지의 URL을 입력한 다음, **이미지 읽기** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

## <a name="next-steps"></a>다음 단계

- [Computer Vision API C# 자습서](CSharpTutorial.md)
- [Computer Vision API Python 자습서](PythonTutorial.md)
