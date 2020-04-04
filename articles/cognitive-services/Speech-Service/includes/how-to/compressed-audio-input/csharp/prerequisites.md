---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637275"
---
압축 된 오디오 처리는 [GStreamer를](https://gstreamer.freedesktop.org)사용하여 구현됩니다. 라이선스상의 이유로 GStreamer 바이너리는 컴파일되고 음성 SDK와 연결되지 않습니다. 개발자는 여러 종속성 및 플러그인을 설치해야 [Installing on Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)합니다. 스피치 SDK가 런타임 동안 gstreamer 바이너리를 로드할 수 있도록 Gstreamer 바이너리가 시스템 경로에 있어야 합니다. 음성 SDK가 런타임 중에 libgstreamer-1.0-0.dll을 찾을 수 있다면 이는 gstreamer 바이너리가 시스템 경로에 있음을 의미합니다.

