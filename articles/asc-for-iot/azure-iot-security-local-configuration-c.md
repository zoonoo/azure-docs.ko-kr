---
title: 보안 에이전트 로컬 구성(C)
description: C에 대한 에이전트 로컬 구성에 대한 Azure 보안 센터에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: cd344b9bebb69af210c482f46af6b2dd7edf7816
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311700"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>LocalConfiguration. json 파일 이해 - C 에이전트

IoT 보안 에이전트용 Azure 보안 센터는 로컬 구성 파일의 구성을 사용합니다.
보안 에이전트는 에이전트 시작 시 구성을 한 번 읽습니다.
로컬 구성 파일에 있는 구성에는 인증 구성 및 기타 에이전트 관련 구성이 포함되어 있습니다.
파일에는 JSON 표기형의 "키-값" 쌍의 구성이 포함되어 있으며 에이전트가 설치될 때 구성이 채워집니다.

기본적으로 파일은 다음과 같이 있습니다.

에이전트를 다시 시작할 때 구성 파일의 변경 내용이 적용됩니다.

## <a name="security-agent-configurations-for-c"></a>C에 대한 보안 에이전트 구성

| 구성 이름 | 가능한 값 | 세부 정보 |
|:-----------|:---------------|:--------|
| AgentId | GUID | 에이전트 고유 식별자 |
| 트리거된 이벤트 간격 | ISO8601 문자열 | 트리거된 이벤트 컬렉션에 대한 스케줄러 간격 |
| ConnectionTimeout | ISO8601 문자열 | IoThub에 연결하기 전의 기간이 시간 중지됩니다. |
| 인증 | JsonObject | 인증 구성. 이 개체에는 IoTHub에 대한 인증에 필요한 모든 정보가 포함되어 있습니다. |
| ID | "DPS", "보안 모듈", "장치" | 인증 ID - DPS를 통해 인증이 이루어지는 경우 DPS, 보안 모듈을 통해 인증이 이루어지는 경우 보안 모듈 자격 증명 또는 장치 자격 증명으로 인증이 이루어지는 경우 |
| 인증 방법 | "SasToken", "자체 서명 인증서" | 인증에 대한 사용자 비밀 - 사용 비밀이 대칭 키인 경우 SasToken을 선택하고 비밀이 자체 서명된 인증서인 경우 자체 서명된 인증서를 선택합니다.  |
| FilePath | 파일 경로(문자열) | 인증 비밀이 포함된 파일 경로 |
| HostName | 문자열 | azure iot 허브의 호스트 이름입니다. 일반적으로 내 허브> <.azure-devices.net |
| deviceId | 문자열 | 장치의 ID(Azure IoT Hub에 등록된 대로) |
| DPS | JsonObject | DPS 관련 구성 |
| ID 스코프 | 문자열 | DPS의 ID 범위 |
| RegistrationId | 문자열  | DPS 장치 등록 ID |
| 로깅 | JsonObject | 에이전트 로거 관련 구성 |
| 시스템 로거최소 심각도 | 0 <= 숫자 <= 4 | 이 심각도 를 초과하는 로그 메시지는 /var/log/syslog(0은 가장 낮은 심각도)로 기록됩니다. |
| 진단이벤트최소심각도 | 0 <= 숫자 <= 4 | 로그 메시지가 같고 이 심각도 를 초과하는 메시지가 진단 이벤트로 전송됩니다(0은 심각도가 가장 낮음). |

## <a name="security-agent-configurations-code-example"></a>보안 에이전트 구성 코드 예제

```JSON
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- IoT 서비스를 위한 Azure 보안 센터 [개요](overview.md) 읽기
- IoT [아키텍처를](architecture.md) 위한 Azure 보안 센터에 대해 자세히 알아보기
- IoT [서비스에](quickstart-onboard-iot-hub.md) 대한 Azure 보안 센터 사용
- IoT 서비스 [FAQ를](resources-frequently-asked-questions.md) 위한 Azure 보안 센터 읽기
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [권장 사항](concept-recommendations.md) 이해
- 보안 [경고](concept-security-alerts.md) 이해
