---
title: 설치 하 여 Linux 배포 가이드 C# 의 ASC IoT 미리 보기에 대 한 에이전트 | Microsoft Docs
description: 32 비트 및 64 비트 Linux에서 IoT 에이전트용 ASC를 설치 하는 방법에 알아봅니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: d6b4e6065b0ef198ad583b3760124730e658fe0b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619911"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>IoT 용 ASC를 배포 C#-Linux에 대 한 보안 에이전트 기반

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 가이드에서는 설치 및 IoT에 대 한 ASC를 배포 하는 방법을 설명 C#-Linux에서 보안 에이전트를 기반으로 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>필수 조건

다른 플랫폼 및 에이전트 버전에 대 한 참조 [적합 한 보안 에이전트 선택](how-to-deploy-agent.md)합니다.

1. 보안 에이전트를 배포 하려면 설치 하려는 컴퓨터의 로컬 관리자 권한이 필요 합니다. 

1. [보안 모듈을 만드는](quickstart-create-security-twin.md) 장치에 대 한 합니다.

## <a name="installation"></a>설치 

보안 에이전트를 배포 하려면 다음을 수행 합니다.

1. 컴퓨터에 최신 버전을 다운로드 [Github](https://aka.ms/iot-security-github-cs)합니다.

1. 패키지의 콘텐츠를 추출 하 고 이동 합니다 _/설치_ 폴더입니다.

1. 에 실행 권한을 추가 합니다 **InstallSecurityAgent 스크립트** 실행 하 여 `chmod +x InstallSecurityAgent.sh` 

1. 다음을 실행 합니다. 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   참조 [인증을 구성 하는 방법을](concept-security-agent-authentication-methods.md) 인증 매개 변수에 대 한 자세한 내용은 합니다.

이 스크립트는 다음을 수행합니다.

- 필수 구성 요소를 설치 합니다.

- (사용 하 여 대화형 로그인을 사용 하지 않도록 설정) 서비스 사용자를 추가 합니다.

- 로 에이전트를 설치를 **디먼** -이 장치를 사용 하 여 가정 **systemd** 서비스 관리에 대 한 합니다.

- 구성 **sudoers** 루트로 특정 태스크를 수행 하려면 에이전트를 허용 하도록 합니다.

- 제공 된 인증 매개 변수를 사용 하 여 에이전트를 구성합니다.


추가적인 도움말이 필요한 경우 스크립트를 실행 합니다. – help 매개 변수를 사용 하 여: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거 하려면 – u 매개 변수를 사용 하 여 스크립트를 실행 합니다. `./InstallSecurityAgent.sh -u`합니다. 

> [!NOTE]
> 제거할 설치 중 설치 된 모든 누락 된 필수 구성 요소를 제거 하지 않습니다.

## <a name="troubleshooting"></a>문제 해결  

1. 실행 하 여 배포 상태를 확인 합니다.

    `systemctl status ASCIoTAgent.service`

2. 로깅을 사용 하도록 설정 합니다.  
   에이전트가 시작 되지 않으면, 자세한 내용을 보려면 로깅을 설정 합니다.

   로깅 켜기:

   1. 모든 Linux 편집기에서 편집을 위해 구성 파일을 엽니다.

        `vi /var/ASCIoTAgent/General.config`

   1. 다음 값을 편집 합니다. 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       합니다 **logFilePath** 값은 구성할 수 있습니다. 

       > [!NOTE]
       > 로깅을 설정 하는 것이 좋습니다 **해제** 완료 되 면 문제를 해결 합니다. 로깅 두면 **에서** 증가 로그 파일 크기 및 데이터 사용 합니다.

   1. 실행 하 여 에이전트를 다시 시작 합니다.

       `systemctl restart ASCIoTAgent.service`

   1. 오류에 대 한 자세한 내용은 로그 파일을 봅니다.  

       로그 파일 위치는: `/var/ASCIoTAgent/IotAgentLog.log`

       에 대해 선택한 이름에 따라 파일 위치 경로 변경 합니다 **logFilePath** 에서 2 단계. 

## <a name="next-steps"></a>다음 단계

- IoT 서비스에 대 한 ASC 읽기 [개요](overview.md)
- 에 대해 자세히 알아보려면 ASC IoT 용 [아키텍처](architecture.md)
- 사용 하도록 설정 된 [서비스](quickstart-onboard-iot-hub.md)
- 읽기는 [FAQ](resources-frequently-asked-questions.md)
- 이해 [경고](concept-security-alerts.md)