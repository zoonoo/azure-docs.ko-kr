---
title: 들어오는 호출의 클라이언트 인증 구성 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid 모듈의 가능한 클라이언트 인증 구성에 대해 알아봅니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 05/10/2021
ms.subservice: iot-edge
ms.topic: article
ms.openlocfilehash: 29b75a135df269f695fc63a10a74438733f988fc
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370654"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>들어오는 호출의 클라이언트 인증 구성

이 가이드에서는 Event Grid 모듈에 가능한 클라이언트 인증 구성의 예를 제공합니다. Event Grid 모듈은 다음 두 가지 유형의 클라이언트 인증을 지원합니다.

* SAS(공유 액세스 서명) 키 기반
* 인증서 기반

가능한 모든 구성에 대해서는 [보안 및 인증](security-authentication.md) 가이드를 참조하세요.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>인증서 기반 클라이언트 인증 사용, 자체 서명된 인증서 사용 안 함

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
>자체 서명된 인증서는 일반적으로 사용 가능하므로 테스트 환경에서만 **inbound__clientAuth__clientCert__allowUnknownCA** 속성을 **true** 로 설정합니다. 프로덕션 워크로드의 경우 이 속성을 **false** 로 설정하고 CA(인증 기관)의 인증서를 사용하는 것이 좋습니다.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>인증서 기반 및 SAS 키 기반 클라이언트 인증 사용

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
>SAS 키 기반 클라이언트 인증을 사용하면 IoT Edge 네트워크 외부에서 API 포트에 액세스할 수 있는 경우 비 IoT Edge 모듈이 관리 및 런타임 작업을 수행할 수 있습니다.
