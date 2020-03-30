---
title: 기존 Azure 로드 밸런서 - Azure CLI를 사용 하 고 가상 시스템 규모 집합 구성
description: 기존 Azure 로드 밸런서를 사용하여 가상 시스템 확장 집합을 구성하는 방법에 대해 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349745"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Azure CLI를 사용하여 기존 Azure 로드 밸런서를 사용하여 가상 시스템 확장 집합 구성

이 문서에서는 기존 Azure 로드 밸런서를 사용하여 가상 시스템 확장 집합을 구성하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독
- 가상 시스템 크기 집합이 배포되는 구독의 기존 표준 sku 로드 밸런서입니다.
- 가상 시스템 규모 집합에 대한 Azure 가상 네트워크입니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI를 로컬로 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.28 이상에 설치되어 있어야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="sign-in-to-azure-cli"></a>Azure CLI에 로그인

Azure에 로그인합니다.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>기존 로드 밸워서가 있는 가상 시스템 스케일 세트 배포

괄호 속의 값을 구성의 리소스 이름으로 바꿉니다.

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

아래 예제는 다음과 같은 가상 시스템 규모 집합을 배포합니다.

- **myVMSS라는** 가상 시스템 스케일 세트
- **myLoadBalancer라는** 이름의 Azure 로드 밸러블러
- **myBackendPool이라는** 로드 밸러서 백엔드 풀
- **myVnet이라는** 이름의 Azure 가상 네트워크
- **mySubnet이라는** 이름의 서브넷
- **myResourceGroup이라는** 리소스 그룹
- 가상 머신 스케일 세트에 대한 우분투 서버 이미지

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
> 축척 세트를 만든 후에는 로드 밸런서의 상태 프로브에서 사용하는 부하 분산 규칙에 대해 백 엔드 포트를 수정할 수 없습니다. 포트를 변경하려면 Azure 가상 시스템 크기 집합을 업데이트하여 상태 프로브를 제거하고 포트를 업데이트한 다음 상태 프로브를 다시 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 Azure 로드 밸런서가 있는 가상 시스템 규모 집합을 배포했습니다.  가상 시스템 스케일 세트 및 로드 밸러블러에 대한 자세한 내용은 다음을 참조하십시오.

- [Azure Load Balancer란?](load-balancer-overview.md)
- [가상 머신 크기 집합이란?](../virtual-machine-scale-sets/overview.md)
                                