---
title: 로컬 에이전트를 배포 합니다. | Microsoft Docs
description: 서비스로 Azure Stack 유효성 검사에 대 한 로컬 에이전트를 배포 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0608da33e816b40f7fadbeb1b5da3feb926c28aa
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334060"
---
# <a name="deploy-the-local-agent"></a>로컬 에이전트를 배포 합니다.

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

로컬 에이전트 서비스 (VaaS) 유효성 검사를 사용 하 여 하드웨어를 확인 하는 방법에 알아봅니다. 로컬 에이전트는 유효성 검사 테스트를 실행 하기 전에 유효성이 검사 되는 Azure Stack 솔루션에 배포 되어야 합니다.

> [!Note]  
> 로컬 에이전트가 실행 되는 컴퓨터 인터넷에 아웃 바운드 액세스를 손실 하지 않도록 확인 해야 합니다. 이 컴퓨터는 VaaS 테 넌 트를 대신 하 여 사용 하도록 권한을 부여한 사용자에만 액세스할 수 있어야 합니다.

로컬 에이전트를 배포 합니다.

1. 로컬 에이전트를 설치 합니다.
2. 온전성 검사를 수행 합니다.
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
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. 로컬 에이전트 종속성을 설치 하려면 다음 명령을 실행 합니다.

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **매개 변수**

    | 매개 변수 | 설명 |
    | --- | --- |
    | aadServiceAdminUser | Azure AD 테 넌 트에 대 한 전역 관리자 사용자. 예를 들어 것, vaasadmin@contoso.onmicrosoft.com합니다. |
    | aadServiceAdminPassword | 전역 관리 사용자의 암호입니다. |
    | AadTenantId | Azure 계정에 대 한 azure AD 테 넌 트 ID를 서비스로 유효성 검사를 사용 하 여 등록 합니다. |
    | ExternalFqdn | 구성 파일의 정규화 된 도메인 이름을 가져올 수 있습니다. 명령 참조 [워크플로 일반 매개 변수를 서비스로 Azure Stack 유효성 검사에](azure-stack-vaas-parameters.md)입니다. |
    | 지역 | Azure AD 테 넌 트의 영역입니다. |

명령으로 Azure Stack 저장소 Azure blob storage에서 복사를 공용 이미지 리포지토리 (PIR) 이미지 (VHD (OS)를 다운로드합니다.

![필수 구성 요소 다운로드](media/installingprereqs.png)

> [!Note]
> 이러한 이미지를 다운로드할 때 느린 네트워크 속도 발생 하는 경우 로컬 공유에 개별적으로 다운로드 하 고 매개 변수를 지정 **-LocalPackagePath** *FileShareOrLocalPath*합니다. 섹션에서 PIR 다운로드에서 자세한 지침을 찾을 수 있습니다 [느린 네트워크 연결 핸들](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) 의 [서비스로 유효성 검사 문제 해결](azure-stack-vaas-troubleshoot.md)합니다.

## <a name="checks-before-starting-the-tests"></a>테스트를 시작 하기 전에 확인

원격 작업을 실행 하는 테스트 합니다. 테스트를 실행 하는 컴퓨터에는 Azure Stack 끝점에 액세스할 수 있어야 합니다, 그리고 그렇지 않으면 테스트가 작동 하지 않습니다. VaaS 로컬 에이전트를 사용 하는 경우 사용 하 여 컴퓨터 에이전트가 실행 될 위치입니다. 컴퓨터에는 다음 검사를 실행 하 여 Azure Stack 끝점에 대 한 액세스를 확인할 수 있습니다.

1. 기본 URI에 연결할 수 있는지 확인 합니다. CMD 프롬프트를 열고 shell, bash 또는 다음 실행 명령을 대체 `<EXTERNALFQDN>` 환경의 외부 FQDN을 사용 하 여:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. 웹 브라우저를 열고 이동할 `https://adminportal.<EXTERNALFQDN>` MAS 포털에 연결할 수 있는지 확인 하기 위해.

3. Azure AD를 사용 하 여 로그인 테스트 패스를 만들 때 제공한 관리자 이름 및 암호 값을 서비스 합니다.

4. 실행 하 여 시스템의 상태를 확인 합니다 **테스트 AzureStack** 에 설명 된 대로 PowerShell cmdlet [Azure Stack에 대 한 유효성 검사 테스트를 실행](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)합니다. 모든 테스트를 시작 하기 전에 모든 경고 및 오류를 수정 합니다.

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

에이전트는 이름을 사용 하 여 고유 하 게 식별 됩니다. 기본적으로 시작 된 위치에서 컴퓨터의 정규화 된 도메인 이름 (FQDN) 이름을 사용 합니다. 실수로 인 한 모든 선택으로 창에서를 방지 하려면 기간을 최소화 해야 다른 모든 작업을 일시 중지 포커스를 변경 합니다.

## <a name="next-steps"></a>다음 단계

- [서비스 유효성 검사 문제 해결](azure-stack-vaas-troubleshoot.md)
- [서비스 키 개념으로 유효성 검사](azure-stack-vaas-key-concepts.md)
- [빠른 시작: 첫 번째 테스트를 예약 하려면 서비스 포털로 유효성 검사 사용](azure-stack-vaas-schedule-test-pass.md)