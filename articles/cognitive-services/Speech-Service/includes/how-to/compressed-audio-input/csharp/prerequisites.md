---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417735"
---
압축 된 오디오 처리는 [GStreamer](https://gstreamer.freedesktop.org)을 사용 하 여 구현 됩니다. 라이선스 때문에 GStreamer 이진이 컴파일되지 않고 음성 SDK로 연결 되지 않습니다. 개발자는 여러 종속성 및 플러그 인을 설치 해야 합니다. [Windows에 설치](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) 또는 [Linux에 설치](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)를 참조 하세요. GStreamer 이진 파일은 시스템 경로에 있어야 합니다. 따라서 Speech SDK는 런타임 중에 이진 파일을 로드할 수 있습니다. 예를 들어 Windows에서 음성 SDK가 `libgstreamer-1.0-0.dll` 런타임 중에 또는 (최신 gstreamer)를 찾을 수 있는 경우 `gstreamer-1.0-0.dll` gstreamer 바이너리가 시스템 경로에 있음을 의미 합니다.

