---
title: 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합 구성-Azure CLI
description: Azure CLI를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성 하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 990380d553cc12d1a87b2e1c7ca9b09864801294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333996"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Azure CLI를 사용 하 여 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합 구성

이 문서에서는 기존 Azure Load Balancer를 사용 하 여 가상 머신 확장 집합을 구성 하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독
- 가상 머신 확장 집합이 배포 될 구독의 기존 표준 sku 부하 분산 장치입니다.
- 가상 머신 확장 집합에 대 한 Azure Virtual Network입니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI를 로컬로 사용 하도록 선택 하는 경우이 문서에서는 Azure CLI 버전 2.0.28 이상 버전이 설치 되어 있어야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sign-in-to-azure-cli"></a>Azure CLI에 로그인

Azure에 로그인 합니다.

```azurecli-interactive
az login
```

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
                                