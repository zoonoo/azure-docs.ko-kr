---
title: Azure Container Registry에서 이미지 잠그기
description: 컨테이너 이미지 또는 리포지토리에 대 한 특성을 설정 하 여 Azure container registry에서 삭제 하거나 덮어쓸 수 없습니다.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: 7a313353ee1c7afae10fd7af84570565037e40ab
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310658"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Azure container registry에서 컨테이너 이미지 잠그기

Azure container registry에서 이미지 버전 또는 리포지토리를 잠가 삭제 하거나 업데이트할 수 없습니다. 이미지나 리포지토리를 잠그려면 Azure CLI 명령 [az acr repository update][az-acr-repository-update]를 사용 하 여 해당 특성을 업데이트 합니다. 

이 문서에서는 Azure Cloud Shell 또는 로컬 (버전 2.0.55 이상 권장)에서 Azure CLI를 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="scenarios"></a>시나리오

기본적으로 Azure Container Registry의 태그가 지정 된 이미지는 *변경*가능 하므로 적절 한 사용 권한이 있으면 동일한 태그가 있는 이미지를 반복적으로 업데이트 하 고 레지스트리로 푸시할 수 있습니다. 필요에 따라 컨테이너 이미지를 [삭제할](container-registry-delete.md) 수도 있습니다. 이 동작은 이미지를 개발할 때 레지스트리의 크기를 유지 해야 하는 경우에 유용 합니다.

그러나 컨테이너 이미지를 프로덕션 환경에 배포 하는 경우에는 *변경할* 수 없는 컨테이너 이미지가 필요할 수 있습니다. 변경할 수 없는 이미지는 실수로 삭제 하거나 덮어쓸 수 없는 이미지입니다. [Az acr repository update][az-acr-repository-update] 명령을 사용 하 여 리포지토리 특성을 설정 하 여 다음을 수행할 수 있습니다.

* 이미지 버전 또는 전체 리포지토리 잠그기

* 이미지 버전 또는 리포지토리를 삭제 하지 않고 보호 하지만 업데이트 허용

* 이미지 버전 또는 전체 리포지토리에서 읽기 (끌어오기) 작업 차단

예제는 다음 섹션을 참조 하세요.

## <a name="lock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 

### <a name="show-the-current-repository-attributes"></a>현재 리포지토리 특성 표시
리포지토리의 현재 특성을 보려면 다음 [az acr repository show][az-acr-repository-show] 명령을 실행 합니다.

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>현재 이미지 특성 표시
태그의 현재 특성을 보려면 다음 [az acr repository show][az-acr-repository-show] 명령을 실행 합니다.

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>태그로 이미지 잠그기

Myrepo */myrepo:* *myrepo*의 태그 이미지를 잠그려면 다음 [az acr repository update][az-acr-repository-update] 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>매니페스트 다이제스트로 이미지 잠그기

매니페스트 다이제스트 (로 `sha256:...`표시 되는 SHA-256 해시)로 식별 되는 *myrepo/myrepo* 이미지를 잠그려면 다음 명령을 실행 합니다. 하나 이상의 이미지 태그와 연결 된 매니페스트 다이제스트를 찾으려면 [az acr repository show-manifest][az-acr-repository-show-manifests] 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>리포지토리 잠금

*Myrepo/myrepo* 리포지토리 및 모든 이미지를 잠그려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>삭제할 이미지 또는 리포지토리 보호

### <a name="protect-an-image-from-deletion"></a>삭제에서 이미지 보호

*Myrepo/myrepo: 태그* 이미지를 업데이트 하지만 삭제 하지 않도록 허용 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>저장소에서 삭제 방지

다음 명령은 *myrepo/myrepo* 리포지토리를 삭제할 수 없도록 설정 합니다. 개별 이미지를 여전히 업데이트 하거나 삭제할 수 있습니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>이미지 또는 리포지토리에 대 한 읽기 작업 방지

*Myrepo/myrepo: tag* 이미지에서 읽기 (끌어오기) 작업을 방지 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

*Myrepo/myrepo* 리포지토리의 모든 이미지에 대 한 읽기 작업을 방지 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>이미지 또는 리포지토리 잠금 해제

삭제 하 고 업데이트할 수 있도록 *myrepo/myrepo: tag* 이미지의 기본 동작을 복원 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

삭제 및 업데이트할 수 있도록 myrepo */myrepo* 리포지토리 및 모든 이미지의 기본 동작을 복원 하려면 다음 명령을 실행 합니다.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 [az acr repository update][az-acr-repository-update] 명령을 사용 하 여 리포지토리의 이미지 버전 삭제 또는 업데이트를 방지 하는 방법을 배웠습니다. 추가 특성을 설정 하려면 [az acr repository update][az-acr-repository-update] 명령 참조를 참조 하세요.

이미지 버전 또는 리포지토리에 대해 설정 된 특성을 보려면 [az acr repository show][az-acr-repository-show] 명령을 사용 합니다.

삭제 작업에 대 한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제][container-registry-delete]를 참조 하세요.

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

