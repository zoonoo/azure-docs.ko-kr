---
title: Azure IoT Hub와의 연결 끊김 모니터링 및 문제 해결
description: Azure IoT Hub의 디바이스 연결과 관련된 일반적인 오류를 모니터링하고 해결하는 방법을 알아봅니다.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: b194812ef68820a0c310d0bac3b055360c5b5e4a
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538428"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub와의 연결 끊김 모니터링, 진단 및 문제 해결

IoT 디바이스의 연결 문제는 가능한 실패 지점이 많기 때문에 문제 해결이 어려울 수 있습니다. 애플리케이션 로직, 물리적 네트워크, 프로토콜, 하드웨어, IoT Hub 및 기타 클라우드 서비스는 모두 문제를 일으킬 수 있습니다. 문제의 원인을 감지하고 정확하게 파악하는 기능은 매우 중요합니다. 그러나 대규모 IoT 솔루션에는 수천 개의 디바이스가 있을 수 있으므로 개별 디바이스를 수동으로 확인하는 것은 실용적이지 않습니다. 대규모로 이러한 문제를 감지, 진단 및 해결하는 데 도움이 되도록 IoT Hub가 Azure Monitor를 통해 제공하는 모니터링 기능을 사용합니다. 이러한 기능은 IoT Hub가 관찰할 수 있는 것으로 제한되므로 디바이스 및 기타 Azure 서비스에 대한 모니터링 모범 사례를 따르는 것이 좋습니다.

## <a name="get-alerts-and-error-logs"></a>경고 및 오류 로그 얻기

디바이스 연결이 끊어지면 Azure Monitor를 사용하여 경고를 받고 로그를 작성합니다.

### <a name="turn-on-logs"></a>로그 설정

장치 연결 이벤트 및 오류를 기록 하려면 [IoT Hub 연결 리소스 로그](monitor-iot-hub-reference.md#connections)에 대 한 진단 설정을 만듭니다. 이러한 로그는 기본적으로 수집 되지 않으며, 발생 하는 경우 장치에서 연결을 끊는 문제를 해결 하는 정보가 없으므로 가능한 한 빨리이 설정을 만드는 것이 좋습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. IoT Hub로 이동합니다.

1. **진단 설정** 을 선택합니다.

1. **진단 설정 추가** 를 선택 합니다.

1. **연결** 로그를 선택 합니다.

1. 보다 쉽게 분석 **하려면 Log Analytics 보내기를** 선택 합니다 ( [가격 책정 참조](https://azure.microsoft.com/pricing/details/log-analytics/)). [연결 오류 해결](#resolve-connectivity-errors)의 예제를 참조하세요.

   ![권장 설정](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

자세히 알아보려면 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조 하세요.

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>대규모로 디바이스 연결 끊기에 대한 경고 설정

디바이스 연결이 끊어질 때 경고를 받으려면 **연결된 디바이스(미리 보기)** 메트릭에 경고를 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub로 이동합니다.

3. **경고** 를 선택합니다.

4. **새 경고 규칙** 을 선택합니다.

5. **조건 추가** 를 선택한 다음, "연결된 디바이스(미리 보기)"를 선택합니다.

6. 다음 프롬프트를 통해 임계값 및 경고를 설정합니다.

자세한 내용은 [Microsoft Azure의 경고란?](../azure-monitor/platform/alerts-overview.md)을 참조하세요.

#### <a name="detecting-individual-device-disconnects"></a>개별 디바이스 연결 끊기 검색

출고 시 오프라인으로 전환된 것을 알아야 하는 경우와 같이 *디바이스당* 연결 끊김을 감지하려면 [Event Grid를 사용하여 디바이스 연결 끊기 이벤트를 구성](iot-hub-event-grid.md)합니다.

## <a name="resolve-connectivity-errors"></a>연결 오류 해결

연결 된 장치에 대 한 로그 및 경고를 켜면 오류가 발생할 때 경고가 표시 됩니다. 이 섹션에서는 경고를 받을 때 일반적인 문제를 찾는 방법을 설명합니다. 아래 단계에서는 IoT Hub 연결 로그를 Log Analytics 작업 영역으로 보내는 진단 설정을 이미 만들었다고 가정 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. IoT Hub로 이동합니다.

1. **로그** 를 선택합니다.

1. IoT Hub에 대한 연결 오류 로그를 격리하려면 다음 쿼리를 입력한 다음, **실행** 을 선택합니다.

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 결과가 있는 경우 오류에 대한 자세한 정보를 얻으려면 `OperationName`, `ResultType`(오류 코드) 및 `ResultDescription`(오류 메시지)을 검색합니다.

   ![오류 로그의 예](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. 가장 일반적인 오류에 대해서는 문제 해결 가이드를 따르세요.

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>단계를 시도했지만 작동하지 않습니다.

이전 단계가 도움이 되지 않으면 다음을 시도해 보세요.

* 물리적으로 또는 원격으로(예: SSH) 문제가 있는 디바이스에 액세스할 수 있으면 [디바이스 쪽 문제 해결 가이드](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)에 따라 문제 해결을 계속합니다.

* Azure Portal > IoT Hub > IoT 디바이스에서 해당 디바이스가 **사용 가능** 상태인지 확인합니다.

* 디바이스가 MQTT 프로토콜을 사용하는 경우 포트 8883이 열려 있는지 확인합니다. 자세한 내용은 [IoT Hub(MQTT)에 연결](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조세요.

* [Azure IoT Hub에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 또는 [Azure 지원](https://azure.microsoft.com/support/options/)에서 도움을 받으세요.

이 가이드가 도움이 되지 않는 경우 모두를 위해 설명서 개선에 도움이 되도록 피드백 섹션에 의견을 남겨 주세요.

## <a name="next-steps"></a>다음 단계

* 일시적인 문제 해결에 대한 자세한 내용은 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조하세요.

* Azure IoT SDK 및 재시도 관리에 대한 자세한 내용은 [Azure IoT Hub 디바이스 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징을 관리하는 방법](iot-hub-reliability-features-in-sdks.md#connection-and-retry)을 참조하세요.