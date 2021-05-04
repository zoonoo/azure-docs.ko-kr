---
title: Azure IoT Hub 작업 모니터링을 Azure Monitor의 IoT Hub 리소스 로그로 마이그레이션 | Microsoft Docs
description: 작업 모니터링 대신 Azure Monitor를 사용하여 IoT Hub의 작업 상태를 실시간으로 모니터링하도록 Azure IoT Hub를 업데이트하는 방법을 설명합니다.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: 48b646881b12047b28490999a96326f6076af2c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591838"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>IoT Hub를 작업 모니터링에서 Azure Monitor 리소스 로그로 마이그레이션

[작업 모니터링](iot-hub-operations-monitoring.md)을 사용하여 IoT Hub의 작업 상태를 추적하는 고객은 해당 워크플로를 Azure Monitor의 기능인 [Azure Monitor 리소스 로그](../azure-monitor/essentials/platform-logs-overview.md)로 마이그레이션할 수 있습니다. 리소스 로그는 여러 Azure 서비스에 대한 리소스 수준의 진단 정보를 제공합니다.

**IoT Hub의 작업 모니터링 기능은 더 이상 사용되지 않으며** 포털에서 제거되었습니다. 이 문서에서는 워크로드를 작업 모니터링에서 Azure Monitor 리소스 로그로 이동하는 단계를 설명합니다. 사용 중단 타임라인에 대한 자세한 내용은 [Azure Monitor 및 Azure Resource Health로 Azure IoT 솔루션 모니터링](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)을 참조하세요.

## <a name="update-iot-hub"></a>IoT Hub 업데이트

Azure Portal에서 IoT Hub를 업데이트하려면 먼저 진단 설정을 만든 다음 작업 모니터링을 꺼야 합니다.  

### <a name="create-a--diagnostic-setting"></a>진단 설정 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 **모니터링** 아래에서 **진단 설정** 을 선택합니다. 그런 다음, **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="모니터링 섹션의 진단 설정을 강조 표시하는 스크린샷.":::

1. **진단 설정** 창에서 진단 설정 이름을 지정합니다.

1. **범주 세부 정보** 아래에서 모니터링하려는 작업의 범주를 선택합니다. IoT Hub에서 사용할 수 있는 작업 범주에 대한 자세한 내용은 [리소스 로그](monitor-iot-hub-reference.md#resource-logs)를 참조하세요.

1. **대상 세부 정보** 아래에서 로그를 보낼 위치를 선택합니다. 다음 대상을 조합하여 선택할 수 있습니다.

   * 스토리지 계정에 보관
   * 이벤트 허브로 스트림
   * Log Analytics 작업 영역을 통해 Azure Monitor 로그로 보내기

   다음 스크린샷은 연결 및 디바이스 원격 분석 범주의 작업을 Log Analytics 작업 영역으로 라우팅하는 진단 설정을 보여 줍니다.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="완료된 진단 설정을 보여 주는 스크린샷":::

1. **저장** 을 선택하여 설정을 저장합니다.

새 설정은 약 10분 후에 적용됩니다. 그런 다음 로그가 구성된 대상에 표시됩니다. 진단을 구성하는 방법에 대한 자세한 내용은 [Azure 리소스에서 로그 데이터 수집 및 사용](../azure-monitor/essentials/platform-logs-overview.md)을 참조하세요.

PowerShell 및 Azure CLI를 포함하여 진단 설정을 만드는 방법에 대한 자세한 내용은 Azure Monitor 설명서의 [진단 설정](../azure-monitor/essentials/diagnostic-settings.md)을 참조하세요.

### <a name="turn-off-operations-monitoring"></a>작업 모니터링 끄기

> [!NOTE]
> 2019년 3월 11일부터 IoT Hub의 Azure Portal 인터페이스에서 작업 모니터링 기능이 제거되었습니다. 아래 단계는 더 이상 적용되지 않습니다. 마이그레이션하려면 위의 Azure Monitor 진단 설정을 사용하여 올바른 범주가 대상으로 라우팅되는지 확인합니다.

워크플로에서 새 진단 설정을 테스트한 후에는 작업 모니터링 기능을 끌 수 있습니다. 

1. IoT Hub 메뉴에서 **작업 모니터링** 을 선택합니다.

2. 각 모니터링 범주에서 **없음** 을 선택합니다.

3. 작업 모니터링 변경 내용을 저장합니다.

## <a name="update-applications-that-use-operations-monitoring"></a>작업 모니터링을 사용하는 애플리케이션 업데이트

작업 모니터링의 스키마와 리소스 로그의 스키마는 약간 다릅니다. 오늘은 작업 모니터링에서 사용하는 애플리케이션을 업데이트하여 리소스 로그에서 사용하는 스키마로 매핑하게 만드는 것이 중요합니다.

또한 IoT Hub 리소스 로그는 추적을 위한 5가지 새 범주를 제공합니다. 기존 스키마에 대한 애플리케이션을 업데이트한 후에는 새 범주를 추가합니다.

* 클라우드-디바이스 쌍 작업
* 디바이스-클라우드 쌍 작업
* 쌍 쿼리
* 작업 연산
* 직접 메서드

특정 스키마 구조는 [리소스 로그](monitor-iot-hub-reference.md#resource-logs)를 참조하세요.

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>디바이스 연결 모니터링 및 낮은 대기 시간을 사용하여 이벤트 연결 해제

프로덕션 환경에서 디바이스 연결 및 연결 끊김 이벤트를 모니터링하려면 Event Grid에서 [**디바이스 연결 끊김** 이벤트](iot-hub-event-grid.md#event-types)를 구독하여 경고를 받고 디바이스 연결 상태를 모니터링하는 것이 좋습니다. 이 [자습서](iot-hub-how-to-order-connection-state-events.md)를 사용하여 IoT 솔루션에서 IoT Hub의 디바이스 연결된 이벤트 및 디바이스 연결 해제된 이벤트를 통합하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

[IoT Hub 모니터링](monitor-iot-hub.md)