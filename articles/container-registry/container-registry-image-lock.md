---
title: 이미지 잠그기
description: Azure 컨테이너 레지스트리에서 삭제하거나 덮어쓸 수 없도록 컨테이너 이미지 또는 리포지토리에 대한 특성을 설정합니다.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659699"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Azure 컨테이너 레지스트리에서 컨테이너 이미지 잠금

Azure 컨테이너 레지스트리에서 이미지 버전 또는 리포지토리를 잠그면 삭제하거나 업데이트할 수 없습니다. 이미지 또는 리포지토리를 잠그려면 Azure CLI 명령 [az acr 리포지토리 업데이트를][az-acr-repository-update]사용하여 특성을 업데이트합니다. 

이 문서에서는 Azure Cloud Shell 또는 로컬(버전 2.0.55 이상 권장)에서 Azure CLI를 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하십시오.

> [!IMPORTANT]
> 이 문서는 Azure 포털의 **설정 > 잠금** 또는 `az lock` Azure CLI의 명령을 사용하여 전체 레지스트리를 잠그는 데 적용되지 않습니다. 레지스트리 리소스를 잠그면 리포지토리에서 데이터를 만들거나 업데이트하거나 삭제할 수 없습니다. 레지스트리를 잠그면 복제를 추가하거나 삭제하거나 레지스트리 자체를 삭제하는 등의 관리 작업에만 영향을 줍니다. [예기치 않은 변경을 방지하기 위해 Lock 리소스에](../azure-resource-manager/management/lock-resources.md)대한 자세한 내용입니다.

## <a name="scenarios"></a>시나리오

기본적으로 Azure 컨테이너 레지스트리의 태그가 지정된 이미지는 *변경할 수*있으므로 적절한 권한을 사용하면 동일한 태그가 있는 이미지를 반복적으로 업데이트하고 레지스트리에 푸시할 수 있습니다. 필요에 따라 컨테이너 이미지를 [삭제할](container-registry-delete.md) 수도 있습니다. 이 동작은 이미지를 개발하고 레지스트리의 크기를 유지해야 할 때 유용합니다.

그러나 컨테이너 이미지를 프로덕션에 배포할 때 *변경할 수 없는* 컨테이너 이미지가 필요할 수 있습니다. 변경할 수 없는 이미지는 실수로 삭제하거나 덮어쓸 수 없는 이미지입니다.

레지스트리에서 [이미지에 태그를 지정하고 버전을 지정하는](container-registry-image-tag-version.md) 전략에 대한 컨테이너 이미지에 태그 지정 및 버전 지정에 대한 권장 사항을 참조하세요.

az [acr 리포지토리 업데이트][az-acr-repository-update] 명령을 사용하여 리포지토리 속성을 설정하여 다음을 수행할 수 있습니다.

* 이미지 버전 또는 전체 리포지토리 잠금

* 이미지 버전 또는 리포지토리를 삭제하지 않도록 보호하지만 업데이트 허용

* 이미지 버전 또는 전체 리포지토리에서 읽기(끌어오기) 작업 방지

예제는 다음 섹션을 참조하십시오. 

## <a name="lock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 

### <a name="show-the-current-repository-attributes"></a>현재 리포지토리 특성 표시
리포지토리의 현재 특성을 보려면 다음 [az acr 저장소 표시][az-acr-repository-show] 명령을 실행합니다.

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>현재 이미지 속성 표시
태그의 현재 특성을 보려면 다음 [az acr 저장소 표시][az-acr-repository-show] 명령을 실행합니다.

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>태그로 이미지 잠금

*myrepo/myimage:tag* 이미지를 내 *레지스트리에서*잠그려면 다음 [az acr 저장소 업데이트][az-acr-repository-update] 명령을 실행합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>매니페스트 다이제스트로 이미지 잠금

매니페스트 다이제스트(SHA-256 해시로 표시됨)로 `sha256:...`식별된 *myrepo/myimage* 이미지를 잠그려면 다음 명령을 실행합니다. 하나 이상의 이미지 태그와 연결된 매니페스트 다이제스트를 찾으려면 [az acr 저장소 표시 매니페스트 명령을 실행합니다.][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>리포지토리 잠금

*myrepo/myimage* 리포지토리와 그 안에 있는 모든 이미지를 잠그려면 다음 명령을 실행합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>삭제로부터 이미지 또는 리포지토리 보호

### <a name="protect-an-image-from-deletion"></a>삭제로부터 이미지 보호

*myrepo/myimage:tag* 이미지를 업데이트하지만 삭제하지 않도록 하려면 다음 명령을 실행합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>리포지토리를 삭제로부터 보호

다음 명령은 *myrepo/myimage* 리포지토리를 삭제할 수 없도록 설정합니다. 개별 이미지는 계속 업데이트하거나 삭제할 수 있습니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>이미지 또는 리포지토리에서 읽기 작업 방지

*myrepo/myimage:tag* 이미지에서 읽기(끌어오기) 작업을 방지하려면 다음 명령을 실행합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

*myrepo/myimage* 리포지토리의 모든 이미지에서 읽기 작업을 방지하려면 다음 명령을 실행합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 해제

*myrepo/myimage:tag* 이미지의 기본 동작을 복원하여 삭제하고 업데이트할 수 있도록 하려면 다음 명령을 실행합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

*myrepo/myimage* 리포지토리및 모든 이미지의 기본 동작을 복원하여 삭제하고 업데이트할 수 있도록 하려면 다음 명령을 실행합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 [az acr 리포지토리 업데이트][az-acr-repository-update] 명령을 사용하여 리포지토리에서 이미지 버전이 삭제또는 업데이트되지 않도록 하는 방법을 배웠습니다. 추가 특성을 설정하려면 [az acr 저장소 업데이트][az-acr-repository-update] 명령 참조를 참조하십시오.

이미지 버전 또는 리포지토리에 대해 설정된 속성을 보려면 [az acr 저장소 표시][az-acr-repository-show] 명령을 사용합니다.

삭제 작업에 대한 자세한 내용은 [Azure 컨테이너 레지스트리의 컨테이너 이미지 삭제를][container-registry-delete]참조하십시오.

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

