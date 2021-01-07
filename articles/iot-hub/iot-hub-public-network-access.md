---
title: Azure IoT hub에 대 한 공용 네트워크 액세스 관리
description: IoT hub에 대 한 공용 네트워크 액세스를 사용 하거나 사용 하지 않도록 설정 하는 방법에 대 한 설명서
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937565"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>IoT hub에 대 한 공용 네트워크 액세스 관리

[VNet에서 IoT hub에 대 한 개인 끝점](virtual-network-support.md)으로만 액세스를 제한 하려면 공용 네트워크 액세스를 사용 하지 않도록 설정 합니다. 이렇게 하려면 Azure Portal 또는 API를 사용 `publicNetworkAccess` 합니다. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Azure Portal를 사용 하 여 공용 네트워크 액세스 해제

1. [Azure Portal](https://portal.azure.com) 방문
2. IoT Hub로 이동
3. 왼쪽 메뉴에서 **네트워킹** 을 선택 합니다.
4. "공용 네트워크 액세스 허용"에서 **사용 안 함** 을 선택 합니다.
5. **저장**을 선택합니다.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="공용 네트워크 액세스를 해제할 Azure Portal를 보여 주는 이미지" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

공용 네트워크 액세스를 설정 하려면 **사용**을 선택 하 고 **저장**을 선택 합니다.

## <a name="ip-filter"></a>IP 필터 

공용 네트워크 액세스를 사용 하지 않도록 설정한 경우 모든 [IP 필터](iot-hub-ip-filtering.md) 규칙은 무시 됩니다. 이는 공용 인터넷의 모든 Ip가 차단 되기 때문입니다. IP 필터를 사용 하려면 **선택한 ip 범위** 옵션을 사용 합니다.

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>기본 제공 Event Hub 호환 끝점을 사용 하 여 버그 수정

IoT Hub에 대 한 공용 네트워크 액세스를 사용할 수 없을 때 공용 인터넷을 통해 [기본 제공 Event Hub 호환 끝점](iot-hub-devguide-messages-read-builtin.md) 에 계속 액세스할 수 있는 IoT Hub 버그가 있습니다. 이 버그에 대 한 자세한 내용을 알아보고 문의 하려면 [기본 제공 이벤트 허브 끝점에 대 한 액세스를 사용 하지 않도록 설정 IoT Hub에 대 한 공용 네트워크 액세스 비활성화](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)를 참조 하세요.