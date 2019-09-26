---
title: Azure Container Registry에서 태그가 없는 매니페스트를 유지 하는 정책
description: 정의 된 기간이 끝난 후 태그가 지정 된 매니페스트를 자동으로 삭제 하기 위해 Azure container registry에서 보존 정책을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306004"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>태그가 없는 매니페스트에 대 한 보존 정책 설정

Azure Container Registry에는 연결 된 태그 (*태그가 없는 매니페스트*)가 없는 저장 된 이미지 매니페스트에 대 한 *보존 정책을* 설정 하는 옵션이 제공 됩니다. 보존 정책을 사용 하도록 설정 하면 설정 된 일 수 후에 레지스트리의 태그 없는 매니페스트가 자동으로 삭제 됩니다. 이 기능을 사용 하면 레지스트리가 필요 하지 않은 아티팩트를 채우지 않고 저장소 비용을 절감할 수 있습니다. 태그가 없는 매니페스트의 `false`특성이로설정 된 경우 매니페스트를 삭제할 수 없으며 보존 정책이 적용 되지 않습니다. `delete-enabled`

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용 하 여이 문서의 명령 예제를 실행할 수 있습니다. 로컬에서 사용 하려는 경우 버전 2.0.74 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

> [!WARNING]
> 보존 정책을 주의 하 여 설정--삭제 된 이미지 데이터는 복구할 수 없습니다. 이미지 이름과 달리 매니페스트 다이제스트로 이미지를 가져오는 시스템이 있는 경우 태그 없는 매니페스트에 대 한 보존 정책을 설정 하면 안 됩니다. 태그가 지정되지 않은 이미지를 삭제하면 해당 시스템은 레지스트리에서 이미지를 끌어올 수 없게 됩니다. 매니페스트를 가져오는 대신 *고유한 태깅* 스키마를 채택 하는 것이 [좋습니다. 권장 되는 모범 사례](container-registry-image-tag-version.md)입니다.

Azure CLI 명령을 사용 하 여 단일 이미지 태그나 매니페스트를 삭제 하려면 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조 하세요.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 보존 정책을 사용 하 여 **프리미엄** 컨테이너 레지스트리만 구성할 수 있습니다. 레지스트리 서비스 계층에 대 한 자세한 내용은 [Azure Container Registry sku](container-registry-skus.md)를 참조 하세요.
* 태그 없는 매니페스트에 대 한 보존 정책만 설정할 수 있습니다.

## <a name="set-a-retention-policy---cli"></a>보존 정책 설정-CLI

다음 예에서는 Azure CLI를 사용 하 여 레지스트리의 태그 없는 매니페스트에 대 한 보존 정책을 설정 하는 방법을 보여 줍니다.

### <a name="enable-a-retention-policy"></a>보존 정책 사용

기본적으로 컨테이너 레지스트리에는 보존 정책이 설정 되지 않습니다. 보존 정책을 설정 하거나 업데이트 하려면 Azure CLI에서 [az acr config 보존 업데이트][az-acr-config-retention-update] 명령을 실행 합니다. 태그가 지정 되지 않은 매니페스트를 유지 하려면 0에서 365 사이의 일 수를 지정할 수 있습니다. 일 수를 지정 하지 않으면 명령의 기본값은 7 일로 설정 됩니다. 보존 기간 후에는 레지스트리의 태그가 없는 모든 매니페스트가 자동으로 삭제 됩니다.

다음 예에서는 레지스트리에서 *myregistry*의 태그가 없는 매니페스트에 대해 30 일의 보존 정책을 설정 합니다.

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

다음 예제에서는 태그를 제거 하는 즉시 레지스트리의 매니페스트를 삭제 하도록 정책을 설정 합니다. 보존 기간을 0 일로 설정 하 여이 정책을 만듭니다.

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>보존 정책 사용 안 함

레지스트리에 설정 된 보존 정책을 보려면 [az acr config 보존이 show][az-acr-config-retention-show] 명령을 실행 합니다.

```azurecli
az acr config retention show --name myregistry
```

레지스트리에서 보존 정책을 사용 하지 않도록 설정 하려면 [az acr config 보존 업데이트][az-acr-config-retention-update] 명령을 실행 하 고 다음을 `--status disabled`설정 합니다.

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>보존 정책 설정-포털

[Azure Portal](https://portal.azure.com)에서 레지스트리의 보존 정책을 설정할 수도 있습니다. 다음 예제에서는 포털을 사용 하 여 레지스트리의 태그가 없는 매니페스트에 대 한 보존 정책을 설정 하는 방법을 보여 줍니다.

### <a name="enable-a-retention-policy"></a>보존 정책 사용

1. Azure container registry로 이동 합니다. **정책**에서 **보존** (미리 보기)을 선택 합니다.
1. **상태**에서 **사용**을 선택 합니다.
1. 태그가 없는 매니페스트를 유지 하려면 0에서 365 사이의 일 수를 선택 합니다. **저장**을 선택합니다.

![Azure Portal에서 보존 정책 사용](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>보존 정책 사용 안 함

1. Azure container registry로 이동 합니다. **정책**에서 **보존** (미리 보기)을 선택 합니다.
1. **상태**에서 **사용 안 함**을 선택 합니다. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* 에서 [이미지 및 리포지토리를 삭제](container-registry-delete.md) 하는 옵션에 대 한 자세한 정보 Azure Container Registry

* 레지스트리에서 선택한 이미지 및 매니페스트를 [자동으로 제거](container-registry-auto-purge.md) 하는 방법을 알아봅니다.

* 레지스트리에서 [이미지 및 매니페스트를 잠그는](container-registry-image-lock.md) 옵션에 대 한 자세한 정보

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
