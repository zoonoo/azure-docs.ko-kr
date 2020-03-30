---
title: IoT 허브의 IP 주소 이해 | 마이크로 소프트 문서
description: IoT 허브 IP 주소 및 해당 속성을 쿼리하는 방법을 이해합니다. IoT 허브의 IP 주소는 재해 복구 또는 지역 장애 조치(failover)와 같은 특정 시나리오에서 변경될 수 있습니다.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367570"
---
# <a name="iot-hub-ip-addresses"></a>IoT 허브 IP 주소

IoT Hub 공용 끝점의 IP 주소 접두사는 _AzureIoTHub_ [서비스 태그](../virtual-network/service-tags-overview.md)아래에 주기적으로 게시됩니다.

> [!NOTE]
> 온-프레미스 네트워크 내에 배포되는 장치의 경우 Azure IoT Hub는 개인 엔드포인트와의 VNET 연결 통합을 지원합니다. 자세한 내용은 [VNET에 대한 IoT Hub 지원을](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints) 참조하십시오.


이러한 IP 주소 접두사를 사용하여 IoT Hub와 장치 또는 네트워크 자산 간의 연결을 제어하여 다양한 네트워크 격리 목표를 구현할 수 있습니다.

| 목표 | 적용 가능한 시나리오 | 접근 방식 |
|------|-----------|----------|
| 장치 및 서비스가 IoT Hub 엔드포인트와만 통신하도록 보장 | [장치-클라우드,](./iot-hub-devguide-messaging.md) [클라우드-장치](./iot-hub-devguide-messages-c2d.md) 메시징, 직접 [메서드,](./iot-hub-devguide-direct-methods.md) [장치 및 모듈 트윈](./iot-hub-devguide-device-twins.md) 및 [장치 스트림](./iot-hub-device-streams-overview.md) | _AzureIoTHub_ 및 _EventHub_ 서비스 태그를 사용하여 IoT Hub 및 이벤트 허브 IP 주소 접두사를 검색하고 해당 IP 주소 접두사에 대한 장치 및 서비스 방화벽 설정에서 ALLOW 규칙을 구성합니다. 다른 대상 IP 주소로 트래픽을 드롭하면 장치 또는 서비스와 통신하지 않으려는 것입니다. |
| IoT Hub 장치 엔드포인트가 장치 및 네트워크 자산에서만 연결을 수신하도록 보장 | [장치-클라우드,](./iot-hub-devguide-messaging.md) [클라우드-장치](./iot-hub-devguide-messages-c2d.md) 메시징, 직접 [메서드,](./iot-hub-devguide-direct-methods.md) [장치 및 모듈 트윈](./iot-hub-devguide-device-twins.md) 및 [장치 스트림](./iot-hub-device-streams-overview.md) | IoT Hub [IP 필터 기능을](iot-hub-ip-filtering.md) 사용하여 장치 및 네트워크 자산 IP 주소의 연결을 [허용합니다(제한](#limitations-and-workarounds) 섹션 참조). | 
| 경로의 사용자 지정 끝점 리소스(저장소 계정, 서비스 버스 및 이벤트 허브)가 네트워크 자산에서만 연결할 수 있는지 확인합니다. | [메시지 라우팅](./iot-hub-devguide-messages-d2c.md) | 제한 연결에 대한 리소스의 지침을 따르십시오(예: [방화벽 규칙,](../storage/common/storage-network-security.md) [개인 링크](../private-link/private-endpoint-overview.md)또는 [서비스 끝점).](../virtual-network/virtual-network-service-endpoints-overview.md) _AzureIoTHub_ 서비스 태그를 사용하여 IoT Hub IP 주소 접두사를 검색하고 리소스의 방화벽 구성에서 해당 [limitations](#limitations-and-workarounds) IP 접두사에 대한 ALLOW 규칙을 추가합니다(제한 섹션 참조). |



## <a name="best-practices"></a>모범 사례

* 장치의 방화벽 구성에 ALLOW 규칙을 추가할 [때는 해당 프로토콜에서 사용하는](./iot-hub-devguide-protocols.md#port-numbers)특정 포트를 제공하는 것이 가장 좋습니다.

* IoT 허브의 IP 주소 접두사는 변경될 수 있습니다. 이러한 변경 내용은 적용되기 전에 서비스 태그를 통해 주기적으로 게시됩니다. 따라서 최신 서비스 태그를 정기적으로 검색하고 사용하는 프로세스를 개발하는 것이 중요합니다. 이 [프로세스는 서비스 태그 검색 API를](../virtual-network/service-tags-overview.md#service-tags-on-premises)통해 자동화할 수 있습니다. 서비스 태그 검색 API는 아직 미리 보기 중이며 경우에 따라 태그 및 IP 주소의 전체 목록이 생성되지 않을 수 있습니다. 검색 API를 일반적으로 사용할 수 있을 때까지 [다운로드 가능한 JSON 형식으로 서비스 태그를](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)사용하는 것이 좋습니다. 

* *AzureIoTHub를 사용합니다.] 특정* 리전에서 IoT 허브 끝점에서 사용하는 IP 접두사를 식별하는 태그입니다. 데이터 센터 재해 복구 또는 [지역 장애 조치를](iot-hub-ha-dr.md) 고려하여 IoT Hub의 지리적 쌍 지역의 IP 접두사에 대한 연결도 활성화됩니다.

* IoT Hub에서 방화벽 규칙을 설정하면 IoT Hub에 대해 Azure CLI 및 PowerShell 명령을 실행하는 데 필요한 연결이 차단될 수 있습니다. 이를 방지하려면 클라이언트의 IP 주소 접두사에 대한 ALLOW 규칙을 추가하여 CLI 또는 PowerShell 클라이언트가 IoT Hub와 통신할 수 있도록 다시 활성화할 수 있습니다.  


## <a name="limitations-and-workarounds"></a>제한 사항 및 해결 방법

* IoT Hub IP 필터 기능에는 10개의 규칙제한이 있습니다. 이 제한및 Azure 고객 지원을 통해 요청을 통해 발생될 수 있습니다. 

* 구성된 [IP 필터링 규칙은](iot-hub-ip-filtering.md) IoT Hub 의 기본 제공 이벤트 허브 엔드포인트가 아닌 IoT Hub IP 엔드포인트에만 적용됩니다. 메시지가 저장되는 이벤트 허브에 IP 필터링을 적용해야 하는 경우 원하는 IP 필터링 규칙을 직접 구성할 수 있는 고유한 이벤트 허브 리소스를 가져올 수 있습니다. 이렇게 하려면 IoT Hub의 기본 제공 이벤트 허브 대신 고유한 이벤트 허브 리소스를 프로비전하고 [메시지 라우팅을](./iot-hub-devguide-messages-d2c.md) 설정하여 해당 리소스로 메시지를 보내야 합니다. 마지막으로 위의 표에서 설명한 대로 메시지 라우팅 기능을 활성화하려면 IoT Hub의 IP 주소 접두사에서 프로비저닝된 이벤트 허브 리소스에 대한 연결을 허용해야 합니다.

* 저장소 계정으로 라우팅할 때 저장소 계정이 IoT Hub와 다른 지역에 있는 경우에만 IoT Hub의 IP 주소 접두사에서 트래픽을 허용하는 것이 가능합니다.

## <a name="support-for-ipv6"></a>IPv6에 대한 지원 

IPv6는 현재 IoT Hub에서 지원되지 않습니다.
