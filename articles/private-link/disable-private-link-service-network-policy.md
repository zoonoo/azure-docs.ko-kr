---
title: 'Azure 개인 링크 서비스 소스 IP 주소에 대 한 네트워크 정책을 사용 하지 않도록 설정 '
description: Azure 개인 링크에 대한 네트워크 정책을 사용하지 않도록 설정하는 방법 알아보기
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453001"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>개인 링크 서비스 소스 IP에 대한 네트워크 정책 비활성화

개인 링크 서비스의 소스 IP 주소를 선택하려면 서브넷에서 `privateLinkServiceNetworkPolicies` 명시적 사용 안 함 설정이 필요합니다. 이 설정은 개인 링크 서비스의 소스 IP로 선택한 특정 개인 IP 주소에만 적용됩니다. 서브넷의 다른 리소스의 경우 NSG(네트워크 보안 그룹) 보안 규칙 정의에 따라 액세스가 제어됩니다. 
 
Azure 클라이언트(PowerShell, CLI 또는 템플릿)를 사용하는 경우 이 속성을 변경하려면 추가 단계가 필요합니다. Azure 포털에서 클라우드 셸을 사용 하 여 정책을 사용 하지 않도록 설정할 수 있습니다 또는 Azure PowerShell, Azure CLI의 로컬 설치 또는 Azure 리소스 관리자 템플릿을 사용 하 여.  
 
*myResourceGroup이라는*리소스 그룹에서 호스팅되는 *기본* 서브넷이 있는 *myVirtualNetwork라는* 가상 네트워크에 대한 개인 링크 서비스 네트워크 정책을 비활성화하려면 아래 단계를 따릅니다. 

## <a name="using-azure-powershell"></a>Azure PowerShell 사용
이 섹션에서는 Azure PowerShell을 사용하여 서브넷 개인 엔드포인트 정책을 사용하지 않도록 설정하는 방법에 대해 설명합니다.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI를 사용하여 서브넷 개인 끝점 정책을 사용하지 않도록 설정하는 방법에 대해 설명합니다.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>다음 단계
- [Azure 개인 엔드포인트에](private-endpoint-overview.md) 대해 자세히 알아보기
 
