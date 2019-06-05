---
title: Azure Container Registry에서 이미지 리소스 삭제
description: 컨테이너 이미지 데이터를 삭제하여 레지스트리 크기를 효과적으로 관리하는 방법에 대한 세부 정보입니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/04/2019
ms.author: danlep
ms.openlocfilehash: 1e496002c869c5d2c072773d37ed5fd5d4a5841e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60430810"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Azure Container Registry에서 컨테이너 이미지 삭제

Azure Container Registry의 크기를 유지하려면 오래된 이미지 데이터를 주기적으로 삭제해야 합니다. 프로덕션 환경에 배포되는 일부 컨테이너 이미지에는 장기 저장소가 필요할 수 있지만 다른 이미지는 일반적으로 좀 더 빠르게 삭제해도 됩니다. 예를 들어, 자동화된 빌드 및 테스트 시나리오에서 레지스트리는 절대 배포되지 않는 이미지로 빠르게 채워진 후, 빌드 및 테스트 단계가 완료되는 즉시 제거될 수 있습니다.

여러 가지 방법으로 이미지 데이터를 삭제할 수 있으므로 각 삭제 작업이 저장소 사용량에 미치는 영향을 이해해야 합니다. 이 문서는 먼저 Docker 레지스트리 및 컨테이너 이미지의 구성 요소를 소개한 후 이미지 데이터를 삭제하는 여러 가지 방법을 설명합니다. 샘플 스크립트는 삭제 작업을 자동화 하기 위해 제공 됩니다.

## <a name="registry"></a>레지스트리

컨테이너 *레지스트리*는 컨테이너 이미지를 저장하고 배포하는 서비스입니다. Docker 허브는 공용 Docker 컨테이너 레지스트리이지만 Azure Container Registry는 Azure의 프라이빗 Docker 컨테이너 레지스트리입니다.

## <a name="repository"></a>리포지토리

컨테이너 레지스트리는 이름은 같지만 태그는 다른 컨테이너 이미지의 컬렉션인 *리포지토리*를 관리합니다. 예를 들어, 다음 3개의 이미지가 "helloworld acr" 리포지토리에 있습니다.

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

리포지토리 이름에 [네임스페이스](container-registry-best-practices.md#repository-namespaces)가 포함될 수도 있습니다. 네임 스페이스 예를 들어 정방향 슬래시로 구분 리포지토리 이름을 사용 하 여 이미지를 그룹화를 수 있습니다.

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>이미지의 구성 요소

레지스트리 내의 컨테이너 이미지는 하나 이상의 태그와 연결되고, 하나 이상의 계층을 가지고, 매니페스트로 식별됩니다. 이러한 구성 요소가 서로 관련되는 방식을 이해하면 레지스트리에서 공간을 확보하는 최상의 방법을 결정하는 데 도움이 될 수 있습니다.

### <a name="tag"></a>태그

이미지의 *태그*는 해당 버전을 지정합니다. 리포지토리 내의 단일 이미지에 하나 이상의 태그를 할당할 수 있으며 "태그를 지정하지 않을" 수도 있습니다. 즉, 레지스트리에서 이미지의 데이터 (레이어) 유지 하는 동안 이미지에서 모든 태그를 삭제할 수 있습니다.

리포지토리(또는 리포지토리 및 네임스페이스)와 태그는 이미지의 이름을 정의합니다. 밀어넣기 및 끌어오기 작업에서 해당 이름을 지정하여 이미지를 밀어넣고 끌어올 수 있습니다.

Azure Container Registry 같은 프라이빗 레지스트리에서 이미지 이름에는 레지스트리 호스트의 정규화된 이름도 포함됩니다. ACR 이미지 레지스트리 호스트가 형식에서 *acrname.azurecr.io* (모두 소문자). 예를 들어, 이전 섹션에서 "marketing" 네임 스페이스의 첫 번째 이미지의 전체 이름을 다음과 같습니다.

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

이미지 태그 지정 모범 사례에 대한 논의는 [Docker 태그 지정: Docker 이미지 태그 지정 및 버전 관리에 대한 모범 사례][tagging-best-practices] MSDN 블로그 포스트를 참조하세요.

### <a name="layer"></a>계층

이미지는 각각이 Dockerfile에서 이미지를 정의하는 줄에 해당하는 하나 이상의 *계층*으로 구성됩니다. 레지스트리의 이미지는 공용 계층을 공유하므로 저장소 효율성이 높아집니다. 예를 들어, 다른 리포지토리에 있는 여러 이미지가 동일한 Alpine Linux 기본 계층을 공유할 수 있지만 해당 계층의 복사본 하나만 레지스트리에 저장됩니다.

계층 공유는 여러 개의 이미지가 공통 계층을 공유하는 방식으로 노드에 대한 계층 분산을 최적화합니다. 예를 들어, 노드에 이미 있는 이미지가 맨 아래에 Alpine Linux 계층을 포함하는 경우 이후에 같은 계층을 참조하는 다른 이미지를 끌어올 경우 해당 계층이 노드로 전송되지 않습니다. 대신, 노드에 이미 존재하는 계층을 참조합니다.

### <a name="manifest"></a>매니페스트

컨테이너 레지스트리로 끌어온 각 컨테이너 이미지는 *매니페스트*와 연결됩니다. 이미지를 밀어넣을 때 레지스트리에서 생성된 매니페스트는 이미지를 고유하게 식별하고 해당 계층을 지정합니다. Azure CLI 명령 [az acr repository show-manifests][az-acr-repository-show-manifests]를 사용하여 리포지토리에 대한 매니페스트를 나열할 수 있습니다.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

예를 들어, "acr-helloworld" 리포지토리에 대한 매니페스트 다이제스트를 나열하려면 다음과 같이 입력합니다.

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
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
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

여기에 설명된 매니페스트는 Azure Portal에서 또는 [docker manifest inspect][docker-manifest-inspect]를 사용하여 볼 수 있는 이미지 매니페스트와는 다릅니다. 다음 섹션에서 "매니페스트 다이제스트"는 이미지 매니페스트의 *config.digest*가 아닌 밀어넣기 작업에서 생성된 다이제스트를 나타냅니다. config.digest가 아닌 **매니페스트 다이제스트**를 통해 이미지를 끌어오고 삭제할 수 있습니다. 다음 이미지에서는 두 가지 유형의 다이제스트를 보여 줍니다.

![Azure Porta의 매니페스트 다이제스트 및 config.digest][manifest-digest]

### <a name="manifest-digest"></a>매니페스트 다이제스트

매니페스트는 고유한 SHA-256 해시 또는 *매니페스트 다이제스트*에 의해 식별됩니다. 태그 지정 여부에 관계없이 각 이미지는 해당 다이제스트로 식별됩니다. 다이제스트 값은 이미지의 계층 데이터가 다른 이미지의 계층 데이터와 동일하더라도 고유합니다. 이 메커니즘에 따르면 동일하게 태그가 지정된 이미지를 레지스트리에 반복적으로 밀어넣을 수 있습니다. 예를 들어, 각 이미지는 고유한 다이제스트로 식별되므로 `myimage:latest`(을)를 레지스트리에 오류 없이 반복적으로 밀어넣을 수 있습니다.

끌어오기 작업에서 해당 다이제스트를 지정하여 레지스트리에서 이미지를 끌어올 수 있습니다. 일부 시스템은 다이제스트별로 끌어오도록 구성이 가능합니다. 동일하게 태그가 지정된 이미지를 나중에 레지스트리로 밀어넣더라도 해당 이미지 버전 끌어오기가 보장되기 때문입니다.

예를 들어, 매니페스트 다이제스트에 따라 "acr-helloworld" 리포지토리에서 이미지를 끌어오려면 다음과 같이 입력합니다.

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 동일한 태그가 지정된 이미지를 수정 후 반복적으로 밀어넣을 경우 태그가 지정되지 않은 이미지는 별도로 분리되어 생성될 수 있지만 이 이미지는 여전히 레지스트리에서 공간을 차지합니다. 태그가 지정되지 않은 이미지는 태그를 기준으로 이미지를 나열하거나 표시할 경우 Azure CLI 또는 Azure Portal에 표시되지 않습니다. 그러나 해당 계층은 여전히 존재하며 레지스트리에서 공간을 차지합니다. 이 문서의 [태그가 지정되지 않은 이미지 삭제](#delete-untagged-images) 섹션에서는 태그가 지정되지 않은 이미지에서 사용되는 공간을 사용 가능하게 해제하는 방법을 설명합니다.

## <a name="delete-image-data"></a>이미지 데이터 삭제

다음과 같은 여러 가지 방법으로 컨테이너 레지스트리에서 이미지 데이터를 삭제할 수 있습니다.

* [리포지토리](#delete-repository) 삭제: 리포지토리 내의 모든 이미지 및 모든 고유한 계층을 삭제합니다.
* [태그](#delete-by-tag)에 따라 삭제: 이미지, 태그, 이미지에서 참조하는 모든 고유한 계층과 이미지에 연결된 다른 모든 태그를 삭제합니다.
* [매니페스트 다이제스트](#delete-by-manifest-digest)에 따라 삭제: 이미지, 이미지에서 참조하는 모든 고유한 계층 및 이미지에 연결된 모든 태그를 삭제합니다.

## <a name="delete-repository"></a>리포지토리 삭제

리포지토리를 삭제하면 리포지토리에서 모든 태그, 고유한 레이어 및 매니페스트를 포함하는 모든 이미지가 삭제됩니다. 리포지토리를 삭제하면 해당 리포지토리에 있던 이미지에서 사용되는 저장소 공간이 복구됩니다.

다음 Azure CLI 명령은 "acr-helloworld" 리포지토리와 해당 리포지토리 내의 모든 태그 및 매니페스트를 삭제합니다. 삭제된 매니페스트가 참조하는 계층이 레지스트리의 다른 이미지에서 참조되지 않을 경우 해당 계층 데이터도 삭제됩니다.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>태그에 따라 삭제

삭제 작업에서 리포지토리 이름 및 태그를 지정하여 리포지토리에서 개별 이미지를 삭제할 수 있습니다. 태그에 따라 삭제하면 이미지의 모든 고유 계층(레지스트리의 다른 이미지에서 공유되지 않는 계층)에 사용되는 저장소 공간이 복구됩니다.

태그에 따라 삭제하려면 [az acr repository delete][az-acr-repository-delete]를 사용하고 `--image` 매개 변수에 이미지 이름을 지정합니다. 이미지에 고유한 모든 계층 및 이미지에 연결된 다른 모든 태그도 삭제됩니다.

예를 들어, "myregistry" 레지스트리에서 "acr-helloworld:latest" 이미지를 삭제하려면 다음을 입력합니다.

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> *태그에 따라* 삭제하는 방식을 태그 삭제(태그 해제)와 혼동하면 안 됩니다. Azure CLI 명령 [az acr repository untag][az-acr-repository-untag]를 사용하여 태그를 삭제할 수 있습니다. 때문에 이미지를 해제 하는 경우 공백이 해제 됩니다 해당 [매니페스트](#manifest) 계층 데이터 레지스트리에 유지 됩니다. 태그 참조 자체만 삭제됩니다.

## <a name="delete-by-manifest-digest"></a>매니페스트 다이제스트에 따라 삭제

[매니페스트 다이제스트](#manifest-digest)는 하나 이상의 태그와 연결되거나 어떤 태그와도 연결되지 않을 수 있습니다. 다이제스트에 따라 삭제하면 이미지에 고유한 모든 계층에 대한 계층 데이터와 마찬가지로 매니페스트에서 참조되는 모든 태그가 삭제됩니다. 공유 계층 데이터는 삭제되지 않습니다.

다이제스트에 따라 삭제하려면 먼저 삭제하려는 이미지를 포함하는 리포지토리의 매니페스트 다이제스트를 나열합니다. 예를 들면 다음과 같습니다.

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
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

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

`acr-helloworld:v2` 계층 데이터가 해당 이미지에 고유한 이미지 레지스트리에서 삭제 됩니다. 매니페스트가 여러 태그에 연결되면 연결된 모든 태그도 삭제됩니다.

### <a name="list-digests-by-timestamp"></a>타임 스탬프에서 목록 다이제스트

리포지토리 또는 레지스트리의 크기를 유지 하려면 특정 날짜 보다 오래 된 매니페스트 다이제스트를 주기적으로 삭제 해야 합니다.

다음 Azure CLI 명령에는 오름차순의 지정 된 타임 스탬프 보다 이전 리포지토리에서 모든 매니페스트 다이제스트를 나열 합니다. `<acrName>` 및 `<repositoryName>`을(를) 사용자 환경에 적절한 값으로 바꿉니다. 타임 스탬프는 전체 날짜 / 시간 식 또는이 예제와 같이 날짜를 수 있습니다.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

### <a name="delete-digests-by-timestamp"></a>타임 스탬프에서 다이제스트 삭제

오래 된 매니페스트 다이제스트를 식별 한 후 지정 된 타임 스탬프 보다 오래 된 매니페스트 다이제스트를 삭제 하려면 다음 Bash 스크립트를 실행할 수 있습니다. 여기에는 Azure CLI 및 **xargs**가 필요합니다. 기본적으로 스크립트는 삭제하지 않고 수행됩니다. 이미지를 삭제하도록 설정하려면 `ENABLE_DELETE` 값을 `true`(으)로 변경합니다.

> [!WARNING]
> 주의-삭제 된 이미지 데이터를 사용 하 여 다음 샘플 스크립트를 사용 하 여 UNRECOVERABLE 됩니다. 시스템 이미지 (이미지 이름)와 달리 매니페스트 다이제스트 끌어오기가 하는 경우에 이러한 스크립트를 실행 해야 합니다. 매니페스트 다이제스트 삭제 하면 해당 시스템 레지스트리에서 이미지를 풀에서 수 없게 됩니다. 매니페스트에 따라 끌어오는 대신, [권장 모범 사례][tagging-best-practices]에 해당하는 *고유 태그 지정* 체계를 채택하는 것이 좋습니다. 

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

[매니페스트 다이제스트](#manifest-digest) 섹션에 설명된 것처럼 기존 태그를 사용하여 수정된 이미지를 밀어넣으면 이전에 밀어넣은 이미지가 **태그 해제**되어 분리된(또는 "현수") 이미지가 됩니다. 이전에 밀어넣은 이미지 매니페스트와 해당 계층 데이터는 레지스트리에 유지됩니다. 다음과 같은 이벤트 시퀀스를 고려해 보세요.

1. 태그 **latest**를 사용하여 *acr helloworld* 이미지를 밀어넣습니다. `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 리포지토리 *acr-helloworld*에 대한 매니페스트를 확인합니다.

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
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

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
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

이제 해당 분리 된 매니페스트는 시퀀스의 마지막 단계의 출력에서 보듯이 `"tags"` 속성은 빈 목록입니다. 이 매니페스트는 참조하는 고유한 계층 데이터와 함께 레지스트리에 내에 계속 존재합니다. **분리된 이미지와 해당 계층 데이터를 삭제하려면 매니페스트 다이제스트에 따라 삭제해야 합니다**.

### <a name="list-untagged-images"></a>태그가 지정되지 않은 이미지 나열

다음 Azure CLI 명령을 사용하여 리포지토리의 태그가 지정되지 않은 모든 이미지를 나열할 수 있습니다. `<acrName>` 및 `<repositoryName>`을(를) 사용자 환경에 적절한 값으로 바꿉니다.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

### <a name="delete-all-untagged-images"></a>태그가 지정되지 않은 모든 이미지 삭제

> [!WARNING]
> 다음 샘플 스크립트는 주의해서 사용하세요. 삭제된 이미지 데이터는 복구 가능하지 않습니다. 시스템 이미지 (이미지 이름)와 달리 매니페스트 다이제스트 끌어오기가 하는 경우에 이러한 스크립트를 실행 해야 합니다. 태그가 지정되지 않은 이미지를 삭제하면 해당 시스템은 레지스트리에서 이미지를 끌어올 수 없게 됩니다. 매니페스트에 따라 끌어오는 대신, [권장 모범 사례][tagging-best-practices]에 해당하는 *고유 태그 지정* 체계를 채택하는 것이 좋습니다.

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
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
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
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>다음 단계

Azure Container Registry의 이미지 저장소에 대한 자세한 내용은 [Azure Container Registry의 컨테이너 이미지 저장소](container-registry-storage.md)를 참조하세요.

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
