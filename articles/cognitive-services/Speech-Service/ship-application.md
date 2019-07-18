---
title: Speech SDK를 사용하여 앱 개발 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 앱을 만드는 방법을 알아봅니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 7c698abb133c14f32b60b22acbbccc37a191a02e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604855"
---
# <a name="ship-an-application"></a>애플리케이션 배송

[Speech SDK 라이선스](https://aka.ms/csspeech/license201809)를 준수하고 Azure Cognitive Services Speech SDK를 배포할 경우 [타사 소프트웨어 공지 사항](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html)도 준수합니다. 또한 [Microsoft 개인정보처리방침](https://aka.ms/csspeech/privacy)을 검토합니다.

플랫폼에 따라 애플리케이션을 실행하기 위한 다양한 종속성이 존재합니다.

## <a name="windows"></a>Windows

Cognitive Services Speech SDK는 Windows 10 및 Windows Server 2016에서 테스트되었습니다.

Cognitive Services 음성 SDK에는 [Microsoft Visual C++ Visual Studio 2019에 대 한 재배포 가능 패키지](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) 시스템에 있습니다. 최신 버전의 `Microsoft Visual C++ Redistributable for Visual Studio 2019`용 설치 관리자는 여기에서 다운로드할 수 있습니다.

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

애플리케이션에서 관리 코드를 사용하는 경우, 대상 머신에 `.NET Framework 4.6.1` 이상이 필요합니다.

마이크 입력을 위해 미디어 파운데이션 라이브러리를 설치해야 합니다. 이러한 라이브러리는 Windows 10 및 Windows Server 2016에 포함됩니다. 마이크를 오디오 입력 디바이스로 사용하지 않는 경우에는 이러한 라이브러리 없이 Speech SDK를 사용할 수 있습니다.

필요한 Speech SDK 파일은 애플리케이션과 동일한 디렉터리에 배포할 수 있습니다. 이렇게 하면 애플리케이션이 라이브러리에 직접 액세스할 수 있습니다. 애플리케이션과 일치하는 올바른 버전(Win32/x64)을 선택해야 합니다.

| 이름 | 함수
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | 네이티브 및 관리된 배포에 필요한 핵심 SDK
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 관리된 배포에 필요

>[!NOTE]
> 릴리스부터 1.3.0 파일 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (이전 릴리스에서 제공) 더 이상 필요 하지 않습니다. Core SDK의에서 기능 통합 되었습니다.

>[!NOTE]
> Windows Forms 앱 (.NET Framework)에 대 한 C# 프로젝트에서 라이브러리 프로젝트의 배포 설정에 포함 되어 있는지 확인 합니다. 아래에서 확인할 수 있습니다 `Properties -> Publish Section`합니다. 클릭 된 `Application Files` 단추 및 목록 아래로 스크롤한에서 해당 라이브러리를 검색 합니다. 값 설정 되어 있는지 확인 `Included`합니다. Visual Studio 프로젝트를 게시 하 고 배포 하는 경우 파일을 포함 됩니다.

## <a name="linux"></a>Linux

Speech SDK는 현재 Ubuntu 16.04, Ubuntu 18.04 및 Debian 9 배포판을 지원합니다.
네이티브 애플리케이션의 경우 Speech SDK 라이브러리 `libMicrosoft.CognitiveServices.Speech.core.so`를 전달해야 합니다.
애플리케이션과 일치하는 버전(x86, x64)을 선택해야 합니다. Linux 버전에 따라 다음 종속성을 포함해야 할 수도 있습니다.

* GNU C 라이브러리의 공유 라이브러리(POSIX Threads Programming 라이브러리, `libpthreads` 포함)
* OpenSSL 라이브러리 (`libssl.so.1.0.0` 또는 `libssl.so.1.0.2`)
* ALSA 애플리케이션의 공유 라이브러리(`libasound.so.2`)

Ubuntu에는 GNU C 라이브러리가 기본적으로 이미 설치되어 있습니다. 마지막 세 가지는 다음 명령을 사용하여 설치할 수 있습니다.

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Debian 9에 이러한 패키지를 설치 합니다.

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
