---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215296"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>센서 지문 공급자 구성

먼저 센서 지문 공급자를 만들고 구성합니다. 센서 지문 공급자는 디바이스의 플랫폼별 센서를 읽고 해당 판독값을 클라우드 앵커 세션에서 사용하는 일반적인 표현으로 변환합니다.

> [!IMPORTANT]
> 활성화하려는 센서를 현재 플랫폼에서 사용할 수 있는지 [여기서 확인](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability)하세요.