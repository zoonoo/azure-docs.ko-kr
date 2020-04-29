---
title: Windows에 대 한 게스트 구성 정책을 만드는 방법
description: Windows에 대 한 Azure Policy 게스트 구성 정책을 만드는 방법에 대해 알아봅니다.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: f09bb543f73e37bd211a55e2238808f57585bb18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024900"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Windows에 대 한 게스트 구성 정책을 만드는 방법

사용자 지정 정책을 만들기 전에 [게스트 구성 Azure Policy](../concepts/guest-configuration.md)페이지에서 개념 개요 정보를 읽는 것이 좋습니다.
 
Linux에 대 한 게스트 구성 정책을 만드는 방법에 대 한 자세한 내용은 [linux 용 게스트 구성 정책을 만드는 방법](./guest-configuration-create-linux.md) 페이지를 참조 하세요.

Windows를 감사할 때는 게스트 구성에서 DSC ( [필요한 상태 구성](/powershell/scripting/dsc/overview/overview) ) 리소스 모듈과 구성 파일을 사용 합니다. DSC 구성은 컴퓨터가 속해야 하는 조건을 정의 합니다.
구성 평가에 실패 하는 경우 정책 효과 **auditIfNotExists** 가 트리거되고 컴퓨터가 **비준수**로 간주 됩니다.

[Azure Policy 게스트 구성은](../concepts/guest-configuration.md) 컴퓨터 내의 설정을 감사 하는 데만 사용할 수 있습니다. 컴퓨터 내에서 설정의 재구성은 아직 사용할 수 없습니다.

다음 작업을 사용 하 여 Azure 또는 비 Azure 컴퓨터의 상태를 확인 하는 고유한 구성을 만듭니다.

> [!IMPORTANT]
> 게스트 구성을 사용 하는 사용자 지정 정책은 미리 보기 기능입니다.
>
> 게스트 구성 확장은 Azure virtual machines에서 감사를 수행 하는 데 필요 합니다.
> 모든 Windows 컴퓨터에서 확장을 대규모로 배포 하려면 다음 정책 정의를 할당 합니다.
>   - [Windows VM에서 게스트 구성 정책을 사용하도록 설정하기 위한 필수 조건 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>PowerShell 모듈 설치

게스트 구성 아티팩트 만들기, 아티팩트 자동 테스트, 정책 정의 만들기 및 정책 게시는 완전히 자동화 가능한 PowerShell의 게스트 구성 모듈을 사용 합니다. 이 모듈은 Windows, macOS 또는 Linux를 실행 하는 컴퓨터에 설치할 수 있습니다. PowerShell 6.2 이상에서 로컬로 실행 하거나 [Azure Cloud Shell](https://shell.azure.com)를 사용 하거나 [Azure PowerShell 핵심 Docker 이미지](https://hub.docker.com/r/azuresdk/azure-powershell-core)를 사용 하 여 설치할 수 있습니다.

> [!NOTE]
> 구성 컴파일은 Linux에서 아직 지원 되지 않습니다.

### <a name="base-requirements"></a>기본 요구 사항

모듈을 설치할 수 있는 운영 체제:

- Linux
- macOS
- Windows

게스트 구성 리소스 모듈에는 다음 소프트웨어가 필요 합니다.

- PowerShell 6.2 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/scripting/install/installing-powershell)을 따릅니다.
- Azure PowerShell 1.5.0 이상입니다. 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.
  - AZ modules ' Az. Accounts ' 및 ' Az .Resources '만 필요 합니다.

### <a name="install-the-module"></a>모듈 설치

PowerShell에서 **GuestConfiguration** 모듈을 설치 하려면 다음을 수행 합니다.

1. PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 모듈을 가져왔는지 확인 합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Windows에 대 한 게스트 구성 아티팩트 및 정책

게스트 구성은 PowerShell 필요한 상태 구성을 언어 추상화로 사용 하 여 Windows에서 감사할 항목을 작성 합니다. 에이전트는 PowerShell 6.2의 독립 실행형 인스턴스를 로드 하므로 Windows PowerShell 5.1의 PowerShell DSC 사용량과 충돌 하지 않으며 PowerShell 6.2 이상을 미리 설치할 필요가 없습니다.

DSC 개념 및 용어에 대 한 개요는 [POWERSHELL Dsc 개요](/powershell/scripting/dsc/overview/overview)를 참조 하세요.

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>게스트 구성 모듈이 Windows PowerShell DSC 모듈과 어떻게 다른 지

게스트 구성에서 컴퓨터를 감사 하는 경우:

1. 에이전트는 먼저를 `Test-TargetResource` 실행 하 여 구성이 올바른 상태 인지 확인 합니다.
1. 함수에서 반환 하는 부울 값은 게스트 할당에 대 한 Azure Resource Manager 상태를 준수/비준수로 지정 해야 하는지 여부를 결정 합니다.
1. 공급자는를 `Get-TargetResource` 실행 하 여 각 설정의 현재 상태를 반환 합니다 .이 경우 컴퓨터가 호환 되지 않는 이유와 현재 상태가 호환 되는지 확인 하는 데 대 한 세부 정보를 모두 사용할 수 있습니다.

### <a name="get-targetresource-requirements"></a>Test-targetresource 요구 사항

이 함수 `Get-TargetResource` 에는 Windows 필요한 상태 구성에 필요 하지 않은 게스트 구성에 대 한 특별 한 요구 사항이 있습니다.

- 반환 되는 해시 테이블에는 **이유**라는 속성이 포함 되어야 합니다.
- 이유 속성은 배열 이어야 합니다.
- 배열의 각 항목은 **코드** 및 **구**라는 키가 있는 해시 테이블 이어야 합니다.

이유 속성은 서비스에서 컴퓨터가 정책을 준수 하지 않는 경우 정보가 표시 되는 방식을 표준화 하는 데 사용 됩니다. 각 항목에는 리소스가 호환 되지 않는 "이유"로 생각할 수 있습니다. 두 가지 이상의 이유로 리소스가 호환 되지 않을 수 있기 때문에 속성은 배열입니다.

서비스에서 속성 **코드** 및 **구가** 필요 합니다. 사용자 지정 리소스를 작성 하는 경우 리소스를 준수 하지 않는 원인으로 표시 하려는 텍스트 (일반적으로 stdout)를 **구의**값으로 설정 합니다. **코드** 에는 감사를 수행 하는 데 사용 되는 리소스에 대 한 정보가 명확 하 게 표시 되도록 특정 형식 지정 요구 사항이 있습니다. 이 솔루션은 게스트 구성을 확장 가능 하 게 만듭니다. 출력을 **구** 속성에 대 한 문자열 값으로 반환할 수 있으면 모든 명령이 실행 될 수 있습니다.

- **Code** (string): 리소스의 이름으로, 반복 된 후 이유에 대 한 식별자로 공백 없이 짧은 이름입니다. 이 세 값은 공백 없이 콜론으로 구분 되어야 합니다.
  - 예를 들면 다음과 같습니다.`registry:registry:keynotpresent`
- **구** (문자열): 사람이 읽을 수 있는 텍스트로, 설정이 호환 되지 않는 이유를 설명 합니다.
  - 예를 들면 다음과 같습니다.`The registry key $key is not present on the machine.`

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

또한 리소스에 대 한 스키마 MOF에는이 속성을 포함 클래스로 추가 해야 합니다.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>구성 요구 사항

사용자 지정 구성의 이름은 모든 위치에서 일관적 이어야 합니다. 콘텐츠 패키지용 .zip 파일의 이름, MOF 파일의 구성 이름 및 리소스 관리자 템플릿에 있는 게스트 할당 이름은 동일 해야 합니다.

### <a name="scaffolding-a-guest-configuration-project"></a>게스트 구성 프로젝트 스 캐 폴딩

시작 및 샘플 코드에서 작업 프로세스를 가속화 하려는 개발자는 **게스트 구성 프로젝트**라는 커뮤니티 프로젝트를 설치할 수 있습니다. 프로젝트는 [Plaster](https://github.com/powershell/plaster) PowerShell 모듈에 대 한 템플릿을 설치 합니다. 이 도구를 사용 하 여 작업 구성 및 샘플 리소스를 비롯 한 프로젝트를 스 캐 폴드 프로젝트의 유효성을 검사 하는 일련의 [Pester](https://github.com/pester/pester) 테스트를 수행할 수 있습니다. 템플릿에는 게스트 구성 패키지의 빌드 및 유효성 검사를 자동화 하는 Visual Studio Code에 대 한 작업 러너도 포함 되어 있습니다. 자세한 내용은 GitHub 프로젝트 [게스트 구성 프로젝트](https://github.com/microsoft/guestconfigurationproject)를 참조 하세요.

일반적인 구성 작업에 대 한 자세한 내용은 [구성 작성, 컴파일 및 적용](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)을 참조 하세요.

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>게스트 구성 아티팩트의 예상 내용

완료 된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용 됩니다. 패키지는 다음으로 구성 됩니다.

- MOF로 컴파일된 DSC 구성
- 모듈 폴더
  - GuestConfiguration 모듈
  - DscNativeResources 모듈
  - Windows MOF에 필요한 DSC 리소스 모듈

PowerShell cmdlet은 패키지를 만드는 데 도움이 됩니다.
루트 수준 폴더 또는 버전 폴더가 필요 하지 않습니다.
패키지 형식은 .zip 파일 이어야 합니다.

### <a name="storing-guest-configuration-artifacts"></a>게스트 구성 아티팩트 저장

.Zip 패키지는 관리 되는 가상 컴퓨터에서 액세스할 수 있는 위치에 저장 해야 합니다.
이러한 예로는 GitHub 리포지토리, Azure 리포지토리 또는 Azure storage가 있습니다. 패키지를 공용으로 설정 하지 않으려는 경우 [SAS 토큰](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 을 URL에 포함할 수 있습니다.
이 구성은 패키지에 액세스 하 고 서비스와 통신 하지 않는 경우에만 적용 되지만 개인 네트워크의 컴퓨터에 대 한 [서비스 엔드포인트](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) 를 구현할 수도 있습니다.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Windows에 대 한 사용자 지정 게스트 구성 감사 정책 만들기 단계별 안내

감사 설정에 대 한 DSC 구성을 만듭니다. 다음 PowerShell 스크립트 예제에서는 **Auditbitlocker**라는 구성을 만들고, **PsDscResources** 리소스 모듈을 가져오고, 리소스를 `Service` 사용 하 여 실행 중인 서비스를 감사 합니다. 구성 스크립트는 Windows 또는 macOS 컴퓨터에서 실행할 수 있습니다.

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
AuditBitLocker ./Config
```

이 파일을 프로젝트 폴더 `config.ps1` 에 이름으로 저장 합니다. 터미널에서를 실행 `./config.ps1` 하 여 PowerShell에서 실행 합니다. 새 mof 파일이 생성 됩니다.

이 `Node AuditBitlocker` 명령은 기술적으로 `localhost.mof`필수는 아니지만 기본이 아닌 `AuditBitlocker.mof` 라는 파일을 생성 합니다. .Mof 파일 이름을 사용 하면 구성에 따라 대규모 작업을 수행할 때 많은 파일을 쉽게 구성할 수 있습니다.

MOF가 컴파일되면 지원 파일을 함께 패키지 해야 합니다. 완료 된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용 됩니다.

Cmdlet `New-GuestConfigurationPackage` 은 패키지를 만듭니다. 구성에 필요한 모듈은에서 `$Env:PSModulePath`사용할 수 있어야 합니다. Windows 콘텐츠를 `New-GuestConfigurationPackage` 만들 때의 cmdlet 매개 변수:

- **이름**: 게스트 구성 패키지 이름입니다.
- **구성**: 컴파일된 DSC 구성 문서 전체 경로입니다.
- **경로**: 출력 폴더 경로입니다. 이 매개 변수는 선택 사항입니다. 지정 하지 않으면 패키지가 현재 디렉터리에 만들어집니다.

다음 명령을 실행 하 여 이전 단계에서 지정한 구성을 사용 하 여 패키지를 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

구성 패키지를 만든 후 Azure에 게시 하기 전에 워크스테이션 또는 CI/CD 환경에서 패키지를 테스트할 수 있습니다. GuestConfiguration cmdlet `Test-GuestConfigurationPackage` 에는 Azure 컴퓨터 내에서 사용 되는 것과 동일한 에이전트가 개발 환경에 포함 되어 있습니다. 이 솔루션을 사용 하 여 청구 되는 클라우드 환경에 릴리스하기 전에 통합 테스트를 로컬로 수행할 수 있습니다.

에이전트는 실제로 로컬 환경을 평가 하므로 대부분의 경우 감사할 계획인 동일한 OS 플랫폼에서 테스트 cmdlet을 실행 해야 합니다. 테스트에는 콘텐츠 패키지에 포함 된 모듈만 사용 됩니다.

`Test-GuestConfigurationPackage` Cmdlet의 매개 변수:

- **이름**: 게스트 구성 정책 이름입니다.
- **매개 변수**: 해시 테이블 형식으로 제공 되는 정책 매개 변수입니다.
- **경로**: 게스트 구성 패키지의 전체 경로입니다.

다음 명령을 실행 하 여 이전 단계에서 만든 패키지를 테스트 합니다.

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Cmdlet은 PowerShell 파이프라인의 입력도 지원 합니다. Cmdlet의 `New-GuestConfigurationPackage` 출력을 `Test-GuestConfigurationPackage` cmdlet으로 파이프 합니다.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

다음 단계는 blob 저장소에 파일을 게시 하는 것입니다. 아래 스크립트에는이 작업을 자동화 하는 데 사용할 수 있는 함수가 포함 되어 있습니다. `publish` 함수에 사용 되는 명령에는 `Az.Storage` 모듈이 필요 합니다.

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

게스트 구성 사용자 지정 정책 패키지를 만들고 업로드 한 후에는 게스트 구성 정책 정의를 만듭니다. Cmdlet `New-GuestConfigurationPolicy` 은 사용자 지정 정책 패키지를 사용 하 고 정책 정의를 만듭니다.

`New-GuestConfigurationPolicy` Cmdlet의 매개 변수:

- **Contenturi**: 게스트 구성 콘텐츠 패키지의 공용 http (s) uri입니다.
- **DisplayName**: Policy 표시 이름입니다.
- **설명**: 정책 설명입니다.
- **매개 변수**: 해시 테이블 형식으로 제공 되는 정책 매개 변수입니다.
- **버전**: 정책 버전
- **경로**: 정책 정의가 생성 되는 대상 경로입니다.
- **Platform**: 게스트 구성 정책 및 콘텐츠 패키지를 위한 대상 플랫폼 (Windows/Linux)입니다.

다음 예에서는 사용자 지정 정책 패키지에서 지정 된 경로에 정책 정의를 만듭니다.

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

에서 `New-GuestConfigurationPolicy`생성 되는 파일은 다음과 같습니다.

- **auditIfNotExists**
- **deployIfNotExists. json**
- **이니셔티브. json**

Cmdlet 출력은 정책 파일의 이니셔티브 표시 이름 및 경로를 포함 하는 개체를 반환 합니다.

마지막으로 `Publish-GuestConfigurationPolicy` cmdlet을 사용 하 여 정책 정의를 게시 합니다. Cmdlet에는에서 `New-GuestConfigurationPolicy`만든 JSON 파일의 위치를 가리키는 **Path** 매개 변수만 있습니다.

Publish 명령을 실행 하려면 Azure에서 정책을 만들기 위한 액세스 권한이 필요 합니다. 특정 권한 부여 요구 사항은 [Azure Policy 개요](../overview.md) 페이지에 설명 되어 있습니다. 가장 적합 한 기본 제공 역할은 **리소스 정책 기여자**입니다.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

Cmdlet `Publish-GuestConfigurationPolicy` 은 PowerShell 파이프라인의 경로를 허용 합니다. 이 기능은 정책 파일을 만들고 단일 파이프 된 명령 집합에 게시할 수 있음을 의미 합니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Azure에서 만든 정책을 사용 하 여 마지막 단계는 이니셔티브를 할당 하는 것입니다. [포털](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)및 [Azure PowerShell](../assign-policy-powershell.md)를 사용 하 여 이니셔티브를 할당 하는 방법을 참조 하세요.

> [!IMPORTANT]
> 게스트 구성 정책은 **항상** _AuditIfNotExists_ 및 _deployifnotexists_ 정책을 결합 하는 이니셔티브를 사용 하 여 할당 되어야 합니다. _AuditIfNotExists_ 정책만 할당 된 경우 필수 구성 요소가 배포 되지 않으며 정책에 항상 ' 0 ' 서버가 규정을 준수 하는 것으로 표시 됩니다.

_Deployifnotexists_ 효과를 사용 하 여 정책 정의를 할당 하려면 추가 액세스 수준이 필요 합니다. 최소 권한을 부여 하려면 **리소스 정책 참가자**를 확장 하는 사용자 지정 역할 정의를 만들 수 있습니다. 아래 예제에서는 추가 권한 _Microsoft. Authorization/roleAssignments/write_를 사용 하 여 **리소스 정책 참여자 식사** 라는 역할을 만듭니다.

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

게스트 구성에서는 런타임에 구성의 속성을 재정의할 수 있습니다. 이 기능은 패키지의 MOF 파일에 있는 값을 정적으로 간주할 필요가 없음을 의미 합니다. 재정의 값은 Azure Policy를 통해 제공 되며 구성을 작성 하거나 컴파일하는 방법에 영향을 주지 않습니다.

Cmdlet `New-GuestConfigurationPolicy` 은 매개 `Test-GuestConfigurationPolicyPackage` 변수 라는 매개 변수를 포함 **합니다.** 이 매개 변수는 각 매개 변수에 대 한 모든 세부 정보를 포함 하는 해시 테이블 정의를 사용 하 고 Azure Policy 정의에 사용 되는 각 파일의 필수 섹션을 만듭니다.

다음 예에서는 정책을 할당할 때 사용자가 목록에서 선택 하는 서비스를 감사 하는 정책 정의를 만듭니다.

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

정책에 대 한 업데이트를 해제 하려는 경우 주의가 필요한 두 개의 필드가 있습니다.

- **버전**: `New-GuestConfigurationPolicy` cmdlet을 실행할 때 현재 게시 된 것 보다 큰 버전 번호를 지정 해야 합니다. 속성은 에이전트가 업데이트 된 패키지를 인식 하도록 게스트 구성 할당의 버전을 업데이트 합니다.
- **contentHash**:이 속성은 cmdlet에 `New-GuestConfigurationPolicy` 의해 자동으로 업데이트 됩니다. 에서 `New-GuestConfigurationPackage`만든 패키지의 해시 값입니다. 속성은 게시 하는 `.zip` 파일에 대해 올바른 이어야 합니다. **Contenturi** 속성만 업데이트 된 경우에는 확장에서 콘텐츠 패키지를 수락 하지 않습니다.

업데이트 된 패키지를 해제 하는 가장 쉬운 방법은이 문서에 설명 된 프로세스를 반복 하 고 업데이트 된 버전 번호를 제공 하는 것입니다. 이 프로세스는 모든 속성이 올바르게 업데이트 되었는지 보장 합니다.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows 그룹 정책 콘텐츠를 Azure Policy 게스트 구성으로 변환

Windows 컴퓨터를 감사할 때 게스트 구성은 PowerShell 필요한 상태 구성 구문의 구현입니다. DSC 커뮤니티에서는 내보낸 그룹 정책 템플릿을 DSC 형식으로 변환 하는 도구를 게시 했습니다. 위에서 설명한 게스트 구성 cmdlet과 함께이 도구를 사용 하 여 Windows 그룹 정책 콘텐츠를 변환 하 고 Azure Policy 감사를 위해 패키지/게시할 수 있습니다. 도구를 사용 하는 방법에 대 한 자세한 내용은 [빠른 시작: DSC로 그룹 정책 변환](/powershell/scripting/dsc/quickstarts/gpo-quickstart)문서를 참조 하세요.
콘텐츠를 변환한 후에는 패키지를 만들고 Azure Policy로 게시 하는 단계는 모든 DSC 콘텐츠와 동일 합니다.

## <a name="optional-signing-guest-configuration-packages"></a>선택 사항: 게스트 구성 패키지 서명

게스트 구성 사용자 지정 정책은 SHA256 해시를 사용 하 여 변경 되지 않은 정책 패키지의 유효성을 검사 합니다.
필요에 따라 고객은 인증서를 사용 하 여 패키지에 서명 하 고 게스트 구성 확장에서 서명 된 콘텐츠를 허용 하도록 강제할 수도 있습니다.

이 시나리오를 사용 하려면 두 단계를 완료 해야 합니다. Cmdlet을 실행 하 여 콘텐츠 패키지에 서명 하 고 코드에 서명 해야 하는 컴퓨터에 태그를 추가 합니다.

서명 유효성 검사 기능을 사용 하려면 게시 하기 `Protect-GuestConfigurationPackage` 전에 cmdlet을 실행 하 여 패키지에 서명 합니다. 이 cmdlet에는 ' 코드 서명 ' 인증서가 필요 합니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` Cmdlet의 매개 변수:

- **경로**: 게스트 구성 패키지의 전체 경로입니다.
- **Certificate**: 패키지에 서명할 코드 서명 인증서입니다. 이 매개 변수는 Windows 콘텐츠에 서명 하는 경우에만 지원 됩니다.

GuestConfiguration 에이전트는 인증서 공개 키가 Windows 컴퓨터의 "신뢰할 수 있는 루트 인증 기관" 및 Linux 컴퓨터의 경로 `/usr/local/share/ca-certificates/extra` 에 있는 것으로 예상 합니다. 노드가 서명 된 콘텐츠를 확인 하려면 사용자 지정 정책을 적용 하기 전에 컴퓨터에 인증서 공개 키를 설치 합니다. 이 프로세스는 VM 내에서 또는 Azure Policy를 사용 하 여 수행할 수 있습니다. 예제 템플릿이 여기에 [제공](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)됩니다.
Key Vault 액세스 정책은 배포 하는 동안 계산 리소스 공급자가 인증서에 액세스할 수 있도록 허용 해야 합니다. 자세한 단계는 [Azure Resource Manager에서 가상 컴퓨터에 대 한 Key Vault 설정](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)을 참조 하세요.

다음은 서명 인증서에서 공개 키를 내보내 컴퓨터로 가져오는 예제입니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

콘텐츠를 게시 한 후에는 코드 서명이 필요한 모든 `GuestConfigPolicyCertificateValidation` 가상 컴퓨터 `enabled` 에 이름 및 값이 포함 된 태그를 추가 합니다. Azure Policy를 사용 하 여 태그를 대규모로 배달할 수 있는 방법에 대 한 [태그 샘플](../samples/built-in-policies.md#tags) 을 참조 하세요. 이 태그가 준비 되 면 `New-GuestConfigurationPolicy` cmdlet을 사용 하 여 생성 된 정책 정의를 통해 게스트 구성 확장을 통해 요구 사항을 충족할 수 있습니다.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>게스트 구성 정책 할당 문제 해결 (미리 보기)

도구는 게스트 구성 할당 Azure Policy 문제 해결을 지원 하기 위해 미리 보기에서 사용할 수 있습니다. 이 도구는 미리 보기 상태 이며 PowerShell 갤러리 모듈 이름 [게스트 구성 문제 해결사로](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)게시 되었습니다.

이 도구의 cmdlet에 대 한 자세한 내용은 PowerShell에서 Get-help 명령을 사용 하 여 기본 제공 지침을 표시 합니다. 이 도구를 자주 업데이트 하는 경우 가장 최근의 정보를 얻는 가장 좋은 방법입니다.

## <a name="next-steps"></a>다음 단계

- [게스트 구성을](../concepts/guest-configuration.md)사용 하 여 vm을 감사 하는 방법을 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](get-compliance-data.md)방법에 대해 알아봅니다.
