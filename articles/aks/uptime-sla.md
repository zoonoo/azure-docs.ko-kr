---
title: 가동 시간 SLA를 사용 하는 AKS (Azure Kubernetes Service) 고가용성
description: AKS (Azure Kubernetes Service) API 서버에 대 한 선택적 고가용성 작동 시간 SLA 제품에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125726"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>AKS (Azure Kubernetes Service) 작동 시간 SLA

작동 시간 SLA는 클러스터에 대 한 재정적 지원 높은 SLA를 사용 하도록 설정 하는 선택적 기능입니다. 작동 시간 SLA는 가용성 영역을 사용 하지 않는 클러스터의 가용성 [영역][availability-zones] 및 99.9% 가용성을 사용 하는 클러스터에 대 한 Kubernetes API 서버 끝점의 99.95% 가용성을 보장 합니다. AKS는 업데이트 및 장애 도메인에서 마스터 노드 복제본을 사용 하 여 SLA 요구 사항을 충족 하는지 확인 합니다.

규정 준수 이유에 대 한 SLA를 필요로 하는 고객이 나 SLA를 고객에 게 확장 하려면이 기능을 설정 해야 합니다. SLA 옵션을 사용 하 여 고가용성이 필요한 중요 워크 로드가 있는 고객은이 기능을 사용 하도록 설정할 수 있습니다. 가용성 영역에서 기능을 사용 하도록 설정 하 여 Kubernetes API 서버의 가용성을 높입니다.  

고객은 SLO (서비스 수준 목표)가 99.5% 인 무제한 무료 클러스터를 만들 수 있습니다.

> [!Important]
> 송신 잠금이 있는 클러스터의 경우 가동 시간 SLA에 대 한 적절 한 포트를 열기 위해 [송신 트래픽 제한](limit-egress-traffic.md) 을 참조 하세요.

## <a name="sla-terms-and-conditions"></a>SLA 사용 약관

작동 시간 SLA는 유료 기능이 며 클러스터 별로 사용 하도록 설정 됩니다. 작동 시간 SLA 가격은 클러스터의 크기에 따라 결정 되는 것이 아니라 클러스터의 수에 따라 결정 됩니다. 자세한 내용은 [작동 시간 SLA 가격 정보](https://azure.microsoft.com/pricing/details/kubernetes-service/) 를 볼 수 있습니다.

## <a name="region-availability"></a>지역 가용성

작동 시간 SLA는 다음 지역에서 제공 됩니다.

* 오스트레일리아 동부
* 캐나다 중부
* 미국 동부
* 미국 동부 2
* 미국 중남부
* 동남아시아
* 미국 서부 2

## <a name="before-you-begin"></a>시작하기 전에

* Azure CLI 버전 합니다 이상

## <a name="creating-a-cluster-with-uptime-sla"></a>작동 시간 SLA를 사용 하 여 클러스터 만들기

작동 시간 SLA를 사용 하 여 새 클러스터를 만들려면 Azure CLI를 사용 합니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
[az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드가 있는 *myAKSCluster*라는 클러스터를 만듭니다. 컨테이너용 Azure Monitor는 *--enable-addons monitoring* 매개 변수를 사용하여 설정할 수도 있습니다.  이 작업을 완료하는 데 몇 분이 걸립니다.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
몇 분 후 명령이 완료되면 클러스터에 대한 JSON 형식 정보가 반환됩니다. 다음 JSON 코드 조각에서는 SKU에 대 한 유료 계층을 보여 줍니다 .이는 클러스터에 작동 시간 SLA가 설정 되어 있음을 나타냅니다.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>제한 사항

* 현재는 작동 시간 SLA를 기존 클러스터에 추가할 수 없습니다.
* 현재 AKS 클러스터에서 작동 시간 SLA를 제거할 수 있는 방법은 없습니다.  

## <a name="next-steps"></a>다음 단계

[가용성 영역][availability-zones] 를 사용 하 여 AKS 클러스터 워크 로드를 통해 고가용성을 높일 수 있습니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
