---
title: Azure Cognitive Services, Cognitive Services Speech SDK API 설명서 - 자습서, API 참조 | Microsoft Docs
description: Cognitive Services Speech SDK를 사용하여 앱을 만들고 개발하는 방법 알아보기
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: fe171ba9f6f0ff36a7c23c47f145d83f7a94fb5d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069491"
---
# <a name="shipping-an-application"></a>응용 프로그램 전달

[Speech SDK 라이선스](license.md)를 준수하고 Cognitive Services Speech SDK를 배포할 경우 [타사 소프트웨어 공지 사항](third-party-notices.md)도 준수합니다. 또한 [Microsoft 개인정보처리방침](https://aka.ms/csspeech/privacy)을 검토합니다.

플랫폼에 따라 응용 프로그램을 실행하기 위한 다양한 종속성이 존재합니다.

## <a name="windows"></a>Windows

Cognitive Services Speech SDK는 Windows 10 및 Windows Server 2016에서 테스트되었습니다.

Cognitive Services Speech SDK에는 [Visual Studio 2017용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)가 필요합니다. 최신 버전의 `Microsoft Visual C++ Redistributable for Visual Studio 2017`용 설치 관리자는 여기에서 다운로드할 수 있습니다.

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

응용 프로그램이 관리 코드를 사용 중인 경우, 대상 머신에 `.Net Framework 4.6.1` 이상이 필요합니다.

마이크 입력을 위해 미디어 파운데이션 라이브러리를 설치해야 합니다. 이러한 라이브러리는 Windows 10 및 Windows Server 2016에 포함됩니다. 마이크를 오디오 입력 장치로 사용하지 않는 경우에는 이러한 라이브러리 없이 Speech SDK를 사용할 수 있습니다.

필요한 Speech SDK 파일은 응용 프로그램과 동일한 디렉터리에 배포할 수 있습니다. 이렇게 하면 응용 프로그램이 라이브러리에 직접 액세스할 수 있습니다. 응용 프로그램과 일치하는 올바른 버전(Win32/x64)을 선택해야 합니다.

| Name | 함수
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | 네이티브 및 관리된 배포에 필요한 핵심 SDK
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | 관리된 배포에 필요
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 관리된 배포에 필요

## <a name="linux"></a>Linux

네이티브 응용 프로그램의 경우 Speech SDK 라이브러리 `libMicrosoft.CognitiveServices.Speech.core.so`를 전달해야 합니다.
응용 프로그램과 일치하는 버전(x86/x64)을 선택해야 합니다. Linux 버전에 따라 다음 종속성을 포함해야 할 수도 있습니다.

* GNU C 라이브러리의 공유 라이브러리(POSIX Threads Programming 라이브러리, `libpthreads` 포함)
* OpenSSL 라이브러리(`libssl.so.1.0.0`)
* cURL 라이브러리(`libcurl.so.4`)
* ALSA 응용 프로그램의 공유 라이브러리(`libasound.so.2`)

예를 들어, Ubuntu 16.04에서는 기본적으로 GNU C 라이브러리가 설치되어 있어야 합니다. 마지막 세 가지는 다음 명령을 사용하여 설치할 수 있습니다.

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
