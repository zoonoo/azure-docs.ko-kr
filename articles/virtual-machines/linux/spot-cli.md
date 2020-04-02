---
title: CLI를 사용하여 Azure 스팟 VM 배포
description: CLI를 사용하여 Azure 스팟 VM을 배포하여 비용을 절감하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5b6a07bfbcf56f3ca78fa4991e7741a3d44c25b9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544345"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Azure CLI를 사용하여 스팟 VM 배포

[Azure Spot VM을](spot-vms.md) 사용하면 사용하지 않는 용량을 크게 절약할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스팟 VM을 제거합니다. 따라서 스팟 VM은 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같은 중단을 처리할 수 있는 워크로드에 적합합니다.

스팟 VM의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows용](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)VM 가격을 참조하십시오. 

VM에 대해 시간당 지불할 최대 가격을 설정할 수 있습니다. 스팟 VM의 최대 가격은 소수점 이하 5자리까지 사용하여 미국 달러(USD)로 설정할 수 있습니다. 예를 들어 값은 `0.98765`시간당 0.98765 USD의 최대 가격입니다. 최대 가격을 `-1`설정하면 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 현재 스팟 가격 또는 표준 VM의 가격이 될 것이며, 용량과 할당량을 사용할 수 있는 한 더 적습니다. 최대 가격 설정에 대한 자세한 내용은 [스팟 VM - 가격 책정을](spot-vms.md#pricing)참조하십시오.

Azure CLI를 사용하여 스팟을 사용하여 VM을 만드는 프로세스는 [빠른 시작 문서에서](/azure/virtual-machines/linux/quick-create-cli)자세히 설명한 것과 동일합니다. 그냥 '--우선 순위 스팟'매개 변수를 `-1`추가하고 최대 가격 또는 .


## <a name="install-azure-cli"></a>Azure CLI 설치

스팟 VM을 만들려면 Azure CLI 버전 2.0.74 이상을 실행해야 합니다. 버전을 찾으려면 **az --version**을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

[az login](/cli/azure/reference-index#az-login)을 사용하여 Azure에 로그인합니다.

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>스팟 VM 만들기

이 예제에서는 가격에 따라 제거되지 않는 Linux 스팟 VM을 배포하는 방법을 보여 주며 있습니다. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

VM을 만든 후 리소스 그룹의 모든 VM에 대한 최대 청구 가격을 확인하도록 쿼리할 수 있습니다.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**다음 단계**

[Azure PowerShell](../windows/spot-powershell.md) 또는 [템플릿을](spot-template.md)사용하여 스팟 VM을 만들 수도 있습니다.

오류가 발생하면 [오류 코드를](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)참조하십시오.
