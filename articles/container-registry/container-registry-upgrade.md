---
title: Classic Azure Container Registry 업그레이드
description: 관리되지 않는 Classic 컨테이너 레지스트리를 업그레이드하여 Basic, Standard 및 Premium의 관리되는 컨테이너 레지스트리의 확장된 기능 집합을 활용할 수 있습니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a5099feee34eb5497b68987485412e29ad5d5365
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480337"
---
# <a name="upgrade-a-classic-container-registry"></a>Classic 컨테이너 레지스트리 업그레이드

ACR(Azure Container Registry)은 여러 서비스 계층(즉, [SKU](container-registry-skus.md))에서 사용 가능합니다. ACR의 초기 릴리스는 Basic, Standard 및 Premium SKU(통틀어 *관리되는* 레지스트리)에 내재된 몇 가지 기능이 없는 Classic 단일 SKU를 제공했습니다.

Classic SKU가 되며 2019 년 4 월 후 사용할 수 없게 됩니다. 이 문서에서는 향상된 기능 집합의 사용할 수 있도록, 관리되지 않는 Classic 레지스트리를 관리되는 SKU 중 하나로 마이그레이션하는 방법을 자세히 설명합니다.

## <a name="why-upgrade"></a>업그레이드가 필요한 이유

Classic 레지스트리 SKU 되 **더 이상 사용 되지**, 한 후 사용할 수 없게 됩니다 및 **2019 년 4 월**합니다. 2019 년 4 월 전에 모든 기존 Classic 레지스트리 업그레이드 해야 합니다. Classic 레지스트리 포털 관리 기능의 단계적으로 제거 됩니다. 새 Classic 레지스트리 만들기 2019 년 4 월 후 사용할 수 없습니다.

계획 된 중단 및 제한 된 기능의 관리 되지 않는 Classic 레지스트리로 인해 관리 되는 레지스트리 (Basic, Standard 또는 Premium) 모든 Classic 레지스트리 업그레이드 해야 합니다. 이러한 더 높은 수준의 SKU는 레지스트리를 Azure 기능에 더 깊이 통합합니다. 가격 및 각 서비스 계층의 기능에 대 한 자세한 내용은 참조 하세요. [Container Registry Sku](container-registry-skus.md)합니다.

클래식 레지스트리는 해당 레지스트리를 만들 때 Azure 구독에서 Azure가 자동으로 프로비전한 저장소 계정에 따라 다릅니다. 반면, Basic, Standard 및 Premium SKU는 사용자의 이미지 저장소를 투명하게 처리하여 Azure의 [고급 저장소 기능](container-registry-storage.md)을 활용합니다. 별도 저장소 계정이 고유한 구독에서 생성되지 않습니다.

관리되는 레지스트리 저장소는 다음과 같은 이점을 제공합니다.

* 컨테이너 이미지는 [암호화된 상태로 유지](container-registry-storage.md#encryption-at-rest)됩니다.
* 이미지를 사용 하 여 저장 됩니다 [지역 중복 저장소](container-registry-storage.md#geo-redundant-storage)보장, 다중 지역 복제 (Premium SKU에만 해당) 사용 하 여 이미지의 백업 합니다.
* 자유로운 [SKU 간 이동](container-registry-skus.md#changing-skus) 기능으로 더 높은 수준의 SKU를 선택하면 더 높은 처리량을 지원할 수 있습니다. 각 SKU를 사용하여 ACR은 요구 사항 증가에 따라 처리량 요구 사항을 충족할 수 있습니다.
* 레지스트리 및 해당 저장소에 대한 통합 보안 모델은 간소화된 권한 관리를 제공합니다. 또한 별도 저장소 계정에 대한 사용 권한을 관리할 필요 없이, 컨테이너 레지스트리의 사용 권한만 관리하면 됩니다.

ACR의 이미지 저장소에 대한 자세한 내용은 [Azure Container Registry의 컨테이너 이미지 저장소](container-registry-storage.md)를 참조하세요.

## <a name="migration-considerations"></a>마이그레이션 고려 사항

Classic 레지스트리를 관리 되는 레지스트리로 업그레이드 하는 경우 Azure는 Azure에서 관리 되는 저장소 계정에 모든 기존 컨테이너 이미지를 구독의 ACR 생성 저장소 계정에서 복사 해야 합니다. 이 프로세스는 레지스트리 크기에 따라 몇 분에서 몇 시간까지 걸릴 수 있습니다. 예측 용도로 분당 약 0.5 GiB 마이그레이션 시간을 예상 합니다.

변환 프로세스 동안 `docker push` 작업은 마지막 10% 마이그레이션 중 사용할 수 없습니다. `docker pull` 계속 정상적으로 작동 합니다.

변환 프로세스 동안 Classic 레지스트리를 지원하는 저장소 계정의 콘텐츠는 삭제하거나 수정하지 마세요. 이렇게 하면 컨테이너 이미지가 손상될 수 있습니다.

마이그레이션이 완료 되 면 원래 Classic 레지스트리를 백업 하는 구독에서 저장소 계정의 Azure Container Registry에서 더 이상 사용 됩니다. 마이그레이션에 성공했는지 확인한 후에는 비용을 최소화하기 위해 저장소 계정을 삭제하는 것이 좋습니다.

>[!IMPORTANT]
> Classic에서 관리되는 SKU 중 하나로 업그레이드하는 과정은 **단방향 프로세스**입니다. 일단 Classic 레지스트리를 Basic, Standard 또는 Premium으로 변환하면 Classic으로 되돌릴 수 없습니다. 그러나 레지스트리에 대해 충분한 용량이 있는 관리되는 SKU 간에는 자유롭게 이동할 수 있습니다.

## <a name="how-to-upgrade"></a>업그레이드하는 방법

관리되지 않는 Classic 레지스트리를 관리되는 SKU로 업그레이드하는 방법에는 여러 가지가 있습니다. 다음 섹션에서는 [Azure CLI][azure-cli] 및 [Azure Portal][azure-portal]을 사용하는 프로세스를 설명합니다.

## <a name="upgrade-in-azure-cli"></a>Azure CLI에서 업그레이드

Azure CLI에서 Classic 레지스트리를 업그레이드하려면 [az acr update][az-acr-update] 명령을 실행하고 레지스트리에 대한 새로운 SKU를 지정합니다. 다음 예제에서는 *myclassicregistry*라는 Classic 레지스트리가 Premium SKU로 업그레이드됩니다.

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

마이그레이션이 완료되면 다음과 비슷한 출력이 표시됩니다. 있음을 합니다 `sku` "premium" 및 `storageAccount` 는 `null`를 나타내는 Azure 이제이 레지스트리에 대 한 이미지 저장소를 관리 하는 합니다.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

최대 용량이 Classic 레지스트리 크기보다 작은 관리되는 레지스트리 SKU를 지정하면 다음과 유사한 오류 메시지가 수신됩니다.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

유사한 오류가 발생하면 [az acr update][az-acr-update] 명령을 실행하고, 이미지를 수용할 수 있는 다음 최상위 수준 SKU에 해당하는 제안된 SKU를 지정합니다.

## <a name="upgrade-in-azure-portal"></a>Azure Portal에서 업그레이드

Azure portal을 사용 하 여 Classic 레지스트리를 업그레이드 하는 경우 Azure 표준 또는 프리미엄 SKU에 SKU에 따라 이미지를 수용할 수 자동으로 선택 합니다. 예를 들어, 레지스트리에 이미지에서 100 개 미만의 GiB 있으면 Azure 자동으로 선택 하 고 Classic 레지스트리를 표준 변환 (최대 100gib)).

Azure Portal을 사용하여 Classic 레지스트리를 업그레이드하려면 컨테이너 레지스트리의 **개요**로 이동한 후 **관리되는 레지스트리로 업그레이드**를 선택합니다.

![Azure Portal UI의 Classic 레지스트리 업그레이드 단추][update-classic-01-upgrade]

**확인**을 선택하여 관리되는 레지스트리로 업그레이드할 것임을 확인합니다.

마이그레이션하는 동안 포털에서는 레지스트리의 **프로비전 상태**가 *업데이트 중*임을 나타냅니다. 앞서 언급 했 듯이 `docker push` 작업은 마지막 10% 마이그레이션 중 사용할 수 없습니다. 않는 삭제 또는 마이그레이션 이렇게-진행 중인 동안는 Classic 레지스트리에서 사용 되는 저장소 계정을 업데이트 하지 이미지가 손상 될 수 있습니다.

![Azure Portal UI의 Classic 레지스트리 업그레이드 진행 상태][update-classic-03-updating]

마이그레이션이 완료 되 면 합니다 **프로 비전 상태** 나타냅니다 *Succeeded*, 레지스트리를 사용 하 여 일반 작업을 다시 시작할 수 있습니다.

![Azure Portal UI의 Classic 레지스트리 업그레이드 완료 상태][update-classic-04-updated]

## <a name="next-steps"></a>다음 단계

Classic 레지스트리를 관리 되는 레지스트리로 업그레이드 한 후 Azure는 더 이상 원래 Classic 레지스트리를 백업 하는 저장소 계정을 사용 합니다. 비용을 줄이기 위해 이전 컨테이너 이미지를 포함하는 저장소 계정 또는 계정 내의 Blob 컨테이너를 삭제하는 것이 좋습니다.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com