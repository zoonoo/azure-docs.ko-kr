---
title: IoT hub의 IP 주소 이해 | Microsoft Docs
description: IoT hub IP 주소 및 해당 속성을 쿼리 하는 방법을 이해 합니다. IoT hub의 IP 주소는 재해 복구 또는 지역 장애 조치 (failover)와 같은 특정 시나리오에서 변경 될 수 있습니다.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 9c9d02eeead4ccbed372e9a55d9b551da83caa39
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642294"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>IoT hub의 IP 주소 이해

IoT hub의 IP 주소는 허브에 대 한 부하 분산 된 공용 엔드포인트 이며 전용 IP 주소는 아닙니다. 주소는 해당 주소가 배포 된 Azure 지역의 네트워크 주소 범위에 의해 결정 됩니다. 이 IP 주소는 예 고 없이 변경 될 수 있습니다. 데이터 센터 네트워크 업데이트, 데이터 센터 재해 복구 또는 IoT hub의 지역별 장애 조치 (failover)는 IoT hub IP 주소를 변경할 수 있습니다. Azure IoT Hub 지역별 장애 조치 (failover) 및 가용성에 대 한 자세한 내용은 [고가용성 및 재해 복구 IoT Hub](iot-hub-ha-dr.md) 를 참조 하세요.

다른 지역으로 장애 조치 (failover) 후 IoT hub의 IP 주소가 변경 됩니다. [IoT hub의 수동 장애 조치 (failover) 수행](tutorial-manual-failover.md)자습서에 따라이 기능을 테스트할 수 있습니다.

## <a name="discover-your-iot-hub-ip-address"></a>IoT hub IP 주소 검색

CNAME *([azure-devices.net*].)에서 역방향 DNS 조회를 사용 하 여 IoT Hub IP 주소를 검색할 수 있습니다. **Nslookup** 을 사용 하 여 IoT HUB 인스턴스의 IP 주소를 확인할 수 있습니다.

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

이 IP 주소는 예 고 없이 변경 될 수 있습니다. 장애 조치 (failover) 또는 재해 복구 시나리오에서는 보조 지역에서 IoT hub IP 주소를 폴링해야 합니다.

## <a name="outbound-firewall-rules-for-iot-hub"></a>IoT hub에 대 한 아웃 바운드 방화벽 규칙

IoT hub 호스트 이름 또는 도메인에 따라 방화벽 규칙 및 필터링을 만들어 봅니다. 특정 주소에 대 한 아웃 바운드 트래픽만 허용할 수 있는 경우 IoT hub IP 주소를 정기적으로 폴링하고 방화벽 규칙을 업데이트 합니다.

