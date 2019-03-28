---
title: ASC에서 IoT 모듈 쌍에 대 한 ASC IoT 미리 보기에 대 한 수정 지침 하는 방법 | Microsoft Docs
description: IoT 용 ASC에 대 한 IoT 보안 모듈 쌍에 대 한 ASC를 수정 하는 방법에 알아봅니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541949"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>ASC IoT 모듈 쌍에 대 한 수정

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 기존 구성을 수정 하는 방법에 설명 **AzureIoTSecurity 모듈 쌍** 기존 장치에 대 한 합니다. 

참조 [는 ASC IoT 모듈에 대 한 만들기](quickstart-create-security-twin.md) 새 장치에 대 한 새 보안 모듈을 확인 하는 방법을 알아보려면.  

## <a name="modification-considerations"></a>수정 고려 사항

> [!IMPORTANT]
> 쌍 구성에서 각 구성에는 기본 구성을 재정의합니다. 특정 구성 인 쌍 구성에 더 이상 기본 구성이 사용 됩니다. 

## <a name="configuration-schema-and-validation"></a>구성 스키마 및 유효성 검사 

이 대 한 에이전트 구성의 유효성을 검사 해야 [스키마](https://github.com/Azure/asc-for-iot/schema/security_module_twin)합니다. 구성 개체의 스키마와 일치 하지 않으면 에이전트 시작 되지 됩니다.

 
에이전트가 실행 되는 동안 구성 개체 (구성 스키마가 일치 하지 않습니다) 유효 하지 않은 구성으로 변경 되 면 하는 경우 에이전트는 잘못 된 구성을 무시 하 고 현재 구성을 사용 하 여 계속 됩니다. 

## <a name="edit-a-property"></a>속성 편집  

AzureIoTSecurity 모듈 쌍의 에이전트 구성 개체 내에서 모든 사용자 지정 속성을 설정 합니다. 

속성을 설정 하려면 속성 키를 원하는 값을 사용 하 여 구성 개체에 추가 합니다. 기본 속성 값을 사용 하려면 구성 개체에서 속성을 제거 합니다.

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>properties 
다음 표에서 모든 IoT 에이전트용 ASC를 제어 하는 구성 가능한 속성을 포함 합니다. 
          

| 이름| 상태 | 유효한 값| 기본값| 설명 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|필수: false |유효한 값:  ISO 8601 형식으로 기간 |기본값: PT7M |최대 시간 우선 순위가 높은 메시지 보다 먼저 전송 됩니다.|
|lowPriorityMessageFrequency |필수: false|유효한 값:  ISO 8601 형식으로 기간 |기본값: PT5H |최대 시간 우선 순위가 낮은 메시지 보다 먼저 전송 됩니다.| 
|snapshotFrequency |필요: false|ISO 8601 형식으로 올바른 값: 기간 |기본값은 PT13H |장치 상태 스냅숏 만들기에 대 한 시간 간격입니다.| 
|maxLocalCacheSizeInBytes |필수: false |유효한 값:  |기본값: 8192 보다 큰 2560000 | 최대 저장소 (바이트) 에이전트의 메시지가 캐시에 대 한 허용 합니다. 메시지 전송 되기 전에 장치에 메시지를 저장 하도록 허용 하는 공간의 최대 크기입니다.| 
|maxMessageSizeInBytes |필수: false |유효한 값:  8192 보다 작은 262144 보다 큰 양의 숫자 |기본값: 204800 |최대 허용 크기에 클라우드 메시지 에이전트. 이 설정은 각 메시지의 최대 데이터 양을 제어 합니다. |
|eventPriority${EventName} |필수: false |유효한 값:  높음, 낮음, 해제 |기본값: |모든 에이전트의 우선 순위는 이벤트를 생성 합니다. | 
|

### <a name="events"></a>이벤트

다음과 같은 이벤트는 이벤트를 ASC IoT 에이전트에 대 한 모든 장치에서 수집할 수 있습니다. 이러한 이벤트는 수집 되 고 솔루션의 우선 순위를 결정 하도록 AzureIotSecurity 모듈 쌍을 사용 합니다. 
 
|이벤트 이름| PropertyName | 기본값| 스냅숏 이벤트| 자세한 상태 정보  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|진단 이벤트|eventPriorityDiagnostic| 꺼짐| False| 에이전트 관련 진단 이벤트입니다. 이 이벤트를 사용 하 여 자세한 정보 로깅에 대 한 합니다.| 
구성 오류 |eventPriorityConfigurationError |낮음 |False |에이전트는 구성을 구문 분석 하지 못했습니다. 스키마에 대 한 구성을 확인 합니다.| 
|삭제 된 이벤트 통계 |eventPriorityDroppedEventsStatistics |낮음 |True|에이전트 관련 이벤트 통계. |
|메시지 통계|eventPriorityMessageStatistics |낮음 |True |에이전트 관련 메시지 통계입니다. |
|연결 된 하드웨어|eventPriorityConnectedHardware |낮음 |True |스냅숏의 모든 하드웨어 장치에 연결 합니다.|
|수신 대기 포트|eventPriorityListeningPorts |높음 |True |장치에서 열려 있는 모든 수신 포트의 스냅숏.|
| 프로세스 만들기 |eventPriorityProcessCreate |낮음 |False |감사 프로세스는 장치에서 생성 합니다.|
| 프로세스 종료|eventPriorityProcessTerminate |낮음 |False |감사는 장치에서 종료를 처리합니다.| 
 시스템 정보 |eventPrioritySystemInformation |낮음 |True |OS 또는 CPU 같은 시스템 정보의 스냅숏.|
|로컬 사용자| eventPriorityLocalUsers |높음 |True|시스템 내에서 등록 된 로컬 사용자는 스냅숏. |
|로그인|  eventPriorityLogin |높음|False|장치 (로컬 및 원격 로그인)에 로그인 이벤트를 감사합니다.|
|연결 만들기 |eventPriorityConnectionCreate|낮음|False|장치에서 만들어지고 TCP 연결을 감사 합니다. |
|방화벽 구성| eventPriorityFirewallConfiguration|낮음|True|장치 방화벽 구성 (방화벽 규칙)의 스냅숏. |
|OS 기준| eventPriorityOSBaseline| 낮음|True|장치 OS 기준의 스냅숏을 확인 합니다.| 
|


## <a name="next-steps"></a>다음 단계

- ASC IoT에 대 한 읽기 [개요](overview.md)
- IoT에 대 한 ASC 알아봅니다 [아키텍처](architecture.md)
- 이해 및 탐색 [ASC IoT 경고에 대 한](concept-security-alerts.md)
- 에 액세스 하는 방법을 알아봅니다 프로그램 [보안 데이터](how-to-security-data-access.md)
