---
title: Azure IoT Central 규칙에서 여러 작업을 실행 합니다. | Microsoft Docs
description: 단일 IoT Central 규칙에서 여러 작업을 실행 하 고 다시 사용할 수 있는 여러 규칙에서 실행할 수 있는 작업 그룹을 만듭니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517196"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>하나 이상의 규칙에서를 실행 하려면 여러 작업을 그룹화

*이 문서는 작성기와 관리자에 게 적용 됩니다.*

Azure IoT Central의 규칙 조건이 충족 될 때 작업 실행을 만들 수 있습니다. 규칙은 장치 원격 분석 또는 이벤트를 기반으로 합니다. 예를 들어 장치에서 온도 임계값을 초과 하는 경우 운영자를 알릴 수 있습니다. 이 문서에서는 사용 하는 방법 설명 [Azure Monitor](../azure-monitor/overview.md) *작업 그룹* IoT Central 규칙에 여러 작업을 연결 합니다. 여러 규칙에 작업 그룹을 연결할 수 있습니다. [작업 그룹](../azure-monitor/platform/action-groups.md) 는 Azure 구독의 소유자가 정의한 알림 기본 설정의 컬렉션입니다.

## <a name="prerequisites"></a>필수 조건

- 종량제 애플리케이션
- Azure 계정 및 구독을 Azure Monitor 작업 그룹 만들기 및 관리

## <a name="create-action-groups"></a>작업 그룹 만들기

할 수 있습니다 [Azure portal에서 작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md) 또는 [Azure Resource Manager 템플릿을](../azure-monitor/platform/action-groups-create-resource-manager-template.md)합니다.

작업 그룹을 수행할 수 있습니다.

- 전자 메일, SMS, 같은 알림을 보내거나 음성 통화 확인 합니다.
- 웹 후크를 호출 하는 등의 작업을 실행 합니다.

다음 스크린 샷에서 전자 메일 및 SMS 알림을 보내고 웹 후크를 호출 하는 작업 그룹을 보여 줍니다.

![작업 그룹](media/howto-use-action-groups/actiongroup.png)

IoT Central 규칙에 작업 그룹을 사용 하려면 작업 그룹 IoT Central 응용 프로그램으로 동일한 Azure 구독에 있어야 합니다.

## <a name="use-an-action-group"></a>작업 그룹 사용

IoT Central 응용 프로그램에서 작업 그룹을 사용 하려면 먼저 원격 분석 또는 이벤트 규칙을 만듭니다. 규칙에 작업을 추가 하면 선택 **Azure 작업 그룹 모니터링**:

![작업 선택](media/howto-use-action-groups/chooseaction.png)

Azure 구독에서 작업 그룹을 선택 합니다.

![작업 그룹 선택](media/howto-use-action-groups/chooseactiongroup.png)

**저장**을 선택합니다. 작업 그룹 규칙이 트리거될 때 실행할 동작의 목록에 나타납니다.

![작업 그룹 저장](media/howto-use-action-groups/savedactiongroup.png)

다음 표에서 지원 되는 작업 형식으로 전송 되는 정보를 보여 줍니다.

| 작업 유형 | 출력 형식 |
| ----------- | -------------- |
| Email       | 표준 IoT Central 전자 메일 템플릿 |
| sms         | Azure IoT Central 경고: ${applicationName}-"${ruleName}" ${triggerDate} ${triggerTime}에서 "${deviceName}"에 트리거되어 서는 |
| 음성       | Azure I.O.T Central 경고: 응용 프로그램 ${applicationName}에 "${ruleName}" ${triggerDate} ${triggerTime}에서 "${deviceName}" 장치의 트리거 규칙 |
| 웹후크     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[일반 웹 후크 페이로드](#payload)}} |

다음 텍스트는 작업 그룹에서 SMS 메시지의 예:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> 다음 JSON 웹 후크 작업 페이로드를 예제를 보여 줍니다.

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

제안 된 다음 단계에 알아보려면은 이제 규칙을 사용 하 여 작업 그룹을 사용 하는 방법을 배웠으므로 하는 방법 [장치를 관리할](howto-manage-devices.md)합니다.
