---
title: "Azure IoT Hub 끝점 이해 | Microsoft Docs"
description: "개발자 가이드 - IoT Hub 장치 지향 및 서비스 지향 끝점에 대한 참조 정보"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: cdc50ec4bf98d7924ad5bf97c9a757f0bac01173
ms.lasthandoff: 03/23/2017


---
# <a name="reference---iot-hub-endpoints"></a>참조 - IoT Hub 끝점
## <a name="list-of-built-in-iot-hub-endpoints"></a>기본 제공 IoT Hub 끝점 목록
Azure IoT Hub는 다중 테넌트 서비스이며 다양한 행위자에게 기능을 노출합니다. 아래 다이어그램에서는 IoT Hub가 노출하는 다양한 끝점을 보여줍니다.

![IoT Hub 끝점][img-endpoints]

끝점에 대한 설명은 다음과 같습니다.

* **리소스 공급자**. IoT Hub 리소스 공급자는 Azure 구독 소유자가 IoT Hub를 만들거나 삭제하고, IoT Hub 속성을 업데이트할 수 있도록 하는 [Azure Resource Manager][lnk-arm] 인터페이스를 공개합니다. IoT Hub 속성은 장치 수준 액세스 제어와는 달리 [허브 수준 보안 정책][lnk-accesscontrol]과 클라우드-장치 및 장치-클라우드 메시징을 위한 기능 옵션을 제어합니다. 또한 IoT Hub 리소스 공급자는 사용자가 [장치 ID를 내보낼][lnk-importexport] 수 있도록 합니다.
* **장치 ID 관리**. 각 IoT Hub는 HTTP REST 끝점 집합을 노출하여 장치 ID를 관리합니다(만들기, 검색, 업데이트 및 삭제). [장치 ID][lnk-device-identities]는 장치 인증 및 액세스 제어에 사용됩니다.
* **장치 쌍 관리**. 각 IoT Hub에서 [장치 쌍][lnk-twins](tags 및 속성 업데이트)을 쿼리하고 업데이트하기 위해 서비스 지향 HTTP REST 끝점 집합을 공개합니다.
* **작업 관리**. 각 IoT Hub에서 [jobs][lnk-jobs]을 쿼리하고 업데이트하기 위해 서비스 지향 HTTP REST 끝점 집합을 공개합니다.
* **장치 끝점**. ID 레지스트리에 프로비전된 각 장치에 대해 IoT Hub는 해당 장치에서 메시지를 보내고 받는 데 사용하는 끝점 집합을 공개합니다.
  
  * *장치-클라우드 메시지 보내기*. 이 끝점을 사용하여 [장치-클라우드 메시지를 전송합니다][lnk-d2c].
  * *클라우드-장치 메시지 받기*. 장치에서 이 끝점을 사용하여 대상인 [클라우드-장치 메시지][lnk-c2d]를 수신합니다.
  * *파일 업로드를 시작합니다*. [파일을 업로드][lnk-upload]하기 위해 장치에서 이 끝점을 사용하여 IoT Hub에서 Azure Storage SAS URI를 수신합니다.
  * *장치 쌍 속성 검색 및 업데이트*. 장치에서 이 끝점을 사용하여 [장치 쌍][lnk-twins] 속성에 액세스합니다.
  * *직접 메서드 요청 수신*. 장치에서 이 끝점을 사용하여 [직접 메서드][lnk-methods] 요청을 수신합니다.
    
    이러한 끝점은 [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 및 [AMQP 1.0][lnk-amqp] 프로토콜을 사용하여 공개됩니다. AMQP는 포트 443의 [WebSockets][lnk-websockets]를 통해서도 사용할 수 있습니다.
    
    장치 쌍 및 메서드 끝점은 [MQTT v3.1.1][lnk-mqtt]을 사용하는 경우에만 제공됩니다.
* **서비스 끝점**. 각 IoT Hub는 솔루션 백 엔드에서 장치와 통신하는 데 사용할 수 있는 끝점의 집합을 노출합니다. 이러한 끝점은 현재 HTTP 1.1을 통해 공개되는 메서드 호출 끝점은 제외하고는 [AMQP][lnk-amqp] 프로토콜을 사용하여 공개됩니다.
  
  * *장치-클라우드 메시지 받기*. 이 끝점은 [Azure Event Hubs][lnk-event-hubs]와 호환됩니다. 백 엔드 서비스는 이 끝점을 사용하여 장치에서 보낸 모든 [장치-클라우드 메시지][lnk-d2c]를 읽을 수 있습니다. 이 기본 제공 끝점 외에도 IoT Hub에 사용자 지정 끝점을 만들 수 있습니다.
  * *클라우드-장치 메시지를 보내고 배달 승인 받기*. 이러한 끝점을 사용하면 솔루션 백 엔드에서 신뢰할 수 있는 [클라우드-장치 메시지][lnk-c2d]를 전송하고 해당 전달 또는 만료 승인을 수신할 수 있습니다.
  * *파일 알림을 받습니다*. 이 메시징 끝점을 사용하면 장치가 성공적으로 파일을 업로드하는 경우 알림을 받을 수 있습니다. 
  * *직접 메서드 호출*. 이 끝점을 사용하면 백 엔드 서비스가 장치에서 [직접 메서드][lnk-methods]를 호출할 수 있습니다.
  * *작업 모니터링 이벤트를 수신합니다*. 이 끝점을 통해 IoT Hub가 작업 모니터링 이벤트를 내보내도록 구성된 경우 작업 모니터링 이벤트를 수신할 수 있습니다. 자세한 내용은 [IoT Hub 작업 모니터링][lnk-operations-mon]을 참조하세요.

[Azure IoT SDK][lnk-sdks] 문서에서는 이러한 끝점에 액세스할 수 있는 다양한 방법에 대해 설명합니다.

마지막으로 모든 IoT Hub 끝점이 [TLS][lnk-tls] 프로토콜을 사용하고 어떤 끝점도 암호화되지/보안되지 않은 채널에 공개되지 않는다는 점에 유의합니다.

## <a name="custom-endpoints"></a>사용자 지정 끝점
구독의 기존 Azure 서비스를 IoT Hub에 연결하여 메시지 라우팅을 위한 끝점 역할을 할 수 있습니다. 이러한 끝점은 서비스 끝점 역할을 하며 메시지 경로에 대한 싱크로 사용됩니다. 장치는 추가 끝점에 직접 쓸 수 없습니다. 메시지 경로에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기][lnk-devguide-messaging]의 개발자 가이드 항목을 참조하세요.

IoT Hub는 현재 추가 끝점으로 다음과 같은 Azure 서비스를 지원합니다.

* Event Hubs
* 서비스 버스 큐
* 서비스 버스 토픽

IoT Hub는 메시지 라우팅을 작동하기 위해 이러한 서비스 끝점에 대한 쓰기 액세스가 필요합니다. Azure Portal을 통해 끝점을 구성하는 경우 필요한 권한이 추가됩니다. 예상된 처리량을 지원하도록 서비스를 구성해야 합니다. 먼저 IoT 솔루션을 구성한 다음 실제 부하에 필요한 부분을 수정할 때 추가 끝점을 모니터링해야 합니다.

메시지가 동일한 끝점을 가리키는 여러 경로와 일치하는 경우 IoT Hub는 메시지를 해당 끝점에 한 번만 전달합니다. 따라서 Service Bus 큐 또는 항목에 대해 중복 제거를 구성할 필요가 없습니다. 분할된 큐에서 파티션 선호도는 메시지 순서를 보장합니다. 세션이 설정된 큐는 끝점으로 지원되지 않습니다. 중복 제거가 설정된 분할된 큐 및 항목도 지원되지 않습니다.

추가할 수 있는 끝점의 수에 대한 제한은 [할당량 및 제한][lnk-devguide-quotas]을 참조하세요.

## <a name="field-gateways"></a>현장 게이트웨이
IoT 솔루션에서 *필드 게이트웨이*는 장치와 IoT Hub 끝점 사이에 위치하며 일반적으로 장치 가까이에 위치합니다. 장치는 해당 장치에서 지원되는 프로토콜을 사용하여 필드 게이트웨이와 직접 통신합니다. 필드 게이트웨이는 IoT Hub에서 지원하는 프로토콜을 사용하여 IoT Hub 끝점에 연결됩니다. 필드 게이트웨이는 게이트웨이에 의도된 종단 간 시나리오를 완수하는 소프트웨어를 실행하는 매우 특수화된 하드웨어나 저출력 컴퓨터일 수 있습니다.

[Azure IoT Gateway SDK][lnk-gateway-sdk]를 사용하여 필드 게이트웨이를 구현할 수 있습니다. SDK는 여러 장치에서 동일한 IoT Hub 연결로 통신을 다중 송신할 수 있는 기능과 같은 특정한 기능을 제공합니다.

## <a name="next-steps"></a>다음 단계
이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [장치 쌍 및 작업을 위한 IoT Hub 쿼리 언어][lnk-devguide-query]
* [할당량 및 제한][lnk-devguide-quotas]
* [IoT Hub MQTT 지원][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md

