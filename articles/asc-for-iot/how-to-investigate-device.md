---
title: IoT 장치 조사 가이드 미리 보기에 대 한 azure Security Center | Microsoft Docs
description: 이 방법 가이드 IoT 용 Azure Security Center를 사용 하 여 Log Analytics를 사용 하 여 의심 스러운 IoT 장치를 조사 하는 방법에 설명 합니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 6097954e09d5fd62c45f59b009d974d277bafc57
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755236"
---
# <a name="investigate-a-suspicious-iot-device"></a>의심 스러운 IoT 장치를 조사 합니다.

> [!IMPORTANT]
> IoT 용 azure Security Center는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전을 서비스 수준 계약 없이 제공 됩니다 및 프로덕션 워크 로드에 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IoT 서비스 경고 및 증명 정보에 대 한 azure Security Center (ASC) IoT 장치에서 의심 스러운 활동 징후 존재 장치가 노출 되는 하는 경우 참여 것으로 의심 되는 경우 명확한 징후를 제공 합니다. 

이 가이드에서는 조직에 잠재적인 위험을 확인 하 고, 수정 및 나중에 유사한 공격을 방지 하는 최선의 방법을 검색 하는 방법을 결정 하는 데 제공 조사 제안 사항을 사용 합니다.  

> [!div class="checklist"]
> * 장치 데이터 찾기
> * Kql 쿼리를 사용 하 여 조사 합니다.


## <a name="how-can-i-access-my-data"></a>내 데이터를 액세스 하려면 어떻게 해야 합니까?

기본적으로 ASC IoT에 대 한 Log Analytics 작업 영역에서 보안 경고 및 권장 사항을 저장합니다. 원시 보안 데이터를 저장할 수도 있습니다.

찾으려는 데이터 저장소에 대 한 Log Analytics 작업 영역:

1. IoT hub를 열으십시오 
1. 클릭 **Security**을 선택한 후 **설정**합니다.
1. Log Analytics 작업 영역 구성 세부 정보를 변경 합니다. 
1. **저장**을 클릭합니다. 

구성을 다음 Log Analytics 작업 영역에 저장 된 데이터에 액세스 하려면 다음을 수행 합니다.

1. 선택 하 고 IoT Hub에 IoT 경고에 대 한 ASC를 클릭 합니다. 
1. 클릭 **추가로 조사**합니다. 
1. 선택 **여기를 클릭 하 고 DeviceId 열을 확인이 경고는 장치를 확인 하려면**합니다.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>의심 스러운 IoT 장치에 대 한 조사 단계

Insights 및 IoT 장치에 대 한 원시 데이터에 액세스 하려면 Log Analytics 작업 영역으로 이동 [데이터에 액세스 하려면](#how-can-i-access-my-data)합니다.

확인 하 고 다음 세부 정보 및 다음 kql 쿼리를 사용 하 여 활동에 대 한 장치 데이터를 조사 합니다.

### <a name="related-alerts"></a>관련된 경고

다른 경고 된 경우 확인 하려면 다음 kql 쿼리를 동시 주위 트리거됩니다.

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>액세스 권한이 있는 사용자

이 장치에서는 다음 kql 쿼리에 액세스할 수 있는 사용자에 알아보려면: 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
  ~~~
이 데이터를 사용 하 여 검색. 
  1. 장치에 액세스할 수 있는 사용자?
  2. 액세스 권한이 있는 사용자를 예상 대로 사용 권한 수준을 있습니까? 

### <a name="open-ports"></a>포트 열기

장치에서 포트에서에서 현재 사용 중인 또는 사용한 아웃 찾으려면 다음 kql 쿼리를 사용 합니다. 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
  ~~~

    Use this data to discover:
  1. 수신 소켓 장치에서 현재 활성 상태 인가요?
  2. 현재 활성화 되어 있는 수신 대기 소켓을 허용 합니다.
  3. 장치에 연결 된 모든 의심 스러운 원격 주소 있습니까?

### <a name="user-logins"></a>사용자 로그인

장치에 로그인 하는 사용자 확인 하려면 다음 kql 쿼리를 사용 합니다. 
 
  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
  ~~~

    Use the query results to discover:
  1. 사용자는 장치에 로그인?
  2. 사용자 로그인에 로그인 할는?
  3. 예상 된 또는 예기치 않은 IP 주소에서 로그인 하는 사용자 연결 않은?
  
### <a name="process-list"></a>프로세스 목록

프로세스 목록을 확인 하려면은 예상 되는 사용으로 다음 kql 쿼리입니다. 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
  ~~~

    Use the query results to discover:

  1. 장치에서 실행 되는 모든 의심 스러운 프로세스 있었습니까?
  2. 적절 한 사용자가 프로세스 실행 된 됩니까?
  3. 모든 명령줄 실행 올바르고 예상 인수 포함 않은?

## <a name="next-steps"></a>다음 단계
장치를 조사 하 고 위험을 보다 잘 이해 하기을 고려 하려는 [사용자 지정 경고를 구성](quickstart-create-custom-alerts.md) IoT 솔루션 보안 태세 개선 하기 위해. 장치 에이전트 없는 것이 좋습니다 [보안 에이전트 배포](how-to-deploy-agent.md) 하거나 [기존 장치 에이전트의 구성을 변경](how-to-agent-configuration.md) 결과 개선 하기 위해. 