---
title: PowerShell을 사용하여 리소스 관리자로 마이그레이션
description: 이 문서에서는 Azure PowerShell 명령을 사용하여 가상 머신(VM), 가상 네트워크 및 저장소 계정에서 Azure PowerShell 명령을 사용하여 클래식에서 Azure 리소스 관리자로 의 한 IaaS 리소스의 플랫폼에서 지원되는 마이그레이션을 안내합니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 314d7a4725709f00ba5cdbf54595857502bc5805
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865954"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>PowerShell을 사용하여 IaaS 리소스를 클래식에서 Azure 리소스 관리자로 마이그레이션

> [!IMPORTANT]
> 현재 IaaS VM의 약 90%가 [Azure 리소스 관리자를](https://azure.microsoft.com/features/resource-manager/)사용하고 있습니다. 2020년 2월 28일부터 클래식 VM이 더 이상 사용되지 않으며 2023년 3월 1일에 완전히 폐기됩니다. 이 사용 중단에 대해 자세히 알아보고 이 사용 [중단이 사용자에게 미치는 영향에 대해](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)자세히 [알아봅니다.]( https://aka.ms/classicvmretirement)

이러한 단계에서는 Azure PowerShell 명령을 사용하여 클래식 배포 모델의 laaS(Infrastructure as a Service) 리소스를 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 보여 줍니다.

원하는 경우 [Azure CLI](../linux/migration-classic-resource-manager-cli.md)를 사용하여 리소스를 마이그레이션할 수도 있습니다.

* 지원되는 마이그레이션 시나리오에 대한 배경 정보를 보려면 [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](migration-classic-resource-manager-overview.md)을 참조하세요.
* 자세한 지침 및 마이그레이션 연습에 대해서는 [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)를 참조하세요.
* [가장 일반적인 마이그레이션 오류를 검토합니다.](migration-classic-resource-manager-errors.md)

<br>
다음은 마이그레이션 프로세스 중에 단계를 실행해야 하는 순서를 식별하는 순서도입니다.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>1단계: 마이그레이션 계획
다음은 IaaS 리소스를 클래식에서 리소스 관리자로 마이그레이션할지 여부를 평가할 때 권장하는 몇 가지 모범 사례입니다.

* [지원/지원되지 않는 기능 및 구성](migration-classic-resource-manager-overview.md)을 읽어봅니다. 지원되지 않는 구성 또는 기능을 사용하는 가상 시스템이 있는 경우 구성 또는 기능 지원이 발표될 때까지 기다립니다. 필요에 따라서는 마이그레이션이 가능하도록 해당 기능을 제거하거나 구성 외부로 이동할 수도 있습니다.
* 현재 인프라 및 애플리케이션을 배포하는 스크립트를 자동화한 경우 마이그레이션을 위해 해당 스크립트를 사용하여 유사한 테스트 설정을 만들어봅니다. 또는 Azure 포털을 사용하여 샘플 환경을 설정할 수도 있습니다.

> [!IMPORTANT]
> 응용 프로그램 게이트웨이는 현재 클래식에서 리소스 관리자로 마이그레이션할 때 지원되지 않습니다. 응용 프로그램 게이트웨이를 사용하여 가상 네트워크를 마이그레이션하려면 네트워크를 이동하는 준비 작업을 실행하기 전에 게이트웨이를 제거합니다. 마이그레이션을 완료한 후 Azure Resource Manager에서 게이트웨이를 다시 연결합니다.
>
> 다른 구독의 ExpressRoute 회로에 연결하는 Azure ExpressRoute 게이트웨이는 자동으로 마이그레이션할 수 없습니다. 이러한 경우 ExpressRoute 게이트웨이를 제거하고 가상 네트워크를 마이그레이션한 다음 게이트웨이를 다시 만듭니다. 자세한 내용은 [ExpressRoute 회로 및 연결된 가상 네트워크 마이그레이션을 클래식에서 리소스 관리자 배포 모델로 마이그레이션합니다.](../../expressroute/expressroute-migration-classic-resource-manager.md)

## <a name="step-2-install-the-latest-version-of-powershell"></a>2단계: 최신 버전의 PowerShell 설치
Azure PowerShell을 설치하는 두 가지 주요 옵션으로 [PowerShell 갤러리](https://www.powershellgallery.com/profiles/azure-sdk/) 또는 [WebPI(웹 플랫폼 설치 관리자)](https://aka.ms/webpi-azps)가 있습니다. WebPI는 매월 업데이트를 수신합니다. PowerShell 갤러리는 지속적으로 업데이트를 수신합니다. 이 문서는 Azure PowerShell 버전 2.1.0을 기반으로 합니다.

설치 지침은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>3단계: 구독관리자인지 확인
이 마이그레이션을 수행하려면 [Azure 포털에서](https://portal.azure.com)구독의 공동 관리자로 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **허브** 메뉴에서 **구독**을 선택합니다. 표시되지 않으면 **모든 서비스**를 선택합니다.
3. 적절한 구독 항목을 찾은 다음 **내 ROLE** 필드를 살펴봅니다. 공동 관리자의 경우 값은 _계정 관리자여야_합니다.

공동 관리자를 추가할 수 없는 경우 서비스 관리자 또는 공동 관리자에게 구독을 문의하여 자신을 추가하세요.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>4단계: 구독 설정 및 마이그레이션 등록
먼저, PowerShell 프롬프트를 시작합니다. 마이그레이션의 경우 클래식 및 리소스 관리자 모두에 대한 환경을 설정합니다.

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
> 등록은 일회성 단계이지만 마이그레이션을 시도하기 전에 한 번 수행해야 합니다. 등록하지 않으면 다음과 같은 오류 메시지가 표시됩니다.
>
> *BadRequest : 구독이 마이그레이션에 대해 등록되지 않았습니다.*

다음 명령을 사용하여 마이그레이션 리소스 공급자에 등록합니다.

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

등록이 완료될 때까지 5분 간 기다립니다. 다음 명령을 사용하여 승인 상태를 확인합니다.

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

계속 진행하기 전에 RegistrationState가 `Registered` 인지 확인합니다.

클래식 배포 모델로 전환하기 전에 현재 배포 또는 가상 네트워크의 Azure 영역에 충분한 Azure 리소스 관리자 가상 컴퓨터 vCP가 있는지 확인합니다. 다음 PowerShell 명령을 사용하여 Azure Resource Manager에 있는 현재 vCPU 수를 확인할 수 있습니다. vCPU 할당량에 대한 자세한 내용은 [제한 및 Azure 리소스 관리자를](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)참조하십시오.

이 예제에서는 **미국 서부** 지역의 사용 가능 여부를 확인합니다. 예제 지역 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    Get-AzVMUsage -Location "West US"
```

이제 기존 배포 모델에 대한 계정에 로그인합니다.

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


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>5단계: IaaS 리소스를 마이그레이션하는 명령 실행
* [가상 네트워크가 아닌 클라우드 서비스에서 VM 마이그레이션](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [가상 네트워크에서 VM 마이그레이션](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [스토리지 계정 마이그레이션](#step-52-migrate-a-storage-account)

> [!NOTE]
> 여기에 설명된 모든 작업은 idempotent 방식입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼이 해당 작업을 다시 시도합니다.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>5.1 단계: 옵션 1 - 클라우드 서비스(가상 네트워크가 아님)에서 가상 컴퓨터를 마이그레이션합니다.
다음 명령을 사용하여 클라우드 서비스 목록을 가져옵니다. 그런 다음 마이그레이션할 클라우드 서비스를 선택합니다. 클라우드 서비스의 VM이 가상 네트워크에 있거나 웹 또는 작업자 역할을 포함하는 경우 명령을 오류 메시지를 반환합니다.

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

* **옵션 1: VM을 플랫폼에서 만든 가상 네트워크로 마이그레이션합니다.**

    먼저 다음 명령을 사용하여 클라우드 서비스를 마이그레이션할 수 있는지 확인합니다.

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사가 성공하면 준비 단계로 이동할 수 있습니다.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **옵션 2: 리소스 관리자 배포 모델의 기존 가상 네트워크로 마이그레이션합니다.**

    이 예제에서는 리소스 그룹 이름을 **myResourceGroup,** 가상 네트워크 이름을 **myVirtualNetwork로**설정하고 서브넷 이름을 **mySubNet으로**설정합니다. 예제의 이름을 사용자 고유의 리소스 이름으로 바꿉니다.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    먼저 다음 명령을 사용하여 가상 네트워크를 마이그레이션할 수 있는지 확인합니다.

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사가 성공하면 다음 준비 단계를 진행할 수 있습니다.

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

위의 옵션 중 하나를 사용할 경우 준비 작업에 성공적으로 수행되면 VM의 마이그레이션 상태를 쿼리합니다. `Prepared` 상태가 되어 있는지 확인합니다.

이 예제에서는 VM 이름을 **myVM**으로 설정합니다. 예제 이름을 사용자 고유의 VM 이름으로 바꿉니다.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

PowerShell 또는 Azure 포털을 사용하여 준비된 리소스에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았고 이전 상태로 돌아가려면 다음 명령을 사용합니다.

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>5.1 단계: 옵션 2 - 가상 네트워크에서 가상 시스템 마이그레이션

가상 네트워크에서 가상 머신을 마이그레이션하려면 가상 네트워크를 마이그레이션합니다. 가상 머신은 가상 네트워크와 함께 자동으로 마이그레이션됩니다. 마이그레이션할 가상 네트워크를 선택합니다.
> [!NOTE]
> 가상 시스템의 VHD(OS 및 데이터) 파일을 사용하여 관리디스크가 있는 새 Resource Manager 가상 컴퓨터를 만들어 클래식 배포 모델을 사용하여 만든 단일 가상 컴퓨터를 [마이그레이션합니다.](migrate-single-classic-to-resource-manager.md)
<br>

> [!NOTE]
> 가상 네트워크 이름은 새 포털에 표시된 이름과 다를 수 있습니다. 새 Azure 포털은 이름을 `[vnet-name]`로 표시하지만 실제 가상 `Group [resource-group-name] [vnet-name]`네트워크 이름은 형식입니다. 마이그레이션을 시작하기 전에 명령을 `Get-AzureVnetSite | Select -Property Name` 사용하여 실제 가상 네트워크 이름을 찾거나 이전 Azure Portal에서 볼 수 있습니다. 

이 예제에서는 가상 네트워크 이름을 **myVnet**으로 설정합니다. 예제 가상 네트워크 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 가상 네트워크에 웹 또는 작업자 역할이 포함되어 있거나 지원되지 않는 구성을 포함하는 VM이 있으면 유효성 검사 오류 메시지가 표시됩니다.

먼저 다음 명령을 사용하여 가상 네트워크를 마이그레이션할 수 있는지 확인합니다.

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사가 성공하면 다음 준비 단계를 진행할 수 있습니다.

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell 또는 Azure 포털을 사용하여 준비된 가상 머신에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았고 이전 상태로 돌아가려면 다음 명령을 사용합니다.

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>5.2단계: 저장소 계정 마이그레이션
가상 시스템 마이그레이션이 완료되면 저장소 계정을 마이그레이션하기 전에 다음 필수 구성 조건 검사를 수행합니다.

> [!NOTE]
> 저장소 계정에 연결된 디스크 또는 VM 데이터가 없는 경우 "저장소 계정 유효성 검사 및 마이그레이션 시작" 섹션으로 직접 건너뛸 수 있습니다.

* VM을 마이그레이션했는지 또는 저장소 계정에 디스크 리소스가 있는지 확인하는 필수 구성 조건은 다음과 같은 것입니다.
    * 디스크가 저장소 계정에 저장된 가상 컴퓨터를 마이그레이션합니다.

        다음 명령은 저장소 계정의 모든 VM 디스크의 RoleName 및 DiskName 속성을 반환합니다. RoleName은 디스크가 연결된 가상 머신의 이름입니다. 이 명령이 디스크를 반환하는 경우 저장소 계정을 마이그레이션하기 전에 이러한 디스크가 연결된 가상 시스템이 마이그레이션되었는지 확인합니다.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * 저장소 계정에 저장된 연결되지 않은 VM 디스크를 삭제합니다.

        다음 명령을 사용하여 저장소 계정에서 연결되지 않은 VM 디스크를 찾습니다.

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        이전 명령이 디스크를 반환하는 경우 다음 명령을 사용하여 이러한 디스크를 삭제합니다.

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * 저장소 계정에 저장된 VM 이미지를 삭제합니다.

        다음 명령은 저장소 계정에 저장된 OS 디스크가 있는 모든 VM 이미지를 반환합니다.
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
        이 명령을 사용하여 이전 명령에서 반환된 모든 VM 이미지를 삭제합니다.
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* 저장소 계정의 유효성을 검사하고 마이그레이션을 시작합니다.

    다음 명령을 사용하여 마이그레이션을 위한 각 스토리지 계정의 유효성을 검사합니다. 이 예제에서 스토리지 계정 이름은 **myStorageAccount**입니다. 예제 이름을 사용자 고유의 스토리지 계정 이름으로 바꿉니다.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    다음 단계는 마이그레이션을 위해 저장소 계정을 준비하는 것입니다.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Azure PowerShell 또는 Azure 포털을 사용하여 준비된 스토리지 계정에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았고 이전 상태로 돌아가려면 다음 명령을 사용합니다.

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
