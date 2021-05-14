---
title: ID 구성 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid 모듈의 ID 구성
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171689"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Event Grid 모듈의 ID 구성

이 문서는 Edge에서 Grid ID를 구성하는 방법을 보여 줍니다. 기본적으로 Event Grid 모듈은 IoT 보안 디먼에서 구성된 대로 해당 ID 인증서를 제공합니다. Edge의 Event Grid는 이벤트를 전송할 때 발신 호출에서 해당 ID 인증서를 제공합니다. 그러면 구독자가 이벤트를 허용하기 전에 이벤트를 보낸 Event Grid 모듈인지 확인할 수 있습니다.

모든 가능한 구성은 [보안 및 인증](security-authentication.md) 가이드를 참조하세요.

## <a name="always-present-identity-certificate"></a>항상 ID 인증서 제공
다음은 발신 호출에서 항상 ID 인증서를 제공하는 예제 구성입니다. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>ID 인증서를 제공하지 않음
다음은 발신 호출에서 ID 인증서를 제공하지 않는 예제 구성입니다. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
