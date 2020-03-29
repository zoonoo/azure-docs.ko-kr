---
title: IoT 에이전트용 Azure 보안 센터의 Windows 설치 | 마이크로 소프트 문서
description: 32비트 또는 64비트 Windows 장치에 IoT 에이전트용 Azure 보안 센터를 설치하는 방법에 대해 알아봅니다.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597218"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Windows에 IoT용 Azure Security Center C# 기반 보안 에이전트 배포

이 가이드는 Windows에 IoT C#기반 보안 에이전트용 Azure 보안 센터를 설치하는 방법을 설명합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>사전 요구 사항

다른 플랫폼 및 에이전트 맛의 경우 [올바른 보안 에이전트 선택을](how-to-deploy-agent.md)참조하십시오.

1. 설치하려는 컴퓨터에 대한 로컬 관리자 권한입니다. 

1. 디바이스에 대한 [보안 모듈을 만듭니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치 

보안 에이전트를 설치하려면 다음 워크플로를 사용합니다.

1. 장치에 IoT Windows C# 에이전트용 Azure 보안 센터를 설치합니다. IoT [GitHub 리포지토리용](https://github.com/Azure/Azure-IoT-Security-Agent-CS)Azure 보안 센터에서 컴퓨터에 최신 버전을 다운로드합니다.

1. 패키지의 콘텐츠를 추출하고 /Install 폴더로 이동합니다.

1. 관리자로 Windows PowerShell을 엽니다. 
1. 다음을 실행하여 설치보안에이전트 스크립트에 실행 중인 권한을 추가합니다.<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    그런 다음 실행합니다.

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    예를 들어:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    인증 매개 변수에 대한 자세한 내용은 [인증을 구성하는 방법을](concept-security-agent-authentication-methods.md)참조하십시오.

이 스크립트는 다음과 같은 작업을 수행합니다.

- 필수 구성 요소를 설치합니다.

- 서비스 사용자를 추가합니다(대화형 로그인을 사용하지 않도록 설정).

- 에이전트를 **시스템 서비스**로 설치합니다.

- 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다.


추가 도움말을 보려면 PowerShell에서 Get-Help 명령을 사용합니다. <br>Get-Help 예제:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>배포 상태 확인

- 다음을 실행하여 에이전트 배포 상태를 확인합니다.<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거하려면 다음을 수행합니다.

1. **-mode** 매개 변수를 **Uninstall**로 설정한 상태에서 다음 PowerShell 스크립트를 실행합니다.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>문제 해결

에이전트가 시작되지 않는 경우 자세한 정보를 얻으려면 로깅을 설정합니다(기본적으로 로깅은 *해제*임).

로깅을 설정하려면 다음을 수행합니다.

1. 표준 파일 편집기에서 편집할 구성 파일(General.config)을 엽니다.

1. 다음 값을 편집합니다.

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 문제가 해결되면 로깅을 **해제**하는 것이 좋습니다. 로깅을 **설정**으로 그대로 두면 로그 파일 크기와 데이터 사용량이 증가합니다. 

1. 다음 PowerShell 또는 명령줄을 실행하여 에이전트를 다시 시작합니다.

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   또는

    **Cmd**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. 오류에 대한 자세한 내용을 확인하려면 로그 파일을 검토합니다.

   로그 파일 위치: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>다음 단계
- IoT 서비스를 위한 Azure 보안 센터 [개요](overview.md) 읽기
- IoT [아키텍처를](architecture.md) 위한 Azure 보안 센터에 대해 자세히 알아보기
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- 자주 [묻는 질문 읽기](resources-frequently-asked-questions.md)
- [경고](concept-security-alerts.md)를 살펴봅니다.