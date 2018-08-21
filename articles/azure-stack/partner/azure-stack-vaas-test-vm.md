---
title: 로컬 에이전트를 배포 하 고 서비스로 Azure Stack 유효성 검사에 테스트 가상 컴퓨터가 이미지 | Microsoft Docs
description: 로컬 에이전트를 배포 하 고 서비스로 Azure Stack 유효성 검사에 대 한 가상 머신 이미지를 테스트 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 9d32c23f66563988d023df3bf6a33efa30237e57
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235386"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>로컬 에이전트 및 테스트 가상 머신 배포

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

로컬 에이전트 서비스 (VaaS) 유효성 검사를 사용 하 여 하드웨어를 확인 하는 방법에 알아봅니다. 로컬 에이전트는 유효성 검사 테스트를 실행 하기 전에 유효성이 검사 되는 Azure Stack 솔루션에 배포 되어야 합니다.

> [!Note]  
> 로컬 에이전트가 실행 되는 컴퓨터가 인터넷에 액세스 하지 못하게 하지는 않도록 해야 합니다. 컴퓨터만 Azure Stack VaaS 사용할 권한이 있는 사용자에 게 액세스 가능 해야 합니다.

가상 컴퓨터를 테스트 합니다.

1. 로컬 에이전트를 설치 합니다.
2. 시스템 오류 삽입
3. 로컬 에이전트를 실행 합니다.

## <a name="download-and-start-the-local-agent"></a>다운로드 하 고 로컬 에이전트를 시작 합니다.

Azure Stack 시스템의 모든 Azure Stack 끝점에 대 한 액세스 권한이 있는 포함 되지 않은 데이터 센터에서 필수 구성 요소를 충족 하는 컴퓨터에 에이전트를 다운로드 합니다.

### <a name="machine-prerequisites"></a>컴퓨터 필수 조건

컴퓨터는 다음 조건을 충족 하는지 확인 합니다.

- 모든 Azure Stack 끝점에 대 한 액세스
- .NET 4.6을 설치 하는 PowerShell 5.0
- 최소 8GB RAM
- 최소 8 코어 프로세서
- 최소 200GB 디스크 공간
- 인터넷에 대 한 안정적인 네트워크 연결

Azure Stack은 테스트 대상 시스템. 컴퓨터는 Azure Stack의 일부가 아니어야 또는 Azure Stack 클라우드에 호스트 합니다.

### <a name="download-and-install-the-agent"></a>에이전트 다운로드 및 설치

1. 테스트를 실행 하는 데 사용할 컴퓨터에서 관리자 권한 프롬프트에서 Windows PowerShell을 엽니다.
2. 로컬 에이전트를 다운로드 하려면 다음 명령을 실행 합니다.

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. 로컬 에이전트 종속성을 설치 하려면 다음 명령을 실행 합니다.

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **매개 변수**

    | 매개 변수 | 설명 |
    | --- | --- |
    | aadServiceAdminUser | Azure AD 테 넌 트에 대 한 전역 관리자 사용자. 예를 들어 것, vaasadmin@contoso.onmicrosoft.com합니다. |
    | aadServiceAdminPassword | 전역 관리 사용자의 암호입니다. |
    | AadTenantId | Azure 계정에 대 한 azure AD 테 넌 트 ID를 서비스로 유효성 검사를 사용 하 여 등록 합니다. |
    | ExternalFqdn | 구성 파일의 정규화 된 도메인 이름을 가져올 수 있습니다. 명령 참조 [서비스로 Azure Stack 유효성 검사에 대 한 매개 변수를 테스트](azure-stack-vaas-parameters-test.md)합니다. |
    | 지역 | Azure AD 테 넌 트의 영역입니다. |

명령으로 Azure Stack 저장소 Azure blob storage에서 복사를 공용 이미지 리포지토리 (PIR) 이미지 (VHD (OS)를 다운로드합니다. 

![필수 구성 요소 다운로드](media/installingprereqs.png)

> [!Note]  
> 이러한 이미지를 다운로드할 때 느린 네트워크 속도 발생 하는 경우 로컬 공유에 개별적으로 다운로드 하 고 매개 변수를 지정 **-LocalPackagePath** *FileShareOrLocalPath*합니다. 섹션에서 PIR 다운로드에서 자세한 지침을 찾을 수 있습니다 [느린 네트워크 연결 핸들](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) 의 [서비스로 유효성 검사 문제 해결](azure-stack-vaas-troubleshoot.md)합니다.

## <a name="fault-injection"></a>오류 주입

복원 력 및 여러 유형의 소프트웨어 및 하드웨어 오류를 허용할 Azure Stack 설계 되었습니다. 오류 주입 시스템에서 오류 속도 높입니다. 이러한 증가 사용 하면 시스템을 종료 하는 인시던트의 수를 줄일 수 있도록 이전 문제를 발견할 수 있습니다.

시스템에 오류를 삽입 하려면 다음 명령을 실행 합니다.

1. 관리자 권한 프롬프트에서 Windows PowerShell을 엽니다.

2. 다음 명령 실행:

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>에이전트를 실행 합니다.

1. 관리자 권한 프롬프트에서 Windows PowerShell을 엽니다.

2. 다음 명령 실행:

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **매개 변수**  
    
    | 매개 변수 | 설명 |
    | --- | --- |
    | VaaSUserId | VaaS 포털에 로그인 할 때 사용한 사용자 ID (예를 들어 UserName@Contoso.com) |
    | VaaSTenantId | Azure 계정에 대 한 azure AD 테 넌 트 ID를 서비스로 유효성 검사를 사용 하 여 등록 합니다. |

    > [!Note]  
    > 현재 작업 디렉터리에 엔진 작업 호스트 실행 파일의 위치 해야 에이전트를 실행 하면 **Microsoft.VaaSOnPrem.TaskEngineHost.exe 합니다.**

보고 한 오류를 보이지 않으면 로컬 에이전트가 성공 했습니다. 다음 예제에서는 텍스트를 콘솔 창에 나타납니다.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![에이전트 시작된](media/startedagent.png)

에이전트는 이름을 사용 하 여 고유 하 게 식별 됩니다. 기본적으로 시작 된 위치에서 컴퓨터의 정규화 된 도메인 이름 (FQDN) 이름을 사용 합니다. 와 같이 창에 대 한 모든 실수로 클릭을 방지 하려면 기간을 최소화 해야 다른 모든 작업을 일시 중지 포커스를 변경 합니다.

## <a name="next-steps"></a>다음 단계

- [새 Azure Stack 솔루션의 유효성을 검사합니다](azure-stack-vaas-validate-solution-new.md)  
- 인터넷 연결이 느리거나 일시적인 경우 PIR 이미지를 다운로드할 수 있습니다. 자세한 내용은 [핸들 느린 네트워크 연결](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)합니다.
- 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
