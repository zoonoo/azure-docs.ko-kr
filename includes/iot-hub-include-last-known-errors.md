---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 54f4835a904b897370cf9f075ae3c005b1114992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95558889"
---
REST API에서 [엔드포인트 상태 가져오기](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)는 엔드포인트의 상태 및 마지막으로 알려진 오류를 제공하여 엔드포인트가 정상이 아닌 이유를 식별합니다. 아래 표에는 가장 일반적인 오류가 나열되어 있습니다.

|마지막으로 알려진 오류|설명/발생 시기|가능한 완화 방법|
|-----|-----|-----|
|Transient|일시적인 오류가 발생하여 IoT Hub가 작업을 다시 시도합니다.|[경로 리소스 로그](../articles/iot-hub/monitor-iot-hub-reference.md#routes)를 관찰합니다.|
|InternalError|엔드포인트에 메시지를 전달하는 동안 오류가 발생했습니다.|이는 내부 예외이지만 [경로 리소스 로그](../articles/iot-hub/monitor-iot-hub-reference.md#routes)도 관찰합니다.|
|권한 없음|IoT Hub는 지정된 엔드포인트에 메시지를 보낼 수 있는 권한이 없습니다.|엔드포인트에 대한 연결 문자열이 최신 상태인지 확인합니다. 변경된 경우 IoT Hub에 대한 업데이트를 고려하세요. 엔드포인트에서 관리 ID를 사용하는 경우 IoT Hub 보안 주체에 대상에 대한 필수 권한이 있는지 확인합니다.|
|정체됨|IoT Hub는 엔드포인트에 메시지를 쓰는 동안 제한됩니다.|영향을 받는 엔드포인트의 제한 한도를 검토합니다. 필요에 따라 확장할 엔드포인트의 구성을 수정합니다.|
|제한 시간|작업 시간이 초과되었습니다.|작업을 다시 시도하세요.|
|찾을 수 없음|대상 리소스가 없습니다.|대상 리소스가 있는지 확인합니다.|
|컨테이너를 찾을 수 없음|스토리지 컨테이너가 없습니다.|스토리지 컨테이너가 있는지 확인합니다.|
|컨테이너 사용 안 함|스토리지 컨테이너가 비활성화되었습니다.|스토리지 컨테이너가 활성화되었는지 확인합니다.|
|MaxMessageSizeExceeded|메시지 라우팅의 메시지 크기 제한은 256Kb입니다. 라우팅되는 메시지 크기가 이 제한을 초과했습니다.|더 적은 애플리케이션 속성을 사용하거나 더 적은 메시지 강화를 사용하여 메시지 크기를 줄일 수 있는지 확인합니다.|
|PartitioningAndDuplicateDetectionNotSupported|Service Bus에 중복 검색이 활성화되어 있지 않을 수 있습니다.|Service Bus에서 중복 검색을 비활성화하거나 중복 검색 없이 엔터티를 사용하는 것을 고려하세요.|
|SessionfulEntityNotSupported|Service Bus에 세션이 활성화되어 있지 않을 수 있습니다.|Service Bus에서 세션을 비활성화하거나 세션이 없이 엔터티를 사용하는 것을 고려하세요.|
|NoMatchingSubscriptionsForMessage|Service Bus 토픽에 메시지를 쓸 수 있는 구독이 없습니다.|라우팅할 IoT Hub 메시지에 대한 구독을 만듭니다.|
|EndpointExternallyDisabled|엔드포인트가 활성 상태가 아니므로 IoT Hub에서 메시지를 보낼 수 있습니다.|엔드포인트를 활성 상태로 전환되도록 설정합니다.|
|DeviceMaximumQueueDepthExceeded|Service Bus 크기 제한에 도달했습니다.|새 메시지를 Event Hubs로 수집할 수 있도록 대상 Event Hubs에서 메시지를 제거하는 것이 좋습니다.|