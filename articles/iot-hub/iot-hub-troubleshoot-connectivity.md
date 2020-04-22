---
title: Azure IoT Hub로 연결 해제 모니터링 및 문제 해결
description: Azure IoT Hub용 장치 연결을 통해 일반적인 오류를 모니터링하고 해결하는 방법을 알아봅니다.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759615"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub를 통해 연결 해제를 모니터링, 진단 및 해결

IoT 디바이스의 연결 문제는 가능한 실패 지점이 많기 때문에 문제 해결이 어려울 수 있습니다. 응용 프로그램 논리, 물리적 네트워크, 프로토콜, 하드웨어, IoT Hub 및 기타 클라우드 서비스는 모두 문제를 일으킬 수 있습니다. 문제의 원인을 감지하고 찾아낼 수 있는 능력은 매우 중요합니다. 그러나 대규모 IoT 솔루션에는 수천 개의 장치가 있을 수 있으므로 개별 장치를 수동으로 확인하는 것은 실용적이지 않습니다. 이러한 문제를 대규모로 검색, 진단 및 해결하는 데 도움이 있으므로 Azure Monitor를 통해 IoT Hub가 제공하는 모니터링 기능을 사용합니다. 이러한 기능은 IoT Hub가 관찰할 수 있는 기능으로 제한되므로 장치 및 기타 Azure 서비스에 대한 모니터링 모범 사례를 따르는 것이 좋습니다.

## <a name="get-alerts-and-error-logs"></a>경고 및 오류 로그 얻기

Azure 모니터를 사용하여 장치를 연결 해제할 때 경고를 받고 로그를 작성합니다.

### <a name="turn-on-diagnostic-logs"></a>진단 로그 설정

디바이스 연결 이벤트 및 오류를 기록하려면 IoT Hub 진단을 켭니다. 진단 로그를 사용할 수 없는 경우 장치 연결이 끊어지면 문제를 해결할 정보가 없으므로 가능한 한 빨리 이러한 로그를 켜는 것이 좋습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub로 이동합니다.

3. **진단 설정**을 선택합니다.

4. **진단 켜기**를 선택합니다.

5. **연결** 로그가 수집되도록 설정합니다.

6. 쉽게 분석하려면 **Log Analytics에 보내기**([가격 책정 참고](https://azure.microsoft.com/pricing/details/log-analytics/))를 켜야 합니다. [연결 오류 해결](#resolve-connectivity-errors)의 예제를 참조하세요.

   ![권장 설정](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

자세히 알아보려면 [Azure IoT Hub 상태 모니터링 및 신속한 문제 진단](iot-hub-monitor-resource-health.md)을 참조하세요.

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>대규모 장치 연결 끊기 에 대한 경고 설정

장치 연결이 끊어지는 경우 경고를 받으려면 **연결된 장치(미리 보기)** 메트릭에 대한 경고를 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub로 이동합니다.

3. **경고**를 선택합니다.

4. **새 경고 규칙**을 선택합니다.

5. **조건 추가를**선택한 다음 "연결된 장치(미리 보기)"를 선택합니다.

6. 다음과 같은 프롬프트에 의해 임계값 및 경고를 설정합니다.

자세한 내용은 [Microsoft Azure의 경고란 무엇입니까?](../azure-monitor/platform/alerts-overview.md)

#### <a name="detecting-individual-device-disconnects"></a>개별 장치 연결 끊기 감지

팩터리방금 오프라인으로 방금 간 것을 알아야 하는 경우와 같이 *장치별* 연결 끊김을 감지하려면 [Event Grid를 사용하여 장치 연결 끊기 이벤트를 구성합니다.](iot-hub-event-grid.md)

## <a name="resolve-connectivity-errors"></a>연결 오류 해결

연결된 디바이스에 대한 진단 로그 및 경고가 켜져 있는 경우 오류가 발생하면 경고가 표시됩니다. 이 섹션에서는 경고를 받을 때 일반적인 문제를 찾는 방법을 설명합니다. 아래 단계는 진단 로그에 대한 Azure Monitor 로그를 설정했다고 가정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. IoT Hub로 이동합니다.

1. **로그를 선택합니다.**

1. IoT Hub에 대한 연결 오류 로그를 격리하려면 다음 쿼리를 입력한 다음, **실행**을 선택합니다.

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 결과가 있는 경우 오류에 대한 자세한 정보를 얻으려면 `OperationName`, `ResultType`(오류 코드) 및 `ResultDescription`(오류 메시지)을 검색합니다.

   ![오류 로그의 예](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. 가장 일반적인 오류는 문제 해결 가이드를 따르십시오.

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>나는 단계를 시도했지만 작동하지 않았다.

이전 단계가 도움이 되지 않으면 다음을 시도해 보십시오.

* 물리적으로 또는 원격으로(예: SSH) 문제가 있는 디바이스에 액세스할 수 있으면 [디바이스 쪽 문제 해결 가이드](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)에 따라 문제 해결을 계속합니다.

* IoT 허브를 > IoT > 장치에서 장치가 Azure 포털에서 **활성화되어** 있는지 확인합니다.

* 기기에서 MQTT 프로토콜을 사용하는 경우 포트 8883이 열려 있는지 확인합니다. 자세한 내용은 [MQTT(IoT Hub)에 연결)을](iot-hub-mqtt-support.md#connecting-to-iot-hub)참조하십시오.

* [Azure IoT Hub 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 또는 [Azure 고객 지원팀](https://azure.microsoft.com/support/options/)의 도움을 받습니다.

이 가이드가 도움이 되지 않는 경우 모두를 위해 설명서 개선에 도움이 되도록 피드백 섹션에 의견을 남겨 주세요.

## <a name="next-steps"></a>다음 단계

* 일시적인 문제 해결에 대한 자세한 내용은 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조하세요.

* Azure IoT SDK 및 재시도 관리에 대한 자세한 내용은 [Azure IoT Hub 디바이스 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징을 관리하는 방법](iot-hub-reliability-features-in-sdks.md#connection-and-retry)을 참조하세요.
