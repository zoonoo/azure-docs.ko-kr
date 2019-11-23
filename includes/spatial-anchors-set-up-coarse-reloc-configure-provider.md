---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: c18e40b8950d5e75398382a41cf0acc3505ea5a6
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092989"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>센서 지문 공급자 구성

먼저 센서 지문 공급자를 만들고 구성합니다. 이를 통해 디바이스의 플랫폼별 센서를 읽고 클라우드 앵커 세션에서 사용하는 일반적인 표현으로 추상화합니다.