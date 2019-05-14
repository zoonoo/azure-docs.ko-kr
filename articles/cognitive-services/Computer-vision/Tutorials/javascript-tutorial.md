---
title: 이미지 작업 수행 - JavaScript
titlesuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 Computer Vision API를 사용하는 기본 JavaScript 앱을 살펴봅니다. OCR를 수행하고, 썸네일을 만들고, 이미지의 시각적 기능으로 작업합니다.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 91af70406590ab8e65a5d4a4b53835e9e4d4ed2a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231653"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-javascript"></a>REST API 및 JavaScript를 사용 하 여 컴퓨터 비전 기능을 사용 합니다.

이 가이드에서는 Azure Cognitive Services의 Computer Vision REST API의 기능을 보여 줍니다.

Computer Vision REST API를 사용하는 JavaScript 애플리케이션을 탐색하여 OCR(광학 인식)을 수행하고, 스마트하게 잘리는 썸네일을 만들고, 이미지에서 얼굴을 포함한 시각적 기능을 감지, 분류, 태그 지정 및 설명합니다. 이 예제에서는 분석 또는 처리할 이미지 URL을 제출할 수 있습니다. 이 오픈 소스 예제는 Computer Vision REST API를 사용하는 사용자 고유의 JavaScript 앱을 빌드하기 위한 템플릿으로 사용할 수 있습니다.

JavaScript 양식 애플리케이션은 이미 작성되어 있지만 Computer Vision 기능이 없습니다. 이 가이드에서는 응용 프로그램의 기능을 완료 하려면 컴퓨터 비전 REST API에 특정 코드를 추가 합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="platform-requirements"></a>플랫폼 요구 사항

간단한 텍스트 편집기를 사용 하 여이 가이드의 단계를 수행할 수 있습니다.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Computer Vision API 구독 및 구독 키 가져오기

예제를 만들기 전에 먼저 Azure Cognitive Services의 일부인 Computer Vision API를 구독해야 합니다. 구독 및 키 관리에 대한 자세한 내용은 [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요. 기본 및 보조 키를 모두이 가이드의 사용에 유효 합니다.

## <a name="acquire-incomplete-tutorial-project"></a>불완전 한 tutorial 프로젝트를 획득 합니다.

### <a name="download-the-project"></a>프로젝트를 다운로드합니다.

[Cognitive Services JavaScript Computer Vision 자습서](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial)를 복제하거나 .zip 파일을 다운로드하여 빈 디렉터리에 추출합니다.

완료 된 프로젝트를 추가 하는 모든 자습서 코드를 사용 하 여 사용 하려는 경우에 파일을 사용할 수 있습니다 합니다 **Completed** 폴더입니다.

## <a name="add-tutorial-code-to-the-project"></a>자습서 코드 프로젝트에 추가

JavaScript 애플리케이션은 각 기능마다 하나씩 6개의 .html 파일로 설정되어 있습니다. 각 파일에 Computer Vision의 다른 함수를 보여 줍니다 (분석, OCR, 등.). 하나의 파일, 6 개의 파일 또는 파일의 두 자습서 코드를 추가할 수 있도록 여섯 가지 섹션 상호 종속성을 갖지 않습니다. 그리고 자습서 코드는 임의의 순서로 파일에 추가할 수 있습니다.

### <a name="analyze-an-image"></a>이미지 분석

Computer Vision의 분석 기능에는 수천 개의 인식할 수 있는, 생물, 경치 및 동작에 대 한 이미지를 검색합니다. 분석이 완료되면 [분석]에서 설명 태그, 색 분석, 캡션 등으로 이미지를 설명하는 JSON 개체를 반환합니다.

응용 프로그램의 분석 기능을 완료 하려면 다음 단계를 수행 합니다.

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>분석 단추에 대 한 이벤트 처리기 코드를 추가 합니다.

텍스트 편집기에서 **analyze.html** 파일을 열고, 파일의 아래쪽에서 **analyzeButtonClick** 함수를 찾습니다.

**analyzeButtonClick** 이벤트 처리기 함수는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **AnalyzeImage** 함수를 호출하여 이미지를 분석합니다. 다음 코드를 복사하여 **analyzeButtonClick** 함수에 붙여넣습니다.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**AnalyzeImage** 함수는 REST API 호출을 래핑하여 이미지를 분석합니다. 성공적으로 반환되면 형식이 지정된 JSON 분석이 지정된 텍스트 영역에 표시되고 캡션이 지정된 범위에 표시됩니다.

**AnalyzeImage** 함수 코드를 복사하여 **analyzeButtonClick** 함수 바로 아래에 붙여넣습니다.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-analyze-function"></a>분석 함수를 실행 합니다.

**analyze.html** 파일을 저장하고, 웹 브라우저에서 엽니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. 분석할 이미지에 대한 URL을 입력한 다음, **이미지 분석** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="recognize-a-landmark"></a>랜드마크 인식

Computer Vision의 [랜드마크] 기능은 산이나 유명한 건물과 같은 자연 및 인공 랜드마크에 대한 이미지를 분석합니다. 분석이 완료되면 [랜드마크]에서 이미지에 있는 랜드마크를 식별하는 JSON 개체를 반환합니다.

응용 프로그램의 랜드마크 기능을 완료 하려면 다음 단계를 수행 합니다.

#### <a name="add-the-event-handler-code-for-the-landmark-button"></a>랜드마크 단추에 대 한 이벤트 처리기 코드를 추가 합니다.

텍스트 편집기에서 **landmark.html** 파일을 열고, 파일의 아래쪽에서 **landmarkButtonClick** 함수를 찾습니다.

**landmarkButtonClick** 이벤트 처리기 함수는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **IdentifyLandmarks** 함수를 호출하여 이미지를 분석합니다. 다음 코드를 복사하여 **landmarkButtonClick** 함수에 붙여넣습니다.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**IdentifyLandmarks** 함수는 REST API 호출을 래핑하여 이미지를 분석합니다. 성공적으로 반환되면 형식이 지정된 JSON 분석이 지정된 텍스트 영역에 표시되고 캡션이 지정된 범위에 표시됩니다.

**IdentifyLandmarks** 함수 코드를 복사하여 **landmarkButtonClick** 함수 바로 아래에 붙여넣습니다.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-landmark-function"></a>랜드마크 함수 실행

**landmark.html** 파일을 저장하고, 웹 브라우저에서 엽니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. 분석할 이미지에 대한 URL을 입력한 다음, **이미지 분석** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="recognize-celebrities"></a>유명 인사 인식

Computer Vision의 [유명 인사] 기능은 유명 인사에 대한 이미지를 분석합니다. 분석이 완료되면 [유명 인사]에서 이미지에 있는 유명 인사를 식별하는 JSON 개체를 반환합니다.

응용 프로그램의 유명인 200,000 명을 기능을 완료 하려면 다음 단계를 수행 합니다.

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>유명인 200,000 명 단추에 대 한 이벤트 처리기 코드를 추가 합니다.

텍스트 편집기에서 **celebrities.html** 파일을 열고, 파일의 아래쪽에서 **celebritiesButtonClick** 함수를 찾습니다.

**celebritiesButtonClick** 이벤트 처리기 함수는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **IdentifyCelebrities** 함수를 호출하여 이미지를 분석합니다. 다음 코드를 복사하여 **celebritiesButtonClick** 함수에 붙여넣습니다.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-celebrities-function"></a>유명인 200,000 명 함수 실행

**celebrities.html** 파일을 저장하고, 웹 브라우저에서 엽니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. 분석할 이미지에 대한 URL을 입력한 다음, **이미지 분석** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="intelligently-generate-a-thumbnail"></a>지능적으로 썸네일 생성

Computer Vision의 [썸네일] 기능은 이미지에서 썸네일을 생성합니다. **스마트 자르기** 기능을 사용하면 [썸네일] 기능에서 이미지의 관심 영역을 식별하고 이 영역에서 썸네일을 가운데에 맞춰 미적으로 더욱 만족스러운 썸네일 이미지를 생성할 수 있습니다.

응용 프로그램의 미리 보기 기능을 완료 하려면 다음 단계를 수행 합니다.

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>축소판 그림 단추에 대 한 이벤트 처리기 코드를 추가 합니다.

텍스트 편집기에서 **thumbnail.html** 파일을 열고, 파일의 아래쪽에서 **thumbnailButtonClick** 함수를 찾습니다.

**thumbnailButtonClick** 이벤트 처리기 함수는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **getThumbnail** 함수를 두 번 호출하여 두 개의 썸네일을 만듭니다. 하나는 스마트하게 잘린 썸네일이고, 다른 하나는 스마트하게 잘리지 않은 썸네일입니다. 다음 코드를 복사하여 **thumbnailButtonClick** 함수에 붙여넣습니다.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**getThumbnail** 함수는 REST API 호출을 래핑하여 이미지를 분석합니다. 성공적으로 반환되면 썸네일이 지정된 img 요소에 표시됩니다.

다음 **getThumbnail** 함수를 복사하여 **thumbnailButtonClick** 함수 바로 아래에 붙여넣습니다.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumbnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-thumbnail-function"></a>미리 보기 함수 실행

**thumbnail.html** 파일을 저장하고, 웹 브라우저에서 엽니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. 분석할 이미지에 대한 URL을 입력한 다음, **썸네일 생성** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="read-printed-text-ocr"></a>인쇄된 텍스트 읽기(OCR)

Computer Vision의 OCR(광학 인식) 기능은 인쇄된 텍스트의 이미지를 분석합니다. 분석이 완료되면 OCR에서 텍스트와 이미지 내 텍스트 위치가 포함된 JSON 개체를 반환합니다.

응용 프로그램의 OCR 기능을 완료 하려면 다음 단계를 수행 합니다.

### <a name="add-the-event-handler-code-for-the-ocr-button"></a>OCR 단추에 대 한 이벤트 처리기 코드를 추가 합니다.

텍스트 편집기에서 **ocr.html** 파일을 열고, 파일의 아래쪽에서 **ocrButtonClick** 함수를 찾습니다.

**ocrButtonClick** 이벤트 처리기 함수는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **ReadOcrImage** 함수를 호출하여 이미지를 분석합니다. 다음 코드를 복사하여 **ocrButtonClick** 함수에 붙여넣습니다.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**ReadOcrImage** 함수는 REST API 호출을 래핑하여 이미지를 분석합니다. 성공적으로 반환되면 텍스트와 텍스트의 위치를 설명하는 형식이 지정된 JSON이 지정된 텍스트 영역에 표시됩니다.

다음 **ReadOcrImage** 함수를 복사하여 **ocrButtonClick** 함수 바로 아래에 붙여넣습니다.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-ocr-function"></a>OCR 함수 실행

**ocr.html** 파일을 저장하고’ 웹 브라우저에서 엽니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. 읽을 텍스트의 이미지에 대한 URL을 입력한 다음, **이미지 읽기** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

### <a name="read-handwritten-text-handwriting-recognition"></a>필기 텍스트 읽기(필기 인식)

Computer Vision의 [필기 인식] 기능은 손으로 쓴 텍스트의 이미지를 분석합니다. 분석이 완료되면 [필기 인식]에서 텍스트와 이미지 내 텍스트 위치가 포함된 JSON 개체를 반환합니다.

응용 프로그램의 필기 인식 기능을 완료 하려면 다음 단계를 수행 합니다.

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>필기 단추에 대 한 이벤트 처리기 코드를 추가 합니다.

텍스트 편집기에서 **handwriting.html** 파일을 열고, 파일의 아래쪽에서 **handwritingButtonClick** 함수를 찾습니다.

**handwritingButtonClick** 이벤트 처리기 함수는 양식을 지우고, URL에 지정된 이미지를 표시한 다음, **HandwritingImage** 함수를 호출하여 이미지를 분석합니다.

다음 코드를 복사하여 **handwritingButtonClick** 함수에 붙여넣습니다.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>REST API 호출에 대한 래퍼 추가

**ReadHandwrittenImage** 함수는 이미지를 분석하는 데 필요한 두 개의 REST API 호출을 래핑합니다. [필기 인식]은 시간이 오래 걸리는 프로세스이므로 2단계 프로세스가 사용됩니다. 첫 번째 호출에서 처리할 이미지를 제출합니다. 처리가 완료되면 두 번째 호출에서 감지된 텍스트를 검색합니다.

텍스트가 검색되면 텍스트와 텍스트의 위치를 설명하는 형식이 지정된 JSON이 지정된 텍스트 영역에 표시됩니다.

다음 **ReadHandwrittenImage** 함수를 복사하여 **handwritingButtonClick** 함수 바로 아래에 붙여넣습니다.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-handwriting-function"></a>필기 함수 실행

**handwriting.html** 파일을 저장하고, 웹 브라우저에서 엽니다. 구독 키를 **구독 키** 필드에 배치하고, **구독 지역**에서 올바른 지역을 사용하고 있는지 확인합니다. 읽을 텍스트의 이미지에 대한 URL을 입력한 다음, **이미지 읽기** 단추를 클릭하여 이미지를 분석하고 결과를 표시합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 다양 한 사용 가능한 이미지 분석 기능을 테스트 하려면 JavaScript를 사용 하 여 컴퓨터 비전 REST API를 사용 합니다. 다음으로, 관련 Api에 대 한 자세한 내용을 알아보려면 참조 설명서를 참조 하십시오.

- [Computer Vision REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
