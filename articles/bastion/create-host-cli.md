---
title: Azure CLI를 사용하여 요새 호스트 만들기 | Azure 요새
description: 이 문서에서는 배스온 호스트를 만들고 삭제하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: cherylmc
ms.openlocfilehash: 9e216bf2bb8e2e605723256f0511b9bdb63083b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337572"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Azure CLI를 사용하여 Azure 요새 호스트 만들기

이 문서에서는 Azure CLI를 사용하여 Azure 요새 호스트를 만드는 방법을 보여 주며, Azure Bastion 호스트를 만드는 방법을 보여 주시습니다. 가상 네트워크에서 Azure Bastion 서비스를 프로비전하면 동일한 가상 네트워크의 모든 VM에서 원활한 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

선택적으로 Azure [포털을](bastion-create-host-portal.md)사용하거나 [Azure PowerShell](bastion-create-host-powershell.md)을 사용하여 Azure 요새 호스트를 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>요새 호스트 만들기

이 섹션에서는 Azure CLI를 사용하여 새 Azure 요새 리소스를 만드는 데 도움이 됩니다.

1. 가상 네트워크와 Azure 요새 서브넷을 만듭니다. 이름 값 **AzureBastionSubnet을**사용 하 여 Azure 요새 서브넷을 만들어야 합니다. 이 값을 통해 Azure는 Bastion 리소스를 배포할 서브넷을 알 수 있습니다. 이는 게이트웨이 서브넷과 다릅니다. 최소 /27 이상의 서브넷(/27, /26 등)의 서브넷을 사용해야 합니다. 경로 테이블이나 위임 없이 **AzureBastionSubnet을** 만듭니다. **AzureBastionSubnet에서**네트워크 보안 그룹을 사용하는 경우 [NSG사용 문서](bastion-nsg.md) 참조.

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Azure 요새에 대한 공용 IP 주소를 만듭니다. 공용 IP는 RDP/SSH가 액세스할 수 있는 바스티온 리소스(포트 443을 통해)를 처리하는 공용 IP 주소입니다. 공용 IP 주소는 만드는 Bastion 리소스와 동일한 지역에 있어야 합니다.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. 가상 네트워크의 AzureBastionSubnet에서 새 Azure 요새 리소스를 만듭니다. Bastion 리소스를 만들고 배포하는 데 약 5분이 걸립니다.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [배스션 FAQ를](bastion-faq.md) 참조하십시오.

* Azure 요새 서브넷이 있는 네트워크 보안 그룹을 사용하려면 [NSG사용 작업을](bastion-nsg.md)참조하십시오.
