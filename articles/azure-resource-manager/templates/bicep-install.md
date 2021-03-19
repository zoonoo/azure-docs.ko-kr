---
title: Bicep 개발 및 배포 환경 설정
description: Bicep 개발 및 배포 환경을 구성 하는 방법
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: d665a863affdec2009fc208f76b85a7f25de451d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594396"
---
# <a name="setup-bicep-development-and-deployment-environment"></a>Bicep 개발 및 배포 환경 설정

Bicep 개발 및 배포 환경을 설정 하는 방법에 대해 알아봅니다.

## <a name="development-environment"></a>개발 환경

최적의 Bicep 제작 환경을 얻으려면 다음 두 가지 구성 요소가 필요 합니다.

- **Visual Studio Code Bicep 확장** 입니다. Bicep 파일을 만들려면 좋은 Bicep 편집기가 필요 합니다. [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)을 사용 하 여 [Visual Studio Code](https://code.visualstudio.com/) 하는 것이 좋습니다. 이러한 도구는 언어 지원 및 리소스 자동 완성 기능을 제공 합니다. Bicep 파일을 만들고 유효성을 검사 하는 데 도움이 됩니다. Visual Studio Code 및 Bicep 확장 사용에 대 한 자세한 내용은 빠른 시작 [: Visual Studio Code를 사용 하 여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조 하세요.
- **BICEP CLI**. Bicep CLI를 사용 하 여 Bicep 파일을 ARM JSON 템플릿으로 컴파일하고 ARM JSON 템플릿을 Bicep 파일로 컴파일합니다. 자세한 내용은 [Install BICEP CLI](#install-bicep-cli)항목을 참조 하세요.

## <a name="deployment-environment"></a>배포 환경

Azure CLI 또는 Azure PowerShell를 사용 하 여 Bicep 파일을 배포할 수 있습니다. Azure CLI의 경우 버전 2.20.0 이상이 필요 합니다. Azure PowerShell의 경우 버전이 5.6.0 이상 필요 합니다. 설치 지침은 다음을 참조하세요.

- [Azure PowerShell 설치](/powershell/azure/install-az-ps)
- [Windows에 Azure CLI 설치](/cli/azure/install-azure-cli-windows)
- [Linux에 Azure CLI 설치](/cli/azure/install-azure-cli-linux)
- [macOS에 Azure CLI 설치](/cli/azure/install-azure-cli-macos)

> [!NOTE]
> 현재 Azure CLI와 Azure PowerShell 모두 로컬 Bicep 파일만 배포할 수 있습니다. Azure CLI를 사용 하 여 Bicep 파일을 배포 하는 방법에 대 한 자세한 내용은 [배포-CLI](/deploy-cli.md#deploy-remote-template)를 참조 하세요. Azure PowerShell를 사용 하 여 Bicep 파일을 배포 하는 방법에 대 한 자세한 내용은 [배포-PowerShell](/deploy-powershell.md#deploy-remote-template)을 참조 하세요.

지원 되는 Azure PowerShell 또는 Azure CLI 버전이 설치 되 면 다음을 사용 하 여 Bicep 파일을 배포할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Bicep CLI 설치

Azure PowerShell 또는을 (를) 사용 하 여 Azure CLI Bicep CLI를 설치할 수 있습니다.

### <a name="use-azure-cli"></a>Azure CLI 사용

Az CLI version 2.20.0 이상이 설치 되어 있으면이에 종속 된 명령이 실행 될 때 Bicep CLI가 자동으로 설치 됩니다. 예를 들어 `az deployment ... -f *.bicep` 또는 `az bicep ...`입니다.

기본 제공 명령을 사용 하 여 CLI를 수동으로 설치할 수도 있습니다.

```bash
az bicep install
```

최신 버전으로 업그레이드 하려면 다음을 수행 합니다.

```bash
az bicep upgrade
```

특정 버전을 설치 하려면:

```bash
az bicep install --version v0.2.212
```

> [!NOTE]
> Az CLI는 사용할 수 있는 다른 Bicep 설치와 충돌 하지 않는 별도의 Bicep CLI 버전을 설치 하 고 Az CLI는 Bicep를 경로에 추가 하지 않습니다.

설치 된 버전을 표시 하려면:

```bash
az bicep version
```

Bicep CLI의 사용 가능한 모든 버전을 나열 하려면 다음을 수행 합니다.

```bash
az bicep list-versions
```

### <a name="use-azure-powershell"></a>Azure PowerShell 사용

Azure PowerShell에는 Bicep CLI를 아직 설치할 수 있는 기능이 없습니다. Azure PowerShell (v 5.6.0 이상)에는 Bicep CLI가 이미 설치 되어 있고 경로에 사용할 수 있어야 합니다. [수동 설치 방법](#install-manually)중 하나를 수행 합니다. Bicep CLI가 설치 되 면 deployment cmdlet에 필요할 때마다 Bicep CLI가 호출 됩니다. 예: `New-AzResourceGroupDeployment ... -TemplateFile main.bicep`

### <a name="install-manually"></a>수동 설치

다음 메서드는 Bicep CLI를 설치 하 고 경로에 추가 합니다.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>via homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>macOS 수동 설치

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

[최신 Windows installer](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)를 다운로드 하 여 실행 합니다. 설치 관리자에는 관리자 권한이 필요 하지 않습니다. 설치 후에 Bicep CLI가 사용자 경로에 추가 됩니다. 경로 변경 내용을 적용 하려면 열려 있는 모든 명령 셸 창을 닫았다가 다시 엽니다.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>PowerShell을 사용 하 여 수동

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>야간 빌드 설치

릴리스 전에 Bicep의 최신 시험판 비트를 사용해 보려는 경우 [야간 빌드 설치](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)를 참조 하세요.

> [!WARNING]
> 이러한 시험판 빌드는 알려진 버그 또는 알려지지 않은 버그가 있을 가능성이 훨씬 높습니다.

## <a name="next-steps"></a>다음 단계

[Bicep 빠른](./quickstart-create-bicep-use-visual-studio-code.md)시작을 시작 하세요.
