---
title: CLI를 사용 하 여 Azure 스팟 Virtual Machines 배포
description: CLI를 사용 하 여 비용을 절감 하기 위해 Azure 스폿 Virtual Machines를 배포 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: abb29c0826e38af0bbbc1b59e41234acdaaca0f9
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100554775"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure 스팟 Virtual Machines 배포

[Azure 지점 Virtual Machines](../spot-vms.md) 를 사용 하면 비용을 크게 절약할 수 있는 사용 되지 않는 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 경우 언제 든 지 azure 인프라가 azure point Virtual Machines를 제거 합니다. 따라서 Azure 스팟 Virtual Machines는 일괄 처리 작업, 개발/테스트 환경, 큰 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

Azure 스폿 Virtual Machines의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 VM 가격 책정을 참조 하세요. 

VM에 대해 시간당 요금을 지불할 최대 가격을 설정 하는 옵션이 있습니다. Azure 스폿 가상 머신의 최대 가격은 미국 달러 (USD)로 설정 하 여 최대 5 개의 소수 자릿수를 사용할 수 있습니다. 예를 들어 값은 `0.98765` 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을로 설정 하는 경우 `-1` 가격에 따라 VM이 제거 되지 않습니다. VM의 가격은 Azure 스폿 Virtual Machine의 현재 가격 이거나, 사용 가능한 용량과 할당량을 초과 하는 표준 VM에 대 한 가격입니다. 최대 가격을 설정 하는 방법에 대 한 자세한 내용은 [Azure 스폿 Virtual Machines-가격 책정](../spot-vms.md#pricing)을 참조 하세요.

Azure CLI를 사용 하 여 Azure 스팟 가상 머신을 만드는 프로세스는 [빠른 시작 문서](./quick-create-cli.md)에 자세히 설명 된 것과 같습니다. '--Priority 스폿 ' 매개 변수를 추가 하 고를 `--eviction-policy` 할당 취소 (기본값) 또는로 설정 하 `Delete` 고 최대 가격 또는을 제공 합니다 `-1` . 


## <a name="install-azure-cli"></a>Azure CLI 설치

Azure 스폿 Virtual Machines을 만들려면 Azure CLI 버전 2.0.74 이상을 실행 해야 합니다. 버전을 찾으려면 **az--version** 을 실행 합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 

[az login](/cli/azure/reference-index#az-login)을 사용하여 Azure에 로그인합니다.

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Azure 스폿 Virtual Machine 만들기

이 예제에서는 가격에 따라 제거 되지 않을 Linux Azure 스폿 가상 머신을 배포 하는 방법을 보여 줍니다. 제거 정책은 나중에 다시 시작할 수 있도록 VM의 할당을 취소 하도록 설정 됩니다. Vm을 제거할 때 VM 및 기본 디스크를 삭제 하려면 `--eviction-policy` 를로 설정 `Delete` 합니다.

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



VM을 만든 후에 쿼리 하 여 리소스 그룹의 모든 Vm에 대 한 최대 청구 가격을 확인할 수 있습니다.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>제거 시뮬레이션

Azure 스폿 가상 머신의 [제거를 시뮬레이션](/rest/api/compute/virtualmachines/simulateeviction) 하 여 응용 프로그램이 갑자기 제거 되는 것을 얼마나 잘 repond 테스트할 수 있습니다. 

다음을 사용자의 정보로 바꿉니다. 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**다음 단계**

[Azure PowerShell](../windows/spot-powershell.md), [포털](../spot-portal.md)또는 [템플릿을](spot-template.md)사용 하 여 Azure 스팟 가상 머신을 만들 수도 있습니다.

Azure 스폿 Virtual Machine에 대 한 자세한 내용은 [azure 소매 가격 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 를 사용 하 여 현재 가격 정보를 쿼리 하세요. 및에는 `meterName` `skuName` 둘 다 포함 됩니다 `Spot` .

오류가 발생 하는 경우 [오류 코드](../error-codes-spot.md)를 참조 하세요.