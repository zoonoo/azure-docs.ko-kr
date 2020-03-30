---
title: Azure IoT 중앙 규칙에서 여러 작업 실행 | 마이크로 소프트 문서
description: 단일 IoT Central 규칙에서 여러 작업을 실행하고 여러 규칙에서 실행할 수 있는 재사용 가능한 작업 그룹을 만듭니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157690"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>하나 이상의 규칙에서 실행할 여러 작업 그룹화

*이 문서는 빌더 및 관리자에게 적용됩니다.*

Azure IoT Central에서 조건이 충족될 때 작업을 실행하는 규칙을 만듭니다. 규칙은 장치 원격 분석 또는 이벤트를 기반으로 합니다. 예를 들어 장치의 온도가 임계값을 초과하면 운영자에게 알릴 수 있습니다. 이 문서에서는 Azure [Monitor](../../azure-monitor/overview.md) *작업 그룹을* 사용하여 IoT Central 규칙에 여러 작업을 연결하는 방법을 설명합니다. 작업 그룹을 여러 규칙에 연결할 수 있습니다. [작업 그룹은](../../azure-monitor/platform/action-groups.md) Azure 구독 소유자가 정의한 알림 기본 설정의 컬렉션입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 표준 가격 책정 계획을 사용하여 만든 응용 프로그램
- Azure 모니터 작업 그룹을 만들고 관리하는 Azure 계정 및 구독

## <a name="create-action-groups"></a>작업 그룹 만들기

Azure 포털 또는 [Azure 리소스 관리자 템플릿을](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)사용하여 [작업 그룹을 만들고 관리할](../../azure-monitor/platform/action-groups.md) 수 있습니다.

작업 그룹은 다음을 수행할 수 있습니다.

- 전자 메일, SMS 등의 알림을 보내거나 음성 통화를 합니다.
- 웹후크 호출과 같은 작업을 실행합니다.

다음 스크린샷은 이메일 및 SMS 알림을 보내고 웹후크를 호출하는 작업 그룹을 보여 주며 다음과 같은 작업을 보여 주어집니다.

![작업 그룹](media/howto-use-action-groups/actiongroup.png)

IoT Central 규칙에서 작업 그룹을 사용하려면 작업 그룹이 IoT Central 응용 프로그램과 동일한 Azure 구독에 있어야 합니다.

## <a name="use-an-action-group"></a>작업 그룹 사용

IoT Central 응용 프로그램에서 작업 그룹을 사용하려면 먼저 규칙을 만듭니다. 규칙에 작업을 추가할 때 Azure **모니터 작업 그룹을**선택합니다.

![작업 선택](media/howto-use-action-groups/chooseaction.png)

Azure 구독에서 작업 그룹을 선택합니다.

![작업 그룹 선택](media/howto-use-action-groups/chooseactiongroup.png)

**저장**을 선택합니다. 이제 규칙이 트리거될 때 실행할 작업 그룹에 작업 그룹이 표시됩니다.

![저장된 작업 그룹](media/howto-use-action-groups/savedactiongroup.png)

다음 표에는 지원되는 작업 유형으로 전송된 정보가 요약되어 있습니다.

| 동작 유형 | 출력 형식 |
| ----------- | -------------- |
| Email       | 표준 IoT 중앙 이메일 템플릿 |
| sms         | Azure IoT 중앙 경고: ${응용 프로그램 이름} - "${장치 이름}"에서 트리거된 ${트리거Date} ${트리거시간} |
| 음성       | Azure I.O.T 중앙 경고: ${트리거날짜} ${트리거타임}에서 장치 "${장치 이름}"에서 트리거된 규칙 "${ruleName}" 응용 프로그램에서 ${응용 프로그램 이름} |
| 웹후크     | { "스키마Id" : "AzureIoTCentralRuleWebhook", "데이터": {[일반 웹후크 페이로드](howto-create-webhooks.md#payload)}} |

다음 텍스트는 작업 그룹의 예제 SMS 메시지입니다.

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>다음 단계

규칙과 함께 작업 그룹을 사용하는 방법을 배웠으니 다음 단계는 [장치를 관리하는](howto-manage-devices.md)방법을 알아보는 것입니다.
