---
title: 들어오는 호출에 대 한 클라이언트 인증 구성-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge에서 Event Grid에 의해 노출 되는 API 프로토콜을 구성 합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a0bba9559c2a0b4ff6c8a4e5f2287692e27f8a1a
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171706"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>들어오는 호출에 대 한 클라이언트 인증 구성

이 가이드는 Event Grid 모듈에 대해 가능한 클라이언트 인증 구성의 예를 제공 합니다. Event Grid 모듈은 두 가지 유형의 클라이언트 인증을 지원 합니다.

* 공유 액세스 서명 (SAS) 키 기반
* 인증서 기반

모든 가능한 구성에 대 한 [보안 및 인증](security-authentication.md) 가이드를 참조 하세요.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>인증서 기반 클라이언트 인증을 사용 하 고 자체 서명 된 인증서는 사용 하지 않음

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

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>인증서 기반 클라이언트 인증 사용, 자체 서명 된 인증서 허용

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
>일반적으로 자체 서명 된 인증서를 사용할 수 있는 것 처럼 테스트 환경 에서만 속성 **inbound__clientAuth__clientCert__allowUnknownCA** **true** 로 설정 합니다. 프로덕션 워크 로드의 경우이 속성을 **false** 로 설정 하 고 CA (인증 기관)에서 인증서를 설정 하는 것이 좋습니다.

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
>SAS 키 기반 클라이언트 인증을 사용 하면 비 IoT edge 모듈이 관리 및 런타임 작업을 수행할 수 있습니다. 물론 API 포트는 IoT Edge 네트워크 외부에서 액세스할 수 있습니다.
