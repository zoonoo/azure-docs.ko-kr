---
title: Azure CLI를 사용 하 여 요새 호스트 만들기 Azure 방호
description: 이 문서에서는 요새 호스트를 만들고 삭제 하는 방법에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: e4f0a3ea85fa479826fe9e666df2b18f8dba7ce3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079125"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure 방호 호스트 만들기

이 문서에서는 Azure CLI를 사용 하 여 Azure 방호 호스트를 만드는 방법을 보여 줍니다. 가상 네트워크에서 Azure 방호 서비스를 프로 비전 한 후에는 동일한 가상 네트워크의 모든 Vm에서 원활한 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

필요에 따라 [Azure Portal](./tutorial-create-host-portal.md)또는 [Azure PowerShell](bastion-create-host-powershell.md)를 사용 하 여 Azure 방호 호스트를 만들 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [무료 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion 호스트 만들기

이 섹션은 Azure CLI를 사용 하 여 새 Azure 방호 리소스를 만드는 데 도움이 됩니다.

> [!NOTE]
> 예제에 표시 된 것 처럼 `--location` 모든 명령에 대해 매개 변수를 사용 하 여 `--resource-group` 리소스를 함께 배포 하는지 확인 합니다.

1. 가상 네트워크 및 Azure 방호 서브넷을 만듭니다. 이름 값 **AzureBastionSubnet**을 사용 하 여 Azure 방호 서브넷을 만들어야 합니다. 이 값을 통해 Azure에서 Bastion 리소스를 배포할 서브넷을 인식할 수 있습니다. 이는 게이트웨이 서브넷과는 다릅니다. 최소/27 이상의 서브넷 (/27,/26 등)의 서브넷을 사용 해야 합니다. 경로 테이블 또는 위임 없이 **AzureBastionSubnet** 를 만듭니다. **AzureBastionSubnet**의 네트워크 보안 그룹을 사용 하는 경우 [nsgs 작업](bastion-nsg.md) 문서를 참조 하세요.

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Azure 방호에 대 한 공용 IP 주소를 만듭니다. 공용 IP는 RDP/SSH를 액세스할 수 있는 방호 리소스 (443 포트)에 대 한 공용 IP 주소입니다. 공용 IP 주소는 만들려는 Bastion 리소스와 동일한 지역에 있어야 합니다.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. 가상 네트워크의 AzureBastionSubnet에서 새 Azure 방호 리소스를 만드세요. 요새 리소스를 만들고 배포 하는 데 약 5 분이 걸립니다.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>다음 단계

* 추가 정보는 [요새 FAQ](bastion-faq.md) 를 참조 하세요.
* Azure Bastion 서브넷에서 네트워크 보안 그룹을 사용하려면 [NSG 사용](bastion-nsg.md)을 참조하세요.
