---
title: "Azure Service Fabric 노드 형식 및 가상 컴퓨터 확장 집합 | Microsoft Docs"
description: "가상 컴퓨터 확장 집합과 Azure Service Fabric 노드가 어떤 관련이 있으며 확장 집합 인스턴스 또는 클러스터를 원격으로 연결하는 방법에 대해 알아봅니다."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 6cc3be57ed283cafa686d46d4b376c69f06301ea
ms.contentlocale: ko-kr
ms.lasthandoff: 09/15/2017

---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 노드 형식 및 가상 컴퓨터 확장 집합 
가상 컴퓨터 확장 집합은 Azure 계산 리소스입니다. 확장 집합을 사용하여 가상 컴퓨터 컬렉션을 배포 및 관리할 수 있습니다. Azure Service Fabric 클러스터에서 정의한 각 노드 형식에 대해 별도의 확장 집합을 설정합니다. 각 노드 형식을 독립적으로 확장 또는 축소하고, 다른 포트의 집합을 열며 다른 용량 메트릭을 사용할 수 있습니다.

다음 그림에서는 이름이 FrontEnd 및 BackEnd인 두 노드 유형이 있는 클러스터를 보여줍니다. 각 노드 형식에는 5개의 노드가 있습니다.

![두 가지 노드 유형이 있는 클러스터][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>노드에 가상 컴퓨터 확장 집합 인스턴스 매핑
위의 그림에서처럼 확장 집합 인스턴스는 인스턴스 0에서 시작한 다음 1씩 증가합니다. 노드 이름에 이러한 번호 매기기가 반영됩니다. 예를 들어 노드 BackEnd_0은BackEnd 확장 집합의 인스턴스 0입니다. 이 특정 VM 확장 집합에는 이름이 BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 및 BackEnd_4인 5개의 인스턴스가 있습니다.

확장 집합을 확대하는 경우 새 인스턴스가 생성됩니다. 새 확장 집합 인스턴스 이름은 일반적으로 확장 집합 이름 + 다음 인스턴스 번호입니다. 이 예제에서는 BackEnd_5입니다.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>확장 집합 부하 분산 장치를 노드 형식 및 확장 집합에 매핑
Azure Portal에 클러스터를 배포했거나 샘플 Azure Resource Manager 템플릿을 사용한 경우 리소스 그룹의 모든 리소스가 목록에 열거됩니다. 각 확장 집합 또는 노드 형식에 대해 부하 분산 장치를 확인할 수 있습니다. 부하 분산 장치 이름은 **LB-&lt;노드 형식 이름&gt;** 형식을 사용합니다. 다음 그림에서처럼 LB-sfcluster4doc-0을 예로 들 수 있습니다.

![리소스][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>가상 컴퓨터 확장 집합 인스턴스 또는 클러스터 노드에 원격 연결
클러스터에서 정의한 각 노드 형식에 대해 별도의 확장 집합을 설정합니다. 노드 형식을 독립적으로 확대 또는 축소할 수 있습니다. 다른 VM SKU를 사용할 수도 있습니다. 단일 인스턴스 VM과 달리 확장 집합 인스턴스는 고유한 가상 IP 주소가 없습니다. 따라서 특정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트를 찾는 것이 어려울 수 있습니다.

특정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 특정 인스턴스를 찾으려면 다음 단계를 완료합니다.

**1단계**: 원격 데스크톱 프로토콜(RDP)에 대한 인바운드 NAT 규칙을 가져와 노드 형식에 대한 가상 IP 주소를 찾습니다.

먼저 `Microsoft.Network/loadBalancers`에 대해 정의된 리소스 정의의 일부로 정의된 인바운드 NAT 규칙 값을 가져옵니다.

Azure Portal의 부하 분산 장치 페이지에서 **설정** > **인바운드 NAT 규칙**을 선택합니다. 이렇게 하면 첫 번째 확장 집합 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트가 나타납니다. 

![부하 분산 장치][LBBlade]

다음 그림에서는 IP 주소 및 포트가 **104.42.106.156** 및 **3389**입니다.

![NAT 규칙][NATRules]

**2단계**: 특정 확장 집합 인스턴스 또는 노드에 원격으로 연결하는 데 사용할 수 있는 포트를 찾습니다.

확장 집합 인스턴스가 노드에 매핑됩니다. 확장 집합 정보를 사용하여 사용할 정확한 포트를 판단합니다.

포트는 확장 집합 인스턴스에 맞게 오름차순으로 할당됩니다. 앞의 FrontEnd 노드 형식 예에서 다음 표는 5개 노드 인스턴스 각각에 대한 포트를 열거합니다. 확장 집합 인스턴스에 동일한 매핑을 적용합니다.

| **가상 컴퓨터 확장 집합 인스턴스** | **포트** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**3단계**: 특정 확장 집합 인스턴스에 원격으로 연결합니다.

다음 그림에서는 원격 데스크톱 연결을 사용하여 FrontEnd_1 확장 집합 인스턴스에 연결하는 것을 보여 줍니다. 

![원격 데스크톱 연결][RDP]

## <a name="change-the-rdp-port-range-values"></a>RDP 포트 범위 값 변경

### <a name="before-cluster-deployment"></a>클러스터 배포 전에
리소스 관리자 템플릿을 사용하여 클러스터를 설정할 때는 `inboundNatPools`에서 범위를 지정합니다.

`Microsoft.Network/loadBalancers`에 대한 리소스 정의로 이동합니다. `inboundNatPools`에 대한 설명을 찾습니다.  `frontendPortRangeStart` 및 `frontendPortRangeEnd` 값을 바꿉니다.

![inboundNatPools 값][InboundNatPools]

### <a name="after-cluster-deployment"></a>클러스터 배포 후에
클러스터를 배포한 후 RDP 포트 범위 값을 변경하는 것은 복잡합니다. VM을 재활용하지 않도록 Azure PowerShell을 사용하여 새 값을 설정합니다. 

> [!NOTE]
> Azure PowerShell 1.0 이상 버전이 컴퓨터에 설치되어 있어야 합니다. Azure Powershell 1.0 이상 버전이 없는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)에서 설명한 단계를 따르는 것이 좋습니다.

1. Azure 계정에 로그인합니다. 다음 PowerShell 명령이 실패하면 PowerShell을 제대로 설치했는지 확인합니다.

    ```
    Login-AzureRmAccount
    ```

2. 부하 분산 장치에 대한 자세한 내용을 가져오고 `inboundNatPools` 설명의 값을 확인하려면 다음 코드를 실행합니다.

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. `frontendPortRangeEnd` 및 `frontendPortRangeStart`를 원하는 값으로 설정합니다.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>RDP 사용자 이름 및 노드의 암호 변경

특정 노드 형식의 모든 노드에 대한 암호를 변경하려면 다음 단계를 완료합니다. 이러한 변경 내용은 확장 집합의 모든 현재 및 미래의 노드에 적용됩니다.

1. 관리자 권한으로 PowerShell을 엽니다. 
2. 로그인하고 세션에 대한 구독을 선택하려면 다음 명령을 실행합니다. `SUBSCRIPTIONID` 매개 변수를 구독 ID로 변경합니다. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. 다음 스크립트를 실행합니다. 관련 `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` 및 `PASSWORD` 값을 사용합니다. `USERNAME` 및 `PASSWORD` 값은 향후의 RDP 세션에서 사용하는 새 자격 증명입니다. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>다음 단계
* ["어디에나 배포" 기능의 개요 및 Azure 관리된 클러스터와 비교](service-fabric-deploy-anywhere.md)를 참조하세요.
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* [Service Fabric SDK 및 시작](service-fabric-get-started.md)에 대해 알아보기

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

