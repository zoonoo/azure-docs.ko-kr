---
title: Azure IoT Central 규칙에서 여러 작업 실행 | Microsoft Docs
description: 단일 IoT Central 규칙에서 여러 작업을 실행 하 고 여러 규칙에서 실행할 수 있는 재사용 가능한 작업 그룹을 만듭니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 1992b8925d5d9ba59c36452187f5b6eb510e72dc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990813"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>하나 이상의 규칙에서 실행할 여러 작업 그룹화

*이 문서는 빌더 및 관리자에 게 적용 됩니다.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central에서는 조건이 충족 될 때 작업을 실행 하는 규칙을 만듭니다. 규칙은 장치 원격 분석 또는 이벤트를 기반으로 합니다. 예를 들어 장치의 온도가 임계값을 초과 하는 경우 운영자에 게 알릴 수 있습니다. 이 문서에서는 [Azure Monitor](../../azure-monitor/overview.md) *작업 그룹* 을 사용 하 여 여러 작업을 IoT Central 규칙에 연결 하는 방법을 설명 합니다. 여러 규칙에 작업 그룹을 연결할 수 있습니다. [작업 그룹](../../azure-monitor/platform/action-groups.md) 은 Azure 구독의 소유자가 정의한 알림 기본 설정 모음입니다.

## <a name="prerequisites"></a>필수 조건

- 표준 요금제를 사용 하 여 만든 응용 프로그램
- Azure Monitor 작업 그룹을 만들고 관리 하기 위한 Azure 계정 및 구독

## <a name="create-action-groups"></a>작업 그룹 만들기

Azure Portal 또는 [Azure Resource Manager 템플릿을](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)사용 하 여 [작업 그룹을 만들고 관리할](../../azure-monitor/platform/action-groups.md) 수 있습니다.

작업 그룹은 다음을 수행할 수 있습니다.

- 전자 메일, SMS 등의 알림을 보내거나 음성 통화를 설정 합니다.
- 웹 후크 호출과 같은 작업을 실행 합니다.

다음 스크린샷은 전자 메일 및 SMS 알림을 보내고 웹 후크를 호출 하는 작업 그룹을 보여 줍니다.

![작업 그룹](media/howto-use-action-groups/actiongroup.png)

IoT Central 규칙에서 작업 그룹을 사용 하려면 작업 그룹이 IoT Central 응용 프로그램과 동일한 Azure 구독에 있어야 합니다.

## <a name="use-an-action-group"></a>작업 그룹 사용

IoT Central 응용 프로그램에서 작업 그룹을 사용 하려면 먼저 원격 분석 또는 이벤트 규칙을 만듭니다. 규칙에 작업을 추가 하는 경우 **Azure Monitor 작업 그룹**을 선택 합니다.

![작업 선택](media/howto-use-action-groups/chooseaction.png)

Azure 구독에서 작업 그룹을 선택 합니다.

![작업 그룹 선택](media/howto-use-action-groups/chooseactiongroup.png)

**저장**을 선택합니다. 이제 규칙을 트리거할 때 실행할 작업 목록에 작업 그룹이 표시 됩니다.

![작업 그룹 저장 됨](media/howto-use-action-groups/savedactiongroup.png)

다음 표에서는 지원 되는 동작 형식으로 전송 되는 정보를 요약 합니다.

| 동작 유형 | 출력 형식 |
| ----------- | -------------- |
| 이메일       | 표준 IoT Central 전자 메일 템플릿 |
| SMS         | Azure IoT Central 경고: $ {applicationName}-"$ {ruleName}"이 (가) $ {triggerDate} $ {Triggerdate} (으)로 "$ {장치 이름}"에서 트리거 되었습니다. |
| 음성       | Azure. $. T 중앙 경고: $ {applicationName}의 $ {triggerDate} $ {Triggerdate}에서 장치 "$ {ruleName}"에 대 한 규칙 "$ {}"이 (가) 트리거 되었습니다. |
| 웹후크     | {"schemaId": "AzureIoTCentralRuleWebhook", "data": {[regular webhook 페이로드](#payload)}} |

다음 텍스트는 작업 그룹의 예제 SMS 메시지입니다.

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>다음 JSON은 webhook 동작 페이로드의 예를 보여 줍니다.

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

이제 규칙을 사용 하 여 작업 그룹을 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 [장치를 관리](howto-manage-devices.md)하는 방법을 배우는 것입니다.
