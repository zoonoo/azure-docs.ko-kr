---
title: ASC IoT 에이전트 미리 보기에 대 한 Windows 설치 | Microsoft Docs
description: 32 비트 또는 64 비트 Windows 장치에서 IoT 에이전트용 ASC를 설치 하는 방법에 알아봅니다.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 5c3293746fcc52570e708fd4bfab446981d49c24
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621841"
---
# <a name="deploy-an-asc-for-iot-c-based-security-agent-for-windows"></a>IoT 용를 ASC를 배포 C#-Windows에 대 한 보안 에이전트 기반

> [!IMPORTANT]
> IoT 용 ASC는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 가이드에서는 IoT 용 ASC를 설치 하는 방법을 설명 C#-Windows에서 보안 에이전트 기반 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>필수 조건

다른 플랫폼과 에이전트 종류에 대 한 참조 [적합 한 보안 에이전트 선택](how-to-deploy-agent.md)합니다.

1. 설치 하려는 컴퓨터에 대 한 로컬 관리자 권한 

1. [보안 모듈을 만드는](quickstart-create-security-twin.md) 장치에 대 한 합니다.

## <a name="installation"></a>설치 

보안 에이전트를 설치 하려면 다음을 수행 합니다.

1. Windows IoT에 대 한 ASC를 설치 하려면 C# 장치에 에이전트 버전을 다운로드 최신 컴퓨터에 ASC에서 IoT [GitHub 리포지토리](https://github.com/Azure/Azure-IoT-Security-Agent-CS)합니다.

2. 패키지의 콘텐츠를 추출 하 고 /Install 폴더로 이동 합니다.

3. 관리자 권한으로 Windows PowerShell을 엽니다. 
    1. 실행 하 여 InstallSecurityAgent 스크립트에 실행 권한을 추가합니다 ```Unblock-File .\InstallSecurityAgent.ps1```
    
        실행 합니다.

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    예: 
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    참조 [인증을 구성 하는 방법을](concept-security-agent-authentication-methods.md) 인증 매개 변수에 대 한 자세한 내용은 합니다.

이 스크립트는 다음을 수행합니다.

- 필수 구성 요소를 설치 합니다.

- (사용 하 여 대화형 로그인을 사용 하지 않도록 설정) 서비스 사용자를 추가 합니다.

- 로 에이전트 설치를 **시스템 서비스**합니다.

- 제공 된 인증 매개 변수를 사용 하 여 에이전트를 구성합니다.


추가적인 도움말이 필요한 경우 PowerShell에서 Get-help 명령을 사용합니다 <br>Get-help 예제:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>배포 상태를 확인 합니다.

- 실행 하 여 에이전트 배포 상태를 확인 합니다.<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거 합니다.

1. 사용 하 여 다음 PowerShell 스크립트를 실행 합니다 **-모드** 매개 변수 설정 **제거**합니다.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>문제 해결

에이전트를 시작 하지 못하면 로깅 켜기 (로깅은 *해제* 기본적으로) 자세한 정보를 얻을 수 있습니다.

로깅을 켜려면:

1. 표준 파일 편집기를 사용 하 여 편집에 대 한 구성 파일 (General.config)을 엽니다.

1. 다음 값을 편집 합니다.

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 로깅을 설정 하는 것이 좋습니다 **해제** 완료 되 면 문제를 해결 합니다. 로깅 두면 **에서** 증가 로그 파일 크기 및 데이터 사용 합니다. 

1. 다음 PowerShell 또는 명령줄을 실행 하 여 에이전트를 다시 시작 합니다.

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   또는

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. 오류에 대 한 자세한 내용은 로그 파일을 검토 합니다.

   로그 파일 위치: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>다음 단계
- IoT 서비스에 대 한 ASC 읽기 [개요](overview.md)
- 에 대해 자세히 알아보려면 ASC IoT 용 [아키텍처](architecture.md)
- 사용 하도록 설정 된 [서비스](quickstart-onboard-iot-hub.md)
- 읽기는 [FAQ](resources-frequently-asked-questions.md)
- 이해 [경고](concept-security-alerts.md)