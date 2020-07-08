---
title: API 프로토콜 구성-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge에서 Event Grid에 의해 노출 되는 API 프로토콜을 구성 합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841813"
---
# <a name="configure-event-grid-api-protocols"></a>Event Grid API 프로토콜 구성

이 가이드에서는 Event Grid 모듈의 가능한 프로토콜 구성에 대 한 예를 제공 합니다. Event Grid 모듈은 해당 관리 및 런타임 작업에 대 한 API를 노출 합니다. 다음 표에서는 프로토콜 및 포트를 캡처합니다.

| 프로토콜 | 포트 | Description |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | 기본적으로 해제 되어 있습니다. 테스트 중에만 유용 합니다. 프로덕션 워크 로드에는 적합 하지 않습니다.
| HTTPS | 4438 | 기본값

모든 가능한 구성에 대 한 [보안 및 인증](security-authentication.md) 가이드를 참조 하세요.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>동일한에 지 네트워크의 IoT 모듈에 HTTPS 노출

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>다른 IoT 모듈 및 비 IoT 작업에 HTTPS 사용

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
> **Portbindings** 섹션을 사용 하 여 내부 포트를 컨테이너 호스트의 포트에 매핑할 수 있습니다. 이 기능을 사용 하면 IoT Edge 장치를 공개적으로 연결할 수 있는 경우 IoT Edge 컨테이너 네트워크 외부에서 Event Grid 모듈에 연결할 수 있습니다.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>동일한에 지 네트워크의 IoT 모듈에 HTTP 및 HTTPS 노출

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>다른 IoT 모듈 및 비 IoT 작업에서 HTTP 및 HTTPS를 사용 하도록 설정

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
> 기본적으로 모든 IoT 모듈은 브리지 네트워크에서 만든 IoT Edge 런타임의 일부입니다. 동일한 네트워크에 있는 여러 IoT 모듈이 서로 통신할 수 있도록 합니다. **Portbindings** 를 사용 하면 컨테이너 내부 포트를 호스트 컴퓨터에 매핑하여 누구나 외부에서 Event Grid 모듈의 포트에 액세스할 수 있습니다.

>[!IMPORTANT]
> IoT Edge 네트워크 외부에서 포트에 액세스할 수 있지만 클라이언트 인증은 실제로 모듈을 호출 하는 데 사용할 수 있는 사용자를 적용 합니다.
