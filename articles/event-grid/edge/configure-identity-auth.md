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
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992445"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Event Grid 모듈의 id 구성

이 문서에서는 Event Grid 모듈에 대해 사용할 수 있는 id 구성의 예를 제공 합니다. 기본적으로 Event Grid 모듈은 IoT 보안 데몬에 의해 구성 된 대로 해당 id 인증서를 제공 합니다. Id 인증서는 이벤트를 전달할 때 나가는 호출에 Event Grid 모듈에서 제공 됩니다. 그런 다음 Event Grid 이벤트에 대 한 구독자는 이벤트를 수락 하기 전에 이벤트를 보낸 Event Grid 모듈이 정말로 유효한 지 확인 하도록 선택할 수 있습니다.

모든 가능한 구성에 대 한 [보안 및 인증](security-authentication.md) 가이드를 참조 하세요.

## <a name="always-present-identity-certificate"></a>항상 id 인증서 제공
보내는 호출에 항상 id 인증서를 표시 하는 구성 예제는 다음과 같습니다. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Id 인증서를 제공 하지 않음
나가는 호출에 id 인증서를 제공 하지 않는 구성의 예는 다음과 같습니다. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
