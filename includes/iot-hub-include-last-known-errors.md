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
ms.openlocfilehash: d03579f704879bd8d012bb0bb326659d1f778dee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84793294"
---
REST API에서 끝점 상태 [가져오기](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 는 끝점의 상태 및 마지막으로 알려진 오류를 제공 하 여 끝점이 정상이 아닌 이유를 식별 합니다. 다음 표에는 가장 일반적인 오류가 나열 되어 있습니다.

|마지막으로 알려진 오류|설명/발생 시기|가능한 완화 방법|
|-----|-----|-----|
|Transient|일시적인 오류가 발생 하 여 IoT Hub 작업을 다시 시도 합니다.|경로 [진단 로그](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)를 관찰 합니다.|
|InternalError|끝점에 메시지를 전달 하는 동안 오류가 발생 했습니다.|이는 내부 예외 이지만 경로 [진단 로그](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)도 관찰 합니다.|
|권한 없음|IoT Hub 지정 된 끝점으로 메시지를 보낼 수 있는 권한이 없습니다.|끝점에 대 한 연결 문자열이 최신 상태 인지 확인 합니다. 변경 된 경우 IoT Hub에 대 한 업데이트를 고려 합니다. 끝점에서 관리 id를 사용 하는 경우 IoT Hub 보안 주체에 게 대상에 대 한 필수 권한이 있는지 확인 합니다.|
|정체됨|IoT Hub 끝점에 메시지를 쓰는 동안 제한 됩니다.|영향을 받는 끝점의 제한 한도를 검토 합니다. 필요에 따라 확장할 끝점의 구성을 수정 합니다.|
|제한 시간|작업 시간이 초과되었습니다.|작업을 다시 시도하세요.|
|찾을 수 없음|대상 리소스가 없습니다.|대상 리소스가 있는지 확인 하세요.|
|컨테이너를 찾을 수 없음|저장소 컨테이너가 존재 하지 않습니다.|저장소 컨테이너가 있는지 확인 하세요.|
|컨테이너 사용 안 함|저장소 컨테이너를 사용할 수 없습니다.|저장소 컨테이너를 사용 하도록 설정 했는지 확인 합니다.|
|MaxMessageSizeExceeded|메시지 라우팅의 메시지 크기 제한은 256Kb입니다. 라우팅되는 메시지 크기가이 제한을 초과 했습니다.|더 적은 수의 응용 프로그램 속성 또는 더 적은 수의 메시지 강화를 사용 하 여 메시지 크기를 줄일 수 있는지 확인 합니다.|
|PartitioningAndDuplicateDetectionNotSupported|Service bus에서 중복 검색이 사용 하도록 설정 되지 않았을 수 있습니다.|Service Bus에서 중복 검색을 사용 하지 않도록 설정 하거나 중복 검색 없이 엔터티를 사용 하십시오.|
|SessionfulEntityNotSupported|Service bus에서 세션을 사용 하도록 설정할 수 없습니다.|Service Bus에서 세션을 사용 하지 않도록 설정 하거나 세션이 없는 엔터티를 사용 하십시오.|
|NoMatchingSubscriptionsForMessage|Service bus 토픽에 메시지를 쓸 수 있는 구독이 없습니다.|라우팅할 메시지 IoT Hub에 대 한 구독을 만듭니다.|
|EndpointExternallyDisabled|끝점은 활성 상태가 아니므로 IoT Hub 메시지를 보낼 수 있습니다.|끝점이 활성 상태로 전환 되도록 설정 합니다.|
|DeviceMaximumQueueDepthExceeded|Service bus 크기 제한에 도달 했습니다.|새 메시지를 Event Hubs 수집 수 있도록 대상 Event Hubs에서 메시지를 제거 하는 것이 좋습니다.|