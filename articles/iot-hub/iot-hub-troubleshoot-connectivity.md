---
title: Azure IoT Hub의 연결 끊김 문제 진단 및 해결
description: Azure IoT Hub의 장치 연결에 대한 일반적인 오류를 진단하고 해결하는 방법을 알아봅니다.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 91e435c60a342768093b3bc869a78fa61df8782f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446567"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub의 연결 끊김 문제 감지지 및 해결

IoT 장치의 연결 문제는 가능한 실패 지점이 많기 때문에 문제 해결이 어려울 수 있습니다. 장치 쪽 응용 프로그램 로직, 물리적 네트워크, 프로토콜, 하드웨어 및 Azure IoT Hub 모두 문제를 일으킬 수 있습니다. 이 문서에서는 클라우드 쪽(장치 쪽에 대한 반대 개념)에서 장치 연결 문제를 감지하고 해결하는 방법에 대한 권장 사항을 제공합니다.

## <a name="get-alerts-and-error-logs"></a>경고 및 오류 로그 얻기

장치 연결이 끊어지면 Azure Monitor를 사용하여 경고를 받고 로그를 작성합니다.

### <a name="turn-on-diagnostic-logs"></a>진단 로그 켜기 

장치 연결 이벤트 및 오류를 기록하려면 IoT Hub 진단을 켭니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. IoT Hub로 이동
1. **진단 설정**을 선택합니다.
1. 다음으로, **진단 켜기**를 선택합니다.
1. **연결** 로그가 수집되도록 설정했는지 확인합니다. 
1. 쉽게 분석하려면 **Log Analytics에 보내기**([가격 책정 참고](https://azure.microsoft.com/pricing/details/log-analytics/))를 켜야 합니다. 이 문서 뒷부분의 예에는 Log Analytics가 사용됩니다.

   ![권장 설정][2]

자세히 알아보려면 [Azure IoT Hub 상태 모니터링 및 신속한 문제 진단](iot-hub-monitor-resource-health.md)을 참조하세요.

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>연결된 장치 수 메트릭에 대한 경고 설정

장치 연결이 끊어질 때 경고를 받으려면 *연결된 장치* 메트릭에 경고를 구성합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. IoT Hub로 이동합니다.
1. **경고(클래식)** 를 선택합니다.
1. **메트릭 경고 추가(클래식)** 를 클릭합니다.
1. 양식을 채우고 **확인**을 선택합니다. 

   ![권장되는 메트릭 경고][3]

자세한 내용은은 [Microsoft Azure의 클래식 경고란?](../monitoring-and-diagnostics/monitoring-overview-alerts.md)을 참조하세요.

## <a name="resolve-connectivity-errors"></a>연결 오류 해결

연결된 장치에 대한 진단 로그 및 경고가 켜져 있는 경우 문제가 생기면 경고가 표시됩니다. 이 섹션에서는 경고를 받게 되면 일반적인 문제를 해결하는 방법을 설명합니다. 아래 단계에서는 진단 로그에 대해 Log Analytics를 설정했다고 가정합니다. 

1. Azure Portal에서 **Log Analytics**의 작업 영역으로 이동합니다.
1. **로그 검색**을 클릭합니다.
1. IoT Hub에 대한 연결 오류 로그를 격리하려면 **실행**을 누릅니다.

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. 결과가 있는 경우 오류에 대한 자세한 정보를 얻으려면 `OperationName`, `ResultType`(오류 코드) 및 `ResultDescription`(오류 메시지)을 찾아봅니다.

   ![오류 로그의 예][4]

1. 이 표를 사용하여 일반적인 오류를 이해하고 해결합니다.

    | 오류 | 근본 원인 | 해결 방법 |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | 장치가 연결을 종료했지만 IoT Hub가 이유를 모릅니다. 일반적인 원인에는 MQTT/AMQP 시간 초과 및 인터넷 연결 손실이 있습니다. | [연결 테스트](tutorial-connectivity.md)를 통해 장치가 IoT Hub에 연결할 수 있는지 확인합니다. 연결이 정상인데 장치 연결이 간헐적으로 끊어지면 사용자가 선택한 프로토콜 (MQTT/AMPQ)에 적절한 장치 연결 유지 논리를 구현해야 합니다. |
    | 401003 IoTHubUnauthorized | IoT Hub가 연결을 인증할 수 없습니다. | SAS 또는 사용하는 기타 보안 토큰이 만료되지 않았는지 확인합니다. [Azure IoT SDK](iot-hub-devguide-sdks.md)는 특별한 구성없이 토큰을 자동으로 생성합니다. |
    | 409002 LinkCreationConflict | 동일한 장치에 대해 둘 이상의 연결이 있습니다. 장치에 대한 새로운 연결 요청이 들어오면 IoT Hub는 이 오류와 함께 이전 연결을 닫습니다. | 가장 일반적인 경우에 장치는 연결이 끊어진 것을 감지하고 연결을 다시 설정하려고 하지만 IoT Hub에서는 아직 연결이 끊어진 것으로 간주하지 않으므로 이전 연결을 닫고 이 오류를 기록합니다. 이 오류는 대부분 다른 일시적인 문제의 부작용으로 나타나므로 추가로 문제를 해결하려면 로그에서 다른 오류를 찾습니다. 그렇지 않으면, 연결이 끊어질 때만 새 연결 요청을 발급해야 합니다. |
    | 500001 ServerError | IoT Hub에 서버 쪽 문제가 발생했습니다. 대부분의 경우 문제는 일시적입니다. IoT Hub 팀에서는 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)를 유지하기 위해 열심히 노력하고 있지만, IoT Hub 노드의 작은 하위 집합에 이따금 일시적인 오류가 발생할 수 있습니다. 장치에서 문제가 있는 노드에 연결을 시도하면, 이 오류가 나타납니다. | 일시적인 오류를 줄이려면 장치에서 재시도를 발급합니다. [재시도를 자동으로 관리하려면](iot-hub-reliability-features-in-sdks.md#connection-and-retry) 최신 버전의 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 사용해야 합니다.<br><br>일시적인 오류 처리 및 재시도에 대한 모범 사례는 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults.md)를 참조하세요.  <br><br>재시도 후에도 문제가 지속되면[Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) 및 [Azure 상태](https://azure.microsoft.com/status/history/)를 확인하여 IoT Hub에 알려진 문제가 있는지 확인합니다. 알려진 문제가 없이 문제가 계속되면 추가 조사를 위해 [지원팀에 문의](https://azure.microsoft.com/support/options/)하세요. |
    | 500008 GenericTimeout | IoT Hub가 시간이 초과되기 전에 연결 요청을 완료할 수 없습니다. 500001 ServerError 처럼 이 오류는 일시적일 수 있습니다. | 500001 ServerError의 문제 해결 단계에 따라서 근본 원인을 찾고 이 오류를 해결합니다.|

## <a name="other-steps-to-try"></a>시도할만한 다른 단계

위의 단계가 도움이 되지 않는 경우 시도해 볼만한 몇 가지 사항은 다음과 같습니다.

* 물리적으로 또는 원격으로(예: SSH) 문제가 있는 장치에 액세스할 수 있으면 [장치 쪽 문제 해결 가이드](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)에 따라 문제 해결을 계속합니다.
* Azure Portal > IoT Hub > IoT 장치에서 해당 장치가 **사용 가능** 상태인지 확인합니다.
* [Azure IoT Hub 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 또는 [Azure 고객 지원팀](https://azure.microsoft.com/support/options/)의 도움을 받습니다.

모두를 위해 문서를 개선하는 데 도움이 될 수 있도록, 이 가이드가 도움이 되지 않는 경우 아래에 의견을 남겨 주세요.

## <a name="next-steps"></a>다음 단계

* 일시적인 문제 해결에 대한 자세한 내용은 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults.md)를 참조하세요.
* Azure IoT SDK 및 재시도 관리에 대한 자세한 내용은 [Azure IoT Hub 장치 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징을 관리하는 방법](iot-hub-reliability-features-in-sdks.md#connection-and-retry)을 참조하세요.

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
