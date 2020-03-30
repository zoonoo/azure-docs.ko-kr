---
title: 하이브리드 컴퓨터를 대규모로 Azure에 연결
description: 이 문서에서는 서비스 주체를 사용하여 서버에 대한 Azure Arc(미리 보기)를 사용하여 컴퓨터를 Azure에 연결하는 방법을 알아봅니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192273"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>하이브리드 컴퓨터를 대규모로 Azure에 연결

사용자 환경에서 여러 Windows 또는 Linux 컴퓨터에 대해 서버용 Azure Arc(미리 보기)를 활성화할 수 있으며 요구 사항에 따라 여러 가지 유연한 옵션을 사용할 수 있습니다. 우리가 제공하는 템플릿 스크립트를 사용하여 Azure Arc에 대한 연결을 설정하는 것을 포함하여 설치의 모든 단계를 자동화할 수 있습니다. 그러나 대상 컴퓨터 및 Azure에서 높은 사용 권한이 있는 계정으로 이 스크립트를 대화형으로 실행해야 합니다. 서버의 Azure Arc에 컴퓨터를 연결하려면 권한 있는 ID를 사용하여 [컴퓨터를 대화형으로 연결하는](onboard-portal.md)대신 Azure Active Directory 서비스 [주체를](../../active-directory/develop/app-objects-and-service-principals.md) 사용할 수 있습니다. 서비스 주체는 명령을 사용하여 컴퓨터를 Azure에 연결하는 데 필요한 최소 권한만 부여하는 특수 제한된 관리 ID입니다. `azcmagent` 이는 테넌트 관리자와 같은 상위 권한 계정을 사용하는 것보다 안전하며 액세스 제어 보안 모범 사례를 따릅니다. 서비스 주체는 온보딩 중에만 사용되며 다른 목적으로 사용되지 않습니다.  

연결된 컴퓨터 에이전트를 설치하고 구성하는 설치 방법에는 사용하는 자동화된 메서드에 컴퓨터에 대한 관리자 권한이 있어야 합니다. Linux에서 루트 계정을 사용하고 Windows에서 로컬 관리자 그룹의 구성원으로 사용합니다.

시작하려면 먼저 [사전 요구 사항](overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 프로세스가 끝나면 하이브리드 컴퓨터를 서버에 대한 Azure Arc에 성공적으로 연결합니다.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>대규모 온보딩을 위한 서비스 보안 주체 만들기

[Azure PowerShell을](/powershell/azure/install-az-ps) 사용하여 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet을 사용하여 서비스 주체를 만들 수 있습니다. 또는 [Azure 포털을 사용하여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 아래에 나열된 단계를 수행하여 이 작업을 완료할 수 있습니다.

> [!NOTE]
> 서비스 주체를 만들 때 계정은 온보딩에 사용할 구독의 소유자 또는 사용자 액세스 관리자여야 합니다. 역할 할당을 만들 수 있는 충분한 권한이 없는 경우 서비스 주체가 생성될 수 있지만 머신을 온보딩할 수 없습니다.
>

PowerShell을 사용하여 서비스 주체를 만들려면 다음을 수행합니다.

1. 다음 명령을 실행합니다. cmdlet의 출력을 [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) 변수에 저장해야 하거나 이후 단계에서 필요한 암호를 검색할 수 없습니다.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. `$sp` 변수에 저장된 암호를 검색하려면 다음 명령을 실행합니다.

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 출력에서 필드 **암호** 아래에서 암호 값을 찾아 복사합니다. 또한 **ApplicationId** 필드 아래에 있는 값을 찾아 복사합니다. 나중에 안전한 장소에서 저장하십시오. 서비스 주 보안 주체 암호를 잊어버리거나 분실한 [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) 경우 cmdlet을 사용하여 재설정할 수 있습니다.

다음 속성의 값은 `azcmagent`다음에 전달된 매개 변수와 함께 사용됩니다.

* **ApplicationId** 속성의 값이 매개 변수 `--service-principal-id` 값에 사용됩니다.
* **암호** 속성의 값은 에이전트를 `--service-principal-secret` 연결하는 데 사용되는 매개 변수에 사용됩니다.

> [!NOTE]
> **Id** 속성이 아닌 서비스 주체 **ApplicationId** 속성을 사용해야 합니다.
>

**Azure 연결된 컴퓨터 온보딩** 역할에는 컴퓨터를 온보딩하는 데 필요한 권한만 포함됩니다. 서비스 주체 권한을 할당하여 해당 범위에 리소스 그룹 또는 구독을 포함할 수 있도록 허용할 수 있습니다. 역할 할당을 추가하려면 [Azure RBAC 및 Azure 포털을 사용하여 역할 할당 추가 또는 제거를](../../role-based-access-control/role-assignments-portal.md) 참조하거나 [Azure RBAC 및 Azure CLI를 사용하여 역할 할당 추가 또는 제거를 참조합니다.](../../role-based-access-control/role-assignments-cli.md)

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치하고 Azure에 연결

다음 단계는 Azure 포털 문서에서 [하이브리드 컴퓨터 연결에서 Azure에](onboard-portal.md) 설명하는 유사한 단계를 수행하는 스크립트 템플릿을 사용하여 하이브리드 컴퓨터에 연결된 컴퓨터 에이전트를 설치하고 구성합니다. 차이점은 서비스 주체를 사용하여 `azcmagent` 명령을 사용하여 Azure Arc에 대한 연결을 설정하는 마지막 단계에서 다름이 있습니다. 

다음은 서비스 주체에 `azcmagent` 사용할 명령을 구성하는 설정입니다.

* `tenant-id`: Azure AD의 전용 인스턴스를 나타내는 고유 식별자(GUID)입니다.
* `subscription-id`: 컴퓨터를 입력하려는 Azure 구독의 구독 ID(GUID)입니다.
* `resource-group`: 연결된 컴퓨터를 속할 리소스 그룹 이름입니다.
* `location`: [지원되는 Azure 지역을](overview.md#supported-regions)참조하십시오. 이 위치는 리소스 그룹의 위치와 같거나 다를 수 있습니다.
* `resource-name`:*(선택 사항)* 온-프레미스 시스템의 Azure 리소스 표현에 사용됩니다. 이 값을 지정하지 않으면 컴퓨터 호스트 이름이 사용됩니다.

`azcmagent` [Azcmagent 참조를](azcmagent-reference.md)검토하여 명령줄 도구에 대해 자세히 알아볼 수 있습니다.

### <a name="windows-installation-script"></a>윈도우 설치 스크립트

다음은 서비스 주체를 사용하여 에이전트의 완전 자동화된 비대화형 설치를 지원하도록 수정된 Windows 설치 용 연결된 컴퓨터 에이전트의 예입니다.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>리눅스 설치 스크립트

다음은 서비스 주체를 사용하여 에이전트의 완전 자동화된 비대화형 설치를 지원하도록 수정된 Linux 설치 스크립트용 연결된 컴퓨터 에이전트의 예입니다.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

에이전트가 설치되고 서버용 Azure Arc(미리 보기)에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

- Azure [Policy를](../../governance/policy/overview.md)사용하여 컴퓨터를 관리하는 방법, VM [게스트 구성,](../../governance/policy/concepts/guest-configuration.md)컴퓨터가 예상 로그 분석 작업 영역에 보고되고 있는지 확인, [VM을 사용한 Azure 모니터로](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)모니터링 을 활성화하는 방법 등을 알아봅니다.

- [로그 분석 에이전트에](../../azure-monitor/platform/log-analytics-agent.md)대해 자세히 알아봅니다. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-intro.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.
