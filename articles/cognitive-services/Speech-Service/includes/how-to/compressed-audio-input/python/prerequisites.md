---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282744"
---
압축 된 오디오 처리는를 사용 하 여 구현 됩니다 [`GStreamer`](https://gstreamer.freedesktop.org) . 라이선스의 경우 `GStreamer` 이진이 컴파일되고 음성 SDK와 연결 되지 않습니다. 개발자는 여러 종속성 및 플러그 인을 설치 해야 합니다. [Windows에 설치](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)를 참조 하세요. `GStreamer` 이진은 시스템 경로에 있어야 합니다. 따라서 음성 SDK는 런타임 중에 이진 파일을 로드할 수 있습니다. 음성 SDK가 런타임 중에 찾을 수 있는 경우 `libgstreamer-1.0-0.dll` 이진 파일이 시스템 경로에 있음을 의미 합니다.

