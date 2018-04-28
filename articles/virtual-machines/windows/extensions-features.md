---
title: Azure에서 Windows용 가상 머신 확장 및 기능 | Microsoft Docs
description: 확장이 제공하거나 개선하는 기능별로 그룹화하여 Azure 가상 머신에 사용할 수 있는 확장을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09eb2e723be80fd623ad53c9bda8271ef8846a0f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Windows용 가상 머신 확장 및 기능

Azure 가상 머신 확장은 Azure 가상 머신에서 배포 후 구성 및 Automation 작업을 제공하는 작은 응용 프로그램입니다. 예를 들어 가상 컴퓨터에서 소프트웨어가 설치되도록 요구하거나, 바이러스 백신 보호 또는 Docker 구성을 요구하는 경우 VM 확장을 사용하여 이러한 작업을 완료할 수 있습니다. Azure CLI, PowerShell, Azure Resource Manager 템플릿 및 Azure Portal을 사용하여 Azure VM 확장을 실행할 수 있습니다. 확장을 새 가상 머신 배포와 번들로 제공하거나 기존 시스템에 대해 실행할 수 있습니다.

이 문서에서는 가상 머신 확장의 개요, 가상 머신 확장 사용을 위한 필수 구성 요소, 가상 머신 확장을 검색, 관리 및 제거하는 방법에 대한 지침을 제공합니다. 많은 VM 확장 각각을 고유한 구성으로 사용할 수 있으므로 여기서는 일반적인 정보를 제공합니다. 확장 관련 세부 정보는 개별 확장과 관련된 각 문서에서 찾을 수 있습니다.

## <a name="use-cases-and-samples"></a>사용 사례 및 샘플

각각 특정 사용 사례가 있는 많은 다양한 Azure VM 확장을 사용할 수 있습니다. 예제 사용 사례:

- Windows용 DSC 확장을 사용하여 가상 머신에 PowerShell의 필요한 상태 구성을 적용합니다. 자세한 내용은 [Azure 필요한 상태 구성 확장](extensions-dsc-overview.md)을 참조하세요.
- Microsoft Monitoring Agent VM 확장을 사용하여 가상 컴퓨터 모니터링을 구성합니다. 자세한 내용은 [Log Analytics에 Azure Virtual Machines 연결](../../log-analytics/log-analytics-azure-vm-extension.md)을 참조하세요.
- Datadog 확장으로 Azure 인프라의 모니터링을 구성합니다. 자세한 내용은 [Datadog 블로그](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)를 참조하세요.
- Chef를 사용하여 Azure Virtual Machine을 구성합니다. 자세한 내용은 [Chef를 사용하여 Azure 가상 머신 배포 자동화](chef-automation.md)를 참조하세요.

프로세스 관련 확장 외에도 Windows 및 Linux 가상 머신에 대해 사용자 지정 스크립트 확장을 사용할 수 있습니다. Windows용 사용자 지정 스크립트 확장을 사용하면 PowerShell 스크립트를 가상 머신에서 실행할 수 있습니다. 이 확장은 네이티브 Azure 도구로 제공할 수 있는 것 이상의 구성이 필요한 Azure 배포를 디자인할 때 유용합니다. 자세한 내용은 [Windows VM 사용자 지정 스크립트 확장](extensions-customscript.md)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

각 가상 머신 확장에는 고유한 필수 구성 요소 집합이 있을 수 있습니다. 예를 들어 Docker VM 확장에는 지원되는 Linux 배포판의 필수 구성 요소가 있습니다. 개별 확장의 요구 사항에 대해서는 확장 관련 설명서에 자세히 설명되어 있습니다.

### <a name="azure-vm-agent"></a>Azure VM 에이전트
Azure VM 에이전트는 Azure Virtual Machine과 Azure 패브릭 컨트롤러 간 상호 작용을 관리합니다. VM 에이전트는 VM 확장 실행을 포함하여 Azure Virtual Machine 배포 및 관리의 다양한 기능적 측면을 담당합니다. Azure VM 에이전트는 Azure Marketplace 이미지에 미리 설치되며 지원되는 운영 체제에 설치될 수 있습니다.

지원되는 운영 체제 및 설치 지침에 대한 자세한 내용은 [Azure Virtual Machines 에이전트](agent-user-guide.md)를 참조하세요.

## <a name="discover-vm-extensions"></a>VM 확장 검색
Azure Virtual Machine와 함께 여러 다양한 VM 확장을 사용할 수 있습니다. 전체 목록을 보려면 Azure Resource Manager PowerShell 모듈을 사용하여 다음 명령을 실행합니다. 이 명령을 실행하는 경우 원하는 위치를 지정해야 합니다.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>VM 확장 실행

Azure 가상 머신 확장은 기존 가상 머신에서 실행할 수 있습니다. 이러한 기능은 이미 배포한 VM의 구성을 변경하거나 연결을 복구해야 할 때 유용합니다. 또한 VM 확장을 Azure Resource Manager 템플릿 배포와 번들로 묶을 수도 있습니다. Resource Manager 템플릿에서 확장을 사용하면 Azure Virtual Machine을 배포하고, 배포 후 개입할 필요 없이 구성할 수 있습니다.

다음 방법을 사용하여 기존 가상 머신에 대해 확장을 실행할 수 있습니다.

### <a name="powershell"></a>PowerShell

개별 확장을 실행하기 위한 몇 가지 PowerShell 명령이 존재합니다. 목록을 보려면 다음 PowerShell 명령을 실행합니다.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

다음과 유사한 출력이 표시됩니다.

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

다음 예제에서는 사용자 지정 스크립트 확장을 사용하여 GitHub 리포지토리에서 대상 가상 머신에 스크립트를 다운로드한 후 스크립트를 실행합니다. 사용자 지정 스크립트 확장에 대한 자세한 내용은 [사용자 지정 스크립트 확장 개요](extensions-customscript.md)를 참조하세요.

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

이 예제에서는 Windows 가상 머신의 관리자 암호를 다시 설정하기 위해 VM 액세스 확장이 사용됩니다. VM 액세스 확장에 대한 자세한 내용은 [Windows VM에서 원격 데스크톱 서비스 다시 설정](reset-rdp.md)을 참조하세요.

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzureRmVMExtension` 명령을 사용하여 VM 확장을 시작할 수 있습니다. 자세한 내용은 [Set-AzureRmVMExtension 참조](https://msdn.microsoft.com/library/mt603745.aspx)를 참조하세요.


### <a name="azure-portal"></a>Azure portal

Azure Portal을 통해 기존 가상 컴퓨터에 VM 확장을 적용할 수 있습니다. 이렇게 하려면 사용하려는 가상 머신을 선택하고 **확장**을 선택한 후 **추가**를 클릭합니다. 그러면 사용 가능한 확장 목록이 제공됩니다. 원하는 확장을 선택하고 마법사의 단계를 따릅니다.

다음 이미지는 Azure Portal에서 Microsoft 맬웨어 방지 확장을 설치하는 경우를 보여 줍니다.

![Install antimalware extension](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure Resource Manager 템플릿에 VM 확장을 추가하고 템플릿 배포를 통해 실행할 수 있습니다. 템플릿 사용하여 확장을 배포하는 방식은 완전히 구성된 Azure 배포를 만드는 데 유용합니다. 예를 들어 다음 JSON은 부하 분산된 가상 머신 집합 및 Azure SQL Database를 배포한 후 각 VM에 .NET Core 응용 프로그램을 설치하는 Resource Manager 템플릿에서 가져옵니다. VM 확장은 소프트웨어 설치를 관리합니다.

자세한 내용은 전체 [Resource Manager 템플릿](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)을 참조하세요.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

자세한 내용은 [Windows VM 확장을 사용하여 Azure Resource Manager 템플릿 작성](template-description.md#extensions)을 참조하세요.

## <a name="secure-vm-extension-data"></a>VM 확장 데이터 보호

VM 확장을 실행하는 경우 자격 증명, 저장소 계정 이름 및 저장소 계정 액세스 키와 같은 중요한 정보를 포함해야 할 수도 있습니다. 많은 VM 확장에는 데이터를 암호화하고 대상 가상 머신 내에서만 해독하는 보호된 구성이 포함되어 있습니다. 각 확장에는 보호되는 특정 구성 스키마가 있으며 각각은 확장 관련 설명서에 자세히 나와 있습니다.

다음 예제에서는 Windows용 사용자 지정 스크립트 확장의 인스턴스를 보여 줍니다. 실행할 명령에는 자격 증명 집합이 포함됩니다. 이 예제에서는 실행할 명령이 암호화되지 않습니다.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

**실행할 명령** 속성을 **보호됨** 구성으로 이동하여 실행 문자열을 보호합니다.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>VM 확장 문제 해결

각 VM 확장에는 특정 문제 해결 단계가 있을 수 있습니다. 예를 들어 사용자 지정 스크립트 확장을 사용하는 경우 확장이 실행된 가상 머신에서 로컬로 스크립트 실행 세부 정보를 확인할 수 있습니다. 확장별 문제 해결 단계는 확장 관련 설명서에 자세히 나와 있습니다.

다음 문제 해결 단계는 모든 가상 머신 확장에 적용됩니다.

### <a name="view-extension-status"></a>확장 상태 보기

가상 머신에 대해 가상 머신 확장을 실행한 후에 다음 PowerShell 명령을 사용하여 확장 상태를 반환합니다. 매개 변수 이름을 고유한 값으로 바꿉니다. `Name` 매개 변수는 실행 시 확장에 지정된 이름을 사용합니다.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

출력은 다음과 같이 표시됩니다.

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

Azure Portal에서 확장 실행 상태를 찾을 수도 있습니다. 확장의 상태를 확인하려면 가상 컴퓨터를 선택하고 **확장**을 선택한 후 원하는 확장을 선택합니다.

### <a name="rerun-vm-extensions"></a>VM 확장 다시 실행

가상 머신 확장을 다시 실행해야 하는 경우가 있을 수 있습니다. 이를 위해 확장을 제거한 다음 원하는 실행 방법으로 확장을 다시 실행하면 됩니다. 확장을 제거하려면 Azure PowerShell 모듈을 사용하여 다음 명령을 실행합니다. 매개 변수 이름을 고유한 값으로 바꿉니다.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Azure Portal을 사용하여 확장을 제거할 수도 있습니다. 이렇게 하려면 다음을 수행합니다.

1. 가상 머신을 선택합니다.
2. **확장**을 섡택합니다.
3. 원하는 확장을 선택합니다.
4. **제거**를 선택합니다.

## <a name="common-vm-extensions-reference"></a>일반 VM 확장 참조
| 확장 이름 | 설명 | 자세한 정보 |
| --- | --- | --- |
| Windows용 사용자 지정 스크립트 확장 |Azure Virtual Machine에 대해 스크립트 실행 |[Windows용 사용자 지정 스크립트 확장](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Windows용 DSC 확장 |PowerShell DSC(Desired State Configuration) 확장 |[Windows용 DSC 확장](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure 진단 확장 |Azure 진단 관리 |[Azure 진단 확장](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM 액세스 확장 |사용자 및 자격 증명 관리 |[Linux용 VM 액세스 확장](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
