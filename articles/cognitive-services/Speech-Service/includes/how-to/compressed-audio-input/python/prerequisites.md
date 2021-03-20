---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97821522"
---
압축 된 오디오 처리는 [GStreamer](https://gstreamer.freedesktop.org)을 사용 하 여 구현 됩니다. 라이선스 때문에 GStreamer 이진이 컴파일되지 않고 음성 SDK로 연결 되지 않습니다. 개발자는 여러 종속성 및 플러그 인을 설치 해야 합니다. [Windows에 설치](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) 또는 [Linux에 설치](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)를 참조 하세요. GStreamer 이진 파일은 시스템 경로에 있어야 합니다. 따라서 Speech SDK는 런타임 중에 이진 파일을 로드할 수 있습니다. 예를 들어 Windows에서, 음성 SDK가 런타임 중에 찾을 수 있는 경우 `libgstreamer-1.0-0.dll` gstreamer 이진 파일이 시스템 경로에 있음을 의미 합니다.

