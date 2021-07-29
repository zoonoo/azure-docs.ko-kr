---
title: 'Azure Private Link 서비스 원본 IP 주소에 대해 네트워크 정책 사용 안 함 '
description: Azure Private Link에 네트워크 정책을 사용하지 않도록 설정하는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 887cba48aaac9cd7bc4f1f1f88b61733f0fbb9cf
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696597"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>프라이빗 링크 서비스에 대해 네트워크 정책 사용 안 함

프라이빗 링크 서비스의 원본 IP 주소를 선택하려면 서브넷에서 명시적 사용 한 함 설정 `privateLinkServiceNetworkPolicies`이 필요합니다. 이 설정은 프라이빗 링크 서비스의 원본 IP로 선택한 특정 프라이빗 IP 주소에만 적용됩니다. 서브넷의 다른 리소스에 대한 액세스는 NSG(네트워크 보안 그룹) 보안 규칙 정의를 기반으로 제어됩니다. 
 
포털을 사용하여 프라이빗 링크 서비스를 만들 때 해당 설정은 만들기 프로세스의 일부로 사용하지 않도록 자동 설정됩니다. Azure 클라이언트(PowerShell, CLI 또는 템플릿)를 사용하는 배포에는 이 속성을 변경하는 추가 단계가 필요합니다. Azure Portal 또는 Azure PowerShell의 로컬 설치, Azure CLI에서 Cloud Shell을 사용하여 정책을 사용하지 않도록 설정하거나 Azure Resource Manager 템플릿을 사용할 수 있습니다.  
 
아래 단계에 따라 *myResourceGroup* 이라는 리소스 그룹에서 호스트되는 *기본* 서브넷이 있는 *myVirtualNetwork* 가상 네트워크에 대한 프라이빗 링크 서비스 네트워크 정책을 비활성화합니다. 

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 Azure PowerShell을 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다.
코드에서 "default"를 가상 서브넷의 이름으로 바꿉니다.

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI를 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>템플릿 사용
이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>다음 단계
- [Azure 프라이빗 엔드포인트](private-endpoint-overview.md)에 대해 자세히 알아보기
 
