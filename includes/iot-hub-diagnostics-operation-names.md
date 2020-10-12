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
ms.openlocfilehash: 362c13771e7382ead1ba5aebd99a69fd86cd718c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793274"
---
<!-- operation names for the diag logs for IoT Hub -->

|작업 이름|Level|설명|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|정보|메시지를 제공 조건으로 평가할 수 없습니다. 예를 들어 경로 쿼리 조건의 속성이 메시지에 없으면입니다. [쿼리 구문 라우팅](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)에 대해 자세히 알아보세요.|
|RouteEvaluationError|Error|메시지 형식 문제로 인해 메시지를 평가 하는 동안 오류가 발생 했습니다. 예를 들어 콘텐츠 인코딩이 지정 되지 않았거나 메시지에서 콘텐츠 형식이 유효 하지 않은 경우이 오류가 기록 됩니다. 이러한 [속성은 시스템 속성](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties)에서 설정 해야 합니다.|
|DroppedMessage|Error|메시지가 삭제 되었으며 라우팅되지 않습니다. 이는 메시지가 라우팅 쿼리와 일치 하지 않거나 끝점이 중단 되었고 몇 번의 재시도 후 메시지를 배달할 수 없는 등의 이유로 인해 발생할 수 있습니다. 끝점 [상태 가져오기](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)REST API를 사용 하 여 끝점에 대 한 자세한 정보를 가져오는 것이 좋습니다.|
|EndpointUnhealthy|Error|끝점이 IoT Hub 메시지를 수락 하지 않았으므로 IoT Hub 메시지를 다시 전송 하려고 합니다. REST API [가져오기 끝점 상태](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 통해 마지막으로 알려진 오류를 확인 하는 것이 좋습니다.|
|EndpointDead|Error|끝점이 한 시간 넘게 IoT Hub의 메시지를 수락 하지 않았습니다. REST API [가져오기 끝점 상태](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 통해 마지막으로 알려진 오류를 확인 하는 것이 좋습니다.|
|EndpointHealthy|정보|끝점이 정상 상태 이며 IoT Hub에서 메시지를 수신 합니다. 이 메시지는 지속적으로 기록 되지 않지만 끝점이 다시 정상 상태가 되는 경우에만 기록 됩니다. 이 메시지는 IoT Hub 끝점에 메시지를 보낼 수 없음을 의미 하지만 끝점이 이제 정상입니다.|
|OrphanedMessage|정보|메시지가 경로와 일치 하지 않습니다.|
|InvalidMessage|Error|끝점과의 비 호환성 때문에 잘못 된 메시지입니다. 끝점의 구성을 확인 하는 것이 좋습니다.|


작업 *UndefinedRouteEvaluation*, *RouteEvaluationError* 및 *OrphanedMessage* 는 IoT Hub 당 1 분 이상 제한 되어 기록 됩니다.