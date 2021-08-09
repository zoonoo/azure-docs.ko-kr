---
title: Azure IoT 메시지 라우팅 문제 해결
description: Azure IoT Hub 메시지 라우팅에 대한 문제 해결을 수행하는 방법
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: daa0b1dd47884b104cc353a7483d71ff7eded2c7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287863"
---
# <a name="troubleshooting-message-routing"></a>메시지 라우팅 문제 해결

이 문서에서는 IoT Hub [메시지 라우팅](iot-hub-devguide-messages-d2c.md)에 대한 일반적인 문제를 모니터링하는 방법과 문제 해결 지침을 제공합니다.

## <a name="monitoring-message-routing"></a>메시지 라우팅 모니터링

전송된 메시지의 개요를 파악할 수 있도록 [메시지 라우팅 및 엔드포인트와 관련된 IoT Hub 메트릭](monitor-iot-hub-reference.md#routing-metrics)을 모니터링하는 것이 좋습니다. 또한 진단 설정을 만들어 [IoT Hub 리소스 로그 내 **경로**](monitor-iot-hub-reference.md#routes)에 대한 작업을 Azure Monitor 로그, 이벤트 허브 또는 Azure Storage로 전송하여 사용자 지정 처리할 수 있습니다. 메트릭, 리소스 로그 및 진단 설정에 대해 자세히 알아보려면 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요. 자습서에서는 [IoT Hub를 사용하여 메트릭 및 리소스 로그 설정 및 사용](tutorial-use-metrics-and-diags.md)을 참조하세요.

또한 어느 경로의 쿼리이든 쿼리와 일치하지 않는 메시지를 유지하려면 [대체 경로](iot-hub-devguide-messages-d2c.md#fallback-route)를 사용하도록 설정하는 것이 좋습니다. 해당 메시지는 구성된 보존 기간(일) 동안 [기본 제공 엔드포인트](iot-hub-devguide-messages-read-builtin.md)에 보존될 수 있습니다.

## <a name="top-issues"></a>주요 문제

다음은 메시지 라우팅과 관련하여 가장 일반적으로 발생하는 문제입니다. 문제 해결을 시작하려면 세부 단계별로 문제를 클릭하세요.

* [내 디바이스의 메시지가 예상대로 라우팅되지 않습니다.](#messages-from-my-devices-are-not-being-routed-as-expected)
* [기본 제공 Event Hubs 엔드포인트에서 갑자기 메시지 가져오기가 중단되었습니다.](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>내 디바이스의 메시지가 예상대로 라우팅되지 않습니다.

이 문제를 해결하려면 다음을 분석합니다.

#### <a name="the-routing-metrics-for-this-endpoint"></a>이 엔드포인트에 대한 라우팅 메트릭

[라우팅과 관련된 모든 IoT Hub 메트릭](monitor-iot-hub-reference.md#routing-metrics)은 *라우팅* 으로 시작합니다. 여러 메트릭의 정보를 결합하여 문제의 근본 원인을 식별할 수 있습니다. 예를 들어 **라우팅 전달 시도** 메트릭을 사용하면 엔드포인트에 전달된 메시지의 수 또는 어느 경로의 쿼리이든 쿼리와 일치하지 않고 대체 경로가 사용되지 않았을 때 손실된 메시지의 수를 식별할 수 있습니다. **라우팅 대기 시간** 메트릭을 검사하면 메시지 전달 대기 시간이 안정적인지 아니면 증가하는지 확인할 수 있습니다. 대기 시간이 증가하는 것은 특정 엔드포인트에 문제가 있음을 나타낼 수 있으므로 [엔드포인트의 상태](#the-health-of-the-endpoint)를 확인하는 것이 좋습니다. 이러한 라우팅 메트릭에는 엔드포인트 유형, 특정 엔드포인트 이름 및 메시지가 전달되지 않는 이유와 같은 메트릭에 대한 세부 정보를 제공하는 [차원](monitor-iot-hub-reference.md#metric-dimensions)이 있습니다.

#### <a name="the-resource-logs-for-any-operational-issues"></a>운영 문제에 대한 리소스 로그

[**경로** 리소스 로그](monitor-iot-hub-reference.md#routes)를 관찰하여 라우팅 및 엔드포인트 [작업](#operation-names)에 대한 추가 정보를 얻거나 오류 및 관련 [오류 코드](#common-error-codes)를 식별하여 문제를 보다 자세히 이해할 수 있습니다. 예를 들어 로그에서 **RouteEvaluationError** 라는 작업 이름은 메시지 형식 문제로 인해 해당 경로를 평가할 수 없었음을 나타냅니다. 특정 [작업 이름](#operation-names)에 대해 제공된 팁을 사용하여 문제를 완화할 수 있습니다. 이벤트가 오류로 기록되는 경우 해당 로그는 평가에 실패한 이유에 대한 추가 정보도 제공합니다. 예를 들어 작업 이름이 **EndpointUnhealthy** 인 경우 [오류 코드](#common-error-codes) 403004는 엔드포인트에 공간이 부족했음을 나타냅니다.

#### <a name="the-health-of-the-endpoint"></a>엔드포인트의 상태

REST API [Get Endpoint Health](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 사용하여 엔드포인트의 [상태 정보](iot-hub-devguide-endpoints.md#custom-endpoints)를 가져올 수 있습니다. *Get Endpoint Health* API는 메시지가 엔드포인트에 성공적으로 전송된 마지막 시간, [마지막으로 알려진 오류](#last-known-errors-for-iot-hub-routing-endpoints), 마지막으로 알려진 오류 시간 및 이 엔드포인트에 대한 전송 시도가 있었던 마지막 시간에 대한 정보를 제공합니다. 특정 [마지막으로 알려진 오류](#last-known-errors-for-iot-hub-routing-endpoints)에 대해 제공된 완화 방법을 사용하세요.

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>기본 제공 엔드포인트에서 갑자기 메시지 가져오기가 중단되었습니다.

이 문제를 해결하려면 다음을 분석합니다.

#### <a name="was-a-new-route-created"></a>새 경로를 만들었나요?

일단 경로가 생성되면 경로가 해당 엔드포인트에 대해 생성되어 있지 않은 한, 데이터가 기본 제공 엔드포인트로 흐르는 것을 멈춥니다. 새 경로가 추가된 경우에도 메시지가 기본 제공 엔드포인트로 계속 흐르도록 하려면 *이벤트* 엔드포인트에 대한 경로를 구성합니다. 

#### <a name="was-the-fallback-route-disabled"></a>대체 경로를 비활성화 했나요?

대체 경로는 기존 경로에서 쿼리 조건을 충족하지 않는 모든 메시지를 [Event Hubs](../event-hubs/index.yml)와 호환되는 [기본 제공 Event Hubs](iot-hub-devguide-messages-read-builtin.md)(messages/events)로 전송합니다. 메시지 라우팅이 설정되어 있으면 대체 경로 기능을 사용하도록 설정할 수 있습니다. 기본 제공 엔드포인트에 대한 경로가 없고 대체(fallback) 경로를 사용할 수 있는 경우 경로의 모든 쿼리 조건을 충족하지 않는 메시지만 기본 제공 엔드포인트로 전송됩니다. 또한 기존 경로가 모두 삭제된 경우에는 대체(fallback) 경로가 기본 제공 엔드포인트에서 모든 데이터를 수신하도록 설정해야 합니다.

대체 경로는 기존 경로에서 모든 쿼리 조건을 충족하지 않는 모든 메시지를 [Event Hubs](../event-hubs/index.yml)와 호환되는 [기본 제공 Event Hubs](iot-hub-devguide-messages-read-builtin.md)(messages/events)로 전송합니다. 메시지 라우팅이 설정되어 있으면 대체 경로 기능을 사용하도록 설정할 수 있습니다. 기본 제공 엔드포인트에 대한 경로가 없고 대체(fallback) 경로가 사용하도록 설정된 경우 경로의 모든 쿼리 조건을 충족하지 않는 메시지만 기본 제공 엔드포인트로 전송됩니다. 또한 기존 경로가 모두 삭제된 경우에는 대체(fallback) 경로가 기본 제공 엔드포인트에서 모든 데이터를 수신하도록 설정되어 있어야 합니다.

Azure Portal에서 IoT Hub에 대한 메시지 라우팅 블레이드를 사용하여 대체 경로를 사용 또는 사용 안 함으로 설정할 수 있습니다. [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties)에 Azure Resource Manager를 사용하여 대체(fallback) 경로에 대해 사용자 지정 엔드포인트를 사용할 수도 있습니다.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>IoT Hub 라우팅 엔드포인트에 대해 마지막으로 알려진 오류

<a id="last-known-errors"></a>  <!-- why are we using anchors? robin -->
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-resource-logs"></a>경로 리소스 로그

다음은 [경로 리소스 로그](monitor-iot-hub-reference.md#routes)에 기록된 작업 이름 및 오류 코드입니다.

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>작업 이름

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>일반적인 오류 코드

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>다음 단계

도움이 더 필요하면 [Microsoft Q&A 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)의 Azure 전문가에게 문의하세요. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기** 를 선택합니다.
