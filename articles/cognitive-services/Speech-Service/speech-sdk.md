---
title: Cognitive Services 음성 SDK 정보
description: 음성 서비스에 사용할 수 있는 SDK의 개요입니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: c7eaa2aa37b05bd0e125e1841357979af4f6763a
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326062"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Cognitive Services 음성 SDK 정보

Cognitive Services 음성 SDK(소프트웨어 개발 키트)에서는 소프트웨어 개발을 용이하게 하기 위해 음성 서비스의 기능에 대한 응용 프로그램 기본 액세스를 제공합니다. 현재 SDK에서는 **음성 텍스트 변환**, **음성 번역** 및 **의도 인식**에 대한 액세스 권한을 제공합니다.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>SDK 가져오기

### <a name="get-the-windows-sdk"></a>Windows SDK 가져오기

음성 SDK의 Windows 버전에는 32비트 및 64비트 C/C++ 클라이언트 라이브러리뿐만 아니라 C#과 함께 사용할 관리되는 (.NET) 라이브러리가 포함되어 있습니다. SDK는 NuGet을 사용하여 Visual Studio에서 설치할 수 있습니다. `Microsoft.CognitiveServices.Speech`를 검색하기만 하면 됩니다.

### <a name="get-the-linux-sdk"></a>Linux SDK 가져오기

다음 셸 명령을 실행하여 필수 컴파일러 및 라이브러리가 있는지 확인합니다.

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> 이러한 지침에서는 PC(x86 또는 x64)의 Ubuntu 16.04를 실행 중이라고 가정합니다. 다른 Ubuntu 버전 또는 다른 Linux 배포에서 환경에 맞게 이러한 단계를 조정합니다.

그런 다음, [SDK를 다운로드](https://aka.ms/csspeech/linuxbinary)하고 원하는 디렉터리에 파일 압축을 풉니다. 이 표에서는 SDK 폴더 구조를 보여줍니다.

|path|설명|
|-|-|
|`license.md`|License|
|`third-party-notices.md`|타사 알림|
|`include`|C 및 C++용 헤더 파일|
|`lib/x64`|응용 프로그램과 연결할 기본 x64 라이브러리|
|`lib/x86`|응용 프로그램과 연결할 기본 x86 라이브러리|

응용 프로그램을 만들려면 개발 환경에 필수 이진 파일(및 라이브러리)를 복사하고 이동하고 필요에 따라 빌드 프로세스에 포함합니다.

### <a name="get-the-java-sdk"></a>Java SDK 가져오기

Android용 Java SDK는 사용에 필요한 Android 권한 뿐만 아니라 필요한 라이브러리를 포함하는 [AAR(Android 라이브러리)](https://developer.android.com/studio/projects/android-library)로 패키지됩니다.
`https://csspeechstorage.blob.core.windows.net/maven/`의 Maven 리포지토리에서 패키지 `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`로 호스트됩니다.
Android Studio 프로젝트의 패키지를 사용하는 경우 다음과 같이 변경합니다.

* 프로젝트 수준의 `build.gradle` 파일에서 `repository` 섹션에 다음을 추가합니다.

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* 모듈 수준의 `build.gradle` 파일에서 `dependencies` 섹션에 다음을 추가합니다.

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Java SDK는 [Speech Devices SDK](speech-devices-sdk.md)에도 포함됩니다.

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
