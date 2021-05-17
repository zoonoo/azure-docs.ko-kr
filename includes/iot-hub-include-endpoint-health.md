---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95563062"
---
REST API [Endpoint Health 가져오기](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 사용 하 여 Endpoint의 Health 상태 를 가져올 수 있습니다. 엔드포인트가 이러한 상태 중 하나에 있을 때 대기 시간이 더 길어질 것으로 예상되므로, 라우팅 메시지 대기 시간과 관련된 [IoT Hub 라우팅 메트릭](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics)을 사용하여 오류를 식별하고 디버깅하는 것이 좋습니다. IoT Hub 사용에 대한 자세한 내용은 [IoT Hub 모니터링](../articles/iot-hub/monitor-iot-hub.md)을 참조하세요.

|상태|설명|
|---|---|
|healthy|엔드포인트가 예상대로 메시지를 수락하고 있습니다.|
|비정상|엔드포인트가 메시지를 수락하지 않고 있으며 IoT Hub가 이 엔드포인트에 메시지를 보내려고 재시도하고 있습니다.|
|알 수 없음|IoT Hub가 이 엔드포인트에 메시지를 전달하려고 시도하지 않았습니다.|
|성능 저하됨|엔드포인트가 예상보다 느리게 메시지를 수락하거나 비정상 상태에서 복구되고 있습니다.|
|데드|IoT Hub는 이 엔드포인트에 메시지를 더 이상 전달하지 않습니다. 이 엔드포인트로 메시지를 보내려는 재시도가 실패했습니다.|