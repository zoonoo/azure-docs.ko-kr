---
title: "클래식에서 Resource Manager로 ExpressRoute 회로 및 연결된 가상 네트워크 마이그레이션: Azure | Microsoft Docs"
description: "이 페이지에서는 Resource Manager로 클래식 회로 및 연결된 가상 네트워크를 마이그레이션하는 방법을 설명합니다."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9a3ad5be7cc12f1c82eab4a8b3089276a434c234
ms.lasthandoff: 03/29/2017


---
# <a name="migrate-expressroute-circuits-and-associated-virtual-networks-from-the-classic-to-the-resource-manager-deployment-model"></a>클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 및 연결된 가상 네트워크 마이그레이션

이 문서에서는 클래식 배포 모델에서 Azure Resource Manager 배포 모델로 Azure ExpressRoute 회로 및 연결된 가상 네트워크를 마이그레이션하는 방법을 설명합니다. 


## <a name="before-you-begin"></a>시작하기 전에
* Azure PowerShell 모듈의 최신 버전이 있는지 확인합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.
* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토했는지 확인합니다.
* [클래식에서 Resource Manager로 ExpressRoute 회로 이동](expressroute-move.md)에서 제공되는 정보를 검토합니다. 제한 및 제한 사항을 완전히 이해해야 합니다.
* 클래식 배포 모델에서 회로가 완벽하게 작동되는지 확인합니다.
* 리소스 관리자 배포 모델에 만든 리소스 그룹이 있는지 확인합니다.
* 다음 리소스 마이그레이션 설명서를 검토합니다.

    * [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [FAQ: 클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [클래식에서 Azure Resource Manager로 마이그레이션 중 발생하는 일반 오류](../virtual-machines/virtual-machines-migration-errors.md)

## <a name="supported-and-unsupported-scenarios"></a>지원되는 및 지원되지 않는 시나리오

* ExpressRoute 회로를 가동 중지 시간 없이 클래식에서 Resource Manager 환경으로 마이그레이션할 수 있습니다. ExpressRoute 회로를 클래식에서 Resource Manager 환경으로 가동 중지 시간 없이 이동할 수 있습니다. [PowerShell을 사용하여 클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동](expressroute-howto-move-arm.md)의 지침을 따르세요. 가상 네트워크에 연결된 리소스를 이동하기 위한 필수 구성 요소입니다.
* 동일한 구독에서 ExpressRoute 회로에 연결된 가상 네트워크, 게이트웨이 및 가상 네트워크 내 연결된 배포를 가동 중지 시간 없이 Resource Manager 환경으로 마이그레이션할 수 있습니다. 나중에 설명하는 단계에 따라 가상 네트워크, 게이트웨이 및 가상 네트워크 내 배포된 가상 컴퓨터와 같은 리소스를 마이그레이션할 수 있습니다. 마이그레이션하기 전에 가상 네트워크가 올바르게 구성되어 있는지 확인해야 합니다. 
* ExpressRoute 회로와 다른 구독의 가상 네트워크, 게이트웨이 및 가상 네트워크 내 연결된 배포는 마이그레이션을 완료하는 데 가동 중지 시간이 필요합니다. 문서의 마지막 섹션에서는 리소스 마이그레이션을 위해 따라야 하는 단계를 설명합니다.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>클래식에서 Resource Manager로 ExpressRoute 회로 이동
ExpressRoute 회로에 연결된 리소스를 마이그레이션하기 전에 ExpressRoute 회로를 클래식에서 Resource Manager 환경으로 이동해야 합니다. 이 작업을 완료하려면 다음 문서를 참조하세요.

* [클래식에서 Resource Manager로 ExpressRoute 회로 이동](expressroute-move.md)에서 제공되는 정보를 검토합니다.
* [Azure PowerShell을 사용하여 클래식에서 Resource Manager로 회로를 이동합니다](expressroute-howto-move-arm.md).
* Azure Service 관리 포털을 사용합니다. [새 ExpressRoute 회로 만들기](expressroute-howto-circuit-portal-resource-manager.md)에 대한 워크플로를 수행하고 가져오기 옵션을 선택할 수 있습니다. 

이 작업에는 가동 중지 시간이 없습니다. 마이그레이션을 진행하는 동안 프레미스와 Microsoft 간 데이터 전송을 계속할 수 있습니다.

## <a name="prepare-your-virtual-network-for-migration"></a>마이그레이션을 위한 가상 네트워크 준비
마이그레이션할 가상 네트워크의 네트워크에 불필요한 아티팩트가 없도록 확인해야 합니다. 가상 네트워크 구성을 다운로드하고 필요에 따라 업데이트하려면 다음 PowerShell cmdlet을 실행합니다.

    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName <VNET Subscription>
    Get-AzureVNetConfig -ExportToFile C:\virtualnetworkconfig.xml
      
마이그레이션할 가상 네트워크에서 <ConnectionsToLocalNetwork>에 대한 모든 참조가 제거 되었는지 확인해야 합니다. 샘플 네트워크 구성이 다음 코드 조각에 나와 있습니다.

    <VirtualNetworkSite name="MyVNet" Location="East US">
        <AddressSpace>
            <AddressPrefix>10.0.0.0/8</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="Subnet-1">
                <AddressPrefix>10.0.0.0/11</AddressPrefix>
            </Subnet>
            <Subnet name="GatewaySubnet">
                <AddressPrefix>10.32.0.0/28</AddressPrefix>
            </Subnet>
        </Subnets>
        <Gateway>
            <ConnectionsToLocalNetwork>
            </ConnectionsToLocalNetwork>
        </Gateway>
    </VirtualNetworkSite>
 
<ConnectionsToLocalNetwork>가 비어 있지 않은 경우 참조를 삭제하고 네트워크 구성에 제출합니다. 다음 PowerShell cmdlet을 실행하여 이 작업을 수행할 수 있습니다.

    Set-AzureVNetConfig -ConfigurationPath c:\virtualnetworkconfig.xml

## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>ExpressRoute 회로와 동일한 구독의 가상 네트워크, 게이트웨이 및 연결된 배포 마이그레이션
이 섹션에서는 ExpressRoute 회로와 동일한 구독의 가상 네트워크, 게이트웨이 및 연결된 배포를 배포하기 위한 단계를 설명합니다. 이 마이그레이션은 가동 중지 시간이 없습니다. 마이그레이션 프로세스 동안 모든 리소스를 계속 사용할 수 있습니다. 마이그레이션이 진행되는 동안에는 관리 평면이 잠겨 있습니다. 

1. ExpressRoute 회로를 클래식에서 Resource Manager 환경으로 이동하였는지 확인합니다.
2. 가상 네트워크를 마이그레이션에 적합하게 준비하였는지 확인합니다.
3. 리소스 마이그레이션을 위해 구독을 등록합니다. 리소스 마이그레이션을 위해 구독을 등록하려면 다음 PowerShell 코드 조각을 사용합니다. 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
4. 유효성을 검사하고 준비한 후, 마이그레이션합니다. 가상 네트워크를 이동하려면 다음 PowerShell 코드 조작을 사용합니다.
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    또한 다음 PowerShell cmdlet을 실행하여 마이그레이션을 중단할 수 있습니다.
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ``` 
## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-a-different-subscription-from-that-of-the-expressroute-circuit"></a>ExpressRoute 회로와 다른 구독의 가상 네트워크, 게이트웨이 및 연결된 배포 마이그레이션

1. ExpressRoute 회로를 클래식에서 Resource Manager 환경으로 이동하였는지 확인합니다.
2. 가상 네트워크를 마이그레이션에 적합하게 준비하였는지 확인합니다.
3. ExpressRoute 회로가 클래식과 Resource Manager 환경에서 모두 작동할 수 있는지 확인합니다. 회로를 클래식 및 Resource Manager 환경 모두에서 사용하도록 허용하려면 다음 PowerShell 스크립트를 사용합니다. 
    ```
    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName <My subscription>
    $circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    $circuit.AllowClassicOperations = $true
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    ```
4. Resource Manager 환경에서 권한 부여를 만듭니다. 권한 부여를 만드는 방법을 알아보려면 [가상 네트워크를 ExpressRoute 회로에 연결하는 방법](expressroute-howto-linkvnet-arm.md)을 참조합니다. 권한 부여를 만들려면 다음 PowerShell 코드 조각을 사용합니다.
    ```
    circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    $circuit = Get-AzureRmExpressRouteCircuit -Name MigrateCircuit -ResourceGroupName MigrateRGWest

    $id = $circuit.id 
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"

    $key=$auth1.AuthorizationKey 
    ```
    회로 ID와 권한 부여 키를 적어둡니다. 이러한 요소는 마이그레이션이 완료된 후 회로를 가상 네트워크에 연결할 때 사용됩니다.
  
5. 가상 네트워크와 연결된 전용 회로 링크를 삭제합니다. 클래식 환경에서 회로 링크를 제거하려면 다음 cmdlet을 사용합니다. 
    ```
    $skey = Get-AzureDedicatedCircuit | select ServiceKey
    Remove-AzureDedicatedCircuitLink -ServiceKey $skey -VNetName $vnetName
    ```  

6. 리소스 마이그레이션을 위해 구독을 등록합니다. 리소스 마이그레이션을 위해 구독을 등록하려면 다음 PowerShell 코드 조각을 사용합니다. 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
7. 유효성을 검사하고 준비한 후, 마이그레이션합니다. 가상 네트워크를 이동하려면 다음 PowerShell 코드 조작을 사용합니다.
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    또한 다음 PowerShell cmdlet을 실행하여 마이그레이션을 중단할 수 있습니다.
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ```
8. 가상 네트워크를 ExpressRoute 회로에 다시 연결합니다. 다음 PowerShell 코드 조각은 가상 네트워크가 생성된 구독의 컨텍스트에서 실행됩니다. 회로가 생성되는 구독에서는 이 코드 조각을 실행해서는 안 됩니다. 4단계에서 적어둔 회로 ID(PeerID로) 및 권한 부여 키를 사용합니다.
    ```
    Select-AzureRMSubscription –SubscriptionName <customer subscription>  
    $gw = Get-AzureRmVirtualNetworkGateway -Name $vnetName-Default-Gateway -ResourceGroupName ($vnetName + "-Migrated")
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroup  ($vnetName + "-Migrated")  

    New-AzureRmVirtualNetworkGatewayConnection -Name  ($vnetName + "-GwConn") -ResourceGroupName ($vnetName + "-Migrated")  -Location $vnet.Location -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey $key
    ```
## <a name="next-steps"></a>다음 단계
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [FAQ: 클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [클래식에서 Azure Resource Manager로 마이그레이션 중 발생하는 일반 오류](../virtual-machines/virtual-machines-migration-errors.md)

