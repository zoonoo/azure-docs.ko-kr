---
title: C#에 대한 IoT 보안 에이전트 로컬 구성 파일에 대한 Azure 보안 센터 이해 | 마이크로 소프트 문서
description: IoT 보안 서비스를 위한 Azure 보안 센터, C#에 대한 보안 에이전트 로컬 구성 파일에 대해 자세히 알아봅니다.
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
ms.openlocfilehash: 0172ada68ffa652fb0c301c89238beca4f4ce2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664199"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>로컬 구성 파일 이해(C# 에이전트)


IoT 보안 에이전트용 Azure 보안 센터는 로컬 구성 파일의 구성을 사용합니다.

보안 에이전트는 에이전트가 시작될 때 구성 파일을 한 번 읽습니다. 로컬 구성 파일에 있는 구성에는 인증 구성 및 기타 에이전트 관련 구성이 모두 포함됩니다.

C# 보안 에이전트는 여러 구성 파일을 사용합니다.

- **General.config** - 에이전트 관련 구성.
- **Authentication.config** - 인증 관련 구성(인증 세부 정보 포함).
- **SecurityIotInterface.config** - IoT 관련 구성.

구성 파일에는 기본 구성이 포함되어 있습니다. 인증 구성은 에이전트 설치 중에 채워지며 에이전트를 다시 시작할 때 구성 파일이 변경됩니다. 

## <a name="configuration-file-location"></a>구성 파일 위치
Linux의 경우:
- 운영 체제 구성 파일은 에 있습니다. `/var/ASCIoTAgent`

Windows의 경우:
- 운영 체제 구성 파일은 보안 에이전트의 디렉터리 내에 있습니다. 

### <a name="generalconfig-configurations"></a>일반.구성 구성

| 구성 이름 | 가능한 값 | 세부 정보 | 
|:-----------|:---------------|:--------|
| 에이전트 ID | GUID | 에이전트 고유 식별자 |
| 읽기원격 구성 시간 설정 | TimeSpan | IoT Hub에서 원격 구성을 가져오는 기간입니다. 에이전트가 지정된 시간 내에 구성을 가져올 수 없는 경우 작업이 시간 지정됩니다.|
| 스케줄간격 | TimeSpan | 내부 스케줄러 간격입니다. |
| 프로듀서 간격 | TimeSpan | 이벤트 생산자 작업자 간격입니다. |
| 소비자 간격 | TimeSpan | 이벤트 소비자 작업자 간격입니다. |
| 높은 우선 순위큐크기백분율 | 0 < 번호 < 1 | 우선 순위가 높은 메시지에 전용된 총 캐시 의 일부입니다. |
| logLevel | "꺼져", "치명적", "오류", "경고", "정보", "디버그"  | 이 심각도 를 초과하는 로그 메시지는 디버그 콘솔(Linux의 Syslog)에 기록됩니다. |
| 파일 로그 레벨 |  "꺼져", "치명적", "오류", "경고", "정보", "디버그"| 이 심각도 를 초과하는 로그 메시지는 파일에 기록됩니다(Linux의 Syslog). |
| 진단적 세부정보 수준 | "없음", "일부", "모두", | 진단 이벤트의 자세한 수준입니다. 없음 - 진단 이벤트가 전송되지 않고 일부 - 중요도가 높은 진단 이벤트만 전송되고 모든 로그는 진단 이벤트로 전송됩니다. |
| Logfilepath | 파일 경로 | fileLogLevel이 꺼져 > 경우 이 파일에 로그가 기록됩니다. |
| 기본이벤트 우선 순위 | "높음", "낮음", "끄기" | 기본 이벤트 우선 순위입니다. |

### <a name="generalconfig-example"></a>일반.구성 예제
```XML
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>인증.구성

| 구성 이름 | 가능한 값 | 세부 정보 | 
|:-----------|:---------------|:--------|
| moduleName | 문자열 | 보안 모듈 ID의 이름입니다. 이 이름은 장치의 모듈 ID 이름과 일치해야 합니다. |
| deviceId | 문자열 | 장치의 ID(Azure IoT Hub에 등록된 대로). || 스케줄간격 | 시간 범위 문자열 | 내부 스케줄러 간격입니다. |
| 게이트웨이호스트이름 | 문자열 | Azure Iot Hub의 호스트 이름입니다. 일반적으로 내 허브>.azure-devices.net < |
| filePath | 문자열 - 파일 경로 | 인증 비밀이 포함된 파일에 대한 경로입니다.|
| type | "대칭 키", "자체 서명 인증서" | 인증에 대한 사용자 보안 입니다. 사용자 보안이 대칭 키인 경우 *대칭 키를* 선택하고 비밀이 자체 *서명된 인증서인* 경우 자체 서명된 인증서를 선택합니다. |
| ID | "DPS", "모듈", "장치" | 인증 ID - DPS를 통해 인증하는 경우 DPS, 모듈 자격 증명을 사용하여 인증이 이루어지는 경우 모듈 또는 장치 자격 증명을 사용하여 인증이 이루어지는 경우 장치 입니다.
| 인증서위치Kind |  "로컬 파일", "저장" | LocalFile 인증서가 파일에 저장되는 경우 인증서가 인증서 저장소에 있는 경우 저장합니다. |
| idScope | 문자열 | DPS의 ID 범위 |
| 등록ID | 문자열  | DPS 장치 등록 ID. |
|

### <a name="authenticationconfig-example"></a>인증.구성 예제
```XML
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```
### <a name="securityiotinterfaceconfig"></a>시큐리티오토인터페이스.구성

| 구성 이름 | 가능한 값 | 세부 정보 | 
|:-----------|:---------------|:--------|
| 전송 유형 | "앰프" "Mqtt" | IoT 허브 전송 유형입니다. |
|

### <a name="securityiotinterfaceconfig-example"></a>보안IotInterface.config 예제
```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>다음 단계
- IoT 서비스를 위한 Azure 보안 센터 [개요](overview.md) 읽기
- IoT [아키텍처를](architecture.md) 위한 Azure 보안 센터에 대해 자세히 알아보기
- IoT [서비스에](quickstart-onboard-iot-hub.md) 대한 Azure 보안 센터 사용
- IoT 서비스 [FAQ를](resources-frequently-asked-questions.md) 위한 Azure 보안 센터 읽기
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [권장 사항](concept-recommendations.md) 이해
- 보안 [경고](concept-security-alerts.md) 이해