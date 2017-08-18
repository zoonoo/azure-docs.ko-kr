---
title: "Azure Virtual Network 피어링 만들기, 변경 또는 삭제 | Microsoft Docs"
description: "가상 네트워크 피어링을 만들고 변경하거나 삭제하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 43053ce764fdeb88bde574d489fbfecbfcb491eb
ms.contentlocale: ko-kr
ms.lasthandoff: 08/16/2017

---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>가상 네트워크 피어링 만들기, 변경 또는 삭제

가상 네트워크 피어링을 만들고 변경하거나 삭제하는 방법을 알아봅니다. 가상 네트워크 피어링을 사용하면 Azure 백본 네트워크를 통해 같은 Azure 위치에 있는 두 개의 가상 네트워크를 연결할 수 있습니다. 피어링된 후 두 가상 네트워크는 여전히 별도의 리소스로 관리됩니다. 두 가상 네트워크의 리소스는 같은 가상 네트워크의 리소스인 것처럼 같은 대기 시간 및 대역폭으로 통신합니다. 가상 네트워크 피어링에 익숙하지 않은 경우 이 문서의 작업을 완료하기 전에 먼저 [가상 네트워크 피어링 개요](virtual-network-peering-overview.md)를 확인하고 [가상 네트워크 피어링 만들기 자습서](virtual-network-create-peering.md)를 완료하는 것이 좋습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 검토하여 피어링에 대한 제한을 알아봅니다.
- Azure 계정이 있는 Azure Portal, Azure CLI(명령줄 인터페이스) 또는 Azure PowerShell에 로그인합니다. 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- PowerShell 명령을 사용하여 이 문서의 작업을 완료하는 경우 [Azure PowerShell을 설치 및 구성](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)합니다. 최신 버전의 Azure PowerShell cmdlet을 설치했는지 확인합니다. 예제와 함께 PowerShell 명령에 대한 도움말을 보려면 `get-help <command> -full`을 입력합니다.
- Azure CLI(명령줄 인터페이스) 명령을 사용하여 이 문서의 작업을 완료하는 경우 [Azure CLI를 설치 및 구성](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)합니다. 최신 버전의 Azure CLI를 설치했는지 확인합니다. CLI 명령에 대한 도움말을 보려면 `az <command> --help`를 입력합니다. CLI 및 해당 필수 구성 요소를 설치하는 대신 Azure Cloud Shell을 사용할 수 있습니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Cloud Shell에는 Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Cloud Shell을 사용하려면 [Portal](https://portal.azure.com) 위쪽의 Cloud Shell **>_** 단추를 클릭합니다. 

## <a name="create-a-peering"></a>피어링 만들기

>[!NOTE]
>가상 네트워크 피어링을 성공적으로 만들려면 몇 가지 요구 사항을 충족하고 제약 조건과 고려 사항을 확인해야 합니다. 피어링을 만들기 전에 먼저 [요구 사항 및 제약 조건](#requirements-and-constraints)과 [필요한 권한](#permissions)을 숙지하세요.
>

1. 필요한 [역할 또는 권한](#permissions)이 할당된 계정으로 [Portal](https://portal.azure.com)에 로그인합니다.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다. 클래식 배포 모델을 통해 배포된 가상 네트워크에서는 피어링을 만들 수 없으므로 **가상 네트워크(클래식)**가 목록에 나타나더라도 선택하지 않습니다.
3. 나타나는 **가상 네트워크** 블레이드에서 피어링을 만들려는 가상 네트워크를 클릭합니다.
4. 선택한 가상 네트워크에 대해 표시되는 창의 **설정** 섹션에서 **피어링**을 클릭합니다.
5. **+ 추가**를 클릭합니다. 
6. <a name="add-peering"></a>**피어링 추가** 블레이드에서 다음 설정에 대한 값을 입력하거나 선택합니다.
    - **이름:** 피어링의 이름은 가상 네트워크 내에서 고유해야 합니다.
    - **가상 네트워크 배포 모델:** 피어링하려는 가상 네트워크를 배포한 배포 모델을 선택합니다.
    - **리소스 ID를 알고 있음:** 피어링하려는 가상 네트워크에 대한 읽기 권한이 있는 경우 이 확인란을 선택 취소된 상태로 둡니다. 피어링하려는 가상 네트워크 또는 구독에 대한 읽기 권한이 없는 경우 이 확인란을 선택합니다. 확인란을 선택할 때 나타난 **리소스 ID** 상자에 피어링하려는 가상 네트워크의 전체 리소스 ID를 입력합니다. 입력하는 리소스 ID는 이 가상 네트워크와 같은 Azure [위치](https://azure.microsoft.com/regions)에 있는 가상 네트워크의 리소스 ID여야 합니다. 전체 리소스 ID는 /subscriptions/<Id>/resourceGroups/<리소스 그룹 이름>/providers/Microsoft.Network/virtualNetworks/<가상 네트워크 이름>과 유사합니다. 가상 네트워크의 속성을 확인하여 가상 네트워크의 리소스 ID를 알 수 있습니다. 가상 네트워크의 속성을 확인하는 방법을 알아보려면 [가상 네트워크 관리](virtual-network-manage-network.md#view-vnet)를 참조하세요.
    - **구독:** 피어링하려는 가상 네트워크의 [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)을 선택합니다. 계정이 읽기 권한이 있는 구독 수에 따라 하나 이상의 구독이 나열됩니다. **리소스 ID** 확인란을 선택한 경우 이 설정을 사용할 수 없습니다. 두 가상 네트워크가 Resource Manager를 통해 만들어졌기만 하면 다른 구독에 있는 가상 네트워크를 피어링할 수 있습니다. 다른 배포 모델을 통해 만들어진 구독에서 피어링하는 기능은 미리 보기 릴리스 상태입니다. 다른 구독에 있는 다른 배포 모델을 통해 배포된 가상 네트워크 간에 피어링을 만들기 전에 먼저 미리 보기에 등록합니다. 미리 보기에 등록하고 [다른 구독에 있는 다른 배포 모델을 통해 만든 가상 네트워크를 피어링](create-peering-different-deployment-models-subscriptions.md)하는 방법에 대해 자세히 알아보세요.
    - **가상 네트워크:** 피어링하려는 가상 네트워크를 선택합니다. 어느 Azure 배포 모델을 통해 만들어진 가상 네트워크이든 선택할 수 있지만, 가상 네트워크는 피어링을 시작하는 가상 네트워크와 같은 위치에 있어야 합니다. 목록에 가상 네트워크가 표시되게 하려면 가상 네트워크에 대한 읽기 권한이 있어야 합니다. 가상 네트워크가 나열되지만, 회색으로 표시된 경우 가상 네트워크의 주소 공간이 이 가상 네트워크의 주소 공간과 겹치기 때문일 수 있습니다. 가상 네트워크 주소 공간이 겹치면 피어링할 수 없습니다. **리소스 ID** 확인란을 선택한 경우 이 설정을 사용할 수 없습니다.
    - **가상 네트워크 액세스 허용:** 두 가상 네트워크 간 통신을 사용하도록 설정하려면 **사용**(기본값)을 선택합니다. 가상 네트워크 간 통신을 사용하도록 설정하면 어느 쪽 가상 네트워크에든 연결된 리소스가 같은 가상 네트워크에 연결된 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다. 두 가상 네트워크의 리소스 간 모든 통신은 Azure 개인 네트워크를 통해 이루어집니다. 네트워크 보안 그룹에 대한 **VirtualNetwork** 기본 태그는 가상 네트워크와 피어링된 가상 네트워크를 포함합니다. 네트워크 보안 그룹 기본 태그에 대해 자세히 알아보려면 [네트워크 보안 그룹 개요](virtual-networks-nsg.md#default-tags) 문서를 읽어보세요.  트래픽이 피어링된 가상 네트워크로 흐르지 않게 하려면 **사용 안 함**을 선택합니다. 가상 네트워크를 다른 가상 네트워크와 피어링했지만, 종종 두 가상 네트워크 간 트래픽 흐름을 비활성화하려는 경우에도 **사용 안 함**을 선택할 수 있습니다. 피어링을 삭제하고 다시 만드는 것보다 사용/사용 안 함을 설정하는 것이 더 편리함을 알 수 있습니다. 이 설정을 사용하지 않도록 설정하면 피어링된 가상 네트워크 간에 트래픽이 흐르지 않습니다.
    - **전달된 트래픽 허용:** 피어링된 가상 네트워크로 전달된 트래픽(피어링된 가상 네트워크에서 발생하지 않은 트래픽)이 이 가상 네트워크로 흐를 수 있도록 허용하려면 이 확인란을 선택합니다. 트래픽 전달은 피어링 중인 가상 네트워크에 네트워크 가상 어플라이언스를 배포하고 네트워크 가상 어플라이언스를 통해 트래픽을 전달하기 위한 사용자 정의 경로를 만든 경우 일반적입니다. 이 상자를 선택 취소된 상태로 두면(기본값), 피어링된 가상 네트워크에서 전달된 트래픽이 이 가상 네트워크로 흐를 수 없습니다. 이 기능을 사용하도록 설정하여 피어링을 통해 전달된 트래픽을 허용하는 동안에는 사용자 정의 경로나 네트워크 가상 어플라이언스가 만들어지지 않습니다. 사용자 정의 경로와 네트워크 가상 어플라이언스는 개별적으로 만들어집니다. [사용자 정의 경로](virtual-networks-udr-overview.md)에 대해 자세히 알아보세요.
    - **게이트웨이 전송 허용:** 가상 네트워크 게이트웨이를 이 가상 네트워크에 연결했고 피어링된 가상 네트워크의 트래픽이 게이트웨이를 통해 흐르도록 하려면 이 상자를 선택합니다. 예를 들어 가상 네트워크 게이트웨이를 통해 이 가상 네트워크를 온-프레미스 네트워크에 연결할 수 있습니다. 이 상자를 선택하면 피어링된 가상 네트워크에서 이 가상 네트워크에 연결된 게이트웨이를 통해 트래픽이 흐를 수 있습니다. 이 상자를 선택하면 피어링된 가상 네트워크에 대한 게이트웨이를 구성할 수 없습니다. 다른 가상 네트워크에서 이 가상 네트워크로의 피어링을 설정할 때 피어링된 가상 네트워크에 대해 **원격 게이트웨이 사용** 확인란을 선택해야 합니다. 이 상자를 선택 취소된 상태로 두면(기본값), 피어링된 가상 네트워크에서 이 가상 네트워크로 여전히 트래픽이 흐르지만 이 가상 네트워크에 연결된 가상 네트워크 게이트웨이를 통해서는 흐를 수 없습니다. [가상 네트워크 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti)에 대해 자세히 알아보세요. 
    
    가상 네트워크(Resource Manager)를 가상 네트워크(클래식)와 피어링하는 경우 이 옵션을 사용하도록 설정할 수 없습니다. 두 가상 네트워크 간에 트래픽이 흐르기는 하지만, 가상 네트워크(클래식) 트래픽은 가상 네트워크(Resource Manager)에 연결된 네트워크 게이트웨이를 통해 흐를 수 없습니다.
    - **원격 게이트웨이 사용:** 이 가상 네트워크의 트래픽이 피어링 중인 가상 네트워크에 연결된 가상 네트워크 게이트웨이를 통해 흐를 수 있도록 허용하려면 이 상자를 선택합니다. 예를 들어 피어링 중인 가상 네트워크에 온-프레미스 네트워크에 대한 통신을 가능하게 하는 VPN Gateway가 연결되어 있습니다.  이 상자를 선택하면 이 가상 네트워크에서 피어링된 가상 네트워크에 연결된 VPN Gateway를 통해 트래픽이 흐를 수 있습니다. 이 상자를 선택하는 경우 피어링된 가상 네트워크에 가상 네트워크 게이트웨이가 연결되어 있어야 하며 **게이트웨이 전송 허용** 확인란이 선택되어 있어야 합니다. 이 상자를 선택 취소된 상태로 두면(기본값), 피어링된 가상 네트워크에서 이 가상 네트워크로 여전히 트래픽이 흐를 수 있지만 이 가상 네트워크에 연결된 가상 네트워크 게이트웨이를 통해서는 흐를 수 없습니다. 
    
    가상 네트워크(Resource Manager)를 가상 네트워크(클래식)와 피어링하는 경우 이 옵션을 사용하도록 설정할 수 없습니다. 두 가상 네트워크 간에 트래픽이 흐르기는 하지만, 가상 네트워크(Resource Manager) 트래픽은 가상 네트워크(클래식)에 연결된 네트워크 게이트웨이를 통해 흐를 수 없습니다.
7. **확인** 단추를 클릭하여 선택한 가상 네트워크에 서브넷을 추가합니다.

### <a name="commands"></a>명령

|도구|명령|
|---|---|
|CLI|[az network vnet peering create](/cli/azure/network/vnet/peering#create?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="scenarios"></a>시나리오

가상 네트워크 피어링은 동일하거나 다른 구독에 존재하는 동일하거나 다른 배포 모델을 통해 만든 가상 네트워크 간에 만들어집니다. 다음 시나리오 중 하나에 대한 단계별 자습서를 완료합니다.
 
|Azure 배포 모델  | 구독  |
|---------|---------|
|둘 다 Resource Manager |[동일](virtual-network-create-peering.md)|
| |[다름](create-peering-different-subscriptions.md)|
|하나는 Resource Manager, 다른 하나는 클래식     |[동일](create-peering-different-deployment-models.md)|
| |[다름](create-peering-different-deployment-models-subscriptions.md)|

## <a name="view-or-change-peering-settings"></a>피어링 설정 보기 또는 변경

1. 필요한 [역할 또는 권한](#permissions)이 할당된 계정으로 [Portal](https://portal.azure.com)에 로그인합니다.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다.
3. 나타나는 **가상 네트워크** 블레이드에서 피어링을 만들려는 가상 네트워크를 클릭합니다.
4. 선택한 가상 네트워크에 대해 표시되는 창의 **설정** 섹션에서 **피어링**을 클릭합니다.
5. 설정을 보거나 변경할 피어링을 클릭합니다.
6. 해당 설정을 변경합니다. 이 문서의 피어링 만들기 섹션에 있는 [6단계](#add-peering)의 각 설정에 대한 옵션에 대해 읽어보세요. 

    >[!NOTE]
    >가상 네트워크 피어링을 성공적으로 만들려면 몇 가지 요구 사항을 충족하고 제약 조건과 고려 사항을 확인해야 합니다. 피어링을 만들기 전에 먼저 [요구 사항 및 제약 조건](#requirements-and-constraints)과 [필요한 권한](#permissions)을 숙지하세요.
    >

7. **Save**를 클릭합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network vnet peering list](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#list): 가상 네트워크에 대한 피어링을 나열함, [az network vnet peering show](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#show): 특정 피어링에 대한 설정을 표시함, [az network vnet peering update](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#update): 피어링 설정을 변경함|
|PowerShell|[Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json): 피어링 설정을 검색함, [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json): 설정을 변경함|

## <a name="delete-a-peering"></a>피어링 삭제
피어링이 삭제되면 더 이상 가상 네트워크에서 피어링된 가상 네트워크로 트래픽이 흐르지 않습니다. Resource Manager를 통해 배포된 가상 네트워크가 피어링되면 각 가상 네트워크에 다른 가상 네트워크에 대한 피어링이 있습니다. 한 가상 네트워크에서 피어링을 삭제하여 가상 네트워크 간 통신을 사용하지 않도록 설정하더라도 다른 가상 네트워크에서 피어링이 삭제되지는 않습니다. 다른 가상 네트워크에 있는 피어링의 피어링 상태는 **연결 끊김**입니다. 첫 번째 가상 네트워크에서 피어링을 다시 만들고 두 가상 네트워크의 피어링 상태가 모두 *연결됨*으로 변경될 때까지는 피어링을 다시 만들 수 없습니다. 

가상 네트워크가 가끔 통신하되 항상 통신하지는 않게 하려면 피어링을 삭제하는 대신 **가상 네트워크 액세스 허용** 설정을 **사용 안 함**으로 설정할 수 있습니다. 방법을 알아보려면 이 문서의 [피어링 만들기](#create-peering) 섹션에 있는 6단계를 읽어보세요. 피어링을 삭제하고 다시 만드는 것보다 네트워크 액세스를 사용 안 함/사용 설정하는 것이 더 쉬움을 알 수 있습니다.

1. 필요한 [역할 또는 권한](#permissions)이 할당된 계정으로 [Portal](https://portal.azure.com)에 로그인합니다.
2. *리소스 검색* 텍스트가 있는 Azure Portal 위쪽의 상자에서 *가상 네트워크*를 입력합니다. 검색 결과에 표시되는 **가상 네트워크**를 클릭합니다.
3. 나타나는 **가상 네트워크** 블레이드에서 피어링을 삭제하려는 가상 네트워크를 클릭합니다.
4. 선택한 가상 네트워크에 대해 표시되는 블레이드의 **설정** 아래에서 **피어링**을 클릭합니다.
5. 피어링 블레이드에 나타나는 피어링 목록에서 삭제하려는 피어링을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭한 다음 **예**를 클릭하여 첫 번째 가상 네트워크에서 피어링을 삭제합니다.
6. 이전 단계를 완료하여 피어링의 다른 가상 네트워크에서 피어링을 삭제합니다.

**명령**

|도구|명령|
|---|---|
|CLI|[az network vnet peering delete](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="requirements-and-constraints"></a>요구 사항 및 제약 조건 

- 피어링하는 가상 네트워크에 겹치지 않는 IP 주소 공간이 있어야 합니다.
- 가상 네트워크가 다른 가상 네트워크와 피어링되면 가상 네트워크에 주소 공간을 추가하거나 가상 네트워크에서 주소 공간을 삭제할 수 없습니다. 주소 공간을 추가하거나 제거하려면 피어링을 삭제하고 주소 공간을 추가하거나 제거한 다음 피어링을 다시 만듭니다. 가상 네트워크에 주소 공간을 추가하거나 가상 네트워크에서 주소 공간을 제거하려면 [가상 네트워크 만들기, 변경 또는 삭제](virtual-network-manage-network.md#add-address-spaces) 문서를 읽어봅니다. 
- Resource Manager를 통해 배포된 두 가상 네트워크 또는 Resource Manager를 통해 배포된 가상 네트워크와 클래식 배포 모델을 통해 배포된 가상 네트워크를 피어링할 수 있습니다. 클래식 배포 모델을 통해 만든 두 가상 네트워크를 피어링할 수는 없습니다. Azure 배포 모델에 익숙하지 않은 경우 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 읽어보세요. [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)를 사용하여 클래식 배포 모델을 통해 만든 두 가상 네트워크는 연결할 수 없습니다.
- Resource Manager를 통해 만든 두 가상 네트워크를 피어링할 때는 피어링의 각 가상 네트워크에 대해 피어링을 구성해야 합니다. 첫 번째 가상 네트워크에서 두 번째 가상 네트워크로의 피어링을 만들면 피어링 상태가 *시작됨*입니다.  두 번째 가상 네트워크에서 첫 번째 가상 네트워크로의 피어링을 만들면 피어링 상태가 *연결됨*입니다. 첫 번째 가상 네트워크의 피어링 상태를 보면 해당 상태가 *시작됨*에서 *연결됨*으로 변경된 것을 알 수 있습니다. 두 가상 네트워크 피어링의 피어링 상태가 *연결됨*이 될 때까지는 피어링이 설정되지 않습니다. 
- Resource Manager를 통해 만든 가상 네트워크를 클래식 배포 모델을 통해 만든 가상 네트워크와 피어링하는 경우 Resource Manager를 통해 배포된 가상 네트워크에 대한 피어링만 구성합니다. 가상 네트워크(클래식)에 대해서나 클래식 배포 모델을 통해 배포된 두 가상 네트워크 간에는 피어링을 구성할 수 없습니다. 가상 네트워크(Resource Manager)에서 가상 네트워크(클래식)로의 피어링을 만들면 피어링 상태가 *업데이트 중*이 되었다가 곧 *연결됨*으로 변경됩니다.   
- 두 가상 네트워크 간에 피어링이 설정됩니다. 피어링은 전이적이지 않습니다. 다음 사이에 피어링을 만들면:
    - VirtualNetwork1 및 VirtualNetwork2
    - VirtualNetwork2 및 VirtualNetwork3

  VirtualNetwork2를 통한 VirtualNetwork1과 VirtualNetwork3 사이의 피어링은 없습니다. VirtualNetwork1과 VirtualNetwork3 사이에 가상 네트워크 피어링을 만들려는 경우 VirtualNetwork1과 VirtualNetwork3 사이에 피어링을 만들어야 합니다.
- 기본 Azure 이름 확인을 사용하여 피어링된 가상 네트워크에서 이름을 확인할 수 없습니다. 다른 가상 네트워크에서 이름을 확인하려면 사용자 지정 DNS 서버를 사용해야 합니다. 자체 DNS 서버를 설정하는 방법을 알아보려면 [자체 DNS 서버를 이용한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 문서를 읽어보세요.
- 피어링의 두 가상 네트워크 둘 다에 있는 리소스는 같은 가상 네트워크에 있는 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다. 그러나 각 가상 컴퓨터 크기에는 고유한 최대 네트워크 대역폭이 있습니다. 다양한 가상 컴퓨터 크기의 최대 네트워크 대역폭에 대한 자세한 내용을 알아보려면 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 컴퓨터 크기 문서를 참조하세요.
- Resource Manager를 통해 배포된, 같은 구독 또는 다른 구독에 있는 가상 네트워크를 피어링할 수 있습니다.
- 같거나 다른 구독에 있는 다른 배포 모델을 통해 배포된 가상 네트워크를 피어링할 수 있습니다(미리 보기). 
- 두 가상 네트워크에 있는 구독은 같은 Azure Active Directory 테넌트에 연결되어야 합니다. 아직 AD 테넌트가 없는 경우 빠르게 [만들](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) 수 있습니다. [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)를 사용하여 다른 Active Directory 테넌트에 연결된 다른 구독에 있는 2개의 가상 네트워크를 연결할 수 있습니다.
- 가상 네트워크를 다른 가상 네트워크에 피어링할 수 있으며, Azure Virtual Network 게이트웨이를 통해 다른 가상 네트워크에 연결할 수도 있습니다. 가상 네트워크가 피어링 및 게이트웨이를 통해 연결된 경우 가상 네트워크 간 트래픽은 게이트웨이가 아니라 피어링 구성을 통해 흐릅니다.
- 가상 네트워크 피어링을 활용하는 수신 및 송신 트래픽에 대한 명목 요금이 부과됩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-network)를 참조하세요.


## <a name="permissions"></a>권한

가상 네트워크 피어링을 만드는 데 사용하는 계정에 필요한 역할 또는 권한이 있어야 합니다. 예를 들어 이름이 myVnetA와 myVnetB인 두 가상 네트워크를 피어링하는 경우 계정에는 각 가상 네트워크에 대한 다음과 같은 최소 역할 또는 권한이 할당되어야 합니다.
    
|가상 네트워크|배포 모델|역할|권한|
|---|---|---|---|
|myVnetA|리소스 관리자|[네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |클래식|[클래식 네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|해당 없음|
|myVnetB|리소스 관리자|[네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||클래식|[클래식 네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

[기본 제공 역할](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 및 [사용자 지정 역할](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 권한 할당(Resource Manager만 해당)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[허브 및 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)를 만드는 방법을 알아봅니다. 

