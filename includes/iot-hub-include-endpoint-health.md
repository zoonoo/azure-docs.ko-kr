---
title: 파일 포함
description: 파일 포함
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 36c05badb3b2292a29b8227c7f03b841474c97ad
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548199"
---
REST API [Endpoint Health 가져오기](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 사용 하 여 Endpoint의 Health 상태 를 가져올 수 있습니다. 끝점이 이러한 상태 중 하나에 있을 때 대기 시간을 더 많이 사용 하는 것으로 간주 되므로, 라우팅 메시지 대기 시간과 관련 된 [IoT Hub 라우팅 메트릭을](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) 사용 하 여 오류를 식별 하 고 디버그 하는 것이 좋습니다. IoT Hub 메트릭을 사용 하는 방법에 대 한 자세한 내용은 [Monitor IoT Hub](../articles/iot-hub/monitor-iot-hub.md)를 참조 하세요.

|상태|설명|
|---|---|
|healthy|끝점이 예상 대로 메시지를 수락 합니다.|
|상태가|끝점이 메시지를 수락 하지 않으며이 끝점으로 메시지를 보내려고 시도 하는 중 IoT Hub.|
|알 수 없음|IoT Hub에서이 끝점으로 메시지를 배달 하려고 시도 하지 않았습니다.|
|성능|끝점이 예상 보다 느린 메시지를 수락 하거나 비정상 상태에서 복구 되 고 있습니다.|
|무반응|IoT Hub에서이 끝점으로 메시지를 더 이상 전달 하지 않습니다. 이 끝점으로 메시지를 보내기 위해 다시 시도 하지 못했습니다.|
