---
title: API 프로토콜 구성 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드에서 노출되는 API 프로토콜을 구성합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841813"
---
# <a name="configure-event-grid-api-protocols"></a>이벤트 그리드 API 프로토콜 구성

이 가이드는 이벤트 그리드 모듈의 가능한 프로토콜 구성에 대한 예제를 제공합니다. Event Grid 모듈은 관리 및 런타임 작업에 대한 API를 노출합니다. 다음 표는 프로토콜과 포트를 캡처합니다.

| 프로토콜 | 포트 | 설명 |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 기본적으로 꺼져 있습니다. 테스트 하는 동안에만 유용합니다. 프로덕션 워크로드에 적합하지 않습니다.
| HTTPS | 4438 | 기본값

가능한 모든 구성은 [보안 및 인증](security-authentication.md) 가이드를 참조하십시오.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>동일한 에지 네트워크에서 IoT 모듈에 HTTPS 노출

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>다른 IoT 모듈 및 비 IoT 워크로드에 HTTPS 를 사용하도록 설정

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
> **PortBindings** 섹션을 사용하면 컨테이너 호스트의 포트에 내부 포트를 매핑할 수 있습니다. 이 기능을 사용하면 IoT 에지 장치가 공개적으로 연결할 수 있는 경우 IoT Edge 컨테이너 네트워크 외부에서 이벤트 그리드 모듈에 연결할 수 있습니다.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>동일한 에지 네트워크에서 IOT 모듈에 HTTP 및 HTTPS 노출

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
> 기본적으로 모든 IoT 모듈은 브리지 네트워크에서 만든 IoT Edge 런타임의 일부입니다. 이를 통해 동일한 네트워크의 다른 IoT 모듈이 서로 통신할 수 있습니다. **PortBindings를** 사용하면 컨테이너 내부 포트를 호스트 컴퓨터에 매핑하여 누구나 외부에서 Event Grid 모듈의 포트에 액세스할 수 있습니다.

>[!IMPORTANT]
> 포트는 IoT Edge 네트워크 외부에서 액세스할 수 있지만 클라이언트 인증은 실제로 모듈에 호출할 수 있는 사람을 강제합니다.
