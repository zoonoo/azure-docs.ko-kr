---
title: IoT 에이전트용 Azure 보안 센터의 Linux C 에이전트 설치 및 배포 가이드| 마이크로 소프트 문서
description: 32비트 및 64비트 Linux모두에서 IoT 에이전트용 Azure 보안 센터를 설치하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68812738"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Linux에 IoT용 Azure Security Center C 기반 보안 에이전트 배포

이 가이드는 Linux에서 IoT C 기반 보안 에이전트용 Azure 보안 센터를 설치하고 배포하는 방법을 설명합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>사전 요구 사항

다른 플랫폼 및 에이전트 맛의 경우 [올바른 보안 에이전트 선택을](how-to-deploy-agent.md)참조하십시오.

1. 보안 에이전트를 배포하려면 설치하려는 컴퓨터(sudo)에 로컬 관리자 권한이 필요합니다.

1. 디바이스에 대한 [보안 모듈을 만듭니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치 

보안 에이전트를 설치하고 배포하려면 다음 워크플로를 사용합니다.


1. [GitHub에서](https://aka.ms/iot-security-github-c)컴퓨터에 최신 버전을 다운로드합니다.

1. 패키지의 내용을 추출하고 _/src/설치_ 폴더로 이동합니다.

1. 다음 명령을 실행 하여 **설치 보안 에이전트 스크립트에** 실행 중인 권한을 추가합니다.
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 다음으로, 다음을 실행합니다. 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   인증 매개 변수에 대한 자세한 내용은 [인증 구성 방법](concept-security-agent-authentication-methods.md)을 참조하세요.

이 스크립트는 다음 기능을 수행합니다.

1. 필수 구성 요소를 설치합니다.

2. 서비스 사용자를 추가합니다(대화형 로그인을 사용하지 않도록 설정).

3. 에이전트를 **디먼**으로 설치합니다. 여기서는 서비스 관리를 위해 디바이스에서 **systemd**를 사용한다고 가정합니다.

4. 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다. 

추가 도움말을 보려면 -help 매개 변수를 사용하여 스크립트를 실행합니다. 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거하려면 --제거 매개 변수를 사용하여 스크립트를 실행합니다.

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>문제 해결
다음을 실행하여 배포 상태를 확인합니다.

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>다음 단계
- IoT 서비스를 위한 Azure 보안 센터 [개요](overview.md) 읽기
- IoT [아키텍처를](architecture.md) 위한 Azure 보안 센터에 대해 자세히 알아보기
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- 자주 [묻는 질문 읽기](resources-frequently-asked-questions.md)
- [보안 경고](concept-security-alerts.md) 이해
