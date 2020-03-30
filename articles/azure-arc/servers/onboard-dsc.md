---
title: Windows PowerShell DSC를 사용하여 연결된 컴퓨터 에이전트 설치
description: 이 문서에서는 Windows PowerShell DSC를 사용하여 서버에 대한 Azure Arc(미리 보기)를 사용하여 컴퓨터를 Azure에 연결하는 방법을 알아봅니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164684"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Windows PowerShell DSC를 사용하여 연결된 컴퓨터 에이전트를 설치하는 방법

[Windows PowerShell 원하는 상태](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) 구성(DSC)을 사용하여 Windows 컴퓨터에 대한 소프트웨어 설치 및 구성을 자동화할 수 있습니다. 이 문서에서는 DSC를 사용하여 하이브리드 Windows 컴퓨터에서 연결된 컴퓨터 에이전트에 대한 Azure Arc를 설치하는 방법에 대해 설명합니다.

## <a name="requirements"></a>요구 사항

- 윈도우 파워쉘 버전 4.0 이상

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC 모듈

- 시스템을 Azure Arc에 연결하여 서버를 비대화형으로 연결하는 서비스 주체입니다. 서버에 대한 Arc에 대한 서비스 주체를 아직 만들지 않은 경우 [대규모로 온보딩할](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 수 있는 서비스 주체 만들기 섹션의 단계를 따릅니다.

## <a name="install-the-connectedmachine-dsc-module"></a>커넥티드머신 DSC 모듈 설치

1. 모듈을 수동으로 설치하려면 소스 코드를 다운로드하고 프로젝트 디렉터리 내용의 압축을 `$env:ProgramFiles\WindowsPowerShell\Modules folder`해제합니다. 또는 PowerShellGet(PowerShell 5.0)을 사용하여 PowerShell 갤러리에서 설치하려면 다음 명령을 실행합니다.

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 설치를 확인하려면 다음 명령을 실행하고 사용 가능한 Azure 연결된 컴퓨터 DSC 리소스가 표시되는지 확인합니다.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   출력에는 다음과 유사한 항목이 표시됩니다.

   ![커넥티드 머신 DSC 모듈 설치 예의 확인](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치하고 Azure에 연결

이 모듈의 리소스는 Azure 연결된 컴퓨터 에이전트 구성을 관리하도록 설계되었습니다. 또한 폴더에 있는 `AzureConnectedMachineAgent.ps1`PowerShell 스크립트도 포함되어 있습니다. `AzureConnectedMachineDsc\examples` 커뮤니티 리소스를 사용하여 다운로드 및 설치를 자동화하고 Azure Arc와의 연결을 설정합니다. 이 스크립트는 Azure 포털 문서에서 [하이브리드 컴퓨터 연결에서 Azure에 설명된](onboard-portal.md) 유사한 단계를 수행합니다.

컴퓨터가 프록시 서버를 통해 서비스에 통신해야 하는 경우 에이전트를 설치한 후 [여기에](onboard-portal.md#configure-the-agent-proxy-setting)설명된 명령을 실행해야 합니다. 그러면 `https_proxy` 프록시 서버 시스템 환경 변수가 설정됩니다. 명령을 수동으로 실행하는 대신 [ComputeManagementDsc 모듈을](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) 사용하여 DSC에서 이 단계를 수행할 수 있습니다.

>[!NOTE]
>DSC를 실행하려면 로컬 호스트 구성을 실행하는 경우에도 PowerShell 원격 명령을 받도록 Windows를 구성해야 합니다. 환경을 올바르게 구성하려면 관리자 권한 PowerShell 터미널에서 `Set-WsManQuickConfig -Force`를 실행하면 됩니다.
>

`Start-DscConfiguration` 구성 문서(MOF 파일)는 cmdlet을 사용하여 컴퓨터에 적용할 수 있습니다.

다음은 사용할 PowerShell 스크립트에 전달하는 매개 변수입니다.

- `TenantId`: Azure AD의 전용 인스턴스를 나타내는 고유 식별자(GUID)입니다.

- `SubscriptionId`: 컴퓨터를 입력하려는 Azure 구독의 구독 ID(GUID)입니다.

- `ResourceGroup`: 연결된 컴퓨터를 속할 리소스 그룹 이름입니다.

- `Location`: [지원되는 Azure 지역을](overview.md#supported-regions)참조하십시오. 이 위치는 리소스 그룹의 위치와 같거나 다를 수 있습니다.

- `Tags`: 연결된 컴퓨터 리소스에 적용해야 하는 태그의 문자열 배열입니다.

- `Credential`: **ApplicationId** 및 **암호가** 있는 PowerShell 자격 증명 개체는 [서비스 주체를](onboard-service-principal.md)사용하여 대규모로 컴퓨터를 등록하는 데 사용됩니다. 

1. PowerShell 콘솔에서 `.ps1` 파일을 저장한 폴더로 이동합니다.

2. 다음 PowerShell 명령을 실행하여 MOF 문서를 컴파일합니다(DSC 구성 컴파일에 대한 자세한 내용은 [DSC 구성](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7) 참조).

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 이렇게 하면 `localhost.mof file` . `C:\dsc`

에이전트가 설치되고 서버용 Azure Arc(미리 보기)에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

## <a name="adding-to-existing-configurations"></a>기존 구성에 추가

이 리소스는 기존 DSC 구성에 추가하여 컴퓨터에 대한 종단 간 구성을 나타낼 수 있습니다. 예를 들어 보안 운영 체제 설정을 설정하는 구성에 이 리소스를 추가할 수 있습니다.

PowerShell 갤러리의 [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) 모듈을 사용하여 예제 구성의 [복합 리소스를](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) 생성하여 구성 결합을 더욱 단순화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure [Policy를](../../governance/policy/overview.md)사용하여 컴퓨터를 관리하는 방법, VM [게스트 구성,](../../governance/policy/concepts/guest-configuration.md)컴퓨터가 예상 로그 분석 작업 영역에 보고되고 있는지 확인, [VM을 사용한 Azure 모니터로](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)모니터링 을 활성화하는 방법 등을 알아봅니다.

- [로그 분석 에이전트에](../../azure-monitor/platform/log-analytics-agent.md)대해 자세히 알아봅니다. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-intro.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.