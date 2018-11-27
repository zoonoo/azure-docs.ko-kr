---
title: '자습서: .NET SDK를 사용하여 이미지에서 얼굴을 감지하여 표시'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Face API를 사용하여 이미지에서 얼굴을 감지하고 포착하는 Windows 앱을 만듭니다.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 11/05/2018
ms.author: pafarley
ms.openlocfilehash: 8db2437b7f78271aee131cf5b4dfd9f06f744c66
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852676"
---
# <a name="tutorial-create-a-wpf-app-to-display-face-data-in-an-image"></a>자습서: 이미지에서 얼굴 데이터를 표시하는 WPF 앱 만들기

이 자습서에서는 Azure Face API를 사용하여 .NET 클라이언트 SDK를 통해 이미지에서 얼굴을 감지하고 해당 데이터를 UI에 표시하는 방법을 배웁니다. 얼굴을 감지하고, 각 얼굴 주위에 프레임을 그리고, 상태 표시줄에 얼굴에 대한 설명을 표시하는 간단한 WPF(Windows Presentation Framework) 애플리케이션을 만들 것입니다. 

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> - WPF 응용 프로그램 만들기
> - Face API 클라이언트 라이브러리 설치
> - 클라이언트 라이브러리를 사용하여 이미지에서 얼굴 감지
> - 감지된 각 얼굴 주위에 프레임 그리기
> - 강조 표시된 얼굴에 대한 설명을 상태 표시줄에 표시

![사각형으로 포착되어 감지된 얼굴을 보여주는 스크린샷](../Images/getting-started-cs-detected.png)

전체 예제 코드는 GitHub의 [Cognitive Face CSharp 샘플](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) 리포지토리에서 받을 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. 


## <a name="prerequisites"></a>필수 조건

- Face API 구독 키. [Cognitive Services 사용해보기](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다. 또는 [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Face API 서비스를 구독하고 키를 가져옵니다.
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

다음 단계에 따라 새 WPF 애플리케이션 프로젝트를 만듭니다.

1. Visual Studio에서 [새 프로젝트] 대화 상자를 엽니다. **설치된 항목**, **Visual C#** 을 차례로 확장한 다음, **WPF 앱(.NET Framework)** 를 선택합니다.
1. 응용 프로그램 이름을 **FaceTutorial**로 지정하고 **확인**을 클릭합니다.
1. 필요한 NuGet 패키지를 가져옵니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 누르고 **NuGet 패키지 관리**를 선택한 후 다음 패키지를 찾아 설치합니다.
    - Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview

## <a name="add-the-initial-code"></a>초기 코드를 추가합니다.

이 섹션에서는 얼굴별 기능 없이 앱의 기본 프레임워크를 추가하겠습니다.

### <a name="create-the-ui"></a>UI 만들기

*MainWindow.xaml*을 열고 콘텐츠를 다음 코드로 바꿉니다. 그러면 UI 창이 만들어집니다. `FacePhoto_MouseMove` 및 `BrowseButton_Click`은 나중에 정의할 이벤트 처리기입니다.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?range=1-18)]

### <a name="create-the-main-class"></a>기본 클래스 만들기

*MainWindow.xaml.cs*를 열고 클라이언트 라이브러리 네임스페이스와 필요한 기타 네임스페이스를 추가합니다. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=1-12)]

다음으로, **MainWindow** 클래스에 다음 코드를 삽입합니다. 이렇게 하면 구독 키를 사용하여 **FaceClient** 인스턴스가 만들어지며, 구독 키는 사용자가 직접 입력해야 합니다. `faceEndpoint`의 지역 문자열을 구독에 대한 올바른 지역으로 설정해야 합니다(모든 지역 엔드포인트 목록에 대해서는 [Face API 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 참조).

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=18-46)]

다음 코드를 **MainWindow** 메서드에 붙여넣습니다.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=50-61)]

마지막으로, **BrowseButton_Click** 및 **FacePhoto_MouseMove** 메서드를 클래스에 추가합니다. 이것은 *MainWindow.xaml*에 선언된 이벤트 처리기에 해당합니다. **BrowseButton_Click** 메서드는 사용자가 .jpg 이미지를 선택할 수 있도록 허용하는 **OpenFileDialog**를 만듭니다. 그리고 주 창에 이미지를 표시합니다. 이후 단계에서 **BrowseButton_Click** 및 **FacePhoto_MouseMove**의 나머지 코드를 삽입합니다. **DetectedFace** 개체의 목록인 `faceList` 참조도 기록해 두세요. 앱이 실제 얼굴 데이터를 저장하고 호출하는 위치입니다.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=64-90,146)]

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=148-150,187)]

### <a name="try-the-app"></a>앱 시험

메뉴에서 **시작**을 눌러 앱을 테스트합니다. 앱 창이 열리면 왼쪽 아래에 있는 **찾아보기**를 클릭합니다. **파일 열기** 대화가 표시됩니다. 파일 시스템에서 이미지를 선택하고 창에 표시되는지 확인합니다. 앱을 닫고 다음 단계로 넘어갑니다.

![수정되지 않은 얼굴 이미지를 보여주는 스크린샷](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>이미지 업로드 및 얼굴 감지

이 앱은 **FaceClient.Face.DetectWithStreamAsync** 메서드를 호출하여 얼굴을 감지하며, 이 메서드는 로컬 이미지를 업로드하는 데 사용되는 [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API를 래핑합니다.

**MainWindow** 클래스에서 **FacePhoto_MouseMove** 메서드 아래에 다음 메서드를 삽입합니다. 이렇게 하면 검색할 얼굴 특성 목록이 정의되고 제출된 이미지 파일을 **스트림**으로 읽어 들입니다. 그런 다음, 두 개체가 **DetectWithStreamAsync** 메서드 호출로 전달됩니다.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=189-226)]

## <a name="draw-rectangles-around-faces"></a>얼굴 주위에 사각형 그리기

다음으로, 이미지에서 감지된 각 얼굴 주위에 사각형을 그리는 코드를 추가하겠습니다. **MainWindow** 클래스의 **BrowseButton_Click** 메서드 끝부분에서 `FacePhoto.Source = bitmapSource` 줄 뒤에 다음 코드를 삽입합니다. 이렇게 하면 호출에서 감지된 얼굴 목록이 **UploadAndDetectFaces**에 채워집니다. 그런 다음, 각 얼굴 주위에 사각형이 그려지고 수정된 이미지가 주 창에 표시됩니다.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=92-145)]

## <a name="describe-the-faces"></a>얼굴 설명

**MainWindow** 클래스의 **UploadAndDetectFaces** 메서드 아래에 다음 메서드를 추가합니다. 이렇게 하면 검색된 얼굴 특성이 얼굴을 설명하는 문자열로 변환됩니다.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=228-286)]

## <a name="display-the-face-description"></a>얼굴 설명 표시

**FacePhoto_MouseMove** 메서드에 다음 코드를 추가합니다. 이 이벤트 처리기는 마우스 포인터가 감지된 얼굴 사각형을 가리킬 때 `faceDescriptionStatusBar`에 얼굴 설명 문자열을 표시합니다.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?range=151-186)]


## <a name="run-the-app"></a>앱 실행

이 응용 프로그램을 실행하고 얼굴이 포함된 이미지를 찾습니다. Face 서비스가 응답할 수 있도록 몇 초 동안 기다립니다. 이미지의 각 얼굴에 빨간색 사각형이 표시됩니다. 마우스를 얼굴 사각형 위로 이동하면 해당 얼굴에 대한 설명이 상태 표시줄에 표시됩니다.

![사각형으로 포착되어 감지된 얼굴을 보여주는 스크린샷](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Face 서비스 .NET SDK를 사용하는 기본 프로세스를 배웠으며, 이미지에서 얼굴을 감지 및 포착하는 애플리케이션을 만들었습니다. 다음으로, 얼굴 감지에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [이미지에서 얼굴을 감지하는 방법](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
