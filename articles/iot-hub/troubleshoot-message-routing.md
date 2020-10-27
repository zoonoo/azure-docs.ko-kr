---
title: Azure IoT 메시지 라우팅 문제 해결
description: Azure IoT 메시지 라우팅에 대 한 문제 해결 방법을 수행 하는 방법
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 29127a9dff42c0f733e3721d1ea5fea7350e774e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547363"
---
# <a name="troubleshooting-message-routing"></a>메시지 라우팅 문제 해결

이 문서에서는 IoT Hub [메시지 라우팅](iot-hub-devguide-messages-d2c.md)에 대 한 일반적인 문제 및 해결 방법에 대 한 모니터링 및 문제 해결 지침을 제공 합니다.

## <a name="monitoring-message-routing"></a>메시지 라우팅 모니터링

[메시지 라우팅 및 끝점과 관련 된 IoT Hub 메트릭을](monitor-iot-hub-reference.md#routing-metrics) 모니터링 하 여 전송 되는 메시지에 대 한 개요를 제공 하는 것이 좋습니다. [IoT Hub 리소스 로그의 **경로**](monitor-iot-hub-reference.md#routes) 에 대 한 작업을 Azure Monitor 로그, Event Hubs 또는 Azure Storage 사용자 지정 처리에 보내는 진단 설정을 만들 수도 있습니다. 메트릭, 리소스 로그 및 진단 설정을 사용 하는 방법에 대해 자세히 알아보려면 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조 하세요. 자습서는 [IoT hub를 사용 하 여 메트릭 및 리소스 로그 설정 및 사용](tutorial-use-metrics-and-diags.md)을 참조 하세요.

또한 경로에 대 한 쿼리와 일치 하지 않는 메시지를 유지 하려면 [대체 (fallback) 경로](iot-hub-devguide-messages-d2c.md#fallback-route) 를 사용 하도록 설정 하는 것이 좋습니다. 구성 된 보존 기간 (일) 동안 [기본 제공 끝점](iot-hub-devguide-messages-read-builtin.md) 에 보존 될 수 있습니다.

## <a name="top-issues"></a>주요 문제

메시지 라우팅과 관련 하 여 가장 일반적으로 발생 하는 문제는 다음과 같습니다. 문제 해결을 시작 하려면 자세한 단계에 대 한 문제를 클릭 합니다.

* [내 장치의 메시지가 예상 대로 라우팅되지 않습니다.](#messages-from-my-devices-are-not-being-routed-as-expected)
* [기본 제공 Event Hubs 끝점에서 갑자기 메시지 가져오기를 중지 했습니다.](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>내 장치의 메시지가 예상 대로 라우팅되지 않습니다.

이 문제를 해결 하려면 다음을 분석 합니다.

#### <a name="the-routing-metrics-for-this-endpoint"></a>이 끝점에 대 한 라우팅 메트릭입니다.

[라우팅과 관련 된 모든 IoT Hub 메트릭은](monitor-iot-hub-reference.md#routing-metrics) *라우팅을* 접두사로 사용 됩니다. 여러 메트릭의 정보를 결합하여 문제의 근본 원인을 식별할 수 있습니다. 예를 들어 메트릭 **라우팅 전달 시도** 를 사용 하 여 끝점에 배달 된 메시지 수를 식별 하거나 경로 및 대체 (fallback) 경로에 대 한 쿼리와 일치 하지 않을 때 삭제 된 메시지 수를 식별 합니다. **라우팅 대기 시간** 메트릭을 확인 하 여 메시지 배달에 대 한 대기 시간이 일정 한지 또는 증가 하는지 확인 합니다. 대기 시간이 증가 하면 특정 끝점에 문제가 있음을 나타낼 수 있으며 [끝점의 상태를](#the-health-of-the-endpoint)확인 하는 것이 좋습니다. 이러한 라우팅 메트릭에는 끝점 형식, 특정 끝점 이름 및 메시지가 전달 되지 않은 이유와 같은 메트릭에 대 한 세부 정보를 제공 하는 [차원이](monitor-iot-hub-reference.md#metric-dimensions) 있습니다.

#### <a name="the-resource-logs-for-any-operational-issues"></a>운영 문제에 대 한 리소스 로그

[ **경로** 리소스 로그](monitor-iot-hub-reference.md#routes) 를 확인 하 여 라우팅 및 끝점 [작업](#operation-names) 에 대 한 자세한 정보를 확인 하거나 오류 및 관련 [오류 코드](#common-error-codes) 를 확인 하 여 문제를 자세히 파악 합니다. 예를 들어 로그의 작업 이름 **RouteEvaluationError** 은 메시지 형식 문제로 인해 경로를 평가할 수 없음을 나타냅니다. 특정 [작업 이름](#operation-names) 에 제공 된 팁을 사용 하 여 문제를 완화 합니다. 이벤트가 오류로 기록 될 때 평가에 실패 한 이유에 대 한 추가 정보를 제공 합니다. 예를 들어 작업 이름이 **Endpointunhealthy** 인 경우 [오류 코드](#common-error-codes) 403004는 끝점의 공간이 부족 함을 나타냅니다.

#### <a name="the-health-of-the-endpoint"></a>끝점의 상태입니다.

[끝점 상태 가져오기](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) REST API를 사용 하 여 [끝점 상태를 가져옵니다.](iot-hub-devguide-endpoints.md#custom-endpoints) 또한 *Get Endpoint Health* API는 마지막으로 메시지가 끝점에 전송 된 시간, [마지막으로 알려진 오류](#last-known-errors-for-iot-hub-routing-endpoints), 마지막으로 알려진 오류 시간 및이 끝점에 대 한 송신 시도가 마지막으로 수행 된 시간에 대 한 정보를 제공 합니다. [마지막으로 알려진 특정 오류](#last-known-errors-for-iot-hub-routing-endpoints)에 대해 제공 된 가능한 완화 방법을 사용 합니다.

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>기본 제공 끝점에서 갑자기 메시지 가져오기를 중지 했습니다.

이 문제를 해결 하려면 다음을 분석 합니다.

#### <a name="was-a-new-route-created"></a>새 경로를 만들었습니다.

경로를 만든 후에는 해당 끝점에 대 한 경로를 만들지 않는 한 데이터를 기본 끝점으로 이동 하지 않습니다. 새 경로가 추가 된 경우 메시지가 기본 제공 끝점으로 계속 전달 되도록 하려면 *이벤트* 끝점에 대 한 경로를 구성 합니다. 

#### <a name="was-the-fallback-route-disabled"></a>대체 경로를 사용할 수 없습니까?

대체 (fallback) 경로는 [Event Hubs](../event-hubs/index.yml)와 호환 되는 [기본 제공 Event Hubs](iot-hub-devguide-messages-read-builtin.md) (메시지/이벤트)에 대 한 기존 경로에서 쿼리 조건을 충족 하지 않는 모든 메시지를 보냅니다. 메시지 라우팅이 설정되어 있으면 대체 경로 기능을 사용하도록 설정할 수 있습니다. 기본 제공 엔드포인트에 대한 경로가 없고 대체(fallback) 경로를 사용할 수 있는 경우 경로의 모든 쿼리 조건을 충족하지 않는 메시지만 기본 제공 엔드포인트로 전송됩니다. 또한 기존 경로가 모두 삭제된 경우에는 대체(fallback) 경로가 기본 제공 엔드포인트에서 모든 데이터를 수신하도록 설정해야 합니다.

Azure Portal >메시지 라우팅 블레이드에서 대체 경로를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties)에 Azure Resource Manager를 사용하여 대체(fallback) 경로에 대해 사용자 지정 엔드포인트를 사용할 수도 있습니다.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>IoT Hub 라우팅 끝점에 대 한 마지막 알려진 오류

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-resource-logs"></a>리소스 로그를 라우팅합니다.

다음은 [경로 리소스 로그](monitor-iot-hub-reference.md#routes)에 기록 된 작업 이름 및 오류 코드입니다.

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>작업 이름

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>일반적인 오류 코드

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>다음 단계

도움이 더 필요하면 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)의 Azure 전문가에게 문의하세요. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기** 를 선택합니다.