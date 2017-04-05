---
title: "ExpressRoute 회로 만들기 및 수정: PowerShell: Azure 클래식 | Microsoft Docs"
description: "이 문서에서는 Express 경로 회로를 만들고 프로비전하는 단계를 안내합니다. 또한 상태 확인, 업데이트 또는 삭제 및 프로비전 해제 방법을 보여 줍니다."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: efdec32e565bf1d11b562d283e56bd8ed5d292b9
ms.lasthandoff: 03/24/2017


---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell-classic"></a>PowerShell을 사용하여 ExpressRoute 회로 만들기 및 수정(클래식)
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-circuit-arm.md)
> * [클래식 - PowerShell](expressroute-howto-circuit-classic.md)
> * [비디오 - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

이 문서에서는 PowerShell cmdlet 및 일반 배포 모델을 사용하여 Azure Express 경로 회로를 만드는 단계를 안내합니다. 이 문서는 ExpressRoute 회로의 상태 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행하는 방법도 설명합니다.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>시작하기 전에
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>1단계. 필수 조건 및 워크플로 문서에 대한 검토
구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토했는지 확인합니다.  

### <a name="step-2-install-the-latest-versions-of-the-azure-service-management-sm-powershell-modules"></a>2단계. 최신 버전의 Azure SM(서비스 관리) PowerShell 모듈 설치
Azure PowerShell 모듈을 사용하도록 컴퓨터를 구성하는 방법에 대한 단계별 지침은 [Azure PowerShell cmdlet 시작](/powershell/azureps-cmdlets-docs)에 나온 지침을 따르세요.

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>3단계. Azure 계정에 로그인 및 구독 선택
1. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

        Login-AzureRmAccount

2. 계정에 대한 구독을 확인합니다.

        Get-AzureRmSubscription

3. 둘 이상의 구독이 있는 경우 사용할 구독을 선택합니다.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. 다음 cmdlet을 사용하여 클래식 배포 모델용 PowerShell에 Azure 구독을 추가합니다.

        Add-AzureAccount

## <a name="create-and-provision-an-expressroute-circuit"></a>Express 경로 회로 만들기 및 프로비전
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>1단계. Express 경로에 대한 PowerShell 모듈을 가져옵니다.
 먼저 ExpressRoute cmdlet을 사용할 수 있도록 Azure와 ExpressRoute 모듈을 PowerShell 세션으로 가져와야 합니다. 로컬 컴퓨터에 설치된 위치에 있는 모듈을 가져옵니다. 이 위치는 모듈 설치에 사용한 방법에 따라 다음 예제와 다를 수 있습니다. 따라서 필요한 경우 예제를 수정합니다.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2단계. 지원되는 공급자, 위치 및 대역폭 목록을 가져옵니다.
Express 경로 회로를 만들기 전에 지원되는 연결 공급자, 위치 및 대역폭 옵션 목록이 필요합니다.

PowerShell cmdlet `Get-AzureDedicatedCircuitServiceProvider` 은(는) 이후 단계에서 사용할 이 정보를 반환합니다.

    Get-AzureDedicatedCircuitServiceProvider

연결 공급자가 여기에 나열되었는지 확인합니다. 다음 정보는 나중에 회로를 만들 때 필요하므로 적어 둡니다.

* 이름
* PeeringLocations
* BandwidthsOffered

이제 Express 경로 회로를 만들 준비가 되었습니다.         

### <a name="step-3-create-an-expressroute-circuit"></a>3단계. Express 경로 회로 만들기
아래 예제에서는 Equinix 실리콘밸리를 통해 200Mbps Express 경로 회로를 만드는 방법을 보여 줍니다. 다른 공급자와 다른 설정을 사용하는 경우, 요청을 수행할 때 해당 정보를 대체합니다.

> [!IMPORTANT]
> Express 경로 회로는 서비스 키가 발급된 순간부터 비용이 청구됩니다. 연결 공급자가 회로를 프로비전할 준비가 된 후에 이 작업을 수행하도록 하십시오.
> 
> 

다음은 새 서비스 키에 대한 예제 요청입니다.

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

또는 프리미엄 추가 기능을 통해 Express 경로 회로를 만들려면 다음 예를 사용합니다. 프리미엄 추가 기능에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md) 를 참조하세요.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


응답에 서비스 키가 포함됩니다. 다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>4단계. 모든 Express 경로 회로 나열
만들어 놓은 모든 Express 경로 회로 목록을 가져오려면 `Get-AzureDedicatedCircuit` 명령을 실행합니다.

    Get-AzureDedicatedCircuit

응답은 다음 예와 유사합니다.

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

`Get-AzureDedicatedCircuit` cmdlet을 사용하여 이 정보를 언제든지 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다. 서비스 키는 *ServiceKey* 필드에 나열됩니다.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5단계. 프로비전을 위해 연결 공급자에 서비스 키 보내기
*ServiceProviderProvisioningState* 는 서비스 공급자 측의 현재 프로비전 상태에 대한 정보를 제공합니다. *Status* 는 Microsoft 측의 상태를 제공합니다. 회로 프로비전 상태에 대한 자세한 내용은 [워크플로](expressroute-workflows.md#expressroute-circuit-provisioning-states) 문서를 참조하세요.

새 Express 경로 회로를 만들면 회로는 다음 상태가 됩니다.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


연결 공급자가 사용자에 대해 활성화를 처리 중이면 다음 상태가 됩니다.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Express 경로 회로를 사용하려면 다음 상태여야 합니다.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6단계. 회로 키의 상태를 주기적으로 확인
이를 통해 공급자가 회로 활성화를 마치면 알 수 있습니다. 회로가 구성된 후에는 *ServiceProviderProvisioningState*가 아래 예에서처럼 *Provisioned*으로 표시됩니다.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>7단계. 라우팅 구성 만들기
단계별 지침은 [Express 경로 회로 라우팅 구성(회로 피어링 만들기 및 수정)](expressroute-howto-routing-classic.md) 문서를 참조하세요.

> [!IMPORTANT]
> 이 지침은 2계층 연결 서비스를 제공하는 서비스 공급자를 사용하여 만든 회로에만 적용됩니다. 관리된 3계층 서비스(일반적으로 MPLS와 같은 IP VPN)를 제공하는 서비스 공급자를 사용하는 경우 연결 공급자는 사용자를 위해 라우팅을 구성하고 관리합니다.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>8단계: 가상 네트워크를 Express 경로 회로에 연결합니다.
그 다음 가상 네트워크를 Express 경로 회로에 연결합니다. 단계별 지침은 [Express 경로 회로에 가상 네트워크 연결](expressroute-howto-linkvnet-classic.md) 을 참조하세요. ExpressRoute에 클래식 배포 모델을 사용하여 가상 네트워크를 만들어야 하는 경우 [ExpressRoute에 대해 가상 네트워크 만들기](expressroute-howto-vnet-portal-classic.md)를 참조하세요.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Express 경로 회로의 상태 가져오기
`Get-AzureCircuit` cmdlet을 사용하여 이 정보를 언제든지 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

서비스 키를 매개 변수 형태로 호출에 전달하면 특정 Express 경로 회로에 대한 정보를 가져올 수 있습니다.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


다음 예제를 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Express 경로 회로 수정
연결에 미치는 영향 없이 Express 경로 회로의 특정 속성을 수정할 수 있습니다.

가동 중지 시간 없이 다음을 수행할 수 있습니다.

* Express 경로 회로에 대해 Express 경로 프리미엄 추가 기능을 사용하거나 사용하지 않을 수 있습니다.
* 포트에 사용 가능한 용량이 있는 경우 ExpressRoute 회로의 대역폭을 증가시킵니다. 회로 대역폭 다운그레이드는 지원되지 않습니다. 
* 요금제를 데이터 요금에서 무제한 데이터 요금으로 변경합니다. 요금제를 무제한 데이터 요금에서 데이터 요금으로 변경하는 것은 지원되지 않습니다.
* *Allow Classic Operations*을 활성화하거나 비활성화할 수 있습니다.

제한 및 제한 사항에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md) 를 참조하세요.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Express 경로 Premium 추가 기능을 활성화하려면
다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 Express 경로 프리미엄 추가 기능을 활성화할 수 있습니다.

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

이제 Express 경로 프리미엄 추가 기능을 사용할 수 있게 됩니다. 명령이 성공적으로 실행되는 즉시 프리미엄 추가 기능에 대한 청구가 시작됩니다.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Express 경로 Premium 추가 기능을 비활성화하려면
> [!IMPORTANT]
> 표준 회로에 허용된 것보다 많은 리소스를 사용할 경우 이 작업이 실패할 수 있습니다.
> 
> 

#### <a name="considerations"></a>고려 사항

* 프리미엄을 표준으로 다운그레이드하기 전에 회로에 연결된 가상 네트워크 수가 10개 미만인지 확인해야 합니다. 그렇지 않으면 업데이트 요청이 실패하고 프리미엄 요금이 청구됩니다.
* 다른 지리적 위치의 모든 가상 네트워크를 연결 해제해야 합니다. 그렇지 않으면 업데이트 요청이 실패하고 프리미엄 요금이 청구됩니다.
* 사설 피어링을 위해서는 경로 테이블의 경로가 4000개 미만이어야 합니다. 경로 테이블 크기가 4000개 경로 이상이면 BGP 세션이 폐기되고 게시된 프리픽스 수가 4000개 미만이 될 때까지 다시 활성화되지 않습니다.

#### <a name="disable-the-premium-add-on"></a>Premium 추가 기능 사용 안 함
다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 Express 경로 프리미엄 추가 기능을 사용하지 않도록 설정할 수 있습니다.

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Express 경로 회로 대역폭을 업데이트하려면
공급자에 대해 지원되는 대역폭 옵션은 [Express 경로 FAQ](expressroute-faqs.md) 를 확인하세요. 물리적 포트(회로가 생성되어 있는)에 허용되는 한 기존 회로보다 크다면 어떤 크기든 선택할 수 있습니다.

> [!IMPORTANT]
> 기존 포트에 적절한 용량이 없는 경우 ExpressRoute 회로를 다시 만들어야 할 수 있습니다. 해당 위치에서 사용 가능한 추가 용량이 없는 경우 해당 회로를 업그레이드할 수 없습니다.
>
> 그러나 중단 없이 Express 경로 회로의 대역폭을 줄일 수는 없습니다. 대역폭을 다운그레이드하려면 Express 경로 회로의 프로비전을 해제하고 새 Express 경로 회로를 다시 프로비전해야 합니다.
> 
> 

#### <a name="resize-a-circuit"></a>회로 크기 조정

필요한 크기를 선택한 후에, 다음 명령을 사용하여 회로 크기를 조정할 수 있습니다.

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

회로의 크기는 Microsoft 쪽에서 조정됩니다. 변경에 부합하게 구성을 업데이트하려면 해당 공급자에게 연락해야 합니다. 이 지점에서 업데이트된 대역폭 옵션에 대한 요금을 청구하기 시작합니다.

회로 대역폭을 증가시킬 때 다음과 같은 오류가 표시되면 기존 회로가 생성된 물리적 포트에 남아있는 대역폭이 충분하지 않다는 의미입니다. 이 회로를 삭제하고 필요한 크기의 회로를 새로 만들어야 합니다. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Express 경로 회로 프로비전 해제 및 삭제

### <a name="considerations"></a>고려 사항

* 이 작업이 성공하려면 모든 가상 네트워크를 Express 경로 회로에서 연결 해제해야 합니다. 이 작업이 실패할 경우 회로에 연결된 가상 네트워크가 있는지 확인하십시오.
* ExpressRoute 회로 서비스 공급자 프로비전 상태가 **프로비전 중** 또는 **프로비전됨**인 경우에는 서비스 공급자에게 회로 프로비전 해제를 요청해야 합니다. 서비스 공급자가 회로의 프로비전을 해제한 다음 통지를 보낼 때까지 리소스가 계속 예약되며 요금이 청구됩니다.
* 서비스 공급자가 회로 프로비전을 해제하여 서비스 공급자 프로비전 상태가 **프로비전되지 않음**이 되면 회로를 삭제할 수 있습니다. 그러면 회로에 대한 요금 청구가 중지됩니다.

#### <a name="delete-a-circuit"></a>회로 삭제

다음 명령을 실행하여 Express 경로 회로를 삭제할 수 있습니다.

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>다음 단계
회로를 만든 후 다음을 수행합니다.

* [Express 경로 회로의 라우팅 만들기 및 수정](expressroute-howto-routing-classic.md)
* [가상 네트워크를 Express 경로 회로에 연결](expressroute-howto-linkvnet-classic.md)


