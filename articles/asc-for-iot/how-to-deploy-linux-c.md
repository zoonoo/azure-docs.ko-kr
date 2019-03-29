---
title: 설치 하 여 ASC의 IoT 에이전트 미리 보기에 대 한 Linux C 에이전트 배포 가이드 | Microsoft Docs
description: 32 비트 및 64 비트 Linux에서 IoT 에이전트용 ASC를 설치 하는 방법에 알아봅니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 2b18a09e5b79e7b3d3ea837e937397ac92491f9f
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619852"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>ASC IoT C 기반 보안에 대 한 에이전트 Linux 배포

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 가이드에는 설치 하 여 Linux에서 IoT C 기반 보안 에이전트에 대 한 ASC를 배포 하는 방법을 설명 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>필수 조건

다른 플랫폼 및 에이전트 버전에 대 한 참조 [적합 한 보안 에이전트 선택](how-to-deploy-agent.md)합니다.

1. 보안 에이전트를 배포 하려면 로컬 관리자 권한 (sudo)에 설치 하려는 컴퓨터에 필요 합니다.

1. [보안 모듈을 만드는](quickstart-create-security-twin.md) 장치에 대 한 합니다.

## <a name="installation"></a>설치 

를 설치 하 고 보안 에이전트를 배포 하려면 다음을 수행 합니다.


1. 컴퓨터에 최신 버전을 다운로드 [Github](https://aka.ms/iot-security-github-c)합니다.

1. 패키지의 콘텐츠를 추출 하 고 이동 합니다 _/설치_ 폴더입니다.

1. 에 실행 권한을 추가 합니다 **InstallSecurityAgent 스크립트** 다음 실행 하 여:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 다음을 실행 합니다. 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   참조 [인증을 구성 하는 방법을](concept-security-agent-authentication-methods.md) 인증 매개 변수에 대 한 자세한 내용은 합니다.

이 스크립트는 다음을 수행합니다.

1. 필수 구성 요소를 설치 합니다.

2. (사용 하 여 대화형 로그인을 사용 하지 않도록 설정) 서비스 사용자를 추가 합니다.

3. 로 에이전트를 설치를 **디먼** -장치를 사용 하는 것으로 가정 **systemd** 서비스 관리에 대 한 합니다.

4. 제공 된 인증 매개 변수를 사용 하 여 에이전트를 구성 합니다. 

추가적인 도움말이 필요한 경우 스크립트를 실행 합니다. – help 매개 변수를 사용 하 여: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거 하려면 사용 하 여 스크립트를 실행 합니다.--매개 변수를 제거 합니다.

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>문제 해결
실행 하 여 배포 상태를 확인 합니다.

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>다음 단계
- IoT 서비스에 대 한 ASC 읽기 [개요](overview.md)
- 에 대해 자세히 알아보려면 ASC IoT 용 [아키텍처](architecture.md)
- 사용 하도록 설정 된 [서비스](quickstart-onboard-iot-hub.md)
- 읽기는 [FAQ](resources-frequently-asked-questions.md)
- 이해 [보안 경고](concept-security-alerts.md)