---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943740"
---
압축 된 오디오 처리는 [GStreamer를](https://gstreamer.freedesktop.org)사용하여 구현됩니다. 라이선스상의 이유로 GStreamer 바이너리는 컴파일되고 음성 SDK와 연결되지 않습니다. 대신 이러한 기능을 포함하는 래퍼 라이브러리를 빌드하고 SDK를 사용하여 앱과 함께 제공해야 합니다.

이 래퍼 라이브러리를 빌드하려면 먼저 [GStreamer SDK를](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)다운로드하여 설치합니다. 그런 다음 래퍼 라이브러리에 대한 **Xcode** 프로젝트를 [다운로드합니다.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)

**Xcode에서** 프로젝트를 열고 **일반 iOS 장치** 대상에 대해 *not* 빌드합니다.

빌드 단계에서는 `GStreamerWrapper.framework`의 이름으로 필요한 모든 아키텍처에 대한 동적 라이브러리가 있는 동적 프레임워크 번들을 생성합니다.

이 프레임워크는 음성 서비스 SDK에서 압축된 오디오 스트림을 사용하는 모든 앱에 포함되어야 합니다.

이를 위해 **Xcode** 프로젝트에서 다음 설정을 적용합니다.

1. `GStreamerWrapper.framework` 방금 빌드한 사용자 및 [여기에서](https://aka.ms/csspeech/iosbinary)다운로드할 수 있는 인지 서비스 음성 SDK의 프레임워크를 샘플 프로젝트가 포함된 디렉토리에 복사합니다.
1. *프로젝트 설정에서*프레임워크에 대한 경로를 조정합니다.
   1. **임베디드 바이너리** 헤더 아래의 **일반** 탭에서 SDK 라이브러리를 프레임워크로 추가 **>합니다.** > **Add other...**
   1. **빌드 설정** 탭으로 이동하고 **모든** 설정을 활성화합니다.
1. `$(SRCROOT)/..` 디렉터리를 **검색 경로** 제목 아래의 _프레임워크 검색 경로_에 추가합니다.
