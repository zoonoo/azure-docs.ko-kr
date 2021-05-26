---
title: API 프로토콜 구성 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid 모듈의 가능한 프로토콜 구성에 대해 알아봅니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 05/10/2021
ms.subservice: iot-edge
ms.topic: article
ms.openlocfilehash: 2bd533cb59fd661e3ac6dff9ff6aa3fa2d646889
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370722"
---
# <a name="configure-event-grid-api-protocols"></a>Event Grid API 프로토콜 구성

이 가이드에서는 Event Grid 모듈의 가능한 프로토콜 구성에 대한 예를 제공합니다. Event Grid 모듈은 관리 및 런타임 작업에 대한 API를 공개합니다. 다음 표에서는 프로토콜 및 포트를 캡처합니다.

| 프로토콜 | 포트 | Description |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 기본적으로 꺼져 있습니다. 테스트하는 동안에만 유용합니다. 프로덕션 워크로드에는 적합하지 않습니다.
| HTTPS | 4438 | 기본값

가능한 모든 구성에 대해서는 [보안 및 인증](security-authentication.md) 가이드를 참조하세요.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>동일한 에지 네트워크의 IoT 모듈에 HTTPS 공개

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>다른 IoT 모듈 및 비 IoT 워크로드에 HTTPS 사용

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> **PortBindings** 섹션에서 내부 포트를 컨테이너 호스트의 포트에 매핑할 수 있습니다. 이 기능을 사용하면 IoT Edge 디바이스에 공개적으로 연결할 수 있는 경우 IoT Edge 컨테이너 네트워크 외부에서 Event Grid 모듈에 연결할 수 있습니다.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>동일한 에지 네트워크의 IoT 모듈에 HTTP 및 HTTPS 공개

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>다른 IoT 모듈 및 비 IoT 워크로드에 HTTP 및 HTTPS 사용

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> 기본적으로 모든 IoT 모듈은 브리지 네트워크에서 만든 IoT Edge 런타임의 일부입니다. 이를 통해 동일한 네트워크의 여러 IoT 모듈이 서로 통신할 수 있습니다. **PortBindings** 를 사용하면 컨테이너 내부 포트를 호스트 컴퓨터에 매핑할 수 있으므로 누구나 외부에서 Event Grid 모듈의 포트에 액세스할 수 있습니다.

>[!IMPORTANT]
> 이러한 포트는 IoT Edge 네트워크 외부에서 액세스할 수 있지만 클라이언트 인증은 실제로 누가 모듈을 호출할 수 있는지 적용합니다.
