---
title: Azure IoT Central에서 규칙에 대해 웹후크 만들기 | Microsoft Docs
description: 규칙이 실행되면 자동으로 다른 애플리케이션에 알리기 위해 Azure IoT Central에 웹 후크를 만듭니다.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158098"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Azure IoT Central에서 규칙에 대해 웹후크 작업 만들기

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’**

웹후크를 사용하면 원격 모니터링 및 알림을 위해 IoT Central 앱을 다른 애플리케이션 및 서비스에 연결할 수 있습니다. 웹후크는 IoT Central 앱에서 규칙이 트리거될 때마다 연결하는 다른 애플리케이션 및 서비스에 자동으로 알립니다. IoT Central 앱은 규칙이 트리거될 때마다 POST 요청을 다른 응용 프로그램의 HTTP 끝점으로 보냅니다. 페이로드에는 장치 세부 정보 및 규칙 트리거 세부 정보가 포함되어 있습니다.

## <a name="set-up-the-webhook"></a>웹후크 설정

이 예제에서는 RequestBin에 연결하여 웹후크를 사용하여 규칙이 발생하면 알림을 받습니다.

1. [RequestBin](https://requestbin.net/)을 엽니다.

1. 새 RequestBin을 만들고 **Bin URL**을 복사합니다.

1. 원격 [분석 규칙을](tutorial-create-telemetry-rules.md)만듭니다. 규칙을 저장하고 새 작업을 추가합니다.

    ![웹후크 만들기 화면](media/howto-create-webhooks/webhookcreate.png)

1. 웹후크 작업을 선택하고 표시 이름을 입력한 후 Bin URL을 콜백 URL로서 붙여넣습니다.

1. 규칙을 저장합니다.

이제 규칙이 트리거되면 RequestBin에 새 요청이 나타납니다.

## <a name="payload"></a>페이로드

규칙이 트리거되면 원격 분석, 장치, 규칙 및 응용 프로그램 세부 정보가 포함된 json 페이로드가 포함된 콜백 URL에 HTTP POST 요청이 이루어집니다. 페이로드는 다음과 같습니다.

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>알려진 제한 사항

현재, API 통해 이러한 웹후크에서 구독/구독 취소하는 프로그래밍 방법은 없습니다.

이 기능을 개선하는 방법에 대한 아이디어가 있는 경우 [사용자 음성 포럼에](https://feedback.azure.com/forums/911455-azure-iot-central)제안 사항을 게시하십시오.

## <a name="next-steps"></a>다음 단계

웹 후크를 설정하고 사용하는 방법을 배웠으니 다음 단계는 [Azure Monitor 작업 그룹 구성을](howto-use-action-groups.md)살펴보는 것입니다.
