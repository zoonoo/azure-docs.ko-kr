---
title: IoT 장치 프로비저닝 서비스(DPS) 인스턴스의 IP 주소 이해 | 마이크로 소프트 문서
description: IoT 장치 프로비저닝 서비스(DPS) 주소 및 해당 속성을 쿼리하는 방법을 이해합니다. DPS 인스턴스의 IP 주소는 재해 복구 또는 지역 장애 조치(failover)와 같은 특정 시나리오에서 변경될 수 있습니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284929"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT 허브 DPS IP 주소

IoT 허브 장치 프로비저닝 서비스(DPS)의 공용 끝점에 대한 IP 주소 접두사는 _AzureIoTHub_ [서비스 태그](../virtual-network/service-tags-overview.md)아래에 주기적으로 게시됩니다. 이러한 IP 주소 접두사를 사용하여 IoT DPS 인스턴스와 장치 또는 네트워크 자산 간의 연결을 제어하여 다양한 네트워크 격리 목표를 구현할 수 있습니다.

| 목표 | 접근 방식 |
|------|----------|
| 장치 및 서비스가 IoT Hub DPS 엔드포인트와만 통신하도록 보장 | _AzureIoTHub_ 서비스 태그를 사용하여 IoT Hub DPS 인스턴스를 검색합니다. 그에 따라 해당 IP 주소 접두사에 대한 장치 및 서비스의 방화벽 설정에 ALLOW 규칙을 구성합니다. 장치 또는 서비스와 통신하지 않으려는 다른 대상 IP 주소로 트래픽을 드롭하도록 규칙을 구성합니다. |
| IoT Hub DPS 엔드포인트가 장치 및 네트워크 자산에서만 연결을 수신하도록 보장 | IoT DPS [IP 필터 기능을](iot-dps-ip-filtering.md) 사용하여 장치 및 DPS 서비스 API에 대한 필터 규칙을 만듭니다. 이러한 필터 규칙은 장치 및 네트워크 자산 IP 주소에서만 연결을 허용하는 데 사용할 수 [있습니다(제한](#limitations-and-workarounds) 섹션 참조). | 




## <a name="best-practices"></a>모범 사례

* 장치의 방화벽 구성에 ALLOW 규칙을 추가할 [때는 해당 프로토콜에서 사용하는](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)특정 포트를 제공하는 것이 가장 좋습니다.

* IoT DPS 인스턴스의 IP 주소 접두사는 변경될 수 있습니다. 이러한 변경 내용은 적용되기 전에 서비스 태그를 통해 주기적으로 게시됩니다. 따라서 최신 서비스 태그를 정기적으로 검색하고 사용하는 프로세스를 개발하는 것이 중요합니다. 이 [프로세스는 서비스 태그 검색 API를](../virtual-network/service-tags-overview.md#service-tags-on-premises)통해 자동화할 수 있습니다. 서비스 태그 검색 API는 아직 미리 보기 중이며 경우에 따라 태그 및 IP 주소의 전체 목록이 생성되지 않을 수 있습니다. 검색 API를 일반적으로 사용할 수 있을 때까지 [다운로드 가능한 JSON 형식으로 서비스 태그를](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)사용하는 것이 좋습니다. 

* *AzureIoTHub를 사용합니다.] 지역 이름]* 태그를 사용하여 특정 지역의 DPS 끝점에서 사용하는 IP 접두사를 식별합니다. 데이터 센터 재해 복구 또는 [지역 장애 조치를](../iot-hub/iot-hub-ha-dr.md)고려하려면 DPS 인스턴스의 지리적 쌍 영역의 IP 접두사에 대한 연결도 활성화되어 있는지 확인합니다.

* DPS 인스턴스에 대한 방화벽 규칙을 설정하면 Azure CLI 및 PowerShell 명령을 실행하는 데 필요한 연결이 차단될 수 있습니다. 이러한 연결 문제를 방지하려면 클라이언트의 IP 주소 접두사에 대한 ALLOW 규칙을 추가하여 CLI 또는 PowerShell 클라이언트가 DPS 인스턴스와 통신하도록 다시 활성화할 수 있습니다.  


## <a name="limitations-and-workarounds"></a>제한 사항 및 해결 방법

* DPS IP 필터 기능에는 100개의 규칙제한이 있습니다. 이 제한및 Azure 고객 지원을 통해 요청을 통해 발생될 수 있습니다. 

* 구성된 [IP 필터링 규칙은](iot-dps-ip-filtering.md) 연결된 IoT Hub 끝점이 아닌 DPS 끝점에만 적용됩니다. 연결된 IoT 허브에 대한 IP 필터링은 별도로 구성해야 합니다. 자세한 내용은 [IoT Hub IP 필터링 규칙을](../iot-hub/iot-hub-ip-filtering.md)참조하십시오.

## <a name="support-for-ipv6"></a>IPv6에 대한 지원 

IPv6는 현재 IoT 허브 또는 DPS에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

DPS를 사용하여 IP 주소 구성에 대해 자세히 알아보려면 다음을 참조하십시오.

* [IP 필터링 구성](iot-dps-ip-filtering.md)
