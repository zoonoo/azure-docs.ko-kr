---
title: Bicep 개발 및 배포 환경 설정
description: Bicep 개발 및 배포 환경을 구성하는 방법
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 39bac9d8494c9da94fe962dda9521bca4d071f1e
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109752772"
---
# <a name="install-bicep-preview"></a>Bicep 설치(미리 보기)

Bicep 개발 및 배포 환경을 설정하는 방법을 알아봅니다.

## <a name="development-environment"></a>개발 환경

최적의 Bicep 제작 환경을 얻으려면 다음 두 가지 구성 요소가 필요합니다.

- **Visual Studio Code용 Bicep 확장** Bicep 파일을 만들려면 적절한 Bicep 편집기가 필요합니다. [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)이 포함된 [Visual Studio Code](https://code.visualstudio.com/)가 권장됩니다. 이러한 도구는 언어 지원 및 리소스 자동 완성 기능을 제공합니다. 이러한 도구는 Bicep 파일을 만들고 유효성을 검사하는 데 유용합니다. Visual Studio Code 및 Bicep 확장 사용에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기](./quickstart-create-bicep-use-visual-studio-code.md)를 참조하세요.
- **Bicep CLI**. Bicep CLI를 사용하여 Bicep 파일을 ARM JSON 템플릿으로 컴파일하고 ARM JSON 템플릿을 Bicep 파일로 디컴파일합니다. 설치 지침은 [Bicep CLI 설치](#install-manually)를 참조하세요.

## <a name="deployment-environment"></a>배포 환경

로컬 Bicep 파일을 배포하려면 다음 두 가지 구성 요소가 필요합니다.

- **Azure CLI 버전 2.20.0 이상 또는 Azure PowerShell 버전 5.6.0 이상** 설치 지침은 다음을 참조하세요.

  - [Azure PowerShell 설치](/powershell/azure/install-az-ps)
  - [Windows에 Azure CLI 설치](/cli/azure/install-azure-cli-windows)
  - [Linux에 Azure CLI 설치](/cli/azure/install-azure-cli-linux)
  - [macOS에 Azure CLI 설치](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > 현재 Azure CLI와 Azure PowerShell 모두 로컬 Bicep 파일만 배포할 수 있습니다. Azure CLI를 사용하여 Bicep 파일을 배포하는 방법에 대한 자세한 내용은 [배포 - CLI](./deploy-cli.md#deploy-remote-template)를 참조하세요. Azure PowerShell을 사용하여 Bicep 파일을 배포하는 방법에 대한 자세한 내용은 [배포 - PowerShell]( ./deploy-powershell.md#deploy-remote-template)을 참조하세요.

- **Bicep CLI**. Bicep CLI는 배포 전에 Bicep 파일을 JSON 템플릿으로 컴파일하는 데 필요합니다. 설치 지침은 [Bicep CLI 설치](#install-bicep-cli)를 참조하세요.

구성 요소가 설치된 후 다음을 사용하여 Bicep 파일을 배포할 수 있습니다.

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

- Bicep CLI를 사용하여 Bicep 파일을 컴파일 및 디컴파일하려면 [수동으로 설치](#install-manually)를 참조하세요.
- Azure CLI를 사용하여 Bicep 파일을 배포하려면 [Azure CLI 사용](#use-with-azure-cli)을 참조하세요.
- Azure PowerShell을 사용하여 Bicep 파일을 배포하려면 [Azure PowerShell 사용](#use-with-azure-powershell)을 참조하세요.

### <a name="use-with-azure-cli"></a>Azure CLI 사용

Azure CLI 버전 2.20.0 이상 버전을 설치한 경우에는 종속된 명령이 실행될 때 Bicep CLI가 자동으로 설치됩니다. 예를 들면 다음과 같습니다.

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

또는

```azurecli
az bicep ...
```

기본 제공 명령을 사용하여 CLI를 수동으로 설치할 수도 있습니다.

```bash
az bicep install
```

최신 버전으로 업그레이드하려면 다음을 수행합니다.

```bash
az bicep upgrade
```

특정 버전을 설치하려면

```bash
az bicep install --version v0.3.255
```

> [!IMPORTANT]
> Azure CLI는 발생할 수 있는 다른 Bicep 설치와 충돌하지 않는 별도의 Bicep CLI 버전을 설치하고 Azure CLI는 경로에 Bicep CLI를 추가하지 않습니다. Bicep CLI를 사용하여 Bicep 파일을 컴파일/디컴파일하거나, Azure PowerShell을 사용하여 Bicep 파일을 배포하려면 [수동으로 설치](#install-manually) 또는 [Azure PowerShell 사용](#use-with-azure-powershell)을 참조하세요.

Bicep CLI의 사용 가능한 모든 버전을 나열하려면

```bash
az bicep list-versions
```

설치된 버전을 표시하려면

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Azure PowerShell 사용

Azure PowerShell에는 아직 Bicep CLI를 설치할 수 있는 기능이 없습니다. Azure PowerShell(v 5.6.0 이상)에서는 Bicep CLI가 이미 설치되어 있고 PATH에서 사용할 수 있어야 합니다. [수동 설치 방법](#install-manually) 중 하나를 수행합니다.

Bicep 파일을 배포하려면 Bicep CLI 버전 0.3.1 이상이 필요합니다. Bicep CLI 버전을 확인하려면

```cmd
bicep --version
```

> [!IMPORTANT]
> Azure CLI는 고유한 자체 포함 버전 Bicep CLI를 설치합니다. Azure CLI에 대한 필수 버전이 설치되어 있는 경우에도 Azure PowerShell 배포가 실패합니다.

Bicep CLI가 설치되면 deployment cmdlet에 필요할 때마다 Bicep CLI가 호출됩니다. 예를 들면 다음과 같습니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>수동 설치

다음 메서드는 Bicep CLI를 설치하고 경로에 추가합니다.

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

##### <a name="via-homebrew"></a>homebrew를 통해

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
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

[최신 Windows 설치 관리자](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)를 다운로드하여 실행합니다. 설치 관리자에는 관리자 권한이 필요하지 않습니다. 설치 후에 Bicep CLI가 사용자 PATH에 추가됩니다. PATH 변경 내용을 적용하려면 열려 있는 모든 명령 셸 창을 닫았다가 다시 엽니다.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>PowerShell을 사용하여 수동으로

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

릴리스 전에 Bicep의 시험판 비트를 사용해 보려는 경우 [야간 빌드 설치](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)를 참조하세요.

> [!WARNING]
> 이러한 시험판 빌드에는 알려진 버그 또는 알려지지 않은 버그가 있을 가능성이 훨씬 높습니다.

## <a name="next-steps"></a>다음 단계

[Bicep 빠른 시작](./quickstart-create-bicep-use-visual-studio-code.md)을 시작합니다.
