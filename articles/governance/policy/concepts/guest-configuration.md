---
title: Learn to audit the contents of virtual machines
description: Learn how Azure Policy uses the Guest Configuration agent to audit settings inside virtual machines.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f68bbc64ee8f0da02d213895a70e4c533b9a5f63
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463799"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy 게스트 구성 이해

Beyond auditing and [remediating](../how-to/remediate-resources.md) Azure resources, Azure Policy can audit settings inside a machine. 게스트 구성 확장 및 클라이언트가 유효성 검사를 수행합니다. 클라이언트를 통한 확장은 다음과 같은 설정의 유효성을 검사합니다.

- The configuration of the operating system
- 애플리케이션 구성 또는 현재 상태
- 환경 설정

현재 Azure Policy 게스트 구성은 머신 내의 설정만 감사합니다. 구성은 적용하지 않습니다.

## <a name="extension-and-client"></a>확장 및 클라이언트

To audit settings inside a machine, a [virtual machine extension](../../../virtual-machines/extensions/overview.md) is enabled. 이 확장은 적용 가능한 정책 할당 및 해당 구성 정의를 다운로드합니다.

### <a name="limits-set-on-the-extension"></a>Limits set on the extension

To limit the extension from impacting applications running inside the machine, the Guest Configuration isn't allowed to exceed more than 5% of CPU utilization. This limitation exists for both built-in and custom definitions.

## <a name="register-guest-configuration-resource-provider"></a>게스트 구성 리소스 공급자 등록

게스트 구성을 사용하려면 먼저 리소스 공급자를 등록해야 합니다. 포털 또는 PowerShell을 통해 등록할 수 있습니다. The resource provider is registered automatically if assignment of a Guest Configuration policy is done through the portal.

### <a name="registration---portal"></a>등록 - 포털

Azure Portal을 통해 게스트 구성용 리소스 공급자를 등록하려면 다음 단계를 수행합니다.

1. Azure Portal을 시작하고 **모든 서비스**를 클릭합니다. **구독**을 검색하여 선택합니다.

1. 게스트 구성을 사용하도록 설정할 구독을 찾아서 클릭합니다.

1. **구독** 페이지의 왼쪽 메뉴에서 **리소스 공급자**를 클릭합니다.

1. **Microsoft.GuestConfiguration**이 표시될 때까지 필터링하거나 스크롤한 다음 같은 행에서 **등록**을 클릭합니다.

### <a name="registration---powershell"></a>등록 - PowerShell

PowerShell을 통해 게스트 구성용 리소스 공급자를 등록하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>유효성 검사 도구

Inside the machine, the Guest Configuration client uses local tools to run the audit.

다음 표에는 지원되는 각 운영 체제에서 사용되는 로컬 도구 목록이 나와 있습니다.

|운영 체제|유효성 검사 도구|참고|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 게스트 구성 확장을 통해 Ruby 및 Python이 설치됩니다. |

### <a name="validation-frequency"></a>유효성 검사 빈도

게스트 구성 클라이언트는 5분마다 새 콘텐츠를 확인합니다. 게스트 할당이 수신되면 15분 간격으로 설정을 확인합니다. 감사가 완료되는 즉시 게스트 구성 리소스 공급자로 결과가 전송됩니다. 정책 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)가 발생하면 컴퓨터 상태가 게스트 구성 리소스 공급자에 기록됩니다. This update causes Azure Policy to evaluate the Azure Resource Manager properties. An on-demand Azure Policy evaluation retrieves the latest value from the Guest Configuration resource provider. However, it doesn't trigger a new audit of the configuration within the machine.

## <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에는 Azure 이미지에서 지원되는 운영 체제 목록이 나와 있습니다.

|게시자|name|버전|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows 클라이언트|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> Guest Configuration can audit nodes running a supported OS. If you would like to audit virtual machines that use a custom image, you need to duplicate the **DeployIfNotExists** definition and modify the **If** section to include your image properties.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

Windows Server Nano Server isn't supported in any version.

## <a name="guest-configuration-extension-network-requirements"></a>Guest Configuration Extension network requirements

To communicate with the Guest Configuration resource provider in Azure, machines require outbound access to Azure datacenters on port **443**. If you're using a private virtual network in Azure that doesn't allow outbound traffic, configure exceptions with [Network Security Group](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) rules. A service tag doesn't currently exist for Azure Policy Guest Configuration.

For IP address lists, you can download [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 포함합니다. You only need to allow outbound access to the IPs in the regions where your VMs are deployed.

> [!NOTE]
> Azure 데이터 센터 IP 주소 XML 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이 파일에는 컴퓨팅, SQL 및 스토리지 범위가 포함되어 있습니다. 업데이트된 파일이 매주 게시됩니다. 이 파일에는 현재 배포된 범위 및 IP 범위에 대해 예정된 변경 내용이 반영됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다. 새 XML 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. Azure ExpressRoute 사용자는 이 파일을 사용해 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하고 있음에 유의해야 합니다.

## <a name="guest-configuration-definition-requirements"></a>게스트 구성 정의 요구 사항

Each audit run by Guest Configuration requires two policy definitions, a **DeployIfNotExists** definition and an **AuditIfNotExists** definition. The **DeployIfNotExists** definition is used to prepare the machine with the Guest Configuration agent and other components to support the [validation tools](#validation-tools).

**DeployIfNotExists** 정책 정의는 다음 항목의 유효성을 검사하고 수정합니다.

- Validate the machine has been assigned a configuration to evaluate. If no assignment is currently present, get the assignment and prepare the machine by:
  - Authenticating to the machine using a [managed identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - **Microsoft.GuestConfiguration** 확장의 최신 버전 설치
  - [유효성 검사 도구](#validation-tools) 및 종속성(필요한 경우) 설치

If the **DeployIfNotExists** assignment is Non-compliant, a [remediation task](../how-to/remediate-resources.md#create-a-remediation-task) can be used.

Once the **DeployIfNotExists** assignment is Compliant, the **AuditIfNotExists** policy assignment uses the local validation tools to determine if the configuration assignment is Compliant or Non-compliant. 유효성 검사 도구는 게스트 구성 클라이언트에 결과를 제공합니다. 클라이언트는 게스트 확장에 결과를 전달하므로 게스트 구성 리소스 공급자를 통해 사용할 수 있습니다.

Azure Policy는 게스트 구성 리소스 공급자 **complianceStatus** 속성을 사용하여 **규정 준수** 노드에서 규정 준수를 보고합니다. 자세한 내용은 [규정 준수 데이터 가져오기](../how-to/get-compliance-data.md)를 참조하세요.

> [!NOTE]
> The **DeployIfNotExists** policy is required for the **AuditIfNotExists** policy to return results. Without the **DeployIfNotExists**, the **AuditIfNotExists** policy shows "0 of 0" resources as status.

할당에 사용할 정의를 그룹화할 수 있도록, 게스트 구성을 위한 모든 기본 제공 정책은 이니셔티브에 포함됩니다. The built-in initiative named _\[Preview\]: Audit Password security settings inside Linux and Windows machines_ contains 18 policies. 그리고 Window용 **DeployIfNotExists** 및 **AuditIfNotExists** 쌍 6개와 Linux용 쌍 3개가 있습니다. The [policy definition](definition-structure.md#policy-rule) logic validates that only the target operating system is evaluated.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditing operating system settings following industry baselines

One of the initiatives available in Azure Policy provides the ability to audit operating system settings inside virtual machines following a "baseline" from Microsoft. The definition, _\[Preview\]: Audit Windows VMs that do not match Azure security baseline settings_ includes a complete set of audit rules based on settings from Active Directory Group Policy.

Most of the settings are available as parameters. This functionality allows you to customize what is audited to align the policy with your organizational requirements or to map the policy to third party information such as industry regulatory standards.

Some parameters support an integer value range. For example, the Maximum Password Age parameter can be set using a range operator to give flexibility to machine owners. You could audit that the effective Group Policy setting requiring users to change their passwords should be no more than 70 days, but shouldn't be less than one day. As described in the info-bubble for the parameter, to make this business policy the effective audit value, set the value to "1,70".

If you assign the policy using an Azure Resource Manager deployment template, you can use a parameters file to manage these settings from source control. Using a tool such as Git to manage changes to Audit policies with comments at each check-in documents evidence as to why an assignment should be an exception to the expected value.

#### <a name="applying-configurations-using-guest-configuration"></a>Applying configurations using Guest Configuration

The latest feature of Azure Policy configures settings inside machines. The definition _Configure the time zone on Windows machines_ makes changes to the machine by configuring the time zone.

When assigning definitions that begin with _Configure_, you must also assign the definition _Deploy prerequisites to enable Guest Configuration Policy on Windows VMs_. You can combine these definitions in an initiative if you choose.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Assigning policies to machines outside of Azure

The Audit policies available for Guest Configuration include the **Microsoft.HybridCompute/machines** resource type. Any machines onboarded to [Azure Arc for Servers](../../../azure-arc/servers/overview.md) that are in the scope of the policy assignment are automatically included.

### <a name="multiple-assignments"></a>Multiple assignments

Guest Configuration policies currently only support assigning the same Guest Assignment once per machine, even if the Policy assignment uses different parameters.

## <a name="built-in-resource-modules"></a>Built-in resource modules

When installing the Guest Configuration extension, the 'GuestConfiguration' PowerShell module is included with the latest version of DSC resource modules. This module can be downloaded from the PowerShell Gallery by using the 'Manual Download' link from the module page [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). The '.nupkg' file format can be renamed to '.zip' to uncompress and review.

## <a name="client-log-files"></a>Client log files

The Guest Configuration extension writes log files to the following locations:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Where `<version>` refers to the current version number.

### <a name="collecting-logs-remotely"></a>Collecting logs remotely

The first step in troubleshooting Guest Configuration configurations or modules should be to use the `Test-GuestConfigurationPackage` cmdlet following the steps in [Test a Guest Configuration package](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
If that isn't successful, collecting client logs can help diagnose issues.

#### <a name="windows"></a>Windows

To use the Azure VM Run Command capability to capture information from log files in Windows machines, the following example PowerShell script can be helpful. For more information, see [Run PowerShell scripts in your Windows VM with Run Command](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

To use the Azure VM Run Command capability to capture information from log files in Linux machines, the following example Bash script can be helpful. For more information, see [Run shell scripts in your Linux VM with Run Command](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Guest Configuration samples

Samples for Policy Guest Configuration are available in the following locations:

- [Samples index - Guest Configuration](../samples/index.md#guest-configuration)
- [Azure Policy samples GitHub repo](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>다음 단계

- Review examples at [Azure Policy samples](../samples/index.md).
- [Azure Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- Understand how to [programmatically create policies](../how-to/programmatically-create.md).
- Learn how to [get compliance data](../how-to/get-compliance-data.md).
- Learn how to [remediate non-compliant resources](../how-to/remediate-resources.md).
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
