---
title: "Azure 가상 네트워크 서비스 끝점 구성 | Microsoft Docs"
description: "가상 네트워크에서 서비스 끝점을 사용 및 사용하지 않도록 설정하는 방법에 대해 알아봅니다"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 9c953e697d5d1c6e53df8eaf72c4e4fbd83ca286
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="configure-virtual-network-service-endpoints"></a>가상 네트워크 서비스 끝점 구성

VNet(Virtual Network) 서비스 끝점을 사용하면 이러한 리소스에 대한 인터넷 액세스를 완전히 제거하여 Azure Virtual Network에 대한 Azure 서비스 리소스를 보호할 수 있습니다. 서비스 끝점은 가상 네트워크에서 Azure 서비스로의 직접 연결을 제공하며 VNet의 개인 주소 공간을 사용하여 Azure 서비스에 액세스할 수 있도록 합니다. 서비스 끝점을 통해 Azure 서비스에 도달하는 트래픽은 항상 Microsoft Azure backbone 네트워크에 유지됩니다. 자세한 내용은 ["가상 네트워크 서비스 끝점"](virtual-network-service-endpoints-overview.md)을 참조하세요.

이 문서에서는 서비스 끝점을 사용 및 사용하지 않도록 설정하는 단계를 제공합니다. 끝점을 Azure 서비스에 대한 서브넷에서 사용할 수 있게 되면 가상 네트워크에 대한 특정 서비스 리소스를 보호할 수 있습니다.

[Azure Portal](#azure-portal), [Azure PowerShell](#azure-powershell), [Azure 명령줄 인터페이스](#azure-cli) 또는 Azure Resource Manager [ 템플릿](#resource-manager-template)을 사용하여 서비스 끝점을 구성할 수 있습니다.

>[!NOTE]
미리 보기 중에 VNet 서비스 끝점 기능은 특정 지역에서 지원됩니다. 지원되는 지역 목록은 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 참조하세요.

## <a name="service-endpoint-configuration-overview"></a>서비스 끝점 구성 개요

- 서비스 끝점은 Azure Resource Manager 배포 모델을 통해 배포된 VNet에서만 구성될 수 있습니다.

- 서비스 끝점은 VNet의 각 서브넷에서 설정됩니다.

- 서브넷의 경우 서비스에 대해 하나의 서비스 끝점만을 구성할 수 있습니다. 다른 서비스(예: Azure Storage, Azure SQL)에서는 여러 개의 서비스 끝점을 구성할 수 있습니다.

- 기존 또는 새로운 서브넷에서 끝점을 사용할 수 있습니다.

- 끝점에 대한 위치는 자동으로 구성됩니다. 기본적으로 서비스 끝점은 VNet의 지역으로 구성됩니다. Azure Storage의 경우 지역 장애 조치 시나리오를 지원하려면 끝점을 [Azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)으로 자동으로 구성합니다.

  >[!NOTE]
  VNet/서브넷의 크기에 따라 서비스 끝점을 사용하도록 설정하는 데 다소 시간이 걸릴 수 있습니다. 서비스 끝점을 사용하도록 설정할 때 중요한 작업이 진행 중이지 않도록 합니다. 서비스 끝점은 서브넷의 모든 NIC에서 경로를 전환하고 열린 TCP 연결을 종료할 수 있습니다. 

- 서브넷의 모든 NIC에서 서비스에 대한 트래픽 흐름이 VNet 개인 IP 주소로 전환된 후에 서비스 끝점 호출은 "성공"을 반환합니다.

- 끝점 구성의 유효성을 검사하는 유효 경로:

   서비스 끝점이 올바르게 구성되어 있는지 유효성을 검사하려면 서브넷의 모든 NIC에서 서비스별 및 지역별 "유효 경로"가 nextHopType: VirtualNetworkServiceEndpoint인 새 "기본" 경로를 표시해야 합니다. [유효 경로 관련 문제 해결](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)에 대해 자세히 알아봅니다.

   >[!NOTE]
   하나 이상의 NIC(네트워크 인터페이스)를 구성하고 서브넷에서 실행 중인 가상 컴퓨터와 연결한 경우 유효 경로를 볼 수 있습니다.

## <a name="azure-portal"></a>Azure 포털

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>VNet 생성 중에 서브넷에서 서비스 끝점 설정

1. [Azure Portal](https://portal.azure.com/)을 엽니다.
Azure 계정을 사용하여 Azure에 로그인합니다. Azure 계정이 없으면 평가판에 등록할 수 있습니다. 계정에는 가상 네트워크 및 서비스 끝점을 만드는 데 필요한 [사용 권한](#provisioning)이 있어야 합니다.
2. +새로 만들기 > 네트워킹 > 가상 네트워크 > +추가를 클릭합니다.
3. "가상 네트워크 만들기"에서 다음 값을 입력한 다음 만들기를 클릭합니다.

설정 | 값
------- | -----
이름    | myVnet
주소 공간 | 10.0.0.0/16
서브넷 이름|mySubnet
서브넷 주소 범위|10.0.0.0/24
리소스 그룹|새로 만들기를 선택한 채로 이름을 입력합니다.
위치|지원되는 지역, 예: 오스트레일리아 동부
구독|사용 중인 구독을 선택합니다.
__ServiceEndpoints__|사용
__서비스__ | 사용 가능한 서비스 중 하나 또는 모두를 선택합니다. 미리 보기에서 지원되는 서비스입니다. __"Microsoft.Storage", "Microsoft.Sql"__

끝점에 대한 서비스를 선택합니다. ![서비스 끝점 서비스 선택](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. 모든 설정이 올바른지 유효성을 검사하고 "만들기"를 클릭합니다.

![서비스 끝점 설정](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. VNet에 대한 Azure 서비스 리소스의 보안을 완료하려면 [다음 단계](#Next%20Steps)에서 서비스 설명서를 클릭합니다.


### <a name="validating-service-endpoint-configuration"></a>서비스 끝점 구성 유효성 검사

서비스 끝점이 다음 단계를 사용하여 구성되는지 확인합니다.

- 리소스에서 "Virtual Network"를 클릭합니다. VNet을 검색합니다.
- VNet 이름을 클릭하고 "서비스 끝점"으로 이동합니다.
- 구성된 끝점이 "성공"으로 표시됩니다. 자동 구성된 위치를 확인할 수도 있습니다.

![서비스 끝점 구성 확인](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>끝점 구성의 유효성을 검사하는 유효 경로

서브넷의 NIC(네트워크 인터페이스)에서 유효 경로를 보려면 해당 서브넷의 NIC를 클릭합니다. "지원 + 문제 해결"에서 "유효 경로"를 클릭합니다. 끝점이 구성된 경우 서비스의 주소 접두사를 대상으로, nextHopType을 "VirtualNetworkServiceEndpoint"로 설정한 새 "기본" 경로가 표시됩니다.

![서비스 끝점의 유효 경로](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>VNet에서 기존 서브넷의 서비스 끝점 설정

1. 리소스에서 "가상 네트워크"를 클릭하고 기존 VNet을 검색합니다.
2. VNet 이름을 클릭하고 "서비스 끝점"으로 이동합니다.
3. "추가"를 클릭합니다. "서비스"를 선택합니다. 한 번에 하나의 서비스에 대한 끝점을 만들 수 있습니다. 
4. 끝점을 적용하려는 모든 서브넷을 선택합니다. "추가"를 클릭합니다.

![서브넷 서비스 끝점 구성](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>서비스 끝점 삭제
1. 리소스에서 "Virtual Network"를 클릭합니다. VNet 이름을 필터링하여 기존 VNet을 검색합니다.
2. VNet 이름을 클릭하고 "서비스 끝점"으로 이동합니다.
3. 서비스 이름을 클릭하고 서비스 끝점 항목을 마우스 오른쪽 단추로 클릭합니다.
4. "삭제"를 선택합니다.

![서비스 끝점 삭제](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure Powershell

필수 구성 요소 설정:

- 최신 버전의 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- PowerShell 세션을 시작하려면 **시작**으로 이동하고 **powershell**을 입력한 다음 **PowerShell**을 클릭합니다.
- PowerShell에서 `login-azurermaccount` 명령을 입력하여 Azure에 로그인합니다. 계정에는 가상 네트워크 및 서비스 끝점을 만드는 데 필요한 [사용 권한](#provisioning)이 있어야 합니다.

### <a name="get-available-service-endpoints-for-azure-region"></a>Azure 지역에 사용 가능한 서비스 끝점 가져오기

아래 명령을 사용하여 Azure 지역의 끝점에 지원되는 서비스의 목록을 가져옵니다.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

출력: 
이름 | ID | 형식
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>가상 네트워크 *myVNet*을 만드는 동안 서브넷 *mySubnet*에 Azure Storage 서비스 끝점을 추가합니다.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
서비스 이름의 쉼표로 구분된 목록을 사용하여 여러 서비스를 사용할 수 있습니다.
예: "Microsoft.Storage", "Microsoft.Sql"

예상 출력:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

VNet에 대한 Azure 서비스 리소스의 보안을 완료하려면 [다음 단계](#Next%20Steps)에서 서비스 설명서를 클릭합니다.

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>기존 서브넷에 여러 개의 서비스 끝점 추가

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

예상 출력: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>서브넷에서 구성된 서비스 끝점 보기

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
출력:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>서브넷에서 서비스 끝점 삭제
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Azure CLI

필수 구성 요소 설정:
- [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 로그인에 대한 자세한 내용은 [Azure CLI 2.0 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)을 참조하세요.
 - 계정에는 가상 네트워크 및 서비스 끝점을 만드는 데 필요한 [사용 권한](#provisioning)이 있어야 합니다.

 가상 네트워크에 대한 명령의 전체 목록은 [Azure CLI 가상 네트워크 명령](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)을 참조하세요.

### <a name="get-available-service-endpoints-for-azure-region"></a>Azure 지역에 사용 가능한 서비스 끝점 가져오기

아래 명령을 사용하여 Azure 지역(예: "EastUS")의 끝점에 지원되는 서비스의 목록을 가져옵니다.
```azure-cli
az network vnet list-endpoint-services -l eastus
```
출력:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>가상 네트워크 *myVNet*을 만드는 동안 서브넷 *mySubnet*에 Azure Storage 서비스 끝점을 추가합니다.

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

여러 끝점을 추가하려면: --service-endpoints Microsoft.Storage Microsoft.Sql

출력:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

VNet에 대한 Azure 서비스 리소스의 보안을 완료하려면 [다음 단계](#Next%20Steps)에서 서비스 설명서를 클릭합니다.

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>기존 서브넷에 여러 개의 서비스 끝점 추가

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

예상 출력:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>서브넷에서 구성된 서비스 끝점 보기

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>서브넷에서 서비스 끝점 삭제
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

출력: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Resource Manager 템플릿

### <a name="securing-azure-service-resources-to-vnets"></a>VNet에 대한 Azure 서비스 리소스 보호

서비스 끝점을 통해 가상 네트워크에 대한 특정 Azure 리소스를 보호할 수 있습니다.

샘플 [Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)을 다운로드하여 VNet의 서브넷에 대한 저장소 계정을 보호합니다.

이 템플릿은 각 서브넷에서 NIC를 포함한 VM, 2개의 서브넷을 포함한 VNet을 만듭니다. 하나의 서브넷에서 끝점을 사용하고 해당 서브넷에 대한 저장소 계정을 보호합니다.

템플릿을 다운로드하고 시나리오에 맞게 일부 수정할 수 있습니다.

지침은 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 템플릿을 배포하기 위한 템플릿으로 제공됩니다. 끝점을 설정하고 계정을 보호하는 필수 [사용 권한](#provisioning)이 있는지 확인합니다.

서브넷에 대한 Azure 리소스를 보호하려면:

- 서비스 끝점이 해당 서브넷에 구성되어야 합니다.
- 리소스에서 가상 네트워크 규칙을 추가하여 VNet에 대해 리소스를 보호해야 합니다.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>서브넷에 대해 보호된 리소스를 포함하는 서비스 끝점을 삭제합니다.
서브넷에 대한 Azure 서비스 리소스를 보호하고 서비스 끝점을 삭제하는 경우 서브넷의 리소스에 더 이상 액세스할 수 없습니다.
끝점을 다시 사용하도록 설정하면 서브넷에 대해 이전에 보호된 리소스에 대한 액세스를 복원하지 않습니다.

이 서브넷에 대한 서비스 리소스를 다시 보호하려면 다음을 수행해야 합니다.

- 끝점을 다시 사용하도록 설정
- 리소스에서 이전 VNet 규칙 제거
- 서브넷에 대해 리소스를 보호하는 새 규칙 추가

## <a name="provisioning"></a>프로비전

가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 끝점을 구성할 수 있습니다.

VNet에 대한 Azure 서비스 리소스를 보호하려면 사용자는 추가되는 서브넷의 "Microsoft.Network/JoinServicetoaSubnet"에 대한 사용 권한이 있어야 합니다. 이 권한은 기본적으로 기본 제공 서비스 관리자 역할에 포함되고 사용자 지정 역할을 만들어서 수정될 수 있습니다.

[기본 제공 역할](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) 및 [사용자 지정 역할](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)에 특정 권한 할당에 대해 자세히 알아보세요.

VNet 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. 리소스가 다른 구독에 있는 경우 이 미리 보기에서는 리소스가 동일한 AD(Active Directory) 테넌트 아래에 있어야 합니다.

## <a name="next-steps"></a>다음 단계

VNet에 대한 서비스 리소스를 보호하기 위한 자세한 정보는 아래 링크를 참조하세요.

[가상 네트워크에 대한 Azure Storage 계정 보호](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[가상 네트워크에 대한 Azure SQL 보호](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

