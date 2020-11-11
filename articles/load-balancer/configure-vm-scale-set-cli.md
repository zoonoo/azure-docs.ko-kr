---
title: 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합 구성-Azure CLI
description: Azure CLI를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성 하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518212"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Azure CLI를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합 구성

이 문서에서는 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성 하는 방법을 알아봅니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소 

- 가상 머신 확장 집합이 배포 되는 구독에 기존 표준 sku 부하 분산 장치가 필요 합니다.

- 가상 머신 확장 집합에 대 한 Azure Virtual Network 필요 합니다.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>기존 부하 분산 장치를 사용 하 여 가상 머신 확장 집합 배포

괄호 안의 값을 구성에 있는 리소스의 이름으로 바꿉니다.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

아래 예제에서는를 사용 하 여 가상 머신 확장 집합을 배포 합니다.

- **Myvmss** 라는 가상 머신 확장 집합
- **Myloadbalancer** 라는 Azure Load Balancer
- **MyBackendPool** 라는 부하 분산 장치 백 엔드 풀
- **Myvnet** 이라는 Azure Virtual Network
- **Mysubnet** 이라는 서브넷
- **Myresourcegroup** 이라는 리소스 그룹
- 가상 머신 확장 집합에 대 한 Ubuntu 서버 이미지

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> 확장 집합을 만든 후에는 부하 분산 장치의 상태 프로브에서 사용 하는 부하 분산 규칙에 대해 백 엔드 포트를 수정할 수 없습니다. 포트를 변경 하려면 Azure 가상 머신 확장 집합을 업데이트 하 고, 포트를 업데이트 한 후 상태 프로브를 다시 구성 하 여 상태 프로브를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 배포 했습니다.  가상 머신 확장 집합 및 부하 분산 장치에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Load Balancer란?](load-balancer-overview.md)
- [가상 머신 크기 집합이란?](../virtual-machine-scale-sets/overview.md)
                                