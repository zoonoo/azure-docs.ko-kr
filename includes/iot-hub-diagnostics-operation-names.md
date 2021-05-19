---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7d6718fb25b3743a50c52f11c8e19d80839b485c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95562355"
---
<!-- operation names for the diag logs for IoT Hub -->

|작업 이름|Level|설명|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|정보|메시지를 지정된 조건으로 평가할 수 없습니다. 예를 들어 경로 쿼리 조건의 속성이 메시지에 없는 경우입니다. [쿼리 구문 라우팅](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md)에 대해 자세히 알아보세요.|
|RouteEvaluationError|오류|메시지 형식 문제로 인해 메시지를 평가하는 동안 오류가 발생했습니다. 예를 들어 콘텐츠 인코딩이 지정되지 않았거나 메시지에서 콘텐츠 형식이 유효하지 않은 경우 이 오류가 기록됩니다. [시스템 속성](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)에서 설정해야 합니다.|
|DroppedMessage|오류|메시지가 삭제되었으며 라우팅되지 않습니다. 이는 메시지가 라우팅 쿼리와 일치하지 않거나 엔드포인트가 중단되었고 몇 번의 재시도 후 메시지를 전달할 수 없는 등의 이유로 인해 발생할 수 있습니다. [엔포인트 상태 가져오기](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) REST API를 사용하여 엔드포인트에 대한 자세한 정보를 가져오는 것이 좋습니다.|
|EndpointUnhealthy|오류|엔드포인트가 IoT Hub의 메시지를 수락하지 않았으므로 IoT Hub 메시지를 다시 보내려고 합니다. REST API [엔드포인트 상태 가져오기](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 통해 마지막으로 알려진 오류를 확인하는 것이 좋습니다.|
|EndpointDead|오류|엔드포인트가 한 시간 넘게 IoT Hub의 메시지를 수락하지 않았습니다. REST API [엔드포인트 상태 가져오기](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 통해 마지막으로 알려진 오류를 확인하는 것이 좋습니다.|
|EndpointHealthy|정보|엔드포인트가 정상이며 IoT Hub에서 메시지를 수신합니다. 이 메시지는 지속적으로 기록되지 않지만 엔드포인트가 다시 정상 상태가 될 때만 기록됩니다. 이 메시지는 IoT Hub가 엔드포인트에 메시지를 보낼 수 없었지만 이제 엔드포인트가 정상 상태임을 의미합니다.|
|OrphanedMessage|정보|메시지가 어떤 경로와도 일치하지 않습니다.|
|InvalidMessage|오류|엔드포인트와 호환되지 않아 메시지가 잘못되었습니다. 엔드포인트의 구성을 확인하는 것이 좋습니다.|


*UndefinedRouteEvaluation*, *RouteEvaluationError* 및 *OrphanedMessage* 작업이 제한되어 IoT Hub당 1분에 한 번만 기록됩니다.