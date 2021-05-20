---
title: 레지스트리의 다중 아키텍처 이미지
description: Azure Container Registry를 사용하여 다중 아키텍처 이미지 빌드, 가져오기, 저장 및 배포
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802457"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Azure Container Registry의 다중 아키텍처 이미지

이 문서에서는 **다중 아키텍처** 이미지가 무엇인지와 Azure Container Registry 기능을 사용하여 이러한 이미지를 만들고 저장하고 사용하는 방법을 소개합니다. 

다중 아키텍처 이미지는 서로 다른 아키텍처 및 경우에 따라 서로 다른 운영 체체에 대한 변형을 결합할 수 있는 컨테이너 이미지 유형입니다. 다중 아키텍처 지원으로 이미지를 실행하는 경우 컨테이너 클라이언트는 OS 및 아키텍처와 일치하는 이미지 변형을 자동으로 선택합니다.

## <a name="manifests-and-manifest-lists"></a>매니페스트 및 매니페스트 목록

다중 아키텍처 이미지는 이미지 매니페스트 및 매니페스트 목록을 기반으로 합니다.

### <a name="manifest"></a>file:///

각 컨테이너 이미지는 [매니페스트](container-registry-concepts.md#manifest)로 표시됩니다. 매니페스트는 이미지를 고유하게 식별하고 해당 레이어 및 해당 크기를 참조하는 JSON 파일입니다. 

Linux `hello-world` 이미지에 대한 기본 매니페스트는 다음과 유사합니다.

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Azure Portal을 사용하거나 Azure CLI에서 [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) 명령과 같은 도구를 사용하여 Azure Container Registry에서 매니페스트를 볼 수 있습니다.

### <a name="manifest-list"></a>매니페스트 목록

다중 아키텍처 이미지에 대한 *매니페스트 목록*(일반적으로 OCI 이미지에 대한 [이미지 인덱스](https://github.com/opencontainers/image-spec/blob/master/image-index.md)로 알려짐)은 이미지 컬렉션(인덱스)으로, 하나 이상의 이미지 이름을 지정하여 만듭니다. 여기에는 지원되는 OS 및 아키텍처, 크기 및 매니페스트 다이제스트와 같은 각 이미지에 대한 세부 정보가 포함됩니다. 매니페스트 목록은 `docker pull` 및 `docker run` 명령에서 이미지 이름과 동일한 방식으로 사용할 수 있습니다. 

`docker` CLI는 [docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) 명령을 사용하여 매니페스트 및 매니페스트 목록을 관리합니다.

> [!NOTE]
> 현재 `docker manifest` 명령과 하위 명령은 실험적입니다. 실험적 명령 사용에 대한 자세한 내용은 Docker 설명서를 참조하세요.

`docker manifest inspect` 명령을 사용하여 매니페스트 목록을 볼 수 있습니다. 다음은 Linux OS 아키텍처에 대한 2개의 매니페스트와 Windows 아키텍처에 대한 1개의 매니페스트를 포함하여 3개의 매니페스트가 있는 다중 아키텍처 이미지 `mcr.microsoft.com/mcr/hello-world:latest`에 대한 출력입니다.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

다중 아키텍처 매니페스트 목록이 Azure Container Registry에 저장된 경우 Azure Portal을 사용하거나 [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests) 명령과 같은 도구를 사용하여 매니페스트 목록을 볼 수도 있습니다.

## <a name="import-a-multi-arch-image"></a>다중 아키텍처 이미지 가져오기 

기존 다중 아키텍처 이미지는 [az acr import](/cli/azure/acr#az_acr_import) 명령을 사용하여 Azure Container Registry로 가져올 수 있습니다. 이미지 가져오기 구문은 단일 아키텍처 이미지를 사용하는 것과 같습니다. 단일 아키텍처 이미지 가져오기와 마찬가지로 다중 아키텍처 이미지 가져오기는 Docker 명령을 사용하지 않습니다. 

자세한 내용은 [컨테이너 이미지를 컨테이너 레지스트리로 가져오기](container-registry-import-images.md)를 참조하세요.

## <a name="push-a-multi-arch-image"></a>다중 아키텍처 이미지 푸시

서로 다른 아키텍처의 컨테이너 이미지를 만드는 빌드 워크플로가 있는 경우 다음 단계에 따라 다중 아키텍처 이미지를 Azure Container Registry에 푸시합니다.

1. 각 아키텍처 관련 이미지를 태그 지정하고 컨테이너 레지스트리에 푸시합니다. 다음 예제에서는 두 가지 Linux 아키텍처인 arm64 및 amd64를 가정합니다. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. `docker manifest create`을 실행하여 이전 이미지를 다중 아키텍처 이미지로 결합하는 매니페스트 목록을 만듭니다.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. `docker manifest push`를 사용하여 매니페스트를 컨테이너 레지스트리에 푸시합니다.

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. `docker manifest inspect` 명령을 사용하여 메니페스트 목록을 확인합니다. 명령 출력의 예제는 이전 섹션에 나와 있습니다.

다중 아키텍처 매니페스트를 레지스트리에 푸시한 후에는 단일 아키텍처 이미지를 사용하는 것과 동일한 방식으로 다중 아키텍처 이미지를 사용하여 작업합니다. 예를 들어, `docker pull`을 사용하여 이미지를 풀하고 [az acr repository](/cli/azure/acr/repository#az_acr_repository) 명령을 사용하여 이미지의 태그, 매니페스트 및 기타 속성을 볼 수 있습니다.

## <a name="build-and-push-a-multi-arch-image"></a>다중 아키텍처 이미지 빌드 및 푸시

[ACR 작업](container-registry-tasks-overview.md)의 기능을 사용하여 다중 아키텍처 이미지를 빌드하고 Azure Container Registry에 푸시할 수 있습니다. 예를 들어, Linux 다중 아키텍처 이미지를 빌드하는 YAML 파일에서 [다중 단계 작업](container-registry-tasks-multi-step.md)을 정의합니다.

다음 예제에서는 arm64 및 amd64의 두 아키텍처에 대해 별도의 Dockerfile이 있다고 가정 합니다. 아키텍처 관련 이미지를 빌드하고 푸시한 다음, `latest` 태그를 포함하는 다중 아키텍처 매니페스트를 만들어 푸시합니다.

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>다음 단계

* [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)를 사용하여 서로 다른 아키텍처에 대한 컨테이너 이미지를 빌드합니다.
* 실험적 Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) 플러그 인을 사용하여 다중 플랫폼 이미지를 빌드하는 방법에 대해 알아봅니다.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
