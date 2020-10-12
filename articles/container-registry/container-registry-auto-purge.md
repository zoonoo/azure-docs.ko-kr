---
title: 태그 및 매니페스트 제거
description: 제거 명령을 사용하여 연령 및 태그 필터를 기준으로 Azure 컨테이너 레지스트리에서 여러 태그와 매니페스트를 삭제하고 필요에 따라 제거 작업을 예약합니다.
ms.topic: article
ms.date: 05/14/2020
ms.openlocfilehash: ab6794648babd2bd491ded5788455b75c10d675a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83652642"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Azure 컨테이너 레지스트리에서 자동으로 이미지 제거

개발 워크플로의 일부로 Azure 컨테이너 레지스트리를 사용하는 경우 레지스트리는 짧은 기간 후 필요하지 않은 이미지 또는 기타 아티팩트로 신속하게 채울 수 있습니다. 특정 기간보다 오래되었거나 지정된 이름 필터와 일치하는 모든 태그를 삭제하는 것이 좋습니다. 여러 아티팩트를 신속하게 삭제하기 위해 이 문서에서는 주문형 또는 [예약된](container-registry-tasks-scheduled.md) ACR 작업으로 실행할 수 있는 `acr purge` 명령을 소개합니다. 

`acr purge` 명령은 현재 GitHub의 [acr-cli](https://github.com/Azure/acr-cli) 리포지토리의 소스 코드에서 빌드된 공용 컨테이너 이미지(`mcr.microsoft.com/acr/acr-cli:0.2`)에 배포되어 있습니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 문서의 ACR 작업 예제를 실행할 수 있습니다. 로컬로 사용하려는 경우 2.0.76 이상 버전이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요. 

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

> [!WARNING]
> 삭제된 이미지 데이터는 복구할 수 없으므로 `acr purge` 명령을 주의해서 사용해야 합니다. 매니페스트 다이제스트(이미지 이름 아님)로 이미지를 풀하는 시스템이 있는 경우 태그가 지정되지 않은 이미지를 제거하지 않아야 합니다. 태그가 지정되지 않은 이미지를 삭제하면 해당 시스템은 레지스트리에서 이미지를 끌어올 수 없게 됩니다. 매니페스트에 따라 풀하는 대신 [권장 모범 사례](container-registry-image-tag-version.md)에 해당하는 ‘고유 태그 지정’ 체계를 채택하는 것이 좋습니다.

Azure CLI 명령을 사용하여 단일 이미지 태그나 매니페스트를 삭제하려면 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조하세요.

## <a name="use-the-purge-command"></a>제거 명령 사용

`acr purge` 컨테이너 명령은 이름 필터와 일치하고 지정된 기간보다 오래된 리포지토리의 태그로 이미지를 삭제합니다. 기본적으로 기본 [매니페스트](container-registry-concepts.md#manifest) 및 계층 데이터가 아닌 태그 참조만 삭제됩니다. 이 명령에는 매니페스트도 삭제할 수 있는 옵션이 있습니다. 

> [!NOTE]
> `acr purge`는 `write-enabled` 특성이 `false`로 설정된 이미지 태그 또는 리포지토리를 삭제하지 않습니다. 관련 내용은 [Azure 컨테이너 레지스트리에서 컨테이너 이미지 잠금](container-registry-image-lock.md)을 참조하세요.

`acr purge`는 [ACR 작업](container-registry-tasks-overview.md)에서 컨테이너 명령으로 실행되도록 설계되었으므로 작업이 실행되는 레지스트리를 사용하여 자동으로 인증하고 이 레지스트리에서 작업을 수행합니다. 이 문서의 작업 예제에서는 정규화된 컨테이너 이미지 명령 대신 `acr purge` 명령 [별칭](container-registry-tasks-reference-yaml.md#aliases)을 사용합니다.

최소한 `acr purge`를 실행하는 경우 다음을 지정합니다.

* `--filter` - 리포지토리에서 태그를 필터링하기 위한 리포지토리 및 ‘정규식’. 예: `--filter "hello-world:.*"`는 `hello-world` 리포지토리의 모든 태그와 일치하고, `--filter "hello-world:^1.*"`는 `1`로 시작하는 태그와 일치합니다. 여러 개의 `--filter` 매개 변수를 전달하여 여러 리포지토리를 제거합니다.
* `--ago` - 이미지가 삭제되지 않는 기간을 나타내는 이동 스타일 [기간 문자열](https://golang.org/pkg/time/). 기간은 하나 이상의 10진수 시퀀스로 구성되며 각각 단위 접미사가 있습니다. 유효한 시간 단위에는 일에 해당하는 “d”, 시간에 해당하는 “h”, 분에 해당하는 “m”이 포함됩니다. 예를 들어 `--ago 2d3h6m`은 2일 3시간 6분 전보다 이전에 마지막으로 수정된 필터링된 모든 이미지를 선택하고, `--ago 1.5h`는 1.5시간 전보다 이전에 마지막으로 수정된 이미지를 선택합니다.

`acr purge`에서는 여러 선택적 매개 변수를 지원합니다. 다음 두 가지는 이 문서의 예제에서 사용됩니다.

* `--untagged` - 연결된 태그를 포함하지 않는 매니페스트(‘태그가 지정되지 않은 매니페스트’)를 삭제하도록 지정합니다.
* `--dry-run` - 데이터를 삭제하지 않도록 지정하지만, 출력은 이 플래그 없이 명령이 실행될 때와 같습니다. 이 매개 변수는 보존하려는 데이터를 실수로 삭제하지 않도록 제거 명령을 테스트하는 데 유용합니다.

추가 매개 변수의 경우 `acr purge --help`를 실행합니다. 

`acr purge`는 [실행 변수](container-registry-tasks-reference-yaml.md#run-variables) 및 스트리밍되고 나중에 검색할 수 있도록 저장되는 [작업 실행 로그](container-registry-tasks-logs.md)를 포함한 ACR 작업 명령의 다른 기능을 지원합니다.

### <a name="run-in-an-on-demand-task"></a>주문형 작업 실행

다음 예제에서는 [az acr run][az-acr-run] 명령을 사용하여 주문형으로 `acr purge` 명령을 실행합니다. 이 예제에서는 1일 전보다 이전에 수정된 *myregistry*의 `hello-world` 리포지토리에 있는 모든 이미지 태그 및 매니페스트를 삭제합니다. 컨테이너 명령은 환경 변수를 사용하여 전달되며, 작업은 소스 컨텍스트 없이 실행됩니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>예약된 작업에서 실행

다음 예제에서는 [az acr task create][az-acr-task-create] 명령을 사용하여 일별 [예약된 ACR 작업](container-registry-tasks-scheduled.md)을 만듭니다. 작업은 `hello-world` 리포지토리에서 7일 전보다 이전에 수정된 태그를 제거합니다. 컨테이너 명령은 환경 변수를 사용하여 전달되며, 작업은 소스 컨텍스트 없이 실행됩니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

[az acr task show][az-acr-task-show] 명령을 실행하여 타이머 트리거가 구성되어 있는지 확인합니다.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>많은 수의 태그 및 매니페스트 제거

많은 수의 태그와 매니페스트를 제거하려면 몇 분 이상 걸릴 수 있습니다. 수천 개의 태그 및 매니페스트를 제거하려면 명령이 주문형 작업의 기본 제한 시간인 600초 또는 예약된 작업의 기본 제한 시간인 3,600초보다 길게 실행되어야 할 수 있습니다. 제한 시간을 초과하는 경우 태그 및 매니페스트의 하위 집합만 삭제됩니다. 대규모 제거가 완료되었는지 확인하려면 `--timeout` 매개 변수를 전달하여 값을 늘립니다. 

예를 들어 다음 주문형 작업은 시간 제한 시간을 3,600초(1시간)로 설정합니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>예제: 레지스트리의 여러 리포지토리를 대상으로 한 예약된 제거

이 예제에서는 `acr purge`를 사용하여 레지스트리의 여러 리포지토리를 정기적으로 정리하는 과정을 안내합니다. 예를 들어 `samples/devimage1` 및 `samples/devimage2` 리포지토리에 이미지를 푸시하는 개발 파이프라인이 있을 수 있습니다. 배포를 위해 개발 이미지를 프로덕션 리포지토리로 정기적으로 가져오기 때문에 개발 이미지가 더 이상 필요하지 않을 수 있습니다. 다음 주의 작업에 대비하여 `samples/devimage1` 및 `samples/devimage2` 리포지토리를 매주 제거합니다.

### <a name="preview-the-purge"></a>제거 미리 보기

데이터를 삭제하기 전에 `--dry-run` 매개 변수를 사용하여 주문형 제거 작업을 실행하는 것이 좋습니다. 이 옵션을 사용하면 데이터를 제거하지 않고도 명령이 제거하는 태그 및 매니페스트를 볼 수 있습니다. 

다음 예제에서 각 리포지토리의 필터는 모든 태그를 선택합니다. `--ago 0d` 매개 변수는 리포지토리에서 필터와 일치하는 모든 연령의 이미지와 일치합니다. 시나리오에 필요한 대로 선택 조건을 수정합니다. `--untagged` 매개 변수는 태그 외에 매니페스트를 삭제함을 나타냅니다. 컨테이너 명령은 환경 변수를 사용하여 [az acr run][az-acr-run] 명령에 전달됩니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

명령 출력을 검토하여 선택 매개 변수와 일치하는 태그 및 매니페스트를 확인합니다. 명령이 `--dry-run`을 사용하여 실행되므로 데이터가 삭제되지 않습니다.

샘플 출력:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>제거 예약

시험 실행을 확인한 후에는 제거를 자동화하는 예약 작업을 만듭니다. 다음 예제에서는 이전 제거 명령을 실행하도록 일요일 1:00 UTC에 주간 작업을 예약합니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

[az acr task show][az-acr-task-show] 명령을 실행하여 타이머 트리거가 구성되어 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Container Registry에서 [이미지 데이터를 삭제](container-registry-delete.md)하는 다른 옵션에 대해 알아봅니다.

이미지 스토리지에 대한 자세한 내용은 [Azure Container Registry의 컨테이너 이미지 스토리지](container-registry-storage.md)를 참조하세요.

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

