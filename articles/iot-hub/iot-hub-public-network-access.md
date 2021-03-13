---
title: Azure IoT hub에 대 한 공용 네트워크 액세스 관리
description: IoT hub에 대 한 공용 네트워크 액세스를 사용 하거나 사용 하지 않도록 설정 하는 방법에 대 한 설명서
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 539e420cb9085fad10ea3972ba0e9e5ffb9d0622
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419765"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>IoT hub에 대 한 공용 네트워크 액세스 관리

[VNet에서 IoT hub에 대 한 개인 끝점](virtual-network-support.md)으로만 액세스를 제한 하려면 공용 네트워크 액세스를 사용 하지 않도록 설정 합니다. 이렇게 하려면 Azure Portal 또는 API를 사용 `publicNetworkAccess` 합니다. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Azure Portal를 사용 하 여 공용 네트워크 액세스 해제

1. [Azure Portal](https://portal.azure.com) 방문
2. IoT Hub로 이동
3. 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.
4. "공용 네트워크 액세스 허용"에서 **사용 안 함** 을 선택 합니다.
5. **저장** 을 선택합니다.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="공용 네트워크 액세스를 해제할 Azure Portal를 보여 주는 이미지" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

공용 네트워크 액세스를 켜려면 **모든 네트워크** 를 선택한 다음 **저장** 을 클릭 합니다.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>공용 네트워크 액세스를 사용 하지 않도록 설정한 후 IoT Hub 액세스

공용 네트워크 액세스를 사용 하지 않도록 설정한 후에는 [Azure 개인 링크를 사용 하 여 VNet 개인 끝점](virtual-network-support.md)을 통해서만 IoT Hub에 액세스할 수 있습니다. 이 제한에는 IoT Hub 서비스에 대 한 API 호출이 사용자의 자격 증명으로 브라우저를 사용 하 여 직접 수행 되기 때문에 Azure Portal를 통한 액세스도 포함 됩니다. 

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>공용 네트워크 액세스를 사용 하지 않도록 설정한 후 끝점, IP 주소 및 포트를 IoT Hub 합니다.

IoT Hub은 다중 테 넌 트 PaaS (Platform as a Service) 이므로 여러 고객이 동일한 계산, 네트워킹 및 저장소 하드웨어 리소스 풀을 공유 합니다. IoT Hub의 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소를 사용 하는 공용 끝점에 매핑됩니다. 이 IoT Hub 퍼블릭 엔드포인트는 여러 고객이 공유하며, 광역 네트워크와 온-프레미스 네트워크에 있는 IoT 디바이스가 여기에 액세스할 수 있습니다. 

공용 네트워크 액세스를 사용 하지 않도록 설정 하면 특정 IoT hub 리소스에 적용 되므로 격리가 보장 됩니다. 공용 경로를 사용 하 여 다른 고객 리소스에 대해 서비스를 활성 상태로 유지 하기 위해 해당 공용 끝점은 확인 가능 하 고 IP 주소는 검색 가능 하며 포트는 열린 상태로 유지 됩니다. Microsoft는 여러 계층의 보안을 통합 하 여 테 넌 트 간의 완전 한 격리를 보장 하므로이는 문제가 되지 않습니다. 자세히 알아보려면 [Azure 공용 클라우드에서 격리](../security/fundamentals/isolation-choices.md#tenant-level-isolation)를 참조 하세요.

## <a name="ip-filter"></a>IP 필터 

공용 네트워크 액세스를 사용 하지 않도록 설정한 경우 모든 [IP 필터](iot-hub-ip-filtering.md) 규칙은 무시 됩니다. 이는 공용 인터넷의 모든 Ip가 차단 되기 때문입니다. IP 필터를 사용 하려면 **선택한 ip 범위** 옵션을 사용 합니다.

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>기본 제공 Event Hub 호환 끝점을 사용 하 여 버그 수정

IoT Hub에 대 한 공용 네트워크 액세스를 사용할 수 없을 때 공용 인터넷을 통해 [기본 제공 Event Hub 호환 끝점](iot-hub-devguide-messages-read-builtin.md) 에 계속 액세스할 수 있는 IoT Hub 버그가 있습니다. 이 버그에 대 한 자세한 내용을 알아보고 문의 하려면 [기본 제공 이벤트 허브 끝점에 대 한 액세스를 사용 하지 않도록 설정 IoT Hub에 대 한 공용 네트워크 액세스 비활성화](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)를 참조 하세요.
