---
title: 수신 호출의 클라이언트 인증 구성 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드에서 노출되는 API 프로토콜을 구성합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841794"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>수신 호출의 클라이언트 인증 구성

이 가이드는 Event Grid 모듈에 대해 가능한 클라이언트 인증 구성의 예를 제공합니다. Event Grid 모듈은 두 가지 유형의 클라이언트 인증을 지원합니다.

* 공유 액세스 서명(SAS) 키 기반
* 인증서 기반

가능한 모든 구성은 [보안 및 인증](security-authentication.md) 가이드를 참조하십시오.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>인증서 기반 클라이언트 인증 사용, 자체 서명된 인증서 없음

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>인증서 기반 클라이언트 인증 사용, 자체 서명된 인증서 허용

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>일반적으로 자체 서명된 인증서를 사용할 수 있으므로 속성 **inbound__clientAuth__clientCert__allowUnknownCA** 테스트 환경에서만 **true로** 설정합니다. 프로덕션 워크로드의 경우 이 속성을 **CA(인증** 기관)의 거짓 및 인증서로 설정하는 것이 좋습니다.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>인증서 기반 및 sas 키 기반 클라이언트 인증 사용

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>SAS 키 기반 클라이언트 인증을 사용하면 IoT 가 아닌 에지 모듈이 IoT Edge 네트워크 외부에서 API 포트에 액세스할 수 있다고 가정하여 관리 및 런타임 작업을 수행할 수 있습니다.
