---
title: Speech Devices SDK 정보 - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech Service SDK(소프트웨어 개발 키트)를 사용하면 애플리케이션 네이티브에서 Speech 서비스의 기능에 액세스할 수 있으므로 소프트웨어를 더 쉽게 개발할 수 있습니다. 이 문서에서는 Windows, Linux 및 Android용 SDK에 대한 추가 세부 정보를 제공합니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 1c29ed47e499ee23fab9f3b34e3974f479e3dbb7
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604274"
---
# <a name="about-the-speech-service-sdk"></a>Speech Service SDK 정보

Speech Service SDK(소프트웨어 개발 키트)를 사용하면 애플리케이션 네이티브에서 Speech 서비스의 기능에 액세스할 수 있으므로 소프트웨어를 더 쉽게 개발할 수 있습니다. 현재 SDK에서는 **음성 텍스트 변환**, **음성 번역** 및 **의도 인식**에 대한 액세스 권한을 제공합니다.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>SDK 가져오기

### <a name="windows"></a> Windows

Windows의 경우 다음 언어를 지원합니다.

* C#(UWP 및 .NET), C++: 최신 버전의 Speech SDK NuGet 패키지를 참조 및 사용할 수 있습니다. 이 패키지에는 관리(.NET) 라이브러리 외에도 32비트 및 64비트 클라이언트 라이브러리가 포함되어 있습니다. SDK는 NuGet을 사용하여 Visual Studio에서 설치할 수 있습니다. **Microsoft.CognitiveServices.Speech**를 검색합니다.

* Java: 최신 버전의 Speech SDK Maven 패키지를 참조 및 사용할 수 있으며, 이 패키지는 Windows x64만 지원합니다. Maven 프로젝트에서 `https://csspeechstorage.blob.core.windows.net/maven/`을 추가 리포지토리로 추가하고, `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0`을 종속성으로 참조합니다.

### <a name="linux"></a>Linux

> [!NOTE]
> 현재는 PC에서 Ubuntu 16.04 및 18.04만 지원됩니다(C++ 개발용은 x86 또는 x64, .NET Core 및 Java용은 x64).

다음 셸 명령을 실행하여 필수 컴파일러 및 라이브러리가 설치되어 있는지 확인합니다.

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: 최신 버전의 Speech SDK NuGet 패키지를 참조 및 사용할 수 있습니다. SDK를 참조하려면 다음 패키지 참조를 프로젝트에 추가합니다.

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.2.0" />
  ```

* Java: 최신 버전의 Speech SDK Maven 패키지를 참조 및 사용할 수 있습니다. Maven 프로젝트에서 `https://csspeechstorage.blob.core.windows.net/maven/`을 추가 리포지토리로 추가하고, `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0`을 종속성으로 참조합니다.

* C++: SDK를 [.tar package](https://aka.ms/csspeech/linuxbinary)로 다운로드하고 원하는 디렉터리에 파일의 압축을 풉니다. 다음 표는 SDK 폴더 구조를 보여줍니다.

  |path|설명|
  |-|-|
  |`license.md`|License|
  |`ThirdPartyNotices.md`|타사 알림|
  |`include`|C 및 C++용 헤더 파일|
  |`lib/x64`|애플리케이션과 연결할 기본 x64 라이브러리|
  |`lib/x86`|애플리케이션과 연결할 기본 x86 라이브러리|

  애플리케이션을 만들려면 개발 환경에 필수 이진 파일(및 라이브러리)을 복사하고 이동합니다. 필요에 따라 빌드 프로세스에 포함합니다.

### <a name="android"></a>Android

Android용 Java SDK는 필요한 라이브러리와 필요한 Android 권한을 포함하는 [AAR(Android 라이브러리)](https://developer.android.com/studio/projects/android-library)로 패키지됩니다. `https://csspeechstorage.blob.core.windows.net/maven/`의 Maven 리포지토리에서 패키지 `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0`로 호스트됩니다.

Android Studio 프로젝트의 패키지를 사용하는 경우 다음과 같이 변경합니다.

* 프로젝트 수준의 build.gradle 파일에서 `repository` 섹션에 다음을 추가합니다.

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* 모듈 수준의 build.gradle 파일에서 `dependencies` 섹션에 다음을 추가합니다.

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.2.0'
  ```

Java SDK는 [Speech Devices SDK](speech-devices-sdk.md)에도 포함됩니다.

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
