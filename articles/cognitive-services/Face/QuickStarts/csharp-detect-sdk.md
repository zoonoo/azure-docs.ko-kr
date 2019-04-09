---
title: '빠른 시작: Azure Face .NET SDK를 사용하여 이미지에서 얼굴 감지'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 이미지에서 얼굴을 감지하기 위해 C#과 함께 Azure Face SDK를 사용합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.openlocfilehash: 57605f9bd1a39435e27a2f2c56c06cf3bfb38605
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630696"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>빠른 시작: Face .NET SDK를 사용하여 이미지에서 얼굴 감지

이 빠른 시작에서는 이미지에서 사람 얼굴을 감지하기 위해 C#과 함께 Face 서비스 SDK를 사용합니다. 이 빠른 시작의 작동하는 코드 예제를 보려면 GitHub의 [Cognitive Services Vision csharp 빠른 시작](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) 리포지토리에서 Face 프로젝트를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="prerequisites"></a>필수 조건

- Face API 구독 키. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Face API 서비스를 구독하고 키를 가져옵니다.
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만들고 **FaceDetection**으로 이름을 지정합니다. 
1. 솔루션에 다른 프로젝트가 있는 경우 이것을 단일 시작 프로젝트로 선택합니다.
1. 필요한 NuGet 패키지를 가져옵니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. **찾아보기** 탭을 클릭하고 **시험판 포함**을 선택한 후, 다음 패키지를 설치합니다.
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)
1. 프로젝트에 대한 최신 버전의 NuGet 패키지를 모두 설치했는지 확인합니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. **업데이트** 탭을 클릭하고 나타나는 패키지의 최신 버전을 설치합니다.

## <a name="add-face-detection-code"></a>얼굴 감지 코드 추가

새 프로젝트의 *Program.cs* 파일을 엽니다. 여기에 이미지를 로드하고 얼굴을 감지하는 데 필요한 코드를 추가합니다.

### <a name="include-namespaces"></a>네임스페이스 포함

*Program.cs* 파일 위에 다음 `using` 문을 추가합니다.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>필수 필드 추가

다음 필드가 있는 **Program** 클래스를 추가합니다. 이 데이터는 Face 서비스에 연결하는 방법과 입력 데이터를 가져올 위치를 지정합니다. `subscriptionKey` 필드를 구독 키의 값으로 업데이트해야 하며 올바른 지역 식별자가 포함되도록 `faceEndpoint` 문자열을 변경해야 할 수도 있습니다. 또한 `localImagePath` 및/또는 `remoteImageUrl` 값을 실제 이미지 파일을 가리키는 경로로 설정해야 합니다.

`faceAttributes` 필드는 단순히 특정 유형의 특성 배열입니다. 감지된 얼굴에 대해 검색할 정보를 지정합니다.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=9-34)]

### <a name="create-and-use-the-face-client"></a>Face 클라이언트 만들기 및 사용

다음으로, **Program** 클래스의 **Main** 메서드에 다음 코드를 추가합니다. 그러면 Face API 클라이언트를 설정합니다.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=36-41)]

또한 **Main** 메서드에서 다음 코드를 추가하여 새로 생성된 Face 클라이언트를 사용하여 원격 및 로컬 이미지에서 얼굴을 감지합니다. 검지 메서드는 다음에 정의합니다. 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-50)]

### <a name="detect-faces"></a>얼굴 감지

**Program** 클래스에 다음 메서드를 추가합니다. Face 서비스 클라이언트를 사용하여 URL로 참조되는 원격 이미지에서 얼굴을 감지합니다. 여기서는 `faceAttributes` 필드를 사용합니다. `faceList`에 추가된 **DetectedFace** 개체는 지정된 특성(이 경우 나이 및 성별)을 포함합니다.&mdash;

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

마찬가지로 **DetectLocalAsync** 메서드를 추가합니다. Face 서비스 클라이언트를 사용하여 파일 경로로 참조되는 로컬 이미지에서 얼굴을 감지합니다.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>얼굴 특성 검색 및 표시

다음으로, **GetFaceAttributes** 메서드를 정의합니다. 관련 특성 정보와 함께 문자열을 반환합니다.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

마지막으로, **DisplayAttributes** 메서드를 정의하여 얼굴 특성 데이터를 콘솔 출력에 기록합니다. 그런 다음, 클래스와 네임스페이스를 닫습니다.

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-125)]

## <a name="run-the-app"></a>앱 실행

성공적인 응답에는 이미지에 있는 각 얼굴에 대한 성별과 나이가 표시됩니다. 예: 

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 Face API 서비스를 사용하여 로컬 및 원격 이미지의 얼굴을 감지할 수 있는 간단한 .NET 콘솔 애플리케이션을 만들었습니다. 다음으로, 보다 자세한 자습서에 따라 사용자에게 얼굴 정보를 직관적으로 표시하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 이미지에서 얼굴을 감지하고 분석하는 WPF 앱 만들기](../Tutorials/FaceAPIinCSharpTutorial.md)
