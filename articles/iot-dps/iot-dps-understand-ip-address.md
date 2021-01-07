---
title: IoT (IoT Device 프로 비전 서비스) 인스턴스의 IP 주소 이해 | Microsoft Docs
description: IoT (IoT Device 프로 비전 서비스) 주소 및 해당 속성을 쿼리 하는 방법을 이해 합니다. DPS 인스턴스의 IP 주소는 재해 복구 또는 지역 장애 조치 (failover)와 같은 특정 시나리오에서 변경 될 수 있습니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "79284929"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT Hub DPS IP 주소

IoT Hub Device Provisioning Service (DPS)의 공용 끝점에 대 한 IP 주소 접두사는 주기적으로 _AzureIoTHub_ [Service 태그](../virtual-network/service-tags-overview.md)아래에 게시 됩니다. 이러한 IP 주소 접두사를 사용 하 여 다양 한 네트워크 격리 목표를 구현 하기 위해 IoT DPS 인스턴스와 장치 또는 네트워크 자산 간의 연결을 제어할 수 있습니다.

| 목표 | 접근 방식 |
|------|----------|
| 장치 및 서비스가 IoT Hub DPS 끝점과만 통신 하는지 확인 | _AzureIoTHub_ service 태그를 사용 하 여 IoT Hub DPS 인스턴스를 검색 합니다. 해당 IP 주소 접두사에 대 한 장치 및 서비스의 방화벽 설정에 대 한 허용 규칙을 적절 하 게 구성 합니다. 장치 또는 서비스와 통신 하지 않으려는 다른 대상 IP 주소에 대 한 트래픽을 삭제 하는 규칙을 구성 합니다. |
| IoT Hub DPS 끝점이 장치 및 네트워크 자산 에서만 연결을 수신 하는지 확인 합니다. | IoT DPS [IP 필터 기능](iot-dps-ip-filtering.md) 을 사용 하 여 장치 및 DPS 서비스 api에 대 한 필터 규칙을 만듭니다. 이러한 필터 규칙을 사용 하 여 장치와 네트워크 자산 IP 주소의 연결만 허용할 수 있습니다 ( [제한](#limitations-and-workarounds) 섹션 참조). | 




## <a name="best-practices"></a>모범 사례

* 디바이스의 방화벽 구성에서 허용 규칙을 추가할 때 [적용 가능한 프로토콜에서 사용하는 특정 포트](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)를 제공하는 것이 가장 좋습니다.

* IoT DPS 인스턴스의 IP 주소 접두사는 변경 될 수 있습니다. 이러한 변경 내용은 적용되기 전에 서비스 태그를 통해 정기적으로 게시됩니다. 따라서 최신 서비스 태그를 정기적으로 검색하고 사용하는 프로세스를 개발하는 것이 중요합니다. 이 프로세스는 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)를 통해 자동화할 수 있습니다. 서비스 태그 검색 API는 아직 미리 보기 상태 이며 경우에 따라 태그 및 IP 주소의 전체 목록을 생성 하지 못할 수도 있습니다. 검색 API를 일반적으로 사용할 수 있을 때까지 [서비스 태그를 다운로드 가능한 JSON 형식](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)으로 사용하는 것이 좋습니다. 

* AzureIoTHub를 사용 *합니다. [ 지역 이름]* 태그를 사용 하 여 특정 지역의 DPS 끝점에서 사용 하는 IP 접두사를 식별 합니다. 데이터 센터 재해 복구 또는 [지역 장애 조치 (failover)](../iot-hub/iot-hub-ha-dr.md)를 고려 하 여 DPS 인스턴스의 지역 쌍 지역에 대 한 IP 접두사의 연결도 사용 하도록 설정 해야 합니다.

* DPS 인스턴스에 대 한 방화벽 규칙을 설정 하면 Azure CLI 및 PowerShell 명령을 실행 하는 데 필요한 연결을 차단할 수 있습니다. 이러한 연결 문제를 방지 하려면 클라이언트의 IP 주소 접두사에 대 한 허용 규칙을 추가 하 여 CLI 또는 PowerShell 클라이언트가 DPS 인스턴스와 통신할 수 있도록 다시 설정할 수 있습니다.  


## <a name="limitations-and-workarounds"></a>제한 사항 및 해결 방법

* DPS IP 필터 기능에는 100 규칙의 제한이 있습니다. 이 제한은 Azure 고객 지원을 통한 요청을 통해 발생할 수 있습니다. 

* 구성 된 [IP 필터링 규칙](iot-dps-ip-filtering.md) 은 연결 된 IoT Hub 끝점이 아니라 DPS 끝점에만 적용 됩니다. 연결 된 IoT Hub에 대 한 IP 필터링은 별도로 구성 해야 합니다. 자세한 내용은 [IOT HUB IP 필터링 규칙](../iot-hub/iot-hub-ip-filtering.md)을 참조 하세요.

## <a name="support-for-ipv6"></a>IPv6에 대한 지원 

I p v 6은 현재 IoT Hub 또는 DPS에서 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

DPS를 사용 하는 IP 주소 구성에 대해 자세히 알아보려면 다음을 참조 하세요.

* [IP 필터링 구성](iot-dps-ip-filtering.md)
