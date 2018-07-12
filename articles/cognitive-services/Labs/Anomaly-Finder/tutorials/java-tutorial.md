---
title: 변칙 검색 Java 앱 - Microsoft Cognitive Services | Microsoft Docs
description: Microsoft Cognitive Services의 변칙 검색 API를 사용하는 Java 앱을 탐색합니다. 원래 데이터 요소를 API에 전송하고 예상 값과 변칙 요소를 가져옵니다.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375326"
---
# <a name="anomaly-detection-java-application"></a>변칙 검색 Java 응용 프로그램

이 문서에서는 단순 Java 응용 프로그램을 사용하여 변칙 검색 API를 호출하는 방법을 설명합니다.  
예제는 시계열 데이터를 구독 키가 있는 변칙 검색 API에 제출한 후 API에서 각 데이터 요소에 대한 모든 변칙 요소와 예상 값을 가져옵니다.

## <a name="prerequisites"></a>필수 조건

### <a name="platform-requirements"></a>플랫폼 요구 사항

이 자습서는 [IntelliJ IDEA](https://www.jetbrains.com/idea)를 사용하여 개발되었습니다. 또한 [JDK(Java Development Kit)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 버전 1.8 이상과 최신 [Apache Maven](http://maven.apache.org/) 빌드 도구를 설치해야 합니다.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>변칙 검색을 구독하고 구독 키 받기 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>자습서 프로젝트 다운로드

1. MicrosoftAnomalyDetection [Java 리포지토리](https://github.com/MicrosoftAnomalyDetection/java-sample)로 이동합니다.
2. 복제 또는 다운로드 단추를 클릭합니다.
3. ZIP 파일 다운로드를 클릭하여 자습서 프로젝트의 .zip 파일을 다운로드합니다.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>자습서 프로젝트 열기

1. 자습서 프로젝트의 .zip 파일을 추출합니다.
2. IntelliJ IDEA에서 **파일 > 열기**를 클릭합니다. 파일 또는 프로젝트 열기 대화 상자가 나타납니다.
3. 추출된 프로젝트의 루트 경로를 선택하고 확인을 클릭합니다.
4. 프로젝트 패널에서 **src > main > java**를 확장합니다.
5. com.microsoft.cognitiveservice.anomalydetection.Main.java를 두 번 클릭하여 파일을 편집기에 로드합니다.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>subscriptionKey 및 URI 영역 바꾸기

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>자습서 프로젝트 빌드 및 실행

1. com.microsoft.cognitiveservice.anomalydetection.Main.java 소스 코드 탭에서 아무 곳이나 마우스 오른쪽 단추로 클릭하여 메뉴를 엽니다. 
2. ‘Main.main()’ 실행을 선택합니다.
3. 샘플 요청의 결과가 반환되고 터미널에 표시됩니다.

### <a name="result-of-the-tutorial-project"></a>자습서 프로젝트의 결과

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
