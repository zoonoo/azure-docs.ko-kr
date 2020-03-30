---
title: Windows용 게스트 구성 정책을 만드는 방법
description: Windows용 Azure 정책 게스트 구성 정책을 만드는 방법에 대해 알아봅니다.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 24069ff6518c4244026378e48216d4568fffeb8a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365463"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Windows용 게스트 구성 정책을 만드는 방법

사용자 지정 정책을 만들기 전에 [Azure 정책 게스트 구성](../concepts/guest-configuration.md)페이지에서 개념개요 정보를 읽는 것이 좋습니다.
 
Linux용 게스트 구성 정책을 만드는 방법에 대해 알아보려면 [Linux용 게스트 구성 정책을 만드는 방법](./guest-configuration-create-linux.md) 페이지를 참조하세요.

Windows를 감사할 때 게스트 구성은 [원하는 DSC(상태 구성)](/powershell/scripting/dsc/overview/overview) 리소스 모듈을 사용하여 구성 파일을 구성합니다. DSC 구성은 컴퓨터가 있어야 하는 조건을 정의합니다.
구성 평가에 실패하면 정책 효과 **auditIfNotExists가** 트리거되고 컴퓨터가 **비호환으로**간주됩니다.

[Azure 정책 게스트 구성은](../concepts/guest-configuration.md) 컴퓨터 내부의 설정을 감사하는 데만 사용할 수 있습니다. 컴퓨터 내부의 설정 수정은 아직 사용할 수 없습니다.

다음 작업을 사용하여 Azure 또는 비 Azure 컴퓨터의 상태를 검증하기 위한 고유한 구성을 만듭니다.

> [!IMPORTANT]
> 게스트 구성을 가진 사용자 지정 정책은 미리 보기 기능입니다.

## <a name="install-the-powershell-module"></a>PowerShell 모듈 설치

게스트 구성 아티팩트 만들기, 아티팩트 자동 테스트, 정책 정의 작성 및 정책 게시는 PowerShell의 게스트 구성 모듈을 사용하여 완전히 자동화할 수 있습니다. 이 모듈은 PowerShell 6.2 이상 로컬에서 실행되는 Windows, macOS 또는 Linux를 실행하는 컴퓨터에 설치하거나 [Azure Cloud Shell](https://shell.azure.com)또는 Azure [PowerShell Core Docker 이미지를](https://hub.docker.com/r/azuresdk/azure-powershell-core)사용할 수 있습니다.

> [!NOTE]
> 구성의 컴파일은 아직 Linux에서 지원되지 않습니다.

### <a name="base-requirements"></a>기본 요구 사항

모듈을 설치할 수 있는 운영 체제:

- Linux
- macOS
- Windows

게스트 구성 리소스 모듈에는 다음 소프트웨어가 필요합니다.

- PowerShell 6.2 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/scripting/install/installing-powershell)을 따릅니다.
- Azure PowerShell 1.5.0 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.
  - AZ 모듈 'Az.Accounts'와 'Az.Resources'만 필요합니다.

### <a name="install-the-module"></a>모듈 설치

PowerShell에 **게스트 구성** 모듈을 설치하려면 다음을 수행하십시오.

1. PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 모듈을 가져왔는지 확인합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Windows용 게스트 구성 아티팩트 및 정책

게스트 구성은 PowerShell 원하는 상태 구성을 Windows에서 감사할 내용을 작성하기 위한 언어 추상화로 사용합니다. 에이전트는 PowerShell 6.2의 독립 실행형 인스턴스를 로드하므로 Windows PowerShell 5.1에서 PowerShell DSC 사용과 충돌하지 않으며 PowerShell 6.2 이상을 사전 설치할 필요가 없습니다.

DSC 개념 및 용어에 대한 개요는 [PowerShell DSC 개요를](/powershell/scripting/dsc/overview/overview)참조하십시오.

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>게스트 구성 모듈이 Windows PowerShell DSC 모듈과 다른 방법

게스트 구성이 컴퓨터를 감사할 때 `Test-TargetResource` 먼저 실행되어 올바른 상태인지 확인합니다. 함수에서 반환되는 부울 값은 게스트 할당에 대한 Azure 리소스 관리자 상태가 준수/비준수상태여야 하는지 여부를 결정합니다. 다음으로 공급자가 `Get-TargetResource` 실행되어 각 설정의 현재 상태를 반환하여 컴퓨터가 호환되지 않는 이유에 대한 세부 정보를 사용할 수 있도록 하거나 현재 상태가 호환되는지 확인합니다.

### <a name="get-targetresource-requirements"></a>대상 리소스 요구 사항 받기

이 `Get-TargetResource` 함수에는 Windows 원하는 상태 구성에 필요하지 않은 게스트 구성에 대한 특별한 요구 사항이 있습니다.

- 반환되는 해시 테이블에는 **Reasons**라는 속성이 포함되어야 합니다.
- Reasons 속성은 배열이어야 합니다.
- 배열의 각 항목은 **Code** 및 **Phrase라는**키가 있는 해시테이블이어야 합니다.

이유 속성은 서비스에서 컴퓨터가 규정을 준수하지 않는 경우 정보가 표시되는 방식을 표준화하는 데 사용됩니다. 이유의 각 항목을 리소스가 호환되지 않는 "이유"로 생각할 수 있습니다. 리소스가 여러 가지 이유로 규정을 준수하지 않을 수 있기 때문에 속성은 배열입니다.

속성 **코드** 및 **구는** 서비스에 의해 예상 됩니다. 사용자 지정 리소스를 작성할 때 리소스가 **구값으로**호환되지 않는 이유를 표시할 텍스트(일반적으로 stdout)를 설정합니다. **코드에는** 특정 서식 지정 요구 사항이 있으므로 보고는 감사를 수행하는 데 사용되는 리소스에 대한 정보를 명확하게 표시할 수 있습니다. 이 솔루션을 사용하면 게스트 구성을 확장 할 수 있습니다. 모든 명령은 출력을 **Phrase** 속성에 대한 문자열 값으로 반환할 수 있는 한 실행할 수 있습니다.

- **코드(문자열):** 리소스의 이름, 반복된 이름 및 공백이 없는 짧은 이름입니다. 이 세 값은 공백 없이 콜론 구분되어야 합니다.
  - 예를 들어 다음과 같은 예입니다.`registry:registry:keynotpresent`
- **구(문자열):** 설정이 호환되지 않는 이유를 설명하는 사람이 읽을 수 있는 텍스트입니다.
  - 예를 들어 다음과 같은 예입니다.`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```
### <a name="configuration-requirements"></a>구성 요구 사항

사용자 지정 구성의 이름은 모든 곳에서 일관되어야 합니다. 콘텐츠 패키지에 대한 .zip 파일의 이름, MOF 파일의 구성 이름 및 리소스 관리자 템플릿의 게스트 할당 이름은 동일해야 합니다.

### <a name="scaffolding-a-guest-configuration-project"></a>게스트 구성 프로젝트 스캐폴딩

샘플 코드를 시작하고 작업하는 프로세스를 가속화하려는 개발자는 **게스트 구성**프로젝트라는 커뮤니티 프로젝트를 설치할 수 있습니다. 이 프로젝트는 [석고](https://github.com/powershell/plaster) PowerShell 모듈에 대한 템플릿을 설치합니다. 이 도구는 작업 구성 및 샘플 리소스를 비롯한 프로젝트를 스캐폴드하는 데 사용할 수 있으며 프로젝트의 유효성을 검사하기 위한 [Pester](https://github.com/pester/pester) 테스트 집합을 포함합니다. 템플릿에는 게스트 구성 패키지의 빌드 및 유효성을 검사하는 Visual Studio 코드에 대한 작업 러너도 포함되어 있습니다. 자세한 내용은 GitHub 프로젝트 [게스트 구성 프로젝트를](https://github.com/microsoft/guestconfigurationproject)참조하십시오.

일반적으로 구성 작업에 대한 자세한 내용은 [구성 작성, 컴파일 및 적용을 참조하십시오.](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>게스트 구성 아티팩트의 예상 내용

완성된 패키지는 게스트 구성에서 Azure 정책 정의를 만드는 데 사용됩니다. 패키지는 다음으로 구성됩니다.

- MOF로 컴파일된 DSC 구성
- 모듈 폴더
  - 게스트 구성 모듈
  - DscNativeResources 모듈
  - (윈도우) MOF에 필요한 DSC 리소스 모듈

PowerShell cmdlet은 패키지 작성을 지원합니다.
루트 수준 폴더 또는 버전 폴더가 필요하지 않습니다.
패키지 형식은 .zip 파일이어야 합니다.

### <a name="storing-guest-configuration-artifacts"></a>게스트 구성 아티팩트 저장

.zip 패키지는 관리되는 가상 컴퓨터에서 액세스할 수 있는 위치에 저장되어야 합니다.
예를 들어 GitHub 리포지토리, Azure 리포지토리 또는 Azure 저장소가 있습니다. 패키지를 공개하지 않으려면 URL에 [SAS 토큰을](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 포함할 수 있습니다.
이 구성은 패키지에 액세스하고 서비스와 통신하지 않는 경우에만 적용되지만 개인 네트워크의 컴퓨터에 대한 [서비스 끝점을](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) 구현할 수도 있습니다.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>단계별로 Windows용 사용자 지정 게스트 구성 감사 정책을 만듭니다.

DSC 구성을 만듭니다. 다음 PowerShell 스크립트 예제에서는 **AuditBitLocker라는**구성을 만들고 **PsDscResources** 리소스 `Service` 모듈을 가져오고 리소스를 사용하여 실행 중인 서비스에 대해 감사합니다. 구성 스크립트는 Windows 또는 macOS 컴퓨터에서 실행할 수 있습니다.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker -out ./Config
```

`Node AuditBitlocker` 명령은 기술적으로 필요하지 않지만 기본값이 아닌 명명된 `AuditBitlocker.mof` 파일을 `localhost.mof`생성합니다. .mof 파일 이름을 사용하여 구성을 수행하면 대규모로 작동할 때 많은 파일을 쉽게 구성할 수 있습니다.

MOF가 컴파일되면 지원 파일을 함께 패키징해야 합니다. 완성된 패키지는 게스트 구성에서 Azure 정책 정의를 만드는 데 사용됩니다.

cmdlet이 `New-GuestConfigurationPackage` 패키지를 만듭니다. Windows 콘텐츠를 `New-GuestConfigurationPackage` 만들 때 cmdlet의 매개 변수:

- **이름**: 게스트 구성 패키지 이름입니다.
- **구성**: 컴파일된 DSC 구성 문서 전체 경로입니다.
- **경로**: 출력 폴더 경로입니다. 이 매개 변수는 선택 사항입니다. 지정하지 않으면 패키지가 현재 디렉터리에서 만들어집니다.

다음 명령을 실행하여 이전 단계에서 제공된 구성을 사용하여 패키지를 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

구성 패키지를 만든 후 Azure에 게시하기 전에 워크스테이션 또는 CI/CD 환경에서 패키지를 테스트할 수 있습니다. GuestConfiguration cmdlet `Test-GuestConfigurationPackage` Azure 컴퓨터 내에서 사용 되는 것과 개발 환경에서 동일한 에이전트를 포함 합니다. 이 솔루션을 사용하면 요금이 청구된 클라우드 환경에 릴리스하기 전에 로컬에서 통합 테스트를 수행할 수 있습니다.

에이전트가 실제로 로컬 환경을 평가하기 때문에 대부분의 경우 감사하려는 것과 동일한 OS 플랫폼에서 Test-cmdlet을 실행해야 합니다.

cmdlet의 `Test-GuestConfigurationPackage` 매개 변수:

- **이름**: 게스트 구성 정책 이름입니다.
- **매개 변수**: 해시 테이블 형식으로 제공되는 정책 매개 변수입니다.
- **경로**: 게스트 구성 패키지의 전체 경로입니다.

다음 명령을 실행하여 이전 단계에서 만든 패키지를 테스트합니다.

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

cmdlet은 PowerShell 파이프라인의 입력도 지원합니다. cmdlet의 `New-GuestConfigurationPackage` 출력을 cmdlet에 파이프합니다. `Test-GuestConfigurationPackage`

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

다음 단계는 파일을 Blob 저장소에 게시하는 것입니다. 아래 스크립트에는 이 작업을 자동화하는 데 사용할 수 있는 함수가 포함되어 있습니다. `publish` 함수에 사용되는 명령에는 모듈이 `Az.Storage` 필요합니다.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

게스트 구성 사용자 지정 정책 패키지를 만들고 업로드하면 게스트 구성 정책 정의를 만듭니다. cmdlet은 `New-GuestConfigurationPolicy` 사용자 지정 정책 패키지를 가져와 정책 정의를 만듭니다.

cmdlet의 `New-GuestConfigurationPolicy` 매개 변수:

- **ContentUri**: 게스트 구성 콘텐츠 패키지의 공개 http(들)uri.
- **표시 이름**: 정책 표시 이름입니다.
- **설명**: 정책 설명.
- **매개 변수**: 해시 테이블 형식으로 제공되는 정책 매개 변수입니다.
- **버전**: 정책 버전입니다.
- **경로**: 정책 정의가 만들어지는 대상 경로입니다.
- **플랫폼**: 게스트 구성 정책 및 콘텐츠 패키지에 대한 대상 플랫폼(Windows/Linux)입니다.

다음 예제에서는 사용자 지정 정책 패키지에서 지정 된 경로에서 정책 정의 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

다음 파일은 다음에 의해 `New-GuestConfigurationPolicy`만들어집니다.

- **auditIfNotExists.json**
- **배포IfNotExists.json**
- **이니셔티브.json**

cmdlet 출력은 정책 파일의 이니셔티브 표시 이름 및 경로를 포함하는 개체를 반환합니다.

마지막으로 `Publish-GuestConfigurationPolicy` cmdlet을 사용하여 정책 정의를 게시합니다. cmdlet에는 `New-GuestConfigurationPolicy`에서 만든 JSON 파일의 위치를 가리키는 **Path** 매개변수만 있습니다.

게시 명령을 실행하려면 Azure에서 정책을 만들 려면 액세스 권한이 필요합니다. 특정 권한 부여 요구 사항은 [Azure 정책 개요](../overview.md) 페이지에 설명되어 있습니다. 가장 좋은 기본 제공 역할은 **리소스 정책 참여자입니다.**

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

cmdlet은 `Publish-GuestConfigurationPolicy` PowerShell 파이프라인의 경로를 수락합니다. 이 기능은 정책 파일을 만들고 파이프된 명령의 단일 집합에 게시할 수 있습니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Azure에서 만든 정책을 사용하여 마지막 단계는 이니셔티브를 할당하는 것입니다. [포털,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)및 [Azure PowerShell을](../assign-policy-powershell.md)사용하여 이니셔티브를 할당하는 방법을 알아봅합니다.

> [!IMPORTANT]
> 게스트 구성 정책은 **항상** _AuditIfNotExists_ 및 _DeployIfNotExists_ 정책을 결합하는 이니셔티브를 사용하여 할당되어야 합니다. _AuditIfNotExists_ 정책만 할당된 경우 필수 구성 조건이 배포되지 않으며 정책에항상 '0' 서버가 준수임을 보여 드립니다.

_DeployIfNotExists_ 효과와 정책 정의를 할당하려면 추가 액세스 수준이 필요합니다. 최소 권한을 부여하려면 **리소스 정책 참여자.** 아래 예제는 _Microsoft.권한 부여/역할 할당/쓰기_를 추가 권한으로 **리소스 정책 기여자 DINE이라는** 역할을 만듭니다.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>사용자 지정 게스트 구성 정책에서 매개 변수 사용

게스트 구성은 런타임에 구성의 재정의 속성을 지원합니다. 이 기능은 패키지의 MOF 파일의 값을 정적으로 간주할 필요가 없다는 것을 의미합니다. 재정의 값은 Azure 정책을 통해 제공되며 구성을 작성하거나 컴파일하는 방법에 영향을 미치지 않습니다.

cmdlet `New-GuestConfigurationPolicy` 및 `Test-GuestConfigurationPolicyPackage` 매개 변수라는 매개 **변수를 포함합니다.** 이 매개 변수는 각 매개 변수에 대한 모든 세부 정보를 포함하여 해시 가능한 정의를 사용하고 Azure Policy 정의에 사용되는 각 파일의 필수 섹션을 만듭니다.

다음 예제에서는 사용자가 정책 할당 시 목록에서 선택하는 서비스를 감사하는 정책 정의를 만듭니다.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>정책 수명 주기

정책에 대한 업데이트를 릴리스하려면 주의가 필요한 두 가지 필드가 있습니다.

- **버전**: cmdlet을 `New-GuestConfigurationPolicy` 실행할 때 현재 게시된 버전 번호보다 큰 버전 번호를 지정해야 합니다. 에이전트가 업데이트된 패키지를 인식할 수 있도록 속성은 게스트 구성 할당의 버전을 업데이트합니다.
- **contentHash**: 이 속성은 cmdlet에 `New-GuestConfigurationPolicy` 의해 자동으로 업데이트됩니다. `New-GuestConfigurationPackage`에서 만든 패키지의 해시 값입니다. 게시하는 파일에 대한 `.zip` 속성이 정확해야 합니다. **contentUri** 속성만 업데이트된 경우 확장은 콘텐츠 패키지를 수락하지 않습니다.

업데이트된 패키지를 릴리스하는 가장 쉬운 방법은 이 문서에서 설명하는 프로세스를 반복하고 업데이트된 버전 번호를 제공하는 것입니다. 이 프로세스는 모든 속성이 올바르게 업데이트되었음을 보장합니다.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows 그룹 정책 콘텐츠를 Azure 정책 게스트 구성으로 변환

Windows 컴퓨터를 감사할 때 게스트 구성은 PowerShell 원하는 상태 구성 구문을 구현한 것입니다. DSC 커뮤니티는 내보낸 그룹 정책 템플릿을 DSC 형식으로 변환하는 도구를 게시했습니다. 위에서 설명한 게스트 구성 cmdlet과 함께 이 도구를 사용하면 Windows 그룹 정책 콘텐츠를 변환하고 Azure Policy를 감사할 수 있도록 해당 콘텐츠를 패키지/게시할 수 있습니다. 도구 사용에 대한 자세한 내용은 [빠른 시작: 그룹 정책을 DSC로 변환하는](/powershell/scripting/dsc/quickstarts/gpo-quickstart)문서를 참조하십시오.
콘텐츠가 변환되면 패키지를 만들고 Azure 정책으로 게시하는 위의 단계는 모든 DSC 콘텐츠와 동일합니다.

## <a name="optional-signing-guest-configuration-packages"></a>선택 사항: 게스트 구성 패키지 서명

게스트 구성 사용자 지정 정책은 SHA256 해시를 사용하여 정책 패키지가 변경되지 않았는지 확인합니다.
선택적으로 고객은 인증서를 사용하여 패키지에 서명하고 게스트 구성 확장이 서명된 콘텐츠만 허용하도록 강제할 수도 있습니다.

이 시나리오를 사용하려면 완료해야 하는 두 단계가 있습니다. cmdlet을 실행하여 콘텐츠 패키지에 서명하고 코드 서명을 해야 하는 컴퓨터에 태그를 추가합니다.

서명 유효성 검사 기능을 사용하려면 cmdlet을 `Protect-GuestConfigurationPackage` 실행하여 패키지가 게시되기 전에 서명합니다. 이 cmdlet에는 '코드 서명' 인증서가 필요합니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

cmdlet의 `Protect-GuestConfigurationPackage` 매개 변수:

- **경로**: 게스트 구성 패키지의 전체 경로입니다.
- **인증서**: 패키지에 서명하는 코드 서명 인증서입니다. 이 매개 변수는 Windows용 콘텐츠에 서명할 때만 지원됩니다.

GuestConfiguration 에이전트는 인증서 공개 키가 Windows 컴퓨터및 Linux 컴퓨터의 경로에 `/usr/local/share/ca-certificates/extra` "신뢰할 수 있는 루트 인증 기관"에 있을 것으로 예상합니다. 노드가 서명된 콘텐츠를 확인하려면 사용자 지정 정책을 적용하기 전에 컴퓨터에 인증서 공개 키를 설치합니다. 이 프로세스는 VM 내부의 모든 기술을 사용하거나 Azure 정책을 사용하여 수행할 수 있습니다. 예제 템플릿이 [여기에 제공됩니다.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)
키 볼트 액세스 정책은 Compute 리소스 공급자가 배포 하는 동안 인증서에 액세스할 수 있도록 허용 해야 합니다. 자세한 단계는 [Azure 리소스 관리자의 가상 컴퓨터에 대한 키 자격 증명 모음 설정을](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)참조하십시오.

다음은 서명 인증서에서 공개 키를 내보내 기계로 가져오는 예제입니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

콘텐츠가 게시된 후 코드 서명이 `GuestConfigPolicyCertificateValidation` 필요한 `enabled` 모든 가상 시스템에 이름과 값이 있는 태그를 부호. Azure 정책을 사용하여 태그를 대규모로 배달하는 방법에 대한 [태그 샘플을](../samples/built-in-policies.md#tags) 참조하십시오. 이 태그가 있으면 `New-GuestConfigurationPolicy` cmdlet을 사용하여 생성된 정책 정의를 통해 게스트 구성 확장을 통해 요구 사항을 사용할 수 있습니다.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>게스트 구성 정책 할당 문제 해결(미리 보기)

미리 보기에서 Azure 정책 게스트 구성 문제 해결을 지원하는 도구를 사용할 수 있습니다. 이 도구는 미리 보기 중이며 모듈 이름 [게스트 구성 문제 해결사로](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)PowerShell 갤러리에 게시되었습니다.

이 도구의 cmdlet에 대한 자세한 내용은 PowerShell의 도움말 Get-도움말 명령을 사용하여 기본 제공 지침을 표시합니다. 이 도구가 자주 업데이트되고 있기 때문에 가장 최신 정보를 얻는 가장 좋은 방법입니다.

## <a name="next-steps"></a>다음 단계

- [게스트 구성을](../concepts/guest-configuration.md)통해 VM 감사에 대해 자세히 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 이해합니다.
- [규정 준수 데이터를 얻는](get-compliance-data.md)방법에 대해 알아봅니다.
