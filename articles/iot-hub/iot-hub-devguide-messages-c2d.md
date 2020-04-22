---
title: Azure IoT Hub 클라우드-디바이스 메시징 이해 | Microsoft Docs
description: 이 개발자 가이드에서는 IoT 허브에서 클라우드-장치 메시징을 사용하는 방법에 대해 설명합니다. 여기에는 메시지 수명 주기 및 구성 옵션에 대한 정보가 포함되어 있습니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt
ms.openlocfilehash: 307ab47c1f7498f71e61108a616d35ef1d4f61c9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730004"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>IoT 허브에서 클라우드-장치 메시지 보내기

솔루션 백 엔드에서 장치 앱에 단방향 알림을 보내려면 IoT 허브에서 장치로 클라우드-장치 메시지를 보냅니다. Azure IoT Hub에서 지원하는 다른 클라우드-장치 옵션에 대한 설명은 [클라우드-장치 통신 지침을](iot-hub-devguide-c2d-guidance.md)참조하십시오.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

서비스 바인딩 끝점, */메시지/장치 바인딩을*통해 클라우드-장치 메시지를 보냅니다. 그런 다음 장치는 장치별 끝점인 */device/{deviceId}/메시지/장치 바인딩을*통해 메시지를 수신합니다.

단일 장치에서 각 클라우드-장치 메시지를 대상으로 지정하기 위해 IoT **허브는** 속성에 속성을 */장치/{deviceId}/메시지/장치 바인딩으로*설정합니다.

각 장치 큐에는 최대 50개의 클라우드-디바이스 메시지를 보유합니다. 동일한 장치에 더 많은 메시지를 보내려고 하면 오류가 발생합니다.

## <a name="the-cloud-to-device-message-life-cycle"></a>클라우드-장치 메시지 수명 주기

한 번 이상의 메시지 배달을 보장하기 위해 IoT 허브는 장치별 큐에서 클라우드-디바이스 메시지를 유지합니다. IoT hub가 큐에서 메시지를 제거하려면 장치가 *완료를*명시적으로 승인해야 합니다. 이 방법은 연결 및 디바이스 오류로부터 복원력을 보장합니다.

라이프 사이클 상태 그래프는 다음 다이어그램에 표시됩니다.

![클라우드-장치 메시지 수명 주기](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

IoT 허브 서비스가 장치에 메시지를 보내면 서비스는 메시지 상태를 *큐에 있는*것으로 설정합니다. 장치가 메시지를 *수신하려는* 경우 IoT 허브는 상태를 *보이지 않는*으로 설정하여 메시지를 *잠그습니다.* 이 상태는 장치의 다른 스레드가 다른 메시지 수신을 시작할 수 있도록 합니다. 장치 스레드가 메시지 처리를 완료하면 메시지를 *완료하여* IoT 허브에 이를 통보합니다. 그런 다음 IoT 허브는 상태를 *완료로*설정합니다.

또한 디바이스는 다음을 수행할 수 있습니다.

* 메시지를 *거부하면* IoT 허브가 *죽은 문자* 로 설정된 상태로 설정됩니다. 메시지 대기열 원격 분석 전송(MQTT) 프로토콜을 통해 연결되는 장치는 클라우드-장치 메시지를 거부할 수 없습니다.

* 상태를 *큐에*넣은 상태로 큐에 다시 넣으면 IoT 허브가 메시지를 큐에 다시 넣도록 하는 메시지를 *포기합니다.* MQTT 프로토콜을 통해 연결되는 장치는 클라우드-장치 메시지를 포기할 수 없습니다.

스레드가 IoT 허브에 알리지 않고 메시지를 처리하지 못할 수 있습니다. 이 경우 메시지는 *가시성* 시간(또는 *잠금* 시간 시간)후 *보이지 않는* 상태에서 *큐에 대기된* 상태로 자동으로 전환됩니다. 이 시간 시간의 값은 1분이며 변경할 수 없습니다.

IoT 허브의 **최대 배달 수** 속성은 메시지가 *Enqueued* 상태와 *보이지 않는* 상태 간에 전환할 수 있는 최대 횟수를 결정합니다. 이 전환 횟수 이후에 IoT 허브는 메시지 상태를 *Dead lettered로*설정합니다. 마찬가지로 IoT 허브는 만료 시간 이후에 *문자로 보낸 데드에* 대한 메시지 상태를 설정합니다. 자세한 내용은 [살 시간](#message-expiration-time-to-live)참조.

[IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법](iot-hub-csharp-csharp-c2d.md) 문서에서는 클라우드에서 클라우드-장치 메시지를 보내고 장치에서 수신하는 방법을 보여 주며, 이를 보여 주는 설명입니다.

메시지 손실이 응용 프로그램 논리에 영향을 주지 않는 경우 장치는 일반적으로 클라우드-장치 메시지를 완료합니다. 예를 들어 장치가 메시지 콘텐츠를 로컬로 유지했거나 작업을 성공적으로 실행한 경우일 수 있습니다. 또한 이 메시지는 일시적인 정보를 전달할 수 있으며, 이 정보는 손실로 인해 응용 프로그램의 기능에 영향을 미치지 않습니다. 경우에 따라 장기 실행 작업에 다음을 수행할 수 있습니다.

* 장치가 로컬 저장소에서 작업 설명을 유지한 후 클라우드-장치 메시지를 완료합니다.

* 작업이 진행되는 다양한 단계에서 하나 이상의 디바이스-클라우드 메시지를 사용하여 솔루션 백 엔드에 알림을 제공할 수 있습니다.

## <a name="message-expiration-time-to-live"></a>메시지 만료(TTL(Time To Live))

모든 클라우드-디바이스 메시지에는 만료 시간이 있습니다. 이 시간은 다음 중 하나에 의해 설정됩니다.

* 서비스의 **만료시간Utc** 속성
* IoT 허브는 기본 시간을 사용하여 IoT 허브 속성으로 지정된 *라이브* 를 사용합니다.

[클라우드-디바이스 구성 옵션](#cloud-to-device-configuration-options)을 참조하세요.

메시지 만료를 활용하고 연결이 끊긴 장치에 메시지를 보내지 않도록 하는 일반적인 방법은 *라이브 값에 짧은 시간을* 설정하는 것입니다. 이 방법은 장치 연결 상태를 유지하는 것과 동일한 결과를 얻을 수 있지만 더 효율적입니다. 메시지 승인을 요청할 때 IoT 허브는 다음 장치를 사용자에게 사용자에게 보태시면 됩니다.

* 메시지를 받을 수 없는 경우
* 온라인 상태가 아니거나 실패한 경우

## <a name="message-feedback"></a>메시지 피드백

클라우드-장치 메시지를 보낼 때 서비스는 해당 메시지의 최종 상태에 대한 메시지별 피드백 배달을 요청할 수 있습니다. 다음 네 가지 값 중 하나로 전송되는 클라우드-장치 메시지에 **iothub-ack** 응용 프로그램 속성을 설정하여 이 작업을 수행합니다.

| Ack 속성 값 | 동작 |
| ------------ | -------- |
| none     | IoT 허브는 피드백 메시지(기본 동작)를 생성하지 않습니다. |
| 긍정 | 클라우드-장치 메시지가 *완료됨* 상태에 도달하면 IoT 허브는 피드백 메시지를 생성합니다. |
| 부정 | 클라우드-장치 메시지가 *죽은 문자* 상태에 도달하면 IoT 허브는 피드백 메시지를 생성합니다. |
| 전체     | IoT 허브는 두 경우 모두 피드백 메시지를 생성합니다. |

**Ack** 값이 *가득 찼고*피드백 메시지가 수신되지 않으면 피드백 메시지가 만료되었음을 의미합니다. 서비스는 원본 메시지에서 발생한 상황을 알지 못합니다. 실제로 서비스는 만료되기 전에 피드백을 처리할 수 있는지 확인해야 합니다. 최대 만료 시간은 2일로, 오류가 발생할 경우 서비스를 다시 실행할 수 있는 시간이 남습니다.

[Endpoint에서](iot-hub-devguide-endpoints.md)설명한 대로 IoT 허브는 서비스 바인딩 끝점, */메시지/서비스 바인딩/피드백을*메시지로 통해 피드백을 전달합니다. 피드백 수신을 위한 의미 체계는 클라우드-디바이스 메시지의 경우와 같습니다. 가능한 경우 메시지 피드백은 다음 형식으로 단일 메시지에서 일괄 처리됩니다.

| 속성     | 설명 |
| ------------ | ----------- |
| EnqueuedTime | 허브에서 피드백 메시지를 받은 시기를 나타내는 타임스탬프 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

본문은 각각 다음과 같은 속성이 있는 레코드의 JSON으로 직렬화된 배열입니다.

| 속성           | 설명 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | 메시지 결과가 발생한 시기를 나타내는 타임스탬프(예: 허브가 피드백 메시지를 받았거나 원래 메시지가 만료됨). |
| OriginalMessageId  | 이 피드백 정보와 관련된 클라우드-장치 메시지 *Id* |
| StatusCode         | IoT 허브에서 생성되는 피드백 메시지에 사용되는 필수 문자열: <br/> *Success* <br/> *만료됨* <br/> *배달 카운트 초과* <br/> *거부* <br/> *제거* |
| 설명        | *상태 코드에* 대한 문자열 값 |
| deviceId           | 이 피드백과 관련된 클라우드-장치 메시지의 대상 장치의 *DeviceId* |
| DeviceGenerationId | 이 피드백과 관련된 클라우드-장치 메시지의 대상 장치의 *DeviceGenerationId* |

클라우드-장치 메시지의 피드백을 원래 메시지와 상호 연관시려면 서비스는 *MessageId*을 지정해야 합니다.

피드백 메시지의 본문이 다음 코드에 표시됩니다.

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

**삭제된 장치에 대한 보류 중인 피드백**

장치가 삭제되면 보류 중인 피드백도 삭제됩니다. 장치 피드백은 일괄 처리로 전송됩니다. 장치가 메시지 수신을 확인하고 다음 피드백 일괄 처리가 준비될 때 사이의 좁은 창(종종 1초 미만)에서 장치가 삭제되면 피드백이 발생하지 않습니다.

보류 중인 피드백이 도착하기 전에 장치를 삭제하기 전에 시간이 대기 중일 때까지 기다려 이 문제를 해결할 수 있습니다. 관련 메시지 피드백은 장치가 삭제되면 손실된 것으로 가정해야 합니다.

## <a name="cloud-to-device-configuration-options"></a>클라우드-디바이스 구성 옵션

각 IoT Hub는 클라우드-디바이스 메시징에 다음 구성 옵션을 노출합니다.

| 속성                  | 설명 | 범위 및 기본값 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 클라우드-장치 메시지의 기본 TTL | 최대 2일(최소 1분)의 간격을 ISO_8601; 기본값: 1시간 |
| maxDeliveryCount          | 디바이스별 클라우드-디바이스 큐에 대한 최대 전송 횟수 | 1 ~ 100; 기본값: 10 |
| feedback.ttlAsIso8601     | 서비스 바인딩된 피드백 메시지에 대한 보존 | 최대 2일(최소 1분)의 간격을 ISO_8601; 기본값: 1시간 |
| feedback.maxDeliveryCount | 피드백 대기열의 최대 배달 횟수 | 1 ~ 100; 기본값: 10 |
| 피드백.lockDurationAsIso8601 | 피드백 대기열의 최대 배달 횟수 | ISO_8601 간격 5 ~ 300 초 (최소 5 초); 기본값: 60초. |

다음 방법 중 하나로 구성 옵션을 설정할 수 있습니다.

* **Azure 포털**: IoT 허브의 **설정에서** **기본 제공 끝점을** 선택하고 **클라우드를 장치 메시징으로**확장합니다. **(feedback.maxDeliveryCount** 및 **feedback.lockDurationAsso8601** 속성설정은 현재 Azure 포털에서 지원되지 않습니다.)

    ![포털에서 클라우드-장치 메시징에 대한 구성 옵션 설정](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: [az iot 허브 업데이트](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update) 명령을 사용합니다.

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>다음 단계

클라우드-장치 메시지를 수신하는 데 사용할 수 있는 SDK에 대한 자세한 내용은 [Azure IoT SDK를](iot-hub-devguide-sdks.md)참조하십시오.

클라우드-디바이스 메시지를 수신하려면 [클라우드-디바이스 보내기](iot-hub-csharp-csharp-c2d.md) 자습서를 참조하세요.
