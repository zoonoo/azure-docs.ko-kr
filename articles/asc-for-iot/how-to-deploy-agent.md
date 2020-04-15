---
title: 보안 에이전트 선택 및 배포
description: IoT 디바이스에서 IoT 보안 에이전트를 위한 Azure 보안 센터를 선택하고 배포하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: c3b514c79be87cd136375b4853226426965f4185
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311210"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>IoT 장치에서 보안 에이전트 선택 및 배포

IoT용 Azure 보안 센터는 IoT 장치에서 데이터를 모니터링하고 수집하는 보안 에이전트에 대한 참조 아키텍처를 제공합니다.
자세한 내용은 [보안 에이전트 참조 아키텍처를](security-agent-architecture.md)참조하십시오.

에이전트는 오픈 소스 프로젝트로 개발되었으며 두 가지 맛으로 제공됩니다. <br> [C](https://aka.ms/iot-security-github-c), 및 [C #](https://aka.ms/iot-security-github-cs).

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 보안 에이전트 맛 비교
> * 지원되는 에이전트 플랫폼 검색
> * 솔루션에 적합한 에이전트 맛 선택

## <a name="understand-security-agent-options"></a>보안 에이전트 옵션 이해

IoT 보안 에이전트 를 위한 모든 Azure 보안 센터는 동일한 기능 집합을 제공하며 유사한 구성 옵션을 지원합니다.

C 기반 보안 에이전트는 메모리 사용 공간이 적으며 사용 가능한 리소스가 적은 장치에 적합합니다.

|     | C 기반 보안 에이전트 | C#기반 보안 에이전트 |
| --- | ----------- | --------- |
| 오픈 소스 | [GitHub에서](https://aka.ms/iot-security-github-cs) [MIT 라이선스로](https://en.wikipedia.org/wiki/MIT_License) 사용 가능 | [GitHub에서](https://aka.ms/iot-security-github-c) [MIT 라이선스로](https://en.wikipedia.org/wiki/MIT_License) 사용 가능 |
| 개발 언어    | C | C# |
| 지원되는 Windows 플랫폼? | 예 | 예 |
| 윈도우 전제 조건 | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| 지원되는 리눅스 플랫폼? | 예, x64 및 x86 | 예, x64전용 |
| Linux 필수 구성 요소 | libunwind8, libcurl3, uuid-런타임, 감사, audispd-플러그인 | libunwind8, libcurl3, uuid-runtime, 감사, audispd 플러그인, sudo, netstat, iptables |
| 디스크 설치 공간 | 10.5 MB | 90 MB |
| 메모리 사용 공간(평균) | 5.5 MB | 33 MB |
| IoT 허브에 대한 [인증](concept-security-agent-authentication-methods.md) | 예 | 예 |
| 보안 데이터 [수집](how-to-agent-configuration.md#supported-security-events) | 예 | 예 |
| 이벤트 집계 | 예 | 예 |
| [보안 모듈 트윈을](concept-security-module.md) 통한 원격 구성 | 예 | 예 |
|

## <a name="security-agent-installation-guidelines"></a>보안 에이전트 설치 지침

**Windows용**: 설치 SecurityAgent.ps1 스크립트는 관리자 PowerShell 창에서 실행되어야 합니다.

**Linux의**경우 : InstallSecurityAgent.sh 수퍼 유저로 실행되어야합니다. 설치 명령을 "sudo"로 접두사하는 것이 좋습니다.

## <a name="choose-an-agent-flavor"></a>에이전트 맛 선택

IoT 장치에 대한 다음 질문에 답하여 올바른 에이전트를 선택하십시오.

- _Windows 서버_ 또는 _Windows IoT 코어를_사용하고 있습니까?

    [Windows에 대한 C#기반 보안 에이전트를 배포합니다.](how-to-deploy-windows-cs.md)

- x86 아키텍처를 사용하여 Linux 배포판을 사용하고 있습니까?

    [Linux용 C 기반 보안 에이전트를 배포합니다.](how-to-deploy-linux-c.md)

- x64 아키텍처를 사용하여 Linux 배포판을 사용하고 있습니까?

    두 제 약 제 맛을 사용할 수 있습니다. <br>
    [Linux용 C 기반 보안 에이전트를 배포하거나 Linux용](how-to-deploy-linux-c.md) [C#기반 보안 에이전트를 배포합니다.](how-to-deploy-linux-cs.md)

두 에이전트 맛모두 동일한 기능 세트를 제공하며 유사한 구성 옵션을 지원합니다.
자세한 내용은 [보안 에이전트 비교를](how-to-deploy-agent.md#understand-security-agent-options) 참조하십시오.

## <a name="supported-platforms"></a>지원 플랫폼

다음 목록에는 현재 지원되는 모든 플랫폼이 포함됩니다.

|IoT 에이전트용 Azure 보안 센터 |운영 체제 |Architecture |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|윈도우 10 IoT 코어, 빌드 17763    |x64|
|

## <a name="next-steps"></a>다음 단계

구성 옵션에 대해 자세히 알아보려면 에이전트 구성에 대한 방법 가이드를 계속 사용하십시오.
> [!div class="nextstepaction"]
> [상담원 구성 안내 방법](./how-to-agent-configuration.md)
