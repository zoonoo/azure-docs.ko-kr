---
title: '빠른 시작: 사용자 지정 경고 만들기'
description: IoT용 Azure Security Center 보안 서비스에 대한 사용자 지정 디바이스 경고를 이해하고, 만들고, 할당합니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2020
ms.author: mlottner
ms.openlocfilehash: 8ba81b669c25d91efa8ad1f07b1a7835b42c5cf4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310843"
---
# <a name="quickstart-create-custom-alerts"></a>빠른 시작: 사용자 지정 경고 만들기

사용자 지정 보안 그룹 및 경고를 사용하면 엔드투엔드 보안 정보와 범주별 디바이스 지식을 최대한 활용하여 IoT 솔루션 전체의 보안을 강화할 수 있습니다.

## <a name="why-use-custom-alerts"></a>사용자 지정 경고를 사용하는 이유는?

사용자가 자신의 IoT 디바이스에 대해 가장 잘 알고 있습니다.

필요한 디바이스 동작을 완전히 이해하고 있는 고객이 IoT용 Azure Security Center를 사용하면 이러한 이해를 디바이스 동작 정책으로 변환하고 필요한 정상 동작으로부터의 이탈에 대해 경고할 수 있습니다.

## <a name="security-groups"></a>보안 그룹

보안 그룹을 사용하면 디바이스의 논리적 그룹을 정의하고 해당 보안 상태를 중앙 집중식으로 관리할 수 있습니다.

이러한 그룹은 특정 하드웨어가 있는 디바이스, 특정 위치에 배포된 디바이스 또는 특정 요구 사항에 적합한 다른 그룹을 나타낼 수 있습니다.

보안 그룹은 **SecurityGroup**이라는 디바이스 쌍 태그 속성으로 정의됩니다. IoT Hub의 각 IoT 솔루션에는 기본적으로 **default**라는 하나의 보안 그룹이 있습니다. 디바이스의 보안 그룹을 변경하려면 **SecurityGroup** 속성의 값을 변경합니다.

다음은 그 예입니다.

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

보안 그룹을 사용하여 디바이스를 논리적 범주로 그룹화합니다. 그룹이 만들어지면 가장 효과적인 엔드투엔드 IoT 보안 솔루션을 위해 해당 그룹을 원하는 사용자 지정 경고에 할당합니다.

## <a name="customize-an-alert"></a>경고 사용자 지정

1. IoT Hub를 엽니다.
1. **보안** 섹션에서 **사용자 지정 경고**를 클릭합니다.
1. 사용자 지정을 적용하려는 보안 그룹을 선택합니다.
1. **사용자 지정 경고 추가**를 클릭합니다.
1. 드롭다운 목록에서 사용자 지정 경고를 선택합니다.
1. 필요한 속성을 편집하고 **확인**을 클릭합니다.
1. **저장**을 클릭해야 합니다. 새 경고를 저장하지 않으면 나중에 IoT Hub를 닫을 때 해당 경고가 삭제됩니다.

## <a name="alerts-available-for-customization"></a>사용자 지정에 사용할 수 있는 경고

IoT용 Azure Security Center는 특정 요구 사항에 따라 사용자 지정을 할 수 있는 많은 수의 경고를 제공합니다. 각 경고가 수신되는 경우 경고 심각도, 데이터 원본, 설명 및 제안된 수정 단계는 [사용자 지정 가능한 경고 테이블](concept-customizable-security-alerts.md)을 검토합니다.

## <a name="next-steps"></a>다음 단계

보안 에이전트를 배포하는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [보안 에이전트 배포](how-to-deploy-agent.md)
