---
title: Azure IoT Hub 서비스 및 리소스 상태 확인 | Microsoft Docs
description: Azure Service Health 및 Azure Resource Health를 사용 하 여 IoT Hub 모니터링
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548519"
---
# <a name="check-iot-hub-service-and-resource-health"></a>IoT Hub 서비스 및 리소스 상태 확인

Azure IoT Hub은 [Azure Service Health 서비스](../service-health/overview.md) 와 통합 되어 IoT Hub 서비스 및 개별 IoT hub의 서비스 수준 상태를 모니터링할 수 있는 기능을 제공 합니다. IoT Hub 서비스 또는 IoT Hub의 상태가 변경 될 때 알림을 받도록 경고를 설정할 수도 있습니다. Azure Service Health 서비스는 Azure Resource Health, Azure Service Health 및 Azure 상태 페이지의 세 가지 작은 서비스의 조합입니다. 이 문서의 섹션에서는 각 서비스 및 해당 관계에 대해 자세히 설명 하 고 IoT Hub.

Azure Service Health 서비스는 IoT Hub 서비스 및 개별 IoT hub의 가용성에 영향을 줄 수 있는 중단 및 업그레이드와 같은 서비스 수준 이벤트를 모니터링 하는 데 도움이 됩니다. 또한 IoT Hub는 Azure Monitor와 통합 되어 특정 IoT Hub에서 발생 하는 작업 오류 및 상태를 모니터링 하는 데 사용할 수 있는 IoT Hub 플랫폼 메트릭과 IoT Hub 리소스 로그를 제공 합니다. 자세히 알아보려면 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조 하세요.

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Azure Resource Health IoT hub의 상태를 확인 합니다.

Azure Resource Health는 개별 리소스의 상태를 추적 하는 Azure Service Health 서비스의 일부입니다. 포털에서 직접 IoT hub의 상태를 확인할 수 있습니다.

포털을 사용 하 여 IoT hub의 상태 및 상태 기록을 보려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Portal의 IoT hub로 이동 합니다.

1. 왼쪽 창의 **지원 + 문제 해결** 에서 **Resource Health** 를 선택 합니다.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="IoT hub에 대 한 리소스 상태 페이지":::

Azure Resource Health 및 상태 데이터를 해석 하는 방법에 대 한 자세한 내용은 Azure Service Health 설명서의 [Resource Health 개요](../service-health/resource-health-overview.md) 를 참조 하세요.

IoT hub의 상태가 변경 될 때 트리거할 경고를 설정 하려면 **리소스 상태 경고 추가** 를 선택할 수도 있습니다. 자세한 내용은 Azure Service Health 설명서의 [서비스 상태 이벤트에 대 한 경고 구성](../service-health/alerts-activity-log-service-notifications-portal.md) 및 관련 항목을 참조 하세요.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Azure Service Health를 사용 하 여 구독에서 IoT hub의 상태를 확인 합니다.

Azure Service Health를 사용 하 여 구독에 있는 모든 IoT hub의 상태를 확인할 수 있습니다.

IoT Hub의 상태를 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Service Health**  >  **리소스 상태** 로 이동 합니다.

3. 드롭다운 상자에서 구독을 선택한 다음 리소스 유형으로 **IoT Hub** 를 선택 합니다.

Azure Service Health 및 상태 데이터를 해석 하는 방법에 대 한 자세한 내용은 Azure Service Health 설명서의 [Service Health 개요](../service-health/service-health-overview.md) 를 참조 하세요.

Azure Service Health를 사용 하 여 경고를 설정 하는 방법에 대 한 자세한 내용은 Azure Service Health 설명서의 [서비스 상태 이벤트에 대 한 경고 구성](../service-health/alerts-activity-log-service-notifications-portal.md) 및 관련 항목을 참조 하세요.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Azure 상태 페이지에서 지역별 IoT Hub 서비스의 상태를 확인 합니다.

지역 전체의 IoT Hub 및 기타 서비스의 상태를 확인 하려면 [Azure 상태 페이지](https://status.azure.com/status)를 사용 하면 됩니다. Azure 상태 페이지에 대 한 자세한 내용은 Azure Service Health 설명서에서 [azure 상태 개요](../service-health/azure-status-overview.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Service Health, Azure Resource Health 및 Azure 상태 페이지에 대 한 자세한 내용은 [Azure Service Health 서비스](../service-health/overview.md) 를 참조 하세요.
* Azure IoT Hub 모니터링에 대 한 설명은 [Azure IoT Hub 모니터링](monitor-iot-hub.md) 을 참조 하세요.
