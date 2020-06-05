---
title: 태그 없는 매니페스트를 유지하는 정책
description: 정의된 기간 후 태그 없는 매니페스트를 자동으로 삭제하기 위해 Azure 컨테이너 레지스트리에서 보존 정책을 사용하도록 설정하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683449"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>태그 없는 매니페스트에 대한 보존 정책 설정

Azure Container Registry는 연결된 태그가 없는 저장된 이미지 매니페스트에 대한 *보존 정책*을 설정하는 옵션을 제공합니다(*태그 없는 매니페스트*). 보존 정책을 사용하도록 설정하면 설정된 일 수 후에 레지스트리의 태그 없는 매니페스트가 자동으로 삭제됩니다. 이 기능을 사용하면 레지스트리가 필요하지 않은 아티팩트를 채우지 않고 스토리지 비용을 절감할 수 있습니다. 태그 없는 매니페스트의 `delete-enabled` 특성이 `false`로 설정된 경우에는 매니페스트를 삭제할 수 없으며 보존 정책이 적용되지 않습니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 문서의 명령 예제를 실행할 수 있습니다. 로컬로 사용하려는 경우 2.0.74 이상 버전이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

보존 정책은 **프리미엄** 컨테이너 레지스트리의 기능입니다. 레지스트리 서비스 계층에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

> [!WARNING]
> 보존 정책을 주의하여 설정하십시오. 삭제된 이미지 데이터는 복구할 수 없습니다. 시스템에서 매니페스트 다이제스트(이미지 이름 아님)에 의해 이미지를 끌어오는 경우 태그 없는 매니페스트에 대해 보존 정책을 설정하면 안 됩니다. 태그가 지정되지 않은 이미지를 삭제하면 해당 시스템은 레지스트리에서 이미지를 끌어올 수 없게 됩니다. 매니페스트에 따라 끌어오는 대신, [권장 모범 사례](container-registry-image-tag-version.md)에 해당하는 *고유 태그 지정* 체계를 채택하는 것이 좋습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 태그 없는 매니페스트에 대한 보존 정책만 설정할 수 있습니다.
* 보존 정책은 현재 정책이 설정된 *후* 태그 없는 매니페스트에만 적용됩니다. 레지스트리의 태그 없는 기존 매니페스트는 정책이 적용되지 않습니다. 태그 없는 기존 매니페스트를 삭제하려면 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)의 예제를 참조하세요.

## <a name="about-the-retention-policy"></a>보존 정책 정보

Azure Container Registry는 레지스트리의 매니페스트에 대한 참조 횟수를 수행합니다. 매니페스트가 태그되지 않은 경우 보존 정책을 확인합니다. 보존 정책을 사용하는 경우 정책에 설정된 일 수에 따라 특정 날짜를 사용하여 매니페스트 삭제 작업이 큐에 대기됩니다.

별도의 큐 관리 작업은 메시지를 지속적으로 처리하고 필요에 따라 크기를 조정합니다. 예를 들어 30일의 보존 정책을 사용하는 레지스트리에 1시간 간격으로 두 개의 매니페스트를 태그 해제했다고 가정합니다. 두 메시지는 큐에 대기됩니다. 그런 다음, 30일 후에 약 1시간마다 정책이 여전이 유효하다고 가정하여 메시지를 큐에서 검색하고 처리합니다.

## <a name="set-a-retention-policy---cli"></a>보존 정책 설정 - CLI

다음 예제에서는 Azure CLI를 사용하여 레지스트리의 태그 없는 매니페스트에 대한 보존 정책을 설정하는 방법을 보여 줍니다.

### <a name="enable-a-retention-policy"></a>보존 정책 사용

기본적으로 컨테이너 레지스트리에는 보존 정책이 설정되지 않습니다. 보존 정책을 설정하거나 업데이트하려면 Azure CLI에서 [az acr config retention update][az-acr-config-retention-update] 명령을 실행합니다. 태그 없는 매니페스트를 유지하려면 0에서 365 사이의 일 수를 지정할 수 있습니다. 일 수를 지정하지 않으면 명령은 7일의 기본값으로 설정합니다. 보존 기간 후에 레지스트리의 태그 없는 모든 매니페스트가 자동으로 삭제됩니다.

다음 예제에서는 레지스트리 *myregistry*에서 태그 없는 매니페스트에 대해 30일의 보존 정책을 설정합니다.

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

다음 예제에서는 태그를 해제하는 즉시 레지스트리의 매니페스트를 삭제하도록 정책을 설정합니다. 보존 기간을 0일로 설정하여 이 정책을 만듭니다. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>보존 정책 유효성 검사

보존 기간이 0일인 이전 정책을 사용하도록 설정하면 태그 없는 매니페스트가 삭제되었는지 빠르게 확인할 수 있습니다.

1. 테스트 이미지 `hello-world:latest` 이미지를 레지스트리에 푸시하거나 원하는 다른 테스트 이미지를 대체합니다.
1. 예를 들어 [az acr repository untag][az-acr-repository-untag] 명령을 사용하여 `hello-world:latest` 이미지를 태그 해제합니다. 태그 없는 매니페스트는 레지스트리에 남아 있습니다.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. 몇 초 안에 태그 없는 매니페스트가 삭제됩니다. 예를 들어 [az acr repository show-manifests][az-acr-repository-show-manifests] 명령을 사용하여 리포지토리에 매니페스트를 나열하여 삭제를 확인할 수 있습니다. 테스트 이미지가 리포지토리에서 유일했던 경우 리포지토리 자체가 삭제됩니다.

### <a name="disable-a-retention-policy"></a>보존 정책 사용 안 함

레지스트리에 설정된 보존 정책을 확인하려면 [az acr config retention show][az-acr-config-retention-show] 명령을 실행합니다.

```azurecli
az acr config retention show --registry myregistry
```

레지스트리에서 보존 정책을 사용하지 않도록 설정하려면 [az acr config retention update][az-acr-config-retention-update] 명령을 실행하고 `--status disabled`를 설정합니다.

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>보존 정책 설정 - 포털

[Azure Portal](https://portal.azure.com)에서 레지스트리의 보존 정책을 설정할 수도 있습니다. 다음 예제에서는 포털을 사용하여 레지스트리의 태그 없는 매니페스트에 대한 보존 정책을 설정하는 방법을 보여 줍니다.

### <a name="enable-a-retention-policy"></a>보존 정책 사용

1. Azure Container Registry로 이동합니다. **정책**에서 **보존**(미리 보기)을 선택합니다.
1. **상태**에서 **사용**을 선택합니다.
1. 태그 없는 매니페스트를 유지하려면 0에서 365 사이의 일 수를 선택합니다. **저장**을 선택합니다.

![Azure Portal에서 보존 정책 사용](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>보존 정책 사용 안 함

1. Azure Container Registry로 이동합니다. **정책**에서 **보존**(미리 보기)을 선택합니다.
1. **상태**에서 **사용 안 함**을 선택합니다. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* Azure Container Registry에서 [이미지 및 리포지토리](container-registry-delete.md)를 삭제하는 옵션에 대한 자세한 정보

* 레지스트리에서 선택한 이미지 및 매니페스트를 [자동으로 제거](container-registry-auto-purge.md)하는 방법에 대한 자세한 정보

* 레지스트리에 [이미지 및 매니페스트 잠금](container-registry-image-lock.md) 옵션에 대한 자세한 정보

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
