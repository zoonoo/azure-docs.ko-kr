---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421780"
---
압축 된 오디오 처리는 [GStreamer](https://gstreamer.freedesktop.org)을 사용 하 여 구현 됩니다. 라이선스 때문에 GStreamer 이진이 컴파일되지 않고 음성 SDK로 연결 되지 않습니다. 대신 이러한 함수를 포함 하는 래퍼 라이브러리를 빌드하고 SDK를 사용 하 여 앱과 함께 제공 해야 합니다.

이 래퍼 라이브러리를 빌드하려면 먼저 [GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)를 다운로드 하 여 설치 합니다. 그런 다음 [래퍼 라이브러리](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)의 **Xcode** 프로젝트를 다운로드 합니다.

**Xcode** 에서 프로젝트를 열고 **일반 iOS 장치** 대상에 대해 빌드합니다. 특정 대상에 대해 빌드하는 것은 작동 *하지 않습니다* .

빌드 단계에서는 이름이 인 모든 필수 아키텍처에 대해 동적 라이브러리를 사용 하 여 동적 프레임 워크 번들을 `GStreamerWrapper.framework`생성 합니다.

이 프레임 워크는 음성 서비스 SDK로 압축 된 오디오 스트림을 사용 하는 모든 앱에 포함 되어야 합니다.

**Xcode** 프로젝트에서 다음 설정을 적용 하 여이 작업을 수행 합니다.

1. `GStreamerWrapper.framework` 방금 빌드한를 복사 하 고 [여기](https://aka.ms/csspeech/iosbinary)에서 다운로드할 수 있는 Cognitive Services Speech SDK의 프레임 워크를 샘플 프로젝트가 포함 된 디렉터리로 복사 합니다.
1. *프로젝트 설정*의 프레임 워크에 대 한 경로를 조정 합니다.
   1. **포함 된 이진 파일** 헤더의 **일반** 탭에서 SDK 라이브러리를 프레임 워크로 추가**합니다** . **포함 된 이진 파일** > 추가 추가 ... > 선택한 디렉터리로 이동 하 고 두 프레임 워크를 모두 선택 합니다.
   1. **빌드 설정** 탭으로 이동하고 **모든** 설정을 활성화합니다.
1. `$(SRCROOT)/..` 디렉터리를 **검색 경로** 제목 아래의 _프레임워크 검색 경로_에 추가합니다.
