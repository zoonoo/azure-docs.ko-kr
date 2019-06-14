---
title: Azure IoT Hub 클라우드-장치 메시징 이해 | Microsoft Docs
description: 이 개발자 가이드에는 IoT hub를 사용 하 여 클라우드-장치 메시징을 사용 하는 방법을 설명 합니다. 메시지 수명 주기 및 구성 옵션에 대 한 정보를 포함합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055336"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>IoT hub에서 클라우드-장치 메시지 보내기

솔루션 백 엔드에서 장치 앱에 단방향 알림을 보내려면 클라우드-장치 메시지를 IoT hub에서 장치에 보냅니다. Azure IoT Hub에서 지 원하는 다른 클라우드-장치 옵션 설명은 참조 하세요 [클라우드-장치 통신 지침](iot-hub-devguide-c2d-guidance.md)합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

서비스 지향 끝점을 통해 클라우드-장치 메시지를 보낼 */메시지/devicebound*합니다. 장치는 장치별 끝점을 통해 메시지를 받습니다 */devices/{deviceid}/files/메시지/devicebound*합니다.

단일 장치에서 각 클라우드-장치 메시지를 대상으로 IoT hub는 다음과 같이 설정 됩니다. 합니다 **하** 속성을 */devices/{deviceid}/files/메시지/devicebound*합니다.

각 장치 큐를 최대 50 클라우드-장치 메시지가 보관 됩니다. 오류가 동일한 장치에 더 많은 메시지를 보내려고 시도 합니다.

## <a name="the-cloud-to-device-message-life-cycle"></a>클라우드-장치 메시지 수명 주기

최소 한 번 메시지 배달을 보장 하려면 IoT hub는 장치 별 큐에 클라우드-장치 메시지를 유지 합니다. 큐에서 메시지를 제거 하려면 IoT hub에 장치를 명시적으로 인정 해야 합니다 *완성*합니다. 이 방법은 연결 및 디바이스 오류로부터 복원력을 보장합니다.

수명 주기 상태 그래프는 다음 다이어그램에 표시 됩니다.

![클라우드-장치 메시지 수명 주기](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

IoT hub 서비스의 장치에 메시지를 보내면 서비스 메시지 상태를 설정 합니다 *큐에 넣은*합니다. 장치 하려고 할 때 *받을* 메시지를 IoT hub *잠금을* 상태를 설정 하 여 메시지 *보이지 않는*합니다. 이 상태는 다른 메시지 수신을 시작 하도록 장치에 다른 스레드를 허용 합니다. 장치 스레드가 메시지의 처리가 완료 되 면 IoT hub에 알립니다 *완료* 메시지입니다. IoT hub는 다음 상태를 설정 *Completed*합니다.

또한 디바이스는 다음을 수행할 수 있습니다.

* *거부* 로 설정 하는 IoT hub는 메시지를 *Dead lettered* 상태입니다. 메시지 큐 전송 (MQTT Telemetry) 프로토콜을 통해 연결 하는 장치는 클라우드-장치 메시지를 거부할 수 없습니다.

* *중단할* 상태 설정 하 여 메시지를 큐에 다시 배치 하는 IoT hub는 메시지 *큐에 넣은*합니다. MQTT 프로토콜을 통해 연결 하는 장치는 클라우드-장치 메시지를 중단할 수 없습니다.

스레드는 IoT hub에 알리지 않고 메시지를 처리 하는 데 실패할 수 있습니다. 메시지에서 자동으로 전환할 경우에 *보이지 않는* 상태를 다시는 *큐에 넣은* 후 상태를 *가시성* 제한 시간 (또는 *잠금* 시간 제한). 이 시간 제한의 기본값은 1 분입니다.

**최대 배달 횟수** 속성을 IoT hub 메시지 간에 전환할 수는 최대 횟수를 결정 합니다 *큐에 넣은* 및 *보이지 않는* 상태입니다. 해당 전환 횟수 후 IoT hub는 메시지의 상태를 설정 *Dead lettered*합니다. IoT hub는 메시지의 상태를 설정 하는 마찬가지로 *Dead lettered* 만료 시간 이후입니다. 자세한 내용은 [Time-to-live](#message-expiration-time-to-live)합니다.

합니다 [IoT Hub를 사용 하 여 클라우드-장치 메시지를 보내는 방법](iot-hub-csharp-csharp-c2d.md) 문서에서는 클라우드에서 클라우드-장치 메시지를 보내고 장치에서 수신 하는 방법을 보여 줍니다.

장치는 일반적으로 메시지 손실이 응용 프로그램 논리에 영향을 주지 하는 경우 클라우드-장치 메시지를 완료 합니다. 이 예제는 장치는 메시지 콘텐츠를 로컬로 유지 하거나 처리가 성공적으로 작업을 실행 하는 경우 수 있습니다. 메시지 임시 정보를 손실 하지 않습니다. 응용 프로그램의 기능에 영향을 수행할 수도 수입니다. 경우에 따라 장기 실행 작업에 다음을 수행할 수 있습니다.

* 장치 로컬 저장소에 작업 설명을 유지 된 후 클라우드-장치 메시지를 완료 합니다.

* 작업이 진행되는 다양한 단계에서 하나 이상의 디바이스-클라우드 메시지를 사용하여 솔루션 백 엔드에 알림을 제공할 수 있습니다.

## <a name="message-expiration-time-to-live"></a>메시지 만료(TTL(Time To Live))

모든 클라우드-장치 메시지에는 만료 시간이 있습니다. 이 이번에는 다음 중 하나를 통해 설정 됩니다.

* 합니다 **ExpiryTimeUtc** 서비스에서 속성
* 기본값을 사용 하 여 IoT hub *time-to-live* IoT hub 속성 처럼 지정 된

[클라우드-장치 구성 옵션](#cloud-to-device-configuration-options)을 참조하세요.

메시지 만료를 활용 하기 위해 연결이 끊긴된 장치에 메시지를 보내는 것을 방지 하는 일반적인 방법은 짧은 설정 하는 것 *time-to-live* 값입니다. 이 방법은 장치 연결 상태를 유지 관리 하는 것과 동일한 결과 얻을 수 있지만 것이 더 효율적입니다. 메시지 승인을 요청 하면 IoT hub는 장치는 어떤을 알립니다.

* 메시지를 받을 수 없는 경우
* 온라인 상태가 아니거나 실패한 경우

## <a name="message-feedback"></a>메시지 피드백

클라우드-장치 메시지를 보내면 서비스는 메시지의 최종 상태에 대 한 메시지 단위 피드백 제공을 요청할 수 있습니다. 설정 하 여이 작업을 수행 합니다 **iothub ack** 다음 네 가지 값 중 하나에 전송 되는 클라우드-장치 메시지에서 응용 프로그램 속성:

| Ack 속성 값 | 동작 |
| ------------ | -------- |
| 없음     | IoT hub (기본 동작) 피드백 메시지를 생성 하지 않습니다. |
| 긍정 | 클라우드-장치 메시지에 도달 하는 경우는 *Completed* 상태인 경우 IoT hub가 피드백 메시지를 생성 합니다. |
| 음수 | 클라우드-장치 메시지에 도달 하는 경우는 *Dead lettered* 상태인 경우 IoT hub가 피드백 메시지를 생성 합니다. |
| 전체     | IoT hub 든에서 피드백 메시지를 생성합니다. |

경우는 **Ack** 값이 *전체*, 피드백 메시지를 받지, 즉, 피드백 메시지가 만료 되었습니다. 서비스는 원본 메시지에서 발생한 상황을 알지 못합니다. 실제로 서비스는 만료되기 전에 피드백을 처리할 수 있는지 확인해야 합니다. 최대 만료 시간이 이틀 시간 서비스을 남겨 다시 실행 오류가 발생 하는 경우.

에 설명 된 대로 [끝점](iot-hub-devguide-endpoints.md), IoT hub 서비스 지향 끝점을 통해 피드백 제공 */messages/servicebound/feedback*, 메시지입니다. 피드백 수신을 위한 의미 체계는 클라우드-장치 메시지의 경우와 같습니다. 가능한 경우 메시지 피드백은 다음 형식으로 단일 메시지에서 일괄 처리됩니다.

| 자산     | 설명 |
| ------------ | ----------- |
| EnqueuedTime | 허브에서 피드백 메시지를 수신한 경우를 나타내는 타임 스탬프 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

본문은 각각 다음과 같은 속성이 있는 레코드의 JSON으로 직렬화된 배열입니다.

| 자산           | 설명 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | 메시지의 결과 발생 한 시기를 나타내는 타임 스탬프 (예를 들어, 피드백 메시지를 수신 하는 허브 또는 원본 메시지가 만료) |
| OriginalMessageId  | 합니다 *MessageId* 이 피드백 정보와 관련 된 클라우드-장치 메시지 |
| StatusCode         | IoT hub에 의해 생성 되는 피드백 메시지에 사용 되는 필수 문자열. <br/> *성공* <br/> *Expired* <br/> *DeliveryCountExceeded* <br/> *거부* <br/> *제거* |
| 설명        | 문자열 값에 대 한 *StatusCode* |
| deviceId           | 합니다 *DeviceId* 피드백의이 관련 된 클라우드-장치 메시지의 대상 장치 |
| DeviceGenerationId | 합니다 *DeviceGenerationId* 피드백의이 관련 된 클라우드-장치 메시지의 대상 장치 |

원본 메시지와 해당 피드백을 상호 연결 하는 클라우드-장치 메시지를 지정 해야 합니다는 *MessageId*합니다.

피드백 메시지의 본문은 다음 코드에 표시 됩니다.

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>클라우드-장치 구성 옵션

각 IoT Hub는 클라우드-장치 메시징에 다음 구성 옵션을 노출합니다.

| 자산                  | 설명 | 범위 및 기본값 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 클라우드-장치 메시지에 대 한 기본 TTL | 까지 ISO_8601 간격 최대 2 일 (최소 1 분)입니다. 기본값: 1시간 |
| maxDeliveryCount          | 클라우드-장치 장치별 큐에 대 한 최대 배달 횟수 | 1에서 100까지. 기본값: 10 |
| feedback.ttlAsIso8601     | 서비스 바인딩 피드백 메시지 보존 | 까지 ISO_8601 간격 최대 2 일 (최소 1 분)입니다. 기본값: 1시간 |
| feedback.maxDeliveryCount | 피드백 큐에 대 한 최대 배달 횟수 | 1에서 100까지. 기본값: 100 |

이러한 구성 옵션을 설정하는 방법에 대한 자세한 내용은 [IoT Hub 만들기](iot-hub-create-through-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

클라우드-장치 메시지를 수신 하는 데 사용할 수 있는 Sdk에 대 한 정보를 참조 하세요 [Azure IoT Sdk](iot-hub-devguide-sdks.md)합니다.

클라우드-장치 메시지를 수신하려면 [클라우드-장치 보내기](iot-hub-csharp-csharp-c2d.md) 자습서를 참조하세요.
