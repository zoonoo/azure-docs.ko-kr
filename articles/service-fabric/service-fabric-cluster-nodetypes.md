---
title: "Service Fabric 노드 형식 및 VM Scale Sets | Microsoft Docs"
description: "VM 크기 조정 설정과 서비스 패브릭 노드 유형의 관계 및 VM 크기 조정 설정 인스턴스 또는 클러스터 노드에 원격으로 연결하는 방법을 설명합니다."
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
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 8c9e91d122591a19d34d944e2d9aaeb327cdafe4
ms.contentlocale: ko-kr
ms.lasthandoff: 09/05/2017

---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>서비스 패브릭 노드 형식과 가상 컴퓨터 확장 집합 간의 관계
가상 컴퓨터 확장 집합은 Azure Compute 리소스입니다. 가상 컴퓨터의 컬렉션을 집합으로 배포 및 관리하는 데 사용할 수 있습니다. Service Fabric 클러스터에 정의된 모든 노드 형식은 별도의 가상 컴퓨터 확장 집합으로 설정됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다.

다음 스크린 샷에서는 프런트 엔드 및 백 엔드 등 두 가지 노드 유형이 있는 클러스터를 보여줍니다.  각 노드 형식에는 5개의 노드가 있습니다.

![두 가지 노드 유형이 있는 클러스터의 스크린 샷][NodeTypes]

## <a name="mapping-virtual-machine-scale-set-instances-to-nodes"></a>노드에 가상 컴퓨터 확장 집합 인스턴스 매핑
위에서 볼 수 있듯이 가상 컴퓨터 확장 집합 인스턴스는 0에서 시작하여 커집니다. 이름에 번호 매기기를 반영합니다. 예를 들어 BackEnd_0은 백 엔드 가상 컴퓨터 확장 집합의 인스턴스 0입니다. 이 특정 가상 컴퓨터 확장 집합에는 BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 및 BackEnd_4라는 5개의 인스턴스가 있습니다.

가상 컴퓨터 확장 집합을 확대하는 경우 새 인스턴스가 생성됩니다. 새 가상 컴퓨터 확장 집합 인스턴스 이름은 일반적으로 가상 컴퓨터 확장 집합 이름+다음 인스턴스 번호입니다. 이 예제에서는 BackEnd_5입니다.

## <a name="mapping-virtual-machine-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>각 노드 형식/가상 컴퓨터 확장 집합에 가상 컴퓨터 확장 집합 부하 분산 장치 매핑
포털에서 클러스터를 배포했거나 샘플 Resource Manager 템플릿을 사용한 경우 리소스 그룹 아래에서 모든 리소스의 목록을 가져옵니다. 각 가상 컴퓨터 확장 집합 또는 노드 형식에 대한 부하 분산 장치를 확인합니다.

이름은 **LB-&lt;NodeType name&gt;**과 같습니다. 예를 들어 이 스크린 샷에 표시된 대로 LB-sfcluster4doc-0입니다.

![리소스][Resources]

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>가상 컴퓨터 확장 집합 인스턴스 또는 클러스터 노드에 원격 연결
클러스터에 정의된 모든 노드 형식은 별도의 가상 컴퓨터 확장 집합으로 설정됩니다.  즉, 노드 유형이 독립적으로 확장 또는 축소될 수 있습니다. 또한 다른 VM SKU로 구성될 수 있습니다. 단일 VM 인스턴스와 달리 가상 컴퓨터 확장 집합 인스턴스는 고유한 가상 IP 주소를 가져오지 못합니다. 따라서 특정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트를 찾기란 조금 어려울 수 있습니다.

찾기 위해 수행할 수 있는 단계는 다음과 같습니다.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>1단계: 노드 형식에 대한 가상 IP 주소 및 RDP에 대한 인바운드 NAT 규칙 찾기
이를 가져오려면 **Microsoft.Network/loadBalancers**에 대한 리소스 정의의 일부로 정의된 인바운드 NAT 규칙 값을 가져와야 합니다.

포털에서 부하 분산 장치 블레이드 및 **설정**으로 이동합니다.

![LBBlade][LBBlade]

**설정**에서 **인바운드 NAT 규칙**을 클릭합니다. 이제 첫 번째 가상 컴퓨터 확장 집합 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트를 제공합니다. 아래 스크린샷에서 **104.42.106.156** 및 **3389**입니다.

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-virtual-machine-scale-set-instancenode"></a>2단계: 특정 가상 컴퓨터 확장 집합 인스턴스/노드에 원격 연결하는 데 사용할 수 있는 포트 찾기
이 문서의 앞부분에서 가상 컴퓨터 확장 집합 인스턴스를 노드에 매핑하는 방법에 대해 이야기했습니다. 정확한 포트를 찾기 위해 그 방법을 사용합니다.

포트는 가상 컴퓨터 확장 집합 인스턴스의 오름차순으로 할당됩니다. 따라서 프런트 엔드 노드 유형에 대한 예제에서 5개의 인스턴스 각각에 대한 포트는 다음과 같습니다. 이제 가상 컴퓨터 확장 집합 인스턴스에 동일한 매핑을 수행해야 합니다.

| **가상 컴퓨터 확장 집합 인스턴스** | **포트** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-virtual-machine-scale-set-instance"></a>3단계: 특정 가상 컴퓨터 확장 집합 인스턴스에 원격 연결
아래 스크린샷에서 원격 데스크톱 연결을 사용하여 FrontEnd_1에 연결합니다.

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>RDP 포트 범위 값을 변경하는 방법
### <a name="before-cluster-deployment"></a>클러스터 배포 전에
Resource Manager 템플릿을 사용하여 클러스터를 설정하는 경우 **inboundNatPools**에서 범위를 지정할 수 있습니다.

**Microsoft.Network/loadBalancers**에 대한 리소스 정의로 이동합니다. 여기서 **inboundNatPools**에 대한 설명을 찾을 수 있습니다.  *frontendPortRangeStart* 및 *frontendPortRangeEnd* 값을 바꿉니다.

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>클러스터 배포 후에
클러스터 배포 후에는 조금 더 복잡하고 VM이 재활용될 수 있습니다. Azure PowerShell을 사용하여 새 값을 설정합니다. 컴퓨터에 Azure PowerShell 1.0 이상이 설치되어 있는지 확인합니다. Azure Powershell 1.0 이상을 설치하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)에서 설명한 단계를 따르는 것이 좋습니다.

Azure 계정에 로그인합니다. Powershell이 어떤 이유로 인해 실패하면 Azure PowerShell이 올바르게 설치되었는지 확인해야 합니다.

```
Login-AzureRmAccount
```

부하 분산 장치에 대한 자세한 내용을 보려면 다음을 실행하고 **inboundNatPools**에 대한 설명에서 값이 확인합니다.

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

*frontendPortRangeEnd* 및 *frontendPortRangeStart*를 원하는 값으로 설정합니다.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```

## <a name="how-to-change-the-rdp-username--password-for-nodes"></a>RDP 사용자 이름 및 노드의 암호를 변경하는 방법

다음 단계에서는 지정된 노드 유형의 모든 노드에 대한 암호를 변경하는 방법을 간략하게 설명합니다. 이러한 변경 내용은 가상 컴퓨터 확장 집합의 모든 현재 및 미래의 노드에 적용됩니다.

### <a name="step-1-open-powershell-with-elevated-privileges-administrator-mode"></a>1단계: 상승된 권한(관리자 모드)으로 PowerShell을 엽니다. 
### <a name="step-2-run-the-following-commands-to-log-in-and-select-your-subscription-for-the-session-change-the-subscriptionid-parameter-to-your-subscription-id"></a>2단계: 다음 명령을 실행하여 로그인하고 세션에 대한 구독을 선택합니다. `SUBSCRIPTIONID` 매개 변수를 구독 ID로 변경합니다. 

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
```

### <a name="step-3-run-the-following-script-with-the-appropriate-nodetypename-resourcegroup-username-and-password-values-the-username-and-password-values-will-be-the-new-credentials-that-should-be-used-in-future-rdp-sessions"></a>3단계: 적절한 `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` 및 `PASSWORD` 값을 사용하여 다음 스크립트를 실행합니다. `USERNAME` 및 `PASSWORD` 값은 나중에 RDP 세션에서 사용해야 하는 새 자격 증명입니다. 

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
* ["어디에나 배포" 기능의 개요 및 Azure 관리된 클러스터와 비교](service-fabric-deploy-anywhere.md)
* [클러스터 보안](service-fabric-cluster-security.md)
* [ 서비스 패브릭 SDK 및 시작](service-fabric-get-started.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

