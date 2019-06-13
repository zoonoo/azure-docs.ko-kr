---
title: Azure 빠른 시작 - Resource Manager 템플릿을 사용하여 VM 백업
description: Azure Resource Manager 템플릿을 사용하여 가상 머신을 백업하는 방법을 알아봅니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b144d7509562b8ca0bca6299caee4a7ce292f4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481094"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Azure에서 가상 머신 백업

[Azure Backup](backup-overview.md)은 온-프레미스 머신과 앱, Azure VM을 백업합니다. 이 문서에서는 Resource Manager 템플릿과 Azure PowerShell을 사용하여 Azure VM을 백업하는 방법을 보여줍니다. 이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들기 위해 Resource Manager 템플릿을 배포하는 과정에 중점을 둡니다. Resource Manager 탬플릿 개발에 대한 자세한 내용은 [Resource Manager 설명서](/azure/azure-resource-manager/) 및 [템플릿 참조](/azure/templates/microsoft.recoveryservices/allversions)를 참조하세요.

또는 [Azure PowerShell](./quick-backup-vm-powershell.md), [Azure CLI](quick-backup-vm-cli.md)를 사용하거나 [Azure Portal](quick-backup-vm-portal.md)에서 VM을 백업할 수 있습니다.

## <a name="create-a-vm-and-recovery-services-vault"></a>VM 및 Recovery Services 자격 증명 모음 만들기

[Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)은 Azure VM과 같은 보호된 리소스에 대한 백업 데이터를 저장하는 논리 컨테이너입니다. 백업 작업이 실행되면 Recovery Services 자격 증명 모음 내에 복구 지점이 만들어집니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.

이 빠른 시작에 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)에서 나온 것입니다. 이 템플릿을 사용하면 보호를 위해 DefaultPolicy로 구성된 간단한 Windows VM 및 Recovery Services 자격 증명 모음을 배포할 수 있습니다.

템플릿을 배포하려면 **사용해 보세요!** 를 선택하여 Azure Cloud Shell을 열고, 다음 PowerShell 스크립트를 셸 창에 붙여넣습니다. 코드를 붙여넣으려면 셸 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

이 빠른 시작에서는 Azure PowerShell이 Resource Manager 템플릿을 배포하는 데 사용됩니다. [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md), [Rest API](../azure-resource-manager/resource-group-template-deploy-rest.md)도 템플릿을 배포하는 데 사용할 수 있습니다.

## <a name="start-a-backup-job"></a>백업 작업 시작

템플릿은 VM을 만들고 VM에서 다시 사용하도록 설정합니다. 템플릿을 배포한 후에는 백업 작업을 시작해야 합니다. 자세한 내용은 [백업 작업 시작](./quick-backup-vm-powershell.md#start-a-backup-job)을 참조하세요.

## <a name="monitor-the-backup-job"></a>백업 작업 모니터링

백업 작업을 모니터링 하려면 [백업 작업 모니터링](./quick-backup-vm-powershell.md#monitor-the-backup-job)을 참조하세요.

## <a name="clean-up-the-deployment"></a>배포 정리

VM을 더 이상 백업할 필요가 없으면 이를 정리할 수 있습니다.

- VM 복원을 시도하려면 정리 단계를 건너뜁니다.
- 기존 VM을 사용한 경우 마지막 단계인 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 건너뛰어 리소스 그룹과 VM을 그대로 유지할 수 있습니다.

보호 기능을 해제하고 복원 지점 및 자격 증명 모음을 제거합니다. 그런 다음, 리소스 그룹 및 연결된 VM 리소스를 다음과 같이 삭제합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들고, VM에 대한 보호를 사용하도록 설정하고, 초기 복구 지점을 만들었습니다.

- Azure Portal에서 VM을 백업하는 방법을 [알아봅니다](tutorial-backup-vm-at-scale.md).
- VM을 빠르게 복원하는 방법을 [알아봅니다](tutorial-restore-disk.md).
