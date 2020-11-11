---
title: Azure IoT Hub와의 연결 끊김 모니터링 및 문제 해결
description: Azure IoT Hub의 디바이스 연결과 관련된 일반적인 오류를 모니터링하고 해결하는 방법을 알아봅니다.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: b179bb3566cc19b8033a56348db34cd1f05cee10
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506399"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub와의 연결 끊김 모니터링, 진단 및 문제 해결

IoT 디바이스의 연결 문제는 가능한 실패 지점이 많기 때문에 문제 해결이 어려울 수 있습니다. 애플리케이션 로직, 물리적 네트워크, 프로토콜, 하드웨어, IoT Hub 및 기타 클라우드 서비스는 모두 문제를 일으킬 수 있습니다. 문제의 원인을 감지하고 정확하게 파악하는 기능은 매우 중요합니다. 그러나 대규모 IoT 솔루션에는 수천 개의 디바이스가 있을 수 있으므로 개별 디바이스를 수동으로 확인하는 것은 실용적이지 않습니다. IoT Hub는 다음과 같은 두 가지 Azure 서비스와 통합 됩니다.

* **Azure Monitor** 이러한 문제를 대규모로 검색, 진단 및 해결 하는 데 도움이 되도록 Azure Monitor를 통해 제공 IoT Hub 모니터링 기능을 사용 합니다. 여기에는 연결이 끊어질 때 알림과 작업을 트리거할 경고를 설정 하 고, 연결을 끊은 상태를 검색 하는 데 사용할 수 있는 로그를 구성 하는 작업이 포함 됩니다.

* **Azure Event Grid** 중요 한 인프라 및 장치별 연결 끊기의 경우 Azure Event Grid를 사용 하 여 장치 연결을 구독 하 고 IoT Hub에서 내보낸 이벤트의 연결을 끊습니다.

두 경우 모두 이러한 기능이 관찰할 수 있는 IoT Hub으로 제한 되므로 장치 및 기타 Azure 서비스에 대 한 모범 사례 모니터링을 따르는 것이 좋습니다.

## <a name="event-grid-vs-azure-monitor"></a>Event Grid와 Azure Monitor 비교

Event Grid은 중요 한 장치 및 인프라에 대 한 장치 연결을 추적 하는 데 사용할 수 있는 낮은 대기 시간, 장치 단위 모니터링 솔루션을 제공 합니다. Azure Monitor는 IoT Hub에 연결 된 장치 수를 모니터링 하 고 해당 수가 고정 임계값 아래로 떨어지면 경고를 트리거하는 데 사용할 수 있는 메트릭 *연결 된 장치* 를 제공 합니다.

특정 시나리오에 대해 Event Grid 또는 Azure Monitor를 사용할지 결정할 때 다음 사항을 고려 하십시오.

* 경고 대기 시간: IoT Hub 연결 이벤트는 Event Grid를 통해 훨씬 더 신속 하 게 전달 됩니다. 이렇게 하면 빠른 알림이 바람직한 시나리오에 대 한 선택이 더 Event Grid.

* 장치 단위 알림: Event Grid는 개별 장치에 대 한 연결 및 연결 끊기를 추적 하는 기능을 제공 합니다. 이렇게 하면 중요 한 장치에 대 한 연결을 모니터링 해야 하는 시나리오에서 Event Grid 수 있습니다.

* 경량 설정: Azure Monitor 메트릭 경고는 전자 메일, SMS, 음성 및 기타 알림을 통해 알림을 배달 하기 위해 다른 서비스와 통합 하지 않아도 되는 간단한 설정 환경을 제공 합니다.  Event Grid를 사용 하 여 다른 Azure 서비스와 통합 하 여 알림을 제공 해야 합니다. 두 서비스 모두 다른 서비스와 통합 하 여 더 복잡 한 작업을 트리거할 수 있습니다.

프로덕션 환경에서는 짧은 대기 시간, 장치 단위 기능으로 인해 Event Grid를 사용 하 여 연결을 모니터링 하는 것이 좋습니다. 물론, 선택은 독점적이 지 않습니다. Azure Monitor 메트릭 경고와 Event Grid를 모두 사용할 수 있습니다. 추적을 해제 하는 데 대 한 선택에 관계 없이 Azure Monitor 리소스 로그를 사용 하 여 예기치 않은 장치 연결이 끊기는 이유를 해결할 수 있습니다. 다음 섹션에서는 이러한 각 옵션에 대해 자세히 설명 합니다.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: 장치 연결 모니터링 및 이벤트 연결 끊기

프로덕션에서 장치 연결 및 연결 끊기 이벤트를 모니터링 하려면 Event Grid에서 [ **DeviceConnected** 및 **devicedisconnected** 이벤트](iot-hub-event-grid.md#event-types) 를 구독 하 여 경고를 트리거하고 장치 연결 상태를 모니터링 하는 것이 좋습니다. Event Grid는 Azure Monitor 보다 훨씬 낮은 이벤트 대기 시간을 제공 하 고, 연결 된 장치의 총 수가 아니라 장치별로 모니터링할 수 있습니다. 이러한 요인은 중요 한 장치 및 인프라를 모니터링 하는 데 선호 되는 방법 Event Grid 합니다.

Event Grid를 사용 하 여 장치 연결 끊기에 대 한 경고를 모니터링 하거나 트리거하는 경우 Azure IoT Sdk를 사용 하는 장치의 SAS 토큰 갱신으로 인해 정기적으로 연결을 끊는 방법으로 빌드를 수행 해야 합니다. 자세히 알아보려면 [Azure IoT sdk를 사용 하 여 Mqtt 장치 연결 끊기 동작](#mqtt-device-disconnect-behavior-with-azure-iot-sdks)을 참조 하세요.

Event Grid를 사용 하 여 장치 연결 이벤트를 모니터링 하는 방법에 대해 자세히 알아보려면 다음 항목을 살펴보세요.

* IoT Hub에서 Event Grid를 사용 하는 방법에 대 한 개요는 Event Grid를 사용 하 여 [IoT Hub 이벤트에 응답을](iot-hub-event-grid.md)참조 하세요. [장치 연결 및 장치 연결이 끊어진 이벤트의 제한 사항](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) 섹션에 특히 주의 하세요.

* 장치 연결 이벤트를 정렬 하는 방법에 대 한 자습서는 [Azure Cosmos DB를 사용 하 여 Azure IoT Hub에서 장치 연결 이벤트 순서](iot-hub-how-to-order-connection-state-events.md)지정을 참조 하세요.

* 전자 메일 알림을 보내는 방법에 대 한 자습서는 Event Grid 설명서의 [Logic Apps Event Grid를 사용 하 여 Azure IoT Hub 이벤트에 대 한 전자 메일 알림 보내기](/azure/event-grid/publish-iot-hub-events-to-logic-apps) 를 참조 하세요.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: 연결 이벤트를 로그로 라우팅

IoT hub는 몇 가지 작업 범주에 대 한 리소스 로그를 지속적으로 내보냅니다. 그러나이 로그 데이터를 수집 하려면 분석 하거나 보관할 수 있는 대상으로 라우팅하는 진단 설정을 만들어야 합니다. 이러한 대상 중 하나는 Log Analytics 작업 영역을 통해 Azure Monitor 로그 ([가격 책정 참조](https://azure.microsoft.com/pricing/details/log-analytics/))로, Kusto 쿼리를 사용 하 여 데이터를 분석할 수 있습니다.

IoT Hub [리소스 로그 연결 범주](monitor-iot-hub-reference.md#connections) 는 장치 연결과 관련 된 작업 및 오류를 내보냅니다. 다음 스크린샷에서는 이러한 로그를 Log Analytics 작업 영역으로 라우팅하는 진단 설정을 보여 줍니다.

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Log Analytics 작업 영역에 연결 로그를 보내기 위한 권장 설정입니다.":::

IoT hub를 만든 후에는 가능한 한 빨리 진단 설정을 만드는 것이 좋습니다. IoT Hub 항상 리소스 로그를 내보내는 것은 아니지만 대상으로 라우팅할 때까지 Azure Monitor에서 수집 되지 않기 때문입니다.

로그를 대상으로 라우팅하는 방법에 대 한 자세한 내용은 [수집 및 라우팅](monitor-iot-hub.md#collection-and-routing)을 참조 하세요. 진단 설정을 만드는 방법에 대 한 자세한 지침은 [메트릭 및 로그 사용 자습서](tutorial-use-metrics-and-diags.md)를 참조 하세요.

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor: 규모에 따라 장치 연결 끊기에 대 한 메트릭 경고 설정

IoT Hub에서 내보낸 플랫폼 메트릭에 따라 경고를 설정할 수 있습니다. 메트릭 경고를 사용 하 여 관심 있는 조건이 발생 했음을 개인에 게 알리고 해당 조건에 자동으로 응답할 수 있는 작업을 트리거할 수 있습니다.

[*연결 된 장치 (미리 보기)*](monitor-iot-hub-reference.md#device-metrics) 메트릭은 IoT Hub에 연결 된 장치 수를 알려 줍니다. 이 메트릭이 임계값 아래로 떨어지면 트리거할 경고를 만들 수 있습니다.

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="연결 된 장치 메트릭에 대 한 경고 논리 설정입니다.":::

메트릭 경고 규칙을 사용 하 여 장치 연결 끊기에 대 한 모니터링을 확장할 수 있습니다. 즉, 많은 수의 장치가 예기치 않게 연결 해제 된 경우입니다. 이러한 상황이 감지 되 면 문제 해결에 도움이 되는 로그를 확인할 수 있습니다. 장치 단위를 모니터링 하 고 중요 한 장치에 대 한 연결을 해제 하려면 그러나 Event Grid를 사용 해야 합니다. 또한 Event Grid는 Azure 메트릭 보다 실시간 환경을 제공 합니다.

IoT Hub 경고에 대해 자세히 알아보려면 [모니터 IoT Hub의 경고](monitor-iot-hub.md#alerts)를 참조 하세요. IoT Hub에서 경고를 만드는 연습에 대해서는 [메트릭 및 로그 사용 자습서](tutorial-use-metrics-and-diags.md)를 참조 하세요. 경고에 대 한 자세한 개요는 Azure Monitor 설명서의 [Microsoft Azure 경고 개요](../azure-monitor/platform/alerts-overview.md) 를 참조 하세요.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: 로그를 사용 하 여 연결 오류 해결

장치 연결이 끊어지는 것을 감지 하면 Azure Monitor 메트릭 경고를 사용 하거나 Event Grid를 사용 하는 경우에도 로그를 사용 하 여 이유를 쉽게 해결할 수 있습니다. 이 섹션에서는 Azure Monitor 로그에서 일반적인 문제를 찾는 방법을 설명 합니다. 아래 단계에서는 IoT Hub 연결 로그를 Log Analytics 작업 영역으로 보내는 [진단 설정을](#azure-monitor-route-connection-events-to-logs) 이미 만들었다고 가정 합니다.

IoT Hub 리소스 로그를 Azure Monitor 로그로 라우팅하는 진단 설정을 만든 후에는 다음 단계에 따라 Azure Portal에서 로그를 확인 합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT hub로 이동 합니다.

1. IoT hub의 왼쪽 창에서 **모니터링** 아래에서 **로그** 를 선택 합니다.

1. IoT Hub에 대 한 연결 오류 로그를 격리 하려면 쿼리 편집기에서 다음 쿼리를 입력 한 다음 **실행** 을 선택 합니다.

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 결과가 있는 경우 오류에 대한 자세한 정보를 얻으려면 `OperationName`, `ResultType`(오류 코드) 및 `ResultDescription`(오류 메시지)을 검색합니다.

   ![오류 로그의 예](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

오류가 표시 되 면 문제 해결 가이드에 따라 가장 일반적인 오류에 대 한 도움말을 확인 합니다.

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Azure IoT Sdk를 사용 하 여 MQTT 장치 연결 끊기 동작

Azure IoT 장치 Sdk IoT Hub에서 연결을 끊은 다음 MQTT 및 Websocket을 통한 MQTT 프로토콜을 통해 SAS 토큰을 갱신할 때 다시 연결 합니다. 로그에서 정보 장치 연결 끊기 및 연결 이벤트와 함께 오류 이벤트가 함께 표시 됩니다.

기본적으로 토큰 수명은 모든 Sdk에 대해 60 분입니다. 그러나 일부 Sdk의 개발자가 변경할 수 있습니다. 다음 표에는 각 Sdk의 토큰 수명, 토큰 갱신 및 토큰 갱신 동작이 요약 되어 있습니다.

| SDK) | 토큰 수명 | 토큰 갱신 | 갱신 동작 |
|-----|----------|---------------------|---------|
| .NET | 60 분, 구성 가능 | 85%의 수명, 구성 가능 | SDK는 토큰 수명에서 10 분의 유예 기간에 연결 하 고 연결을 끊습니다. 로그에 생성 된 정보 이벤트 및 오류 |
| Java | 60 분, 구성 가능 | 85%의 수명, 구성할 수 없음 | SDK는 토큰 수명에서 10 분의 유예 기간에 연결 하 고 연결을 끊습니다. 로그에 생성 된 정보 이벤트 및 오류 |
| Node.js | 60 분, 구성 가능 | 속하는 | SDK는 토큰 갱신 시 연결 하 고 연결을 끊습니다. 로그에는 정보 이벤트만 생성 됩니다. |
| Python | 60 분, 구성할 수 없음 | -- | SDK는 토큰 수명에서 연결 하 고 연결을 끊습니다. |

다음 스크린샷에는 다양 한 Sdk에 대 한 Azure Monitor 로그의 토큰 갱신 동작이 나와 있습니다. 토큰 수명 및 갱신 임계값은 설명 된 대로 기본값에서 변경 되었습니다.

* 1200 초 (20 분) 토큰 수명 및 갱신을 설정 하는 .NET 장치 SDK는 90%의 수명 동안 발생 합니다. 연결이 30 분 마다 발생 합니다.

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text=".NET SDK를 사용 하 여 Azure Monitor 로그의 MQTT에서 토큰 갱신에 대 한 오류 동작입니다.":::

* 300 초 (5 분) 토큰 수명을 포함 하는 Java SDK 및 수명 갱신의 기본값 85% 연결이 15 분 마다 발생 합니다.

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Java SDK를 사용 하 여 Azure Monitor 로그의 MQTT에서 토큰 갱신에 대 한 오류 동작입니다.":::

* 300 초 (5 분) 토큰 수명 및 토큰 갱신을 설정 하는 노드 SDK가 3 분에 발생 합니다. 토큰 갱신에 대 한 연결이 끊어집니다. 또한 오류는 없으며 정보 연결/연결 끊기 이벤트만 내보내집니다.

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="노드 SDK를 사용 하 여 Azure Monitor 로그의 MQTT에서 토큰 갱신에 대 한 오류 동작입니다.":::

다음 쿼리는 결과를 수집 하는 데 사용 되었습니다. 이 쿼리는 속성 모음에서 SDK 이름 및 버전을 추출 합니다. 자세히 알아보려면 [IoT Hub 로그의 SDK 버전](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)을 참조 하세요.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

IoT 솔루션 개발자 또는 운영자는 로그에서 연결/연결 끊기 이벤트와 관련 오류를 해석 하기 위해이 동작을 인식 해야 합니다. 장치에 대 한 토큰 수명 또는 갱신 동작을 변경 하려는 경우 장치가 장치 쌍 설정을 구현 하는지 또는 장치 방법을 구현 하는지 확인 합니다.

Event Hub를 사용 하 여 장치 연결을 모니터링 하는 경우 SAS 토큰 갱신으로 인 한 정기적인 연결 해제를 필터링 하는 방법으로 빌드해야 합니다. 예를 들어, 연결 끊기 이벤트가 특정 시간 범위 내의 connect 이벤트 뒤에 오는 한, 연결 끊기에 기반 하 여 동작을 트리거하지 않을 수 있습니다.

> [!NOTE]
> IoT Hub는 디바이스 당 하나의 활성 MQTT 연결만을 지원합니다. 동일한 디바이스 ID를 대신하는 모든 새 MQTT 연결로 인해 IoT Hub가 기존 연결을 삭제하게 됩니다.
>
> 400027 ConnectionForcefullyClosedOnNewConnection IoT Hub 로그에 기록 됩니다.

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