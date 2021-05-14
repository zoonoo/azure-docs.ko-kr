---
title: Azure IoT Central 규칙에서 여러 작업 실행 | Microsoft Docs
description: 단일 IoT Central 규칙에서 여러 작업을 실행하고 여러 규칙에서 실행할 수 있는 재사용 가능한 작업 그룹을 만듭니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: e8e2497e0762c992c54ed9583d1db0ff8cbb74c8
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108741008"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>하나 이상의 규칙에서 실행할 여러 작업 그룹화

Azure IoT Central에서는 조건이 충족될 때 작업을 실행하는 규칙을 만듭니다. 규칙은 디바이스 원격 분석 또는 이벤트를 기반으로 합니다. 예를 들어 디바이스의 온도가 임계값을 초과하는 경우 운영자에게 알릴 수 있습니다. 이 문서에서는 [Azure Monitor](../../azure-monitor/overview.md) *작업 그룹* 을 사용하여 여러 작업을 IoT Central 규칙에 연결하는 방법을 설명합니다. 여러 규칙에 작업 그룹을 연결할 수 있습니다. [작업 그룹](../../azure-monitor/alerts/action-groups.md)은 Azure 구독 소유자가 정의한 알림 우선 설정 컬렉션입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 표준 가격 책정 플랜을 사용하여 만든 애플리케이션
- Azure Monitor 작업 그룹을 만들고 관리하기 위한 Azure 계정 및 구독

## <a name="create-action-groups"></a>작업 그룹 만들기

[Azure Portal](../../azure-monitor/alerts/action-groups.md) 또는 [Azure Resource Manager 템플릿](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md)에서 작업 그룹을 만들고 관리할 수 있습니다.

작업 그룹은 다음을 수행할 수 있습니다.

- 메일, SMS 등의 알림을 보내거나 음성 통화를 설정합니다.
- 웹후크 호출과 같은 작업을 실행합니다.

다음 스크린샷은 메일 및 SMS 알림을 보내고 웹후크를 호출하는 작업 그룹을 보여 줍니다.

![작업 그룹](media/howto-use-action-groups/actiongroup.png)

IoT Central 규칙에서 작업 그룹을 사용하려면 작업 그룹이 IoT Central 애플리케이션과 동일한 Azure 구독에 있어야 합니다.

## <a name="use-an-action-group"></a>작업 그룹 사용

IoT Central 애플리케이션에서 작업 그룹을 사용하려면 먼저 규칙을 만듭니다. 규칙에 작업을 추가하는 경우 **Azure Monitor 작업 그룹** 을 선택합니다.

![작업 선택](media/howto-use-action-groups/chooseaction.png)

Azure 구독에서 작업 그룹을 선택합니다.

![작업 그룹 선택](media/howto-use-action-groups/chooseactiongroup.png)

**저장** 을 선택합니다. 이제 규칙을 트리거할 때 실행할 작업 목록에 작업 그룹이 표시됩니다.

![작업 그룹 저장됨](media/howto-use-action-groups/savedactiongroup.png)

다음 표에서는 지원되는 작업 형식으로 전송되는 정보를 요약합니다.

| 작업 유형 | 출력 형식 |
| ----------- | -------------- |
| Email       | 표준 IoT Central 메일 템플릿 |
| sms         | Azure IoT Central 경고: ${applicationName}- “${ruleName}”이(가) ${triggerDate} ${triggerTime}에 “${deviceName}”에서 트리거됨 |
| 음성       | Azure I.O.T Central 경고: ${triggerDate} ${triggerTime}에 디바이스 “${deviceName}”에서 애플리케이션 ${applicationName}의 “${ruleName}” 규칙이 트리거됨 |
| 웹후크     | { “schemaId” : “AzureIoTCentralRuleWebhook”, “data”: {[regular webhook payload](howto-create-webhooks.md#payload)}} |

다음 텍스트는 작업 그룹의 예제 SMS 메시지입니다.

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>다음 단계

이제 규칙을 사용하여 작업 그룹을 사용하는 방법을 배웠으니, 다음 단계로 [디바이스 관리](howto-manage-devices.md) 방법을 알아볼 것을 제안합니다.
