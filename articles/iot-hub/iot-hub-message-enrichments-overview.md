---
title: Azure IoT Hub 메시지 원칙이 개요
description: Azure IoT Hub 메시지에 대 한 메시지 원칙이 개요
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 13e35ab93fc37541548785c6355489eaf3a3efc2
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754557"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>장치-클라우드 IoT Hub 메시지 (미리 보기)에 대 한 메시지 강화

*강화 메시지* IoT Hub의 기능은 *스탬프* 메시지는 지정 된 끝점에 전송 되기 전에 추가 정보를 사용 하 여 메시지입니다. 메시지 강화를 사용 하는 이유 중 하나는 다운스트림 처리를 간소화 하기 위해 사용할 수 있는 데이터를 포함 하는 것입니다. 예를 들어, 장치 쌍 태그를 사용 하 여 장치 원격 분석 메시지를 보강 합니다.이 정보에 대 한 API를 호출 하는 장치 쌍을 확인 하는 고객에 게 부하를 줄일 수 있습니다.

![메시지 원칙이 흐름](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

메시지 보강에 세 가지 주요 요소가 있습니다.

* 보강 이름이 나 키

* 값

* 하나 이상의 [끝점](iot-hub-devguide-endpoints.md) 을 보강 해야 적용 됩니다.

키에는 모든 문자열일 수 있습니다.

값을 다음 예제 중 하나일 수 있습니다.

* 모든 정적 문자열입니다. 조건, 논리, 작업 및 함수 등의 동적 값이 허용 되지 않습니다. 예를 들어, 여러 고객에 의해 사용 되는 SaaS 응용 프로그램을 개발 하는 경우 각 고객에 게 식별자를 할당 및 해당 식별자를 응용 프로그램에서 사용할 수 있도록 수 있습니다. 응용 프로그램을 실행 하면 IoT Hub는 스탬프 장치 원격 분석 메시지 고객의 식별자를 사용 하 여 각 고객에 대 한 메시지를 다르게 처리할 수 있도록 합니다.

* 장치 쌍 경로 등의 정보입니다. 예로 *$twin.tags.field* 하 고 *$twin.tags.latitude*합니다.

* 메시지를 보내는 IoT hub의 이름입니다. 이 값은 *$iothubname*합니다.

## <a name="applying-enrichments"></a>원칙이 적용

메시지를 지 원하는 모든 데이터 원본에서 가져올 수 있습니다 [IoT Hub 메시지 라우팅](iot-hub-devguide-messages-d2c.md), 다음 예제를 포함 하 여:

* 압력 또는 온도 같은 장치 원격 분석
* 장치 쌍 변경 알림을-장치 쌍에 대 한 변경 내용
* 장치를 만들거나 삭제 하는 경우 같은 장치 수명 주기 이벤트

Azure Blob storage, Service Bus 큐 또는 Service Bus 토픽와 같은 사용자 지정 끝점으로 라우팅되는 메시지를 IoT Hub의 기본 제공 끝점에 메시지를 강화를 추가할 수 있습니다.

또한 Event Grid로 끝점을 선택 하 여 Event Grid에 게시 되는 메시지를 강화를 추가할 수 있습니다. 자세한 내용은 [Iot Hub 및 Event Grid](iot-hub-event-grid.md)합니다.

끝점당 원칙이 적용 됩니다. 특정 끝점에 대 한 자동 삽입 될 5 강화를 지정 하는 경우 끝점으로 이동 하는 모든 메시지는 동일한 5 원칙이 지정 합니다.

메시지 강화 하는 방법, 참조는 [메시지 원칙이 자습서](tutorial-message-enrichments.md)

## <a name="limitations"></a>제한 사항

* 표준 또는 기본 계층에서 해당 허브에 대 한 IoT Hub 당 최대 10 개의 강화를 추가할 수 있습니다. 무료 계층에서 IoT 허브에 대 한 최대 2 개의 강화를 추가할 수 있습니다.

* 일부 경우는 보강 태그 또는 장치 쌍의 속성에 설정 된 값을 사용 하 여 적용 하는 값이 문자열 값으로 표시 됩니다. 예를 들어, 값을 보강 $twin.tags.field로 하는 경우 메시지 표시 됩니다 "$twin.tags.field" 문자열 필드의 값이 아닌 쌍에서. 이 같은 경우에 발생합니다.

   * IoT Hub 기본 계층의 경우 기본 계층 IoT hub 장치 쌍을 지원 하지 않습니다.

   * IoT Hub 표준 계층에서는 이지만 메시지를 보내는 장치에 장치 쌍에 없습니다.

   * IoT Hub 표준 계층에 있지만 보강의 값에 사용 되는 장치 쌍 경로가 존재 하지 않습니다. 예를 들어, 보강 값 $twin.tags.location로 설정 되어 장치 쌍 태그 아래의 위치 속성을 사용 되지 않은 경우 메시지 스탬프가 지정 되어 "$twin.tags.location" 문자열입니다. 

* 장치 쌍 업데이트에는 해당 보강 값에 반영 되려면 최대 5 분 정도 걸릴 수 있습니다.

* 강화를 포함 하는 총 메시지 크기는 256KB를 초과할 수 없습니다. 메시지 크기가 256KB를 초과 하면 IoT Hub는 메시지를 삭제 합니다. 사용할 수 있습니다 [IoT Hub 메트릭](iot-hub-metrics.md) 를 식별 하 여 메시지 삭제 되 면 오류를 디버그 합니다. 예를 들어 d2c.telemetry.egress.invalid를 모니터링할 수 있습니다.

## <a name="pricing"></a>가격

메시지 원칙이 추가 요금 없이 사용할 수 있습니다. 현재, IoT Hub에 메시지를 전송 하는 경우 요금이 청구 됩니다. 만 요금이 청구 된다는 되 면 해당 메시지에 대 한 메시지를 여러 끝점으로 이동 하는 경우에 합니다.

## <a name="availability"></a>가용성

이 미리 보기로 제공 되는 기능과 미국 동부, 미국 서 부, 유럽 서 부를 제외한 모든 지역에서 제공 됩니다 [Azure Government](/azure/azure-government/documentation-government-welcome)하십시오 [Azure 중국 21Vianet](/azure/china), 및 [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>다음 단계

IoT Hub로 메시지 라우팅에 대 한 자세한 내용은 다음이 문서를 확인해 보십시오.

* [다른 끝점으로 장치-클라우드 메시지를 보내도록 IoT Hub 메시지 라우팅 사용](iot-hub-devguide-messages-d2c.md)

* [자습서: IoT Hub 라우팅](tutorial-routing.md)