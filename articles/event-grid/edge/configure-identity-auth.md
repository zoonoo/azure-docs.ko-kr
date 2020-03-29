---
title: ID 구성 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: 이벤트 그리드 모듈의 ID 구성
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841768"
---
# <a name="configure-identity-for-the-event-grid-module"></a>이벤트 그리드 모듈에 대한 ID 구성

이 문서에서는 에지에서 Grid에 대한 ID를 구성하는 방법을 보여 줍니다. 기본적으로 Event Grid 모듈은 IoT 보안 데몬이 구성한 ID 인증서를 제공합니다. 이벤트 그리드 온 에지는 이벤트를 제공할 때 나가는 호출과 함께 ID 인증서를 제공합니다. 그러면 구독자는 수락하기 전에 이벤트를 보낸 이벤트 그리드 모듈의 유효성을 검사할 수 있습니다.

가능한 모든 구성은 [보안 및 인증](security-authentication.md) 가이드를 참조하십시오.

## <a name="always-present-identity-certificate"></a>항상 ID 인증서를 제시합니다.
다음은 나가는 호출에 항상 ID 인증서를 표시하는 예제 구성입니다. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>ID 인증서를 제시하지 마십시오.
다음은 나가는 호출에 ID 인증서를 표시하지 않는 예제 구성입니다. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
