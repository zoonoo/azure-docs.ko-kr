---
title: Id Azure Event Grid IoT Edge 구성 | Microsoft Docs
description: Event Grid 모듈의 id 구성
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841768"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Event Grid 모듈의 id 구성

이 문서에서는 Edge에서 그리드에 대 한 id를 구성 하는 방법을 보여 줍니다. 기본적으로 Event Grid 모듈은 IoT 보안 데몬에 의해 구성 된 대로 해당 id 인증서를 제공 합니다. Edge에서 Event Grid는 이벤트를 전달할 때 해당 id 인증서를 나가는 호출에 표시 합니다. 그런 다음 구독자는를 허용 하기 전에 이벤트를 보낸 Event Grid 모듈 인지 확인할 수 있습니다.

모든 가능한 구성에 대 한 [보안 및 인증](security-authentication.md) 가이드를 참조 하세요.

## <a name="always-present-identity-certificate"></a>항상 id 인증서 제공
보내는 호출에 항상 id 인증서를 표시 하는 구성 예제는 다음과 같습니다. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Id 인증서를 제공 하지 않음
나가는 호출에 id 인증서를 제공 하지 않는 구성의 예는 다음과 같습니다. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
