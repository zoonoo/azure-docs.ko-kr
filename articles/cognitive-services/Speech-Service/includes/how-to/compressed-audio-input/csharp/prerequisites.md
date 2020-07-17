---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421797"
---
압축 된 오디오 처리는 [GStreamer](https://gstreamer.freedesktop.org)을 사용 하 여 구현 됩니다. 라이선스 때문에 GStreamer 이진이 컴파일되지 않고 음성 SDK로 연결 되지 않습니다. 개발자는 여러 종속성 및 플러그 인을 설치 해야 합니다. [Windows에 설치](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)를 참조 하세요. Gstreamer 이진 파일은 시스템 경로에 있어야 합니다. 따라서 speech SDK는 런타임 중에 Gstreamer 이진 파일을 로드할 수 있습니다. Speech SDK가 런타임 중에 libgstreamer-1.0 64,을 찾을 수 있는 경우 gstreamer 이진 파일이 시스템 경로에 있음을 의미 합니다.

