---
title: Migrate to Resource Manager with PowerShell
description: This article walks through the platform-supported migration of IaaS resources such as virtual machines (VMs), virtual networks, and storage accounts from classic to Azure Resource Manager by using Azure PowerShell commands
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: f87e7795416431305141de24497e9760eb03641e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484380"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrate IaaS resources from classic to Azure Resource Manager by using PowerShell
이러한 단계에서는 Azure PowerShell 명령을 사용하여 클래식 배포 모델의 laaS(Infrastructure as a Service) 리소스를 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 보여 줍니다.

If you want, you can also migrate resources by using the [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* 지원되는 마이그레이션 시나리오에 대한 배경 정보를 보려면 [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](migration-classic-resource-manager-overview.md)을 참조하세요.
* 자세한 지침 및 마이그레이션 연습에 대해서는 [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)를 참조하세요.
* [Review the most common migration errors](migration-classic-resource-manager-errors.md).

<br>
Here's a flowchart to identify the order in which steps need to be executed during a migration process.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>1단계: 마이그레이션 계획
Here are a few best practices that we recommend as you evaluate whether to migrate IaaS resources from classic to Resource Manager:

* [지원/지원되지 않는 기능 및 구성](migration-classic-resource-manager-overview.md)을 읽어봅니다. If you have virtual machines that use unsupported configurations or features, wait for the configuration or feature support to be announced. 필요에 따라서는 마이그레이션이 가능하도록 해당 기능을 제거하거나 구성 외부로 이동할 수도 있습니다.
* 현재 인프라 및 애플리케이션을 배포하는 스크립트를 자동화한 경우 마이그레이션을 위해 해당 스크립트를 사용하여 유사한 테스트 설정을 만들어봅니다. 또는 Azure 포털을 사용하여 샘플 환경을 설정할 수도 있습니다.

> [!IMPORTANT]
> Application gateways aren't currently supported for migration from classic to Resource Manager. To migrate a virtual network with an application gateway, remove the gateway before you run a Prepare operation to move the network. 마이그레이션을 완료한 후 Azure Resource Manager에서 게이트웨이를 다시 연결합니다.
>
> Azure ExpressRoute gateways that connect to ExpressRoute circuits in another subscription can't be migrated automatically. In such cases, remove the ExpressRoute gateway, migrate the virtual network, and re-create the gateway. For more information, see [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Step 2: Install the latest version of PowerShell
Azure PowerShell을 설치하는 두 가지 주요 옵션으로 [PowerShell 갤러리](https://www.powershellgallery.com/profiles/azure-sdk/) 또는 [WebPI(웹 플랫폼 설치 관리자)](https://aka.ms/webpi-azps)가 있습니다. WebPI는 매월 업데이트를 수신합니다. PowerShell 갤러리는 지속적으로 업데이트를 수신합니다. 이 문서는 Azure PowerShell 버전 2.1.0을 기반으로 합니다.

설치 지침은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

<br>

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Step 3: Ensure that you're an administrator for the subscription
To perform this migration, you must be added as a coadministrator for the subscription in the [Azure portal](https://portal.azure.com).

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
2. On the **Hub** menu, select **Subscription**. 표시되지 않으면 **모든 서비스**를 선택합니다.
3. Find the appropriate subscription entry, and then look at the **MY ROLE** field. For a coadministrator, the value should be _Account admin_.

If you're not able to add a coadministrator, contact a service administrator or coadministrator for the subscription to get yourself added.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Step 4: Set your subscription, and sign up for migration
먼저, PowerShell 프롬프트를 시작합니다. For migration, set up your environment for both classic and Resource Manager.

Resource Manager 모델에 대한 계정으로 로그인합니다.

```powershell
    Connect-AzAccount
```

다음 명령으로 사용 가능한 구독을 가져옵니다.

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

현재 세션에 대한 Azure 구독을 설정합니다. 이 예제에서는 기본 구독 이름을 **내 Azure 구독**으로 설정합니다. 예제 구독 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registration is a one-time step, but you must do it once before you attempt migration. 등록하지 않으면 다음과 같은 오류 메시지가 표시됩니다.
>
> *BadRequest : 구독이 마이그레이션에 대해 등록되지 않았습니다.*

다음 명령을 사용하여 마이그레이션 리소스 공급자에 등록합니다.

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Wait five minutes for the registration to finish. Check the status of the approval by using the following command:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

계속 진행하기 전에 RegistrationState가 `Registered` 인지 확인합니다.

Now, sign in to your account for the classic deployment model.

```powershell
    Add-AzureAccount
```

다음 명령으로 사용 가능한 구독을 가져옵니다.

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

현재 세션에 대한 Azure 구독을 설정합니다. 이 예제에서는 기본 구독을 **내 Azure 구독**으로 설정합니다. 예제 구독 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-have-enough-resource-manager-vm-vcpus"></a>Step 5: Have enough Resource Manager VM vCPUs
Make sure that you have enough Azure Resource Manager virtual machine vCPUs in the Azure region of your current deployment or virtual network. 다음 PowerShell 명령을 사용하여 Azure Resource Manager에 있는 현재 vCPU 수를 확인할 수 있습니다. vCPU 할당량에 대한 자세한 내용은 [제한 및 Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager)를 참조하세요.

이 예제에서는 **미국 서부** 지역의 사용 가능 여부를 확인합니다. 예제 지역 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>6단계: IaaS 리소스를 마이그레이션하는 명령 실행
* [Migrate VMs in a cloud service (not in a virtual network)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [가상 네트워크에서 VM 마이그레이션](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrate a storage account](#step-62-migrate-a-storage-account)

> [!NOTE]
> 여기에 설명된 모든 작업은 idempotent 방식입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼이 해당 작업을 다시 시도합니다.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>6\.1단계: 옵션 1 - 클라우드 서비스(가상 네트워크가 아님)에서 가상 머신 마이그레이션
Get the list of cloud services by using the following command. Then pick the cloud service that you want to migrate. 클라우드 서비스의 VM이 가상 네트워크에 있거나 웹 또는 작업자 역할을 포함하는 경우 명령을 오류 메시지를 반환합니다.

```powershell
    Get-AzureService | ft Servicename
```

클라우드 서비스에 대한 배포 이름을 가져옵니다. 이 예제에서는 서비스 이름이 **내 서비스**입니다. 예제 서비스 이름을 사용자 고유의 서비스 이름으로 바꿉니다.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

마이그레이션을 위해 클라우드 서비스의 가상 머신을 준비합니다. 두 가지 옵션 중 선택할 수 있습니다.

* **Option 1: Migrate the VMs to a platform-created virtual network.**

    First, validate that you can migrate the cloud service by using the following commands:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. If validation is successful, you can move on to the Prepare step.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Option 2: Migrate to an existing virtual network in the Resource Manager deployment model.**

    This example sets the resource group name to **myResourceGroup**, the virtual network name to **myVirtualNetwork**, and the subnet name to **mySubNet**. 예제의 이름을 사용자 고유의 리소스 이름으로 바꿉니다.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    First, validate that you can migrate the virtual network by using the following command:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. If validation is successful, you can proceed with the following Prepare step:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

위의 옵션 중 하나를 사용할 경우 준비 작업에 성공적으로 수행되면 VM의 마이그레이션 상태를 쿼리합니다. Ensure that they're in the `Prepared` state.

이 예제에서는 VM 이름을 **myVM**으로 설정합니다. 예제 이름을 사용자 고유의 VM 이름으로 바꿉니다.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

PowerShell 또는 Azure 포털을 사용하여 준비된 리소스에 대한 구성을 확인합니다. If you're not ready for migration and you want to go back to the old state, use the following command:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>6\.1단계: 옵션 2 - 가상 네트워크에서 가상 머신 마이그레이션

가상 네트워크에서 가상 머신을 마이그레이션하려면 가상 네트워크를 마이그레이션합니다. 가상 머신은 가상 네트워크와 함께 자동으로 마이그레이션됩니다. 마이그레이션할 가상 네트워크를 선택합니다.
> [!NOTE]
> [Migrate a single virtual machine](migrate-single-classic-to-resource-manager.md) created using the classic deployment model by creating a new Resource Manager virtual machine with Managed Disks by using the VHD (OS and data) files of the virtual machine.
<br>

> [!NOTE]
> The virtual network name might be different from what is shown in the new portal. The new Azure portal displays the name as `[vnet-name]`, but the actual virtual network name is of type `Group [resource-group-name] [vnet-name]`. Before you start the migration, look up the actual virtual network name by using the command `Get-AzureVnetSite | Select -Property Name` or view it in the old Azure portal. 

이 예제에서는 가상 네트워크 이름을 **myVnet**으로 설정합니다. 예제 가상 네트워크 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 가상 네트워크에 웹 또는 작업자 역할이 포함되어 있거나 지원되지 않는 구성을 포함하는 VM이 있으면 유효성 검사 오류 메시지가 표시됩니다.

First, validate that you can migrate the virtual network by using the following command:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. If validation is successful, you can proceed with the following Prepare step:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell 또는 Azure 포털을 사용하여 준비된 가상 머신에 대한 구성을 확인합니다. If you're not ready for migration and you want to go back to the old state, use the following command:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Step 6.2: Migrate a storage account
After you're done migrating the virtual machines, perform the following prerequisite checks before you migrate the storage accounts.

> [!NOTE]
> If your storage account has no associated disks or VM data, you can skip directly to the "Validate storage accounts and start migration" section.

* Prerequisite checks if you migrated any VMs or your storage account has disk resources:
    * Migrate virtual machines whose disks are stored in the storage account.

        The following command returns RoleName and DiskName properties of all the VM disks in the storage account. RoleName은 디스크가 연결된 가상 머신의 이름입니다. If this command returns disks, then ensure that virtual machines to which these disks are attached are migrated before you migrate the storage account.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Delete unattached VM disks stored in the storage account.

        Find unattached VM disks in the storage account by using the following command:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        If the previous command returns disks, delete these disks by using the following command:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Delete VM images stored in the storage account.

        The following command returns all the VM images with OS disks stored in the storage account.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         다음 명령은 스토리지 계정에 저장된 이미지 중 데이터가 포함된 모든 VM 이미지를 반환합니다.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Delete all the VM images returned by the previous commands by using this command:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Validate storage accounts and start migration.

    다음 명령을 사용하여 마이그레이션을 위한 각 스토리지 계정의 유효성을 검사합니다. 이 예제에서 스토리지 계정 이름은 **myStorageAccount**입니다. 예제 이름을 사용자 고유의 스토리지 계정 이름으로 바꿉니다.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    The next step is to prepare the storage account for migration.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Azure PowerShell 또는 Azure 포털을 사용하여 준비된 스토리지 계정에 대한 구성을 확인합니다. If you're not ready for migration and you want to go back to the old state, use the following command:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>다음 단계
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
