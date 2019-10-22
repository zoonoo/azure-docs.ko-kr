---
title: '빠른 시작: 음성 인식, C#(Xamarin) - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Cognitive Services Speech SDK를 사용하여 Windows UWP, Android 및 iOS용 플랫폼 간 C# Xamarin 애플리케이션을 만듭니다. 디바이스 또는 시뮬레이터의 마이크에서 실시간으로 음성을 텍스트로 변환합니다. 이 애플리케이션은 Speech SDK NuGet 패키지와 Microsoft Visual Studio 2019로 빌드되었습니다.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: c9467bac8b5998252c021faca4eb4177c42a1736
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387379"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 플랫폼 간 Xamarin 앱을 통해 음성 인식

이 빠른 시작은 [음성 텍스트 변환](quickstart-csharp-uwp.md), [텍스트 음성 변환](quickstart-text-to-speech-csharp-uwp.md) 및 [음성 번역](quickstart-translate-speech-uwp.md)에도 사용할 수 있습니다.

이 문서에서는 [Speech SDK](speech-sdk.md)를 사용하여 UWP(유니버설 Windows 플랫폼), Android 및 iOS용 Xamarin을 사용하는 플랫폼 간 C# 애플리케이션을 개발합니다. 프로그램은 디바이스 마이크의 음성을 실시간으로 텍스트로 전사합니다. 이 애플리케이션은 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget) 및 Microsoft Visual Studio 2019(모든 버전)로 빌드되었습니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Speech Service에 대한 Azure 구독 키. [무료로 가져올 수 있습니다](get-started.md).
* Windows 10 Fall Creators Update(10.0 빌드 16299) 이상 및 작동하는 마이크가 있는 Windows PC
* [Visual Studio에 설치된 Xamarin](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows)
* [Windows에 설치된 Xamarin Android](https://docs.microsoft.com/xamarin/android/get-started/installation/windows)
* [Windows에 설치된 Xamarin iOS](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows)
* Android를 대상으로 하는 경우: 
   * Android 디바이스(ARM32/64, x86, API 23: Android 6.0 Marshmallow 이상) - [개발 용도로 사용](https://developer.android.com/studio/debug/dev-options)할 수 있고 작동하는 마이크가 장착되어 있어야 합니다.
* iOS를 대상으로 하는 경우: iOS 디바이스(ARM64) 또는 iOS 시뮬레이터(x64) - [개발 용도로 사용](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/)할 수 있고 작동하는 마이크가 장착되어 있어야 합니다.
* Windows ARM64 빌드를 지원하는 경우 [선택적 빌드 도구 및 ARM/ARM64용 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)를 설치합니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>일반적인 `helloworld` 프로젝트용 샘플 코드 추가

일반적인 `helloworld` 프로젝트에는 플랫폼 간 애플리케이션에 대한 플랫폼 독립적 구현이 포함되어 있습니다.
이제 애플리케이션의 사용자 인터페이스를 정의하는 XAML 코드를 추가하고, C# 코드 숨김 구현을 추가합니다.

1. **솔루션 탐색기**의 일반 `helloworld` 프로젝트 아래에서 `MainPage.xaml`을 엽니다.

1. 디자이너의 XAML 뷰에서 다음 XAML 코드 조각을 **Grid** 태그(`<StackLayout>`와 `</StackLayout>` 사이)에 삽입합니다.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. **솔루션 탐색기**에서 코드 숨김 원본 파일 `MainPage.xaml.cs`를 엽니다. `MainPage.xaml`로 그룹화되어 있습니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. 원본 파일의 `OnRecognitionButtonClicked` 처리기에서 `YourSubscriptionKey` 문자열을 찾아 구독 키로 바꿉니다.

1. `OnRecognitionButtonClicked` 처리기에서 `YourServiceRegion` 문자열을 찾아 구독과 연결된 [지역](regions.md)으로 바꿉니다. 예를 들어 평가판 구독의 경우 `westus`를 사용합니다.

1. 다음으로, 다른 플랫폼 프로젝트(UWP, Android 및 iOS)에서 마이크 권한을 쿼리하는 데 사용되는 [Xamarin 서비스](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)를 만들어야 합니다. 이렇게 하려면 새 `Services` 폴더를 `helloworld` 프로젝트 아래에 추가하고, 새 C# 소스 파일을 이 폴더 아래에 만들고(마우스 오른쪽 단추로 `Services` 폴더 및 **추가** > **새 항목** > **코드 파일**을 차례로 클릭), 파일 이름을 `IMicrophoneService.cs`로 바꾸고, 다음 코드 조각의 모든 코드를 해당 파일에 배치합니다.

[!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>`helloworld.Android` 프로젝트용 샘플 코드 추가

이제 애플리케이션의 Android 관련 부분을 정의하는 C# 코드를 추가합니다.

1. **솔루션 탐색기**의 `helloworld.Android` 프로젝트 아래에서 `MainActivity.cs`를 엽니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. 다음으로, 새 `Services` 폴더를 `helloworld.Android` 프로젝트 아래에 만들어서 `MicrophoneService`에 대한 Android 관련 구현을 추가합니다. 그런 다음, 새 C# 소스 파일을 이 폴더 아래에 만들고, 파일 이름을 `MicrophoneService.cs`로 바꾸고, 다음 코드 조각을 복사하여 해당 파일에 붙여넣습니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. 그런 다음, `Properties` 폴더 아래의 `AndroidManifest.xml`을 열고, 마이크에 대한 다음 사용 권한 설정을 `<manifest>` 및 `</manifest>` 사이에 추가합니다.
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>`helloworld.iOS` 프로젝트용 샘플 코드 추가

이제 애플리케이션의 iOS 관련 부분을 정의하고 Apple 디바이스 특정 구성도 helloworld.iOS 프로젝트에 만드는 C# 코드를 추가합니다.

1. **솔루션 탐색기**의 `helloworld.iOS` 프로젝트 아래에서 `AppDelegate.cs`를 엽니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. 다음으로, 새 `Services` 폴더를 `helloworld.iOS` 프로젝트 아래에 만들어서 `MicrophoneService`에 대한 iOS 관련 구현을 추가합니다. 그런 다음, 새 C# 소스 파일을 이 폴더 아래에 만들고, 파일 이름을 `MicrophoneService.cs`로 바꾸고, 다음 코드 조각을 복사하여 해당 파일에 붙여넣습니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. `helloworld.iOS` 프로젝트 아래에서 Info.plist를 텍스트 편집기로 열고, 다음 키 값 쌍을 <key>NSMicrophoneUsageDescription</key> dict 섹션 아래에 추가합니다.
   <string>이 샘플 앱에는 마이크 액세스 권한이 필요합니다.</string>
   > 참고: iPhone 디바이스에 대한 빌드를 목표로 하는 경우 `Bundle Identifier`가 디바이스의 프로비저닝 프로필 앱 ID와 일치하는지 확인하세요. 그렇지 않으면 빌드가 실패합니다. iPhoneSimulator를 사용하는 경우 그대로 둘 수 있습니다.

1. Windows PC에서 빌드하는 경우 **도구** > **iOS** > **Mac에 페어링**을 통해 빌드할 Mac 디바이스에 대한 연결을 설정해야 합니다. Visual Studio에서 제공하는 지침 마법사에 따라 Mac 디바이스에 대한 연결을 설정합니다.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>`helloworld.UWP` 프로젝트용 샘플 코드 추가

이제 애플리케이션의 UWP 관련 부분을 정의하는 C# 코드를 추가합니다.

1. **솔루션 탐색기**의 `helloworld.UWP` 프로젝트 아래에서 `MainPage.xaml.cs`를 엽니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. 다음으로, 새 `Services` 폴더를 `helloworld.UWP` 프로젝트 아래에 만들어서 `MicrophoneService`에 대한 UWP 관련 구현을 추가합니다. 그런 다음, 새 C# 소스 파일을 이 폴더 아래에 만들고, 파일 이름을 `MicrophoneService.cs`로 바꾸고, 다음 코드 조각을 복사하여 해당 파일에 붙여넣습니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. 다음으로, Visual Studio 내의 `helloworld.UWP` 프로젝트 아래에서 `Package.appxmanifest` 파일을 두 번 클릭하고 **기능** > **마이크**를 차례로 선택한 다음, 파일을 저장합니다.
   > 참고: "Hellowworld.UWP_TemporaryKey.pfx 인증서 파일이 없습니다."라는 경고가 표시되는 경우 자세한 내용은 [음성 텍스트 변환](quickstart-csharp-uwp.md) 샘플을 확인하세요.

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택하여 변경 내용을 저장합니다.

## <a name="build-and-run-the-uwp-application"></a>UWP 애플리케이션 빌드 및 실행

1. `helloworld.UWP`를 시작 프로젝트로 설정하고, 마우스를 사용하여 마우스 오른쪽 단추로 `helloworld.UWP` 프로젝트를 클릭한 다음, **빌드**를 선택하여 애플리케이션을 빌드합니다. 

1. **디버그** > **디버깅 시작**을 선택하거나, **F5** 키를 눌러 애플리케이션을 시작합니다. **helloworld** 창이 나타납니다.

   ![C#으로 작성된 샘플 UWP 음성 인식 애플리케이션 - 빠른 시작](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. **마이크 사용**을 선택하고, 액세스 권한 요청이 팝업되면 **예**를 선택합니다.

   ![마이크 액세스 권한 요청](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. **음성 인식 시작**을 선택하고, 디바이스의 마이크에 영어 문구 또는 문장을 말합니다. 음성은 Speech Services로 전송되어 텍스트로 변환되고 창에 표시됩니다.

   ![음성 인식 사용자 인터페이스](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Android 및 iOS 애플리케이션 빌드 및 실행

디바이스 또는 시뮬레이터에서 Android 및 iOS 애플리케이션을 빌드하고 실행하는 것은 UWP와 비슷한 방식으로 수행됩니다. 이 문서의 `Prerequisites` 섹션에 모든 SDK가 제대로 설치되어 있는지 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 C# 샘플 살펴보기](https://aka.ms/csspeech/samples)
