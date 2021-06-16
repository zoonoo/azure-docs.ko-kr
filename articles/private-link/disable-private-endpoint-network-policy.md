---
title: Azure에서 프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정
description: 프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정하는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6014867ed30901f0b6470d843dd7b3761bc1ba71
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110696617"
---
# <a name="disable-network-policies-for-private-endpoints"></a>프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정

프라이빗 엔드포인트에는 NSG(네트워크 보안 그룹)와 같은 네트워크 정책이 지원되지 않습니다. 지정된 서브넷에 프라이빗 엔드포인트를 배포하려면 해당 서브넷에 명시적 비활성화 설정이 필요합니다. 해당 설정은 프라이빗 엔드포인트에만 적용됩니다. 서브넷의 다른 리소스에 대한 액세스는 NSG(네트워크 보안 그룹) 보안 규칙 정의를 기반으로 제어됩니다. 
 
포털을 사용하여 프라이빗 엔드포인트를 만들 때 만들기 프로세스의 일부로 해당 설정은 자동으로 사용하지 않도록 설정됩니다. 다른 클라이언트를 사용하는 배포에는 해당 설정을 변경하는 추가 단계가 필요합니다. Azure Portal 또는 Azure PowerShell의 로컬 설치, Azure CLI에서 Cloud Shell을 사용하여 설정을 사용하지 않도록 설정하거나 Azure Resource Manager 템플릿을 사용할 수 있습니다.  
 
다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹에서 호스트된 *기본* 서브넷을 사용하여 *myVirtualNetwork* 라는 가상 네트워크에 대해 `PrivateEndpointNetworkPolicies`을 사용하지 않도록 설정하는 방법을 설명합니다.

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 Azure PowerShell을 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI를 사용하여 서브넷 프라이빗 엔드포인트 정책을 사용하지 않도록 설정하는 방법을 설명합니다.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>다음 단계
- [Azure 프라이빗 엔드포인트](private-endpoint-overview.md)에 대해 자세히 알아보기
 
