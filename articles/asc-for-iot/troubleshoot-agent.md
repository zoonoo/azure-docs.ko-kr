---
title: IoT Linux 보안 에이전트 문제 해결 가이드를 위한 Azure 보안 센터 | 마이크로 소프트 문서
description: Linux용 IoT 보안 에이전트용 Azure 보안 센터와 함께 작업하는 문제 해결
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600567"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>보안 에이전트 문제 해결 가이드(Linux)

이 문서에서는 보안 에이전트 시작 프로세스에서 잠재적인 문제를 해결 하는 방법을 설명 합니다.

IoT 에이전트용 Azure 보안 센터는 설치 직후 자체 시작됩니다. 에이전트 시작 프로세스에는 로컬 구성 읽기, Azure IoT Hub 연결 및 원격 쌍 구성 검색이 포함됩니다. 이러한 단계 중 하나에서 실패하면 보안 에이전트가 실패할 수 있습니다.

이 문제 해결 가이드에서는 다음 방법을 알아봅니다.
> [!div class="checklist"]
> * 보안 에이전트가 실행 중인지 확인
> * 보안 에이전트 오류 받기
> * 보안 에이전트 오류 이해 및 해결 

## <a name="validate-if-the-security-agent-is-running"></a>보안 에이전트가 실행 중인지 확인

1. 보안 에이전트가 실행 중임을 확인하려면 에이전트를 설치한 후 몇 분 간 기다렸다가 다음 명령을 실행합니다. 
     <br>

    **C 에이전트**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C# 에이전트**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. 명령이 빈 줄을 반환하는 경우 보안 에이전트가 성공적으로 시작할 수 없습니다.    

## <a name="force-stop-the-security-agent"></a>보안 에이전트를 강제로 중지 
보안 에이전트를 시작할 수 없는 경우 다음 명령을 사용하여 에이전트를 중지한 다음 아래 오류 표로 계속하십시오.

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>보안 에이전트 오류 받기
1. 다음 명령을 실행하여 보안 에이전트 오류를 검색합니다.
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. Get 보안 에이전트 오류 명령은 IoT 에이전트용 Azure 보안 센터에서 만든 모든 로그를 검색합니다. 다음 표를 사용하여 오류를 이해하고 수정에 대한 올바른 단계를 수행합니다. 

> [!Note]
> 오류 로그는 시간순으로 표시됩니다. 각 오류의 타임스탬프를 기록하여 수정에 도움을 줍니다. 

## <a name="restart-the-agent"></a>에이전트 다시 시작

1. 보안 에이전트 오류를 찾아 수정한 후 다음 명령을 실행하여 에이전트를 다시 시작해 보십시오. 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. 이전 프로세스를 반복하여 중지를 검색하고 에이전트가 시작 프로세스에 계속 실패하는 경우 오류를 검색합니다. 

## <a name="understand-security-agent-errors"></a>보안 에이전트 오류 이해

대부분의 보안 에이전트 오류는 다음과 같은 형식으로 표시됩니다. 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| 오류 코드 | 오류 하위 코드 | 오류 세부 정보 | C 수정 | C 수정 # |
|:-----------|:---------------|:--------|:------------|:------------|
| 로컬 구성 | 누락된 구성 | 로컬 구성 파일에 구성이 없습니다. 오류 메시지는 누락된 키를 명시해야 합니다. | /var/LocalConfiguration.json 파일에 누락된 키를 추가하고 자세한 내용은 [cs-localconfig-참조를](azure-iot-security-local-configuration-c.md) 참조하십시오.| General.config 파일에 누락된 키를 추가하고 자세한 내용은 [c#-localconfig-참조를](azure-iot-security-local-configuration-csharp.md) 참조하십시오. |
| 로컬 구성 | 캔트 구문 분석 구성 | 구성 값을 구문 분석할 수 없습니다. 오류 메시지는 구문 분석할 수 없는 키를 명시해야 합니다. 값이 예상된 형식에 없거나 값이 범위를 벗어났기 때문에 구성 값을 구문 분석할 수 없습니다. | /var/LocalConfiguration.json 파일에서 키 값을 수정하여 LocalConfiguration 스키마와 일치시키면 자세한 내용은 [c#-localconfig-참조를](azure-iot-security-local-configuration-csharp.md) 참조하십시오. |  스키마와 일치되도록 General.config 파일에서 키 값을 수정하고 자세한 내용은 [cs-localconfig-참조를](azure-iot-security-local-configuration-c.md) 참조하십시오.|
| 로컬 구성 | 파일 형식 | 구성 파일을 구문 분석하지 못했습니다. | 구성 파일이 손상되어 에이전트를 다운로드한 후 다시 설치합니다. | |
| 원격 구성 | 시간 제한 | 에이전트는 시간 시간 시간 동안 azureiotsecurity 모듈 트윈을 가져올 수 없습니다. | 인증 구성이 올바른지 확인하고 다시 시도하십시오. | 에이전트는 시간 시간 대 기간 내에 azureiotsecurity 모듈 쌍 가져올 수 없습니다. | 인증 구성이 올바른지 확인하고 다시 시도하십시오. |
| 인증 | 파일이 없습니다. | 지정된 경로에 있는 파일이 없습니다. | 파일이 지정된 경로에 있는지 확인하거나 **LocalConfiguration.json** 파일로 이동하여 **FilePath** 구성을 변경합니다. | 파일이 지정된 경로에 있는지 확인하거나 **Authentication.config** 파일로 이동하여 **filePath** 구성을 변경합니다.|
| 인증 | 파일 사용 권한 | 에이전트에 파일을 열 수 있는 충분한 권한이 없습니다. | **asciotagent** 사용자가 지정된 경로의 파일에 대한 읽기 권한을 부여합니다. | 파일에 액세스할 수 있는지 확인합니다. |
| 인증 | 파일 형식 | 지정된 파일이 올바른 형식이 아닙니다. | 파일이 올바른 형식인지 확인합니다. 지원되는 파일 형식은 .pfx 및 .pem입니다. | 파일이 유효한 인증서 파일인지 확인합니다. |
| 인증 | 권한 없음 | 에이전트는 지정된 자격 증명을 사용하여 IoT Hub에 대해 인증할 수 없습니다. | LocalConfiguration 파일에서 인증 구성의 유효성을 검사하고 인증 구성을 살펴보고 모든 세부 정보가 올바른지 확인하고 파일의 비밀이 인증된 ID와 일치하는지 확인합니다. | Authentication.config에서 인증 구성의 유효성을 검사하고 인증 구성을 살펴보고 모든 세부 정보가 올바른지 확인한 다음 파일의 비밀이 인증된 ID와 일치하는지 확인합니다.
| 인증 | 찾을 수 없음 | 장치/모듈을 찾았습니다. | 인증 구성 유효성 검사 - 호스트 이름이 올바른지, 장치가 IoT Hub에 있고 azureiotsecurity 트윈 모듈이 있는지 확인합니다. |  인증 구성 유효성 검사 - 호스트 이름이 올바른지, 장치가 IoT Hub에 있고 azureiotsecurity 트윈 모듈이 있는지 확인합니다. |
| 인증 | 누락된 구성 | *Authentication.config* 파일에 구성이 없습니다. 오류 메시지는 누락된 키를 명시해야 합니다. | 누락된 키를 *LocalConfiguration.json* 파일에 추가합니다.| *Authentication.config* 파일에 누락된 키를 추가하려면 [c#-localconfig-참조를](azure-iot-security-local-configuration-csharp.md) 참조하십시오. |
| 인증 | 캔트 구문 분석 구성 | 구성 값을 구문 분석할 수 없습니다. 오류 메시지는 구문 분석할 수 없는 키를 명시해야 합니다. 값이 예상된 형식이 아니거나 값이 범위를 벗어났기 때문에 구성 값을 구문 분석할 수 없습니다. |**LocalConfiguration.json** 파일에서 키 값을 수정합니다. |**스키마와 일치하도록 Authentication.config** 파일에서 키 값을 수정하고 자세한 내용은 [cs-localconfig-참조를](azure-iot-security-local-configuration-c.md) 참조하십시오.|
|

## <a name="restart-the-agent"></a>에이전트 다시 시작
1. 보안 에이전트 오류를 찾아 수정한 후 다음 명령을 실행하여 에이전트를 다시 시작합니다.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. 필요한 경우 이전 프로세스를 반복하여 에이전트를 강제로 중지하고 에이전트가 시작 프로세스에 계속 실패할 경우 오류를 검색합니다. 

## <a name="next-steps"></a>다음 단계
- IoT 서비스를 위한 Azure 보안 센터 [개요](overview.md) 읽기
- IoT [아키텍처를](architecture.md) 위한 Azure 보안 센터에 대해 자세히 알아보기
- IoT [서비스에](quickstart-onboard-iot-hub.md) 대한 Azure 보안 센터 사용
- IoT 서비스 [FAQ를](resources-frequently-asked-questions.md) 위한 Azure 보안 센터 읽기
- [원시 보안 데이터](how-to-security-data-access.md)에 액세스하는 방법을 알아봅니다.
- [권장 사항](concept-recommendations.md) 이해
- 보안 [경고](concept-security-alerts.md) 이해
