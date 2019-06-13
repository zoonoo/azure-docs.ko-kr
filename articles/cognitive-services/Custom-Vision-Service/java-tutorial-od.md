---
title: '빠른 시작: Java용 Custom Vision SDK를 사용하여 개체 검색 프로젝트 만들기'
titlesuffix: Azure Cognitive Services
description: Java SDK를 사용하여 프로젝트를 만들고, 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 교육하고, 개체를 검색합니다.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 00684df614771437f33655538a808468ee778d29
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487008"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>빠른 시작: Java용 Custom Vision SDK를 사용하여 개체 검색 프로젝트 만들기

이 문서에서는 Java와 함께 Custom Vision SDK를 사용하여 개체 검색 모델 빌드를 시작할 수 있도록 도와주는 정보와 샘플 코드를 제공합니다. 프로젝트를 만든 후에는 태그가 지정된 지역을 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 엔드포인트 URL를 획득하고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. Java 응용 프로그램을 빌드하기 위한 템플릿으로 이 예제를 사용하세요.

## <a name="prerequisites"></a>필수 조건

- 원하는 Java IDE
- [JDK 7 또는 8](https://aka.ms/azure-jdks)이 설치됨.
- Maven 설치

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK 및 샘플 코드 다운로드

Custom Vision을 사용하는 Java 앱을 작성하려면 Custom Vision maven 패키지가 필요합니다. 이러한 요소는 여러분이 다운로드할 샘플 프로젝트에 포함되어 있지만, 여기서 개별적으로 액세스할 수도 있습니다.

Maven 중앙 리포지토리에서 Custom Vision SDK를 설치할 수 있습니다.
- [교육 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [예측 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

[Cognitive Services Java SDK 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) 프로젝트를 복제 또는 다운로드합니다. **Vision/CustomVision/** 폴더로 이동합니다.

이 Java 프로젝트는 [Custom Vision 웹 사이트](https://customvision.ai/)를 통해 액세스할 수 있는 __샘플 Java OD 프로젝트__ 라는 새로운 Custom Vision 개체 검색 프로젝트를 만듭니다. 그런 다음, 분류자를 학습하고 테스트하려면 이미지를 업로드합니다. 이 프로젝트의 분류자는 트리가 __솔송나무__ 인지 아니면 __벗나무__ 인지 확인하는 용도로 사용됩니다.

[!INCLUDE [get-keys](includes/get-keys.md)]

프로그램은 키 데이터를 환경 변수로 저장하도록 구성됩니다. PowerShell에서 **Vision/CustomVision** 폴더로 이동하여 이러한 변수를 설정합니다. 다음 명령을 입력합니다.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>코드 이해

Java IDE에서 `Vision/CustomVision` 프로젝트를 로드하고 _CustomVisionSamples.java_ 파일을 엽니다. **runSample** 메서드를 찾아서 **ImageClassification_Sample** 메서드 호출이라는 주석으로 처리합니다. 이렇게 하면 이미지 분류 시나리오가 실행되는데, 이 내용은 본 가이드에서 다루지 않습니다. **ObjectDetection_Sample** 메서드는 이 빠른 시작의 기본 기능을 구현합니다. 이 메서드의 정의로 이동하여 코드를 검사하세요. 

### <a name="create-a-new-custom-vision-service-project"></a>새 Custom Vision Service 프로젝트 만들기

교육 클라이언트 및 개체 검색 프로젝트를 만드는 코드 블록으로 이동합니다. 생성된 프로젝트는 앞에서 방문한 [Custom Vision 웹 사이트](https://customvision.ai/)에 표시됩니다. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>프로젝트에 태그 추가

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

개체 검색 프로젝트의 이미지에 태그를 지정할 때 정규화된 좌표를 사용하여 태그가 지정된 각 개체의 지역을 지정해야 합니다. `regionMap` 맵의 정의로 이동합니다. 이 코드는 각 샘플 이미지를 태그가 지정된 Azure 지역과 연결합니다.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

그런 다음, 프로젝트에 이미지를 추가하는 코드 블록으로 건너뜁니다. 프로젝트의 **src/main/resources** 폴더에서 이미지를 읽어서 적절한 태그 및 지역 좌표를 사용하여 서비스에 이미지를 업로드합니다.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

이전 코드 조각은 이미지를 리소스 스트림으로 검색하여 서비스에 업로드하는 두 개의 도우미 함수를 사용합니다.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project-and-publish"></a>프로젝트 학습 및 게시

이 코드는 프로젝트에서 첫 번째 반복을 만든 다음, 이 반복을 예측 엔드포인트에 게시합니다. 게시된 반복에 부여된 이름은 예측 요청을 보내는 데 사용할 수 있습니다. 반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>예측 엔드포인트 사용

여기서는 `predictor` 개체를 통해 표현되는 예측 엔드포인트는 현재 모델에 이미지를 제출하고 분류 예측을 가져오는 데 사용되는 참조입니다. 이 샘플에서 `predictor`는 예측 키 환경 변수를 사용하여 다른 곳에 정의됩니다.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>애플리케이션 실행

maven을 사용하여 솔루션을 컴파일하고 실행하려면 PowerShell을 통해 프로젝트 디렉터리에서 다음 명령을 실행합니다.

```powershell
mvn compile exec:java
```

콘솔 출력에서 로깅 및 예측 결과를 살펴봅니다. 그런 다음, 테스트 이미지에 태그가 적절하게 지정되는지, 검색 지역이 올바른지 확인할 수 있습니다.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>다음 단계

개체 검색 프로세스의 모든 단계를 코드로 수행하는 방법을 살펴보았습니다. 이 샘플은 교육을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 교육하고 테스트해야 하는 경우가 많습니다. 다음 가이드에서는 이미지 분류를 다루지만, 원칙은 개체 검색과 비슷합니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](test-your-model.md)
