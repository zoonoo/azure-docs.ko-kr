---
title: Azure에서 개인 끝점에 대한 네트워크 정책 비활성화
description: 개인 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정하는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453013"
---
# <a name="disable-network-policies-for-private-endpoints"></a>개인 끝점에 대한 네트워크 정책 비활성화

NSG(네트워크 보안 그룹)와 같은 네트워크 정책은 개인 끝점에 대해 지원되지 않습니다. 지정된 서브넷에 개인 끝점을 배포하려면 해당 서브넷에 명시적 비활성화 설정이 필요합니다. 이 설정은 개인 끝점에만 적용됩니다. 서브넷의 다른 리소스의 경우 NSG(네트워크 보안 그룹) 보안 규칙 정의에 따라 액세스가 제어됩니다. 
 
포털을 사용하여 개인 끝점을 만들 때 이 설정은 만들기 프로세스의 일부로 자동으로 비활성화됩니다. 다른 클라이언트를 사용하는 배포에는 이 설정을 변경하려면 추가 단계가 필요합니다. Azure 포털에서 클라우드 셸을 사용 하 여 설정을 사용 하지 않도록 설정할 수 있습니다 또는 Azure PowerShell, Azure CLI의 로컬 설치 또는 Azure 리소스 관리자 템플릿을 사용할 수 있습니다.  
 
다음 예제에서는 `PrivateEndpointNetworkPolicies` *myResourceGroup이라는*리소스 그룹에서 호스팅되는 *기본* 서브넷을 사용하여 *myVirtualNetwork라는* 가상 네트워크에 대해 사용하지 않도록 설정하는 방법을 설명합니다.

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 Azure PowerShell을 사용하여 서브넷 개인 엔드포인트 정책을 사용하지 않도록 설정하는 방법에 대해 설명합니다.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI를 사용하여 서브넷 개인 끝점 정책을 사용하지 않도록 설정하는 방법에 대해 설명합니다.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>템플릿 사용
이 섹션에서는 Azure 리소스 관리자 템플릿을 사용하여 서브넷 개인 끝점 정책을 사용하지 않도록 설정하는 방법에 대해 설명합니다.
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
- [Azure 개인 엔드포인트에](private-endpoint-overview.md) 대해 자세히 알아보기
 
