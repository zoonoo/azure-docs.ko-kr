---
title: Azure IoT Central에서 규칙에 대해 웹후크 만들기 | Microsoft Docs
description: 규칙이 실행되면 자동으로 다른 애플리케이션에 알리기 위해 Azure IoT Central에 웹 후크를 만듭니다.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5c2bef7f3eb8d6f8d6d78755d839a33556259b65
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953669"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Azure IoT Central에서 규칙에 대해 웹후크 작업 만들기

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

웹후크를 사용하면 원격 모니터링 및 알림을 위해 IoT Central 앱을 다른 애플리케이션 및 서비스에 연결할 수 있습니다. 웹후크는 IoT Central 앱에서 규칙이 트리거될 때마다 연결하는 다른 애플리케이션 및 서비스에 자동으로 알립니다. IoT Central 앱은 규칙이 트리거될 때마다 다른 응용 프로그램의 HTTP 끝점에 POST 요청을 보냅니다. 페이로드는 장치 세부 정보 및 규칙 트리거 세부 정보를 포함 합니다.

## <a name="set-up-the-webhook"></a>Webhook 설정

이 예제에서는 웹 후크를 사용 하 여 규칙이 실행 될 때 RequestBin에 연결 하 여 알림 메시지를 받습니다.

1. [RequestBin](https://requestbin.net/)을 엽니다.

1. 새 RequestBin을 만들고 **Bin URL**을 복사합니다.

1. [원격 분석 규칙](howto-create-telemetry-rules.md) 또는 [이벤트 규칙](howto-create-event-rules.md)을 만듭니다. 규칙을 저장하고 새 작업을 추가합니다.

    ![Webhook 만들기 화면](media/howto-create-webhooks/webhookcreate.png)

1. 웹후크 작업을 선택하고 표시 이름을 입력한 후 Bin URL을 콜백 URL로서 붙여넣습니다.

1. 규칙을 저장합니다.

이제 규칙이 트리거되면 RequestBin에 새 요청이 표시 됩니다.

## <a name="payload"></a>페이로드

규칙이 트리거되면 측정, 디바이스, 규칙 및 애플리케이션 세부 정보가 있는 json 페이로드를 포함하는 콜백 URL에 대해 HTTP POST 요청이 수행됩니다. 원격 분석 규칙의 경우 페이로드는 다음과 같습니다.

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>알려진 제한 사항

현재, API 통해 이러한 웹후크에서 구독/구독 취소하는 프로그래밍 방법은 없습니다.

이 기능을 개선하는 방법에 대한 아이디어가 있는 경우 [Uservoice 포럼](https://feedback.azure.com/forums/911455-azure-iot-central)에 게시해주세요.

## <a name="next-steps"></a>다음 단계

이제 웹 후크를 설정 하 고 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 [Microsoft Flow에서 워크플로 작성](howto-add-microsoft-flow.md)을 살펴보는 것입니다.
