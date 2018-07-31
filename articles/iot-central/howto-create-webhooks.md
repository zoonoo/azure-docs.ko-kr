---
title: Azure IoT Central에서 규칙에 대해 웹후크 만들기 | Microsoft Docs
description: 규칙이 실행되면 자동으로 다른 응용 프로그램에 알리기 위해 Azure IoT Central에 웹 후크를 만듭니다.
author: viv-liu
ms.author: viviali
ms.date: 07/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1e21076cafe21e6c0efcdf5a8146278eabd9ebc4
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227835"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Azure IoT Central에서 규칙에 대해 웹후크 작업 만들기

웹후크를 사용하면 원격 모니터링 및 알림을 위해 IoT Central 앱을 다른 응용 프로그램 및 서비스에 연결할 수 있습니다. 웹후크는 IoT Central 앱에서 규칙이 트리거될 때마다 연결하는 다른 응용 프로그램 및 서비스에 자동으로 알립니다. IoT Central 앱은 규칙이 트리거될 때마다 다른 응용 프로그램의 HTTP 끝점에 POST 요청을 보냅니다. 페이로드에는 장치 세부 정보 및 규칙 트리거 세부 정보가 포함됩니다. 

## <a name="how-to-set-up-the-webhook"></a>웹후크를 설정하는 방법
이 예제에서는 RequestBin에 연결하여 규칙 실행 시 웹후크를 사용하여 알림이 표시되도록 합니다. 

1. [RequestBin](http://requestbin.net/)을 엽니다. 
1. 새 RequestBin을 만들고 **Bin URL**을 복사합니다. 
1. [원격 분석 규칙](howto-create-telemetry-rules.md) 또는 [이벤트 규칙](howto-create-event-rules.md)을 만듭니다. 규칙을 저장하고 새 작업을 추가합니다.
![웹후크 만들기 화면](media/howto-create-webhooks/webhookcreate.png)
1. 웹후크 작업을 선택하고 표시 이름을 입력한 후 Bin URL을 콜백 URL로서 붙여넣습니다. 
1. 규칙 저장

이제 규칙이 발생하면 RequestBin에 새 요청이 표시됩니다.

## <a name="payload"></a>페이로드
규칙이 트리거되면 측정, 장치, 규칙 및 응용 프로그램 세부 정보가 있는 json 페이로드를 포함하는 콜백 URL에 대해 HTTP POST 요청이 수행됩니다. 원격 분석 규칙의 경우 페이로드는 다음과 같을 수 있습니다.

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
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
웹후크를 설정하고 사용하는 방법을 배웠으므로 제안되는 다음 단계는 [Microsoft Flow에서 워크플로 만들기](howto-add-microsoft-flow.md)입니다.
