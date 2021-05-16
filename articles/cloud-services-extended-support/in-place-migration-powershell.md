---
title: PowerShell을 사용하여 Cloud Services(확장 지원)로 마이그레이션
description: PowerShell을 사용하여 Azure Cloud Services(클래식)에서 Azure Cloud Services(확장 지원)로 마이그레이션하는 방법을 알아봅니다.
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: aab67914b1317bc0cc443f333932ecef924176b6
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293029"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>PowerShell을 사용하여 Cloud Services(확장 지원)로 마이그레이션

다음 단계에서는 Azure PowerShell 명령을 사용하여 [Cloud Services(클래식)](../cloud-services/cloud-services-choose-me.md)에서 [Cloud Services(확장 지원)](overview.md)로 마이그레이션하는 방법을 보여줍니다.

> [!IMPORTANT]
> 마이그레이션 도구를 사용하여 Cloud Services(클래식)에서 Cloud Services(확장 지원)로 마이그레이션하는 것은 현재 공개 미리 보기에 있습니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="1-plan-for-migration"></a>1) 마이그레이션 계획
성공적인 마이그레이션 환경을 위한 가장 중요한 단계는 계획입니다. 마이그레이션 단계를 시작하기 전에 [Cloud Services(확장 지원) 개요](overview.md) 및 [클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션 계획](../virtual-machines/migration-classic-resource-manager-plan.md)을 검토합니다. 

## <a name="2-install-the-latest-version-of-powershell"></a>2) 최신 버전의 PowerShell 설치
Azure PowerShell을 설치하는 두 가지 주요 옵션으로 [PowerShell 갤러리](https://www.powershellgallery.com/profiles/azure-sdk/) 또는 [WebPI(웹 플랫폼 설치 관리자)](https://aka.ms/webpi-azps)가 있습니다. WebPI는 매월 업데이트를 수신합니다. PowerShell 갤러리는 지속적으로 업데이트를 수신합니다. 이 문서는 Azure PowerShell 버전 2.1.0을 기반으로 합니다.

설치 지침은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/servicemanagement/install-azure-ps?preserve-view=true&view=azuresmps-4.0.0)을 참조하세요.

## <a name="3-ensure-admin-permissions"></a>3) 관리자 권한 확인
이 마이그레이션을 수행하려면, 해당 구독에 대해 [Azure Portal](https://portal.azure.com)에 공동 관리자로 추가되어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **허브** 메뉴에서 **구독** 을 선택합니다. 표시되지 않으면 **모든 서비스** 를 선택합니다.
3. 적절한 구독 항목을 찾아 **내 역할** 필드를 확인합니다. 공동 관리자인 경우 해당 값은 *계정 관리자* 여야 합니다.

공동 관리자를 추가할 수 없는 상태라면 서비스 관리자나 공동 관리자에게 자신을 해당 구독에 추가해 달라고 요청합니다.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) 클래식 공급자 및 CloudService 기능 등록
먼저, PowerShell 프롬프트를 시작합니다. 마이그레이션의 경우, 클래식과 Resource Manager 양쪽 모두에 대한 환경을 설정합니다.

Resource Manager 모델에 대한 계정으로 로그인합니다.

```powershell
Connect-AzAccount
```

다음 명령으로 사용 가능한 구독을 가져옵니다.

```powershell
Get-AzSubscription | Sort Name | Select Name
```

현재 세션에 대한 Azure 구독을 설정합니다. 이 예제에서는 기본 구독 이름을 **내 Azure 구독** 으로 설정합니다. 예제 구독 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

다음 명령을 사용하여 마이그레이션 리소스 공급자에 등록합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> 등록은 일회성 단계이지만 마이그레이션 시도 전에 반드시 한 번은 해야 합니다. 등록하지 않으면 다음과 같은 오류 메시지가 표시됩니다.
>
> *BadRequest : 구독이 마이그레이션에 대해 등록되지 않았습니다.*

구독에 대한 CloudServices 기능을 등록합니다. 등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

등록이 완료될 때까지 5분간 기다리세요. 

다음 명령을 사용하여 클래식 공급자 승인 상태를 확인합니다.

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

다음을 사용하여 등록 상태를 확인합니다.  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

계속 진행하기 전에 둘 모두에 대한 RegistrationState가 `Registered`인지 확인합니다.

클래식 배포 모델로 전환하기 전에 현재 배포나 가상 네트워크의 Azure 지역 내에 Azure Resource Manager vCPU 할당량이 충분한지 확인합니다. 다음 PowerShell 명령을 사용하여 Azure Resource Manager에 있는 현재 vCPU 수를 확인할 수 있습니다. vCPU 할당량에 대한 자세한 내용은 [제한 및 Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)를 참조하세요.

이 예제에서는 **미국 서부** 지역의 사용 가능 여부를 확인합니다. 예제 지역 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
Get-AzVMUsage -Location "West US"
```

이제 클래식 배포 모델 계정에 로그인합니다.

```powershell
Add-AzureAccount
```

다음 명령으로 사용 가능한 구독을 가져옵니다.

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

현재 세션에 대한 Azure 구독을 설정합니다. 이 예제에서는 기본 구독을 **내 Azure 구독** 으로 설정합니다. 예제 구독 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) Cloud Services 마이그레이션 
* [가상 네트워크에 없는 클라우드 서비스 마이그레이션](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [가상 네트워크의 클라우드 서비스 마이그레이션](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> 여기에 설명된 모든 작업은 idempotent 방식입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼이 해당 작업을 다시 시도합니다.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5.1) 옵션 1 - 가상 네트워크에 없는 클라우드 서비스 마이그레이션
다음 명령을 사용하여 클라우드 서비스 목록을 가져옵니다. 그런 다음, 마이그레이션하려는 클라우드 서비스를 선택합니다.

```powershell
Get-AzureService | ft Servicename
```

클라우드 서비스에 대한 배포 이름을 가져옵니다. 이 예제에서는 서비스 이름이 **내 서비스** 입니다. 예제 서비스 이름을 사용자 고유의 서비스 이름으로 바꿉니다.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

먼저, 다음 명령을 사용하여 클라우드 서비스를 마이그레이션할 수 있는지 유효성을 검사합니다.

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사가 성공하면 준비 단계로 넘어갈 수 있습니다.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5.1) 옵션 2 - 가상 네트워크의 클라우드 서비스 마이그레이션

가상 네트워크에서 클라우드 서비스를 마이그레이션하려면 가상 네트워크를 마이그레이션합니다. 클라우드 서비스는 가상 네트워크와 함께 자동으로 마이그레이션됩니다.

> [!NOTE]
> 가상 네트워크 이름은 새 포털에 표시된 것과 다를 수 있습니다. 새로운 Azure Portal에는 이름이 `[vnet-name]`처럼 표시되지만 실제 가상 네트워크 이름은 `Group [resource-group-name] [vnet-name]` 형식입니다. 마이그레이션을 시작하기 전에 `Get-AzureVnetSite | Select -Property Name` 명령을 사용하여 실제 가상 네트워크 이름을 조회하거나 이전 Azure Portal에서 그 이름을 확인합니다. 

이 예제에서는 가상 네트워크 이름을 **myVnet** 으로 설정합니다. 예제 가상 네트워크 이름을 사용자 고유의 이름으로 바꿉니다.

```powershell
$vnetName = "myVnet"
```

먼저, 다음 명령을 사용하여 가상 네트워크를 마이그레이션할 수 있는지 유효성을 검사합니다.

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

다음 명령은 마이그레이션을 차단하는 경고와 오류를 표시합니다. 유효성 검사가 성공하면 다음 준비 단계를 이행할 수 있습니다.

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Azure PowerShell 또는 Azure Portal을 사용하여 준비된 클라우드 서비스에 대한 구성을 확인합니다. 마이그레이션 준비가 되어 있지 않아 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>다음 단계
[마이그레이션 후 변경 내용](in-place-migration-overview.md#post-migration-changes) 섹션을 검토하여 배포 파일의 변경 내용, 자동화 및 새 Cloud Services(확장 지원) 배포의 기타 특성을 확인합니다.