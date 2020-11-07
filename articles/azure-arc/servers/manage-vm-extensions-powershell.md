---
title: Azure PowerShell를 사용 하 여 VM 확장 사용
description: 이 문서에서는 Azure PowerShell를 사용 하 여 하이브리드 클라우드 환경에서 실행 되는 Azure Arc 사용 서버에 가상 머신 확장을 배포 하는 방법을 설명 합니다.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 5ed9db23cd19814ff05c2f142f51cea869f2c2d4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359071"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure VM 확장 사용

이 문서에서는 Azure PowerShell를 사용 하 여 Azure Arc 사용 서버에서 지 원하는 Azure VM 확장을 Linux 또는 Windows 하이브리드 컴퓨터에 배포 하 고 제거 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure PowerShell이 설치된 컴퓨터 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

Azure PowerShell를 사용 하 여 Arc 사용 서버에서 관리 하는 하이브리드 서버에서 VM 확장을 관리 하려면 먼저 모듈을 설치 해야 `Az.ConnectedMachine` 합니다. Arc 사용 서버에서 다음 명령을 실행 합니다.

`Install-Module -Name Az.ConnectedMachine`.

설치가 완료 되 면 다음 메시지가 반환 됩니다.

`The installed extension `Az. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>확장 사용

Arc 사용 서버에서 VM 확장을 사용 하도록 설정 하려면, [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) ,,, `-Name` `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` ,- `ExtensionType` 및 `-Settings` 매개 변수와 함께 AzConnectedMachineExtension를 사용 합니다.

다음 예제에서는 Arc 사용 Linux 서버에서 Log Analytics VM 확장을 사용 하도록 설정 합니다.

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

다음 예에서는 Arc 사용 서버에서 사용자 지정 스크립트 확장을 사용 하도록 설정 합니다.

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault VM 확장 (미리 보기)

> [!WARNING]
> PowerShell 클라이언트는 `\` settings.js에를 추가 하 여 `"` akvvm_service 오류로 인해 실패 하는 경우가 많습니다. `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

다음 예에서는 Arc 사용 서버에서 Key Vault VM 확장 (미리 보기)을 사용 하도록 설정 합니다.

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @{
        "observedCert1"
       }
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationLocationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>설치 된 목록 확장

Arc 사용 서버에서 VM 확장 목록을 가져오려면 및 매개 변수와 함께 [AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) 를 사용 `-MachineName` `-ResourceGroupName` 합니다.

예:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>설치 된 확장 제거

Arc 사용 서버에서 설치 된 VM 확장을 제거 하려면 [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) `-Name` , 및 매개 변수와 함께 AzConnectedMachineExtension를 사용 `-MachineName` `-ResourceGroupName` 합니다.

예를 들어 Linux 용 Log Analytics VM 확장을 제거 하려면 다음 명령을 실행 합니다.

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>다음 단계

- [Azure CLI](manage-vm-extensions-cli.md), [Azure Portal](manage-vm-extensions-portal.md)또는 [AZURE RESOURCE MANAGER 템플릿을](manage-vm-extensions-template.md)사용 하 여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 가이드](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
