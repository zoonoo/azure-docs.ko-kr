---
title: 이미지 리소스 삭제
description: Azure CLI 명령을 사용하여 컨테이너 이미지 데이터를 삭제하여 레지스트리 크기를 효과적으로 관리하는 방법에 대한 자세한 내용입니다.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403339"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure 컨테이너 레지스트리에서 컨테이너 이미지 삭제

Azure Container Registry의 크기를 유지하려면 오래된 이미지 데이터를 주기적으로 삭제해야 합니다. 프로덕션 환경에 배포되는 일부 컨테이너 이미지에는 장기 스토리지가 필요할 수 있지만 다른 이미지는 일반적으로 좀 더 빠르게 삭제해도 됩니다. 예를 들어, 자동화된 빌드 및 테스트 시나리오에서 레지스트리는 절대 배포되지 않는 이미지로 빠르게 채워진 후, 빌드 및 테스트 단계가 완료되는 즉시 제거될 수 있습니다.

여러 가지 방법으로 이미지 데이터를 삭제할 수 있으므로 각 삭제 작업이 스토리지 사용량에 미치는 영향을 이해해야 합니다. 이 문서에서는 이미지 데이터를 삭제하는 몇 가지 방법을 설명합니다.

* [리포지토리](#delete-repository) 삭제: 리포지토리 내의 모든 이미지 및 모든 고유한 계층을 삭제합니다.
* [태그](#delete-by-tag)에 따라 삭제: 이미지, 태그, 이미지에서 참조하는 모든 고유한 계층과 이미지에 연결된 다른 모든 태그를 삭제합니다.
* [매니페스트 다이제스트](#delete-by-manifest-digest)에 따라 삭제: 이미지, 이미지에서 참조하는 모든 고유한 계층 및 이미지에 연결된 모든 태그를 삭제합니다.

삭제 작업을 자동화하는 데 도움이 되는 샘플 스크립트가 제공됩니다.

이러한 개념에 대한 소개는 [레지스트리, 리포지토리 및 이미지 소개를](container-registry-concepts.md)참조하십시오.

## <a name="delete-repository"></a>리포지토리 삭제

리포지토리를 삭제하면 리포지토리에서 모든 태그, 고유한 레이어 및 매니페스트를 포함하는 모든 이미지가 삭제됩니다. 리포지토리를 삭제하면 해당 리포지토리의 고유한 레이어를 참조하는 이미지에서 사용하는 저장소 공간을 복구합니다.

다음 Azure CLI 명령은 "acr-helloworld" 리포지토리와 해당 리포지토리 내의 모든 태그 및 매니페스트를 삭제합니다. 삭제된 매니페스트에서 참조하는 레이어가 레지스트리의 다른 이미지에서 참조되지 않으면 레이어 데이터도 삭제되어 저장 공간을 복구합니다.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>태그에 따라 삭제

삭제 작업에서 리포지토리 이름 및 태그를 지정하여 리포지토리에서 개별 이미지를 삭제할 수 있습니다. 태그에 따라 삭제하면 이미지의 모든 고유 계층(레지스트리의 다른 이미지에서 공유되지 않는 계층)에 사용되는 스토리지 공간이 복구됩니다.

태그에 따라 삭제하려면 [az acr repository delete][az-acr-repository-delete]를 사용하고 `--image` 매개 변수에 이미지 이름을 지정합니다. 이미지에 고유한 모든 계층 및 이미지에 연결된 다른 모든 태그도 삭제됩니다.

예를 들어, "myregistry" 레지스트리에서 "acr-helloworld:latest" 이미지를 삭제하려면 다음을 입력합니다.

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> *태그에 따라* 삭제하는 방식을 태그 삭제(태그 해제)와 혼동하면 안 됩니다. Azure CLI 명령 [az acr repository untag][az-acr-repository-untag]를 사용하여 태그를 삭제할 수 있습니다. [매니페스트](container-registry-concepts.md#manifest) 및 레이어 데이터가 레지스트리에 남아 있으므로 이미지에 태그를 해제할 때 공간이 해제되지 않습니다. 태그 참조 자체만 삭제됩니다.

## <a name="delete-by-manifest-digest"></a>매니페스트 다이제스트에 따라 삭제

[매니페스트 다이제스트](container-registry-concepts.md#manifest-digest)는 하나 이상의 태그와 연결되거나 어떤 태그와도 연결되지 않을 수 있습니다. 다이제스트에 따라 삭제하면 이미지에 고유한 모든 계층에 대한 계층 데이터와 마찬가지로 매니페스트에서 참조되는 모든 태그가 삭제됩니다. 공유 계층 데이터는 삭제되지 않습니다.

다이제스트에 따라 삭제하려면 먼저 삭제하려는 이미지를 포함하는 리포지토리의 매니페스트 다이제스트를 나열합니다. 예를 들어:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

다음으로, [az acr repository delete][az-acr-repository-delete] 명령에서 삭제하려는 다이제스트를 지정합니다. 명령 형식은 다음과 같습니다.

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

예를 들어 이전 출력에 나열된 마지막 매니페스트(태그 "v2" 지정)를 삭제하려면 다음을 입력합니다.

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

`acr-helloworld:v2` 해당 이미지에 고유한 레이어 데이터와 마찬가지로 이미지가 레지스트리에서 삭제됩니다. 매니페스트가 여러 태그에 연결되면 연결된 모든 태그도 삭제됩니다.

## <a name="delete-digests-by-timestamp"></a>타임스탬프별로 다이제스트 삭제

리포지토리 또는 레지스트리의 크기를 유지하려면 특정 날짜보다 오래된 매니페스트 다이제스트를 주기적으로 삭제해야 할 수 있습니다.

다음 Azure CLI 명령은 지정된 타임스탬프보다 오래된 리포지토리의 모든 매니페스트 다이제스트를 오름차순으로 나열합니다. `<acrName>` 및 `<repositoryName>`을(를) 사용자 환경에 적절한 값으로 바꿉니다. 타임스탬프는 이 예제와 같이 전체 날짜 시간 식 또는 날짜일 수 있습니다.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

부실 매니페스트 다이제스트를 식별한 후 다음 Bash 스크립트를 실행하여 지정된 타임스탬프보다 오래된 매니페스트 다이제스트를 삭제할 수 있습니다. 여기에는 Azure CLI 및 **xargs**가 필요합니다. 기본적으로 스크립트는 삭제하지 않고 수행됩니다. 이미지를 삭제하도록 설정하려면 `ENABLE_DELETE` 값을 `true`(으)로 변경합니다.

> [!WARNING]
> 삭제된 이미지 데이터는 복구할 수 없습니다. 매니페스트 다이제스트로 이미지를 끌어당기는 시스템이 있는 경우(이미지 이름과 는 달리) 이러한 스크립트를 실행해서는 안 됩니다. 매니페스트 다이제스트를 삭제하면 해당 시스템이 레지스트리에서 이미지를 가져오는 것을 방지할 수 있습니다. 매니페스트에 따라 끌어오는 대신, [권장 모범 사례](container-registry-image-tag-version.md)에 해당하는 *고유 태그 지정* 체계를 채택하는 것이 좋습니다. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>태그가 지정되지 않은 이미지 삭제

[매니페스트 다이제스트](container-registry-concepts.md#manifest-digest) 섹션에 설명된 것처럼 기존 태그를 사용하여 수정된 이미지를 밀어넣으면 이전에 밀어넣은 이미지가 **태그 해제**되어 분리된(또는 "현수") 이미지가 됩니다. 이전에 밀어넣은 이미지 매니페스트와 해당 계층 데이터는 레지스트리에 유지됩니다. 다음과 같은 이벤트 시퀀스를 고려해 보세요.

1. 태그 **latest**를 사용하여 *acr helloworld* 이미지를 밀어넣습니다. `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 리포지토리 *acr-helloworld*에 대한 매니페스트를 확인합니다.

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. *acr-helloworld* Dockerfile을 수정합니다.
1. 태그 **latest**를 사용하여 *acr helloworld* 이미지를 밀어넣습니다. `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 리포지토리 *acr-helloworld*에 대한 매니페스트를 확인합니다.

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

시퀀스의 마지막 단계의 출력에서 볼 수 있듯이 이제 `"tags"` 속성이 빈 목록인 분리된 매니페스트가 있습니다. 이 매니페스트는 참조하는 고유한 계층 데이터와 함께 레지스트리에 내에 계속 존재합니다. **분리된 이미지와 해당 계층 데이터를 삭제하려면 매니페스트 다이제스트에 따라 삭제해야 합니다**.

## <a name="delete-all-untagged-images"></a>태그가 지정되지 않은 모든 이미지 삭제

다음 Azure CLI 명령을 사용하여 리포지토리의 태그가 지정되지 않은 모든 이미지를 나열할 수 있습니다. `<acrName>` 및 `<repositoryName>`을(를) 사용자 환경에 적절한 값으로 바꿉니다.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

스크립트에서 이 명령을 사용하여 리포지토리에서 태그가 지정되지 않은 모든 이미지를 삭제할 수 있습니다.

> [!WARNING]
> 다음 샘플 스크립트는 주의해서 사용하세요. 삭제된 이미지 데이터는 복구 가능하지 않습니다. 매니페스트 다이제스트로 이미지를 끌어당기는 시스템이 있는 경우(이미지 이름과 는 달리) 이러한 스크립트를 실행해서는 안 됩니다. 태그가 지정되지 않은 이미지를 삭제하면 해당 시스템은 레지스트리에서 이미지를 끌어올 수 없게 됩니다. 매니페스트에 따라 끌어오는 대신, [권장 모범 사례](container-registry-image-tag-version.md)에 해당하는 *고유 태그 지정* 체계를 채택하는 것이 좋습니다.

**Bash의 Azure CLI**

다음 Bash 스크립트는 리포지토리에서 태그가 지정되지 않은 모든 이미지를 삭제합니다. 여기에는 Azure CLI 및 **xargs**가 필요합니다. 기본적으로 스크립트는 삭제하지 않고 수행됩니다. 이미지를 삭제하도록 설정하려면 `ENABLE_DELETE` 값을 `true`(으)로 변경합니다.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**PowerShell의 Azure CLI**

다음 PowerShell 스크립트는 리포지토리에서 태그가 지정되지 않은 모든 이미지를 삭제합니다. PowerShell 및 Azure CLI가 모두 필요합니다. 기본적으로 스크립트는 삭제하지 않고 수행됩니다. 이미지를 삭제하도록 설정하려면 `$enableDelete` 값을 `$TRUE`(으)로 변경합니다.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>태그 및 매니페스트 자동 제거(미리 보기)

Azure CLI 명령을 스크립팅하는 대신 온디맨드 또는 예약된 ACR 작업을 실행하여 특정 기간보다 오래된 모든 태그를 삭제하거나 지정된 이름 필터와 일치합니다. 자세한 내용은 [Azure 컨테이너 레지스트리에서 이미지 자동 제거를](container-registry-auto-purge.md)참조하세요.

선택적으로 태그가 지정되지 않은 매니페스트를 관리하기 위해 각 레지스트리에 대한 [보존 정책을](container-registry-retention-policy.md) 설정합니다. 보존 정책을 사용하도록 설정하면 연결된 태그가 없는 레지스트리의 이미지 매니페스트와 기본 레이어 데이터가 설정된 기간 이후에 자동으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계

Azure Container Registry의 이미지 스토리지에 대한 자세한 내용은 [Azure Container Registry의 컨테이너 이미지 스토리지](container-registry-storage.md)를 참조하세요.

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
