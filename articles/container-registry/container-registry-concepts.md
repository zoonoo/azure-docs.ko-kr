---
title: 레지스트리, 리포지토리, 이미지 및 아티팩트 정보
description: Azure container registry, 리포지토리, 컨테이너 이미지 및 기타 아티팩트의 주요 개념을 소개 합니다.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578127"
---
# <a name="about-registries-repositories-and-artifacts"></a>레지스트리, 리포지토리 및 아티팩트 정보

이 문서에서는 컨테이너 레지스트리, 리포지토리 및 컨테이너 이미지와 관련 아티팩트의 주요 개념을 소개 합니다. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="레지스트리, 리포지토리 및 아티팩트":::

## <a name="registry"></a>레지스트리

컨테이너 *레지스트리* 는 컨테이너 이미지 및 관련 아티팩트를 저장 하 고 배포 하는 서비스입니다. Docker 허브는 Docker 컨테이너 이미지의 일반 카탈로그 역할을 하는 공용 컨테이너 레지스트리의 예입니다. Azure Container Registry는 사용자에 게 컨테이너 콘텐츠를 직접 제어 하 고, 통합 인증, 네트워크 연결에 대 한 글로벌 배포 및 안정성을 지 원하는 [지역 복제](container-registry-geo-replication.md) , [개인 링크로 가상 네트워크 구성](container-registry-private-link.md), [태그 잠금](container-registry-image-lock.md)및 기타 향상 된 기능을 제공 합니다. 

Docker 호환 컨테이너 이미지 외에도 Azure Container Registry는 투구 차트 및 OCI (Open Container 이니셔티브) 이미지 형식을 비롯 한 다양 한 [콘텐츠 아티팩트](container-registry-image-formats.md) 를 지원 합니다.

## <a name="repository"></a>리포지토리

*리포지토리* 는 이름이 같지만 태그가 다른 레지스트리의 컨테이너 이미지 또는 기타 아티팩트의 컬렉션입니다. 예를 들어 다음 세 이미지는 `acr-helloworld` 리포지토리에 있습니다.

- *acr-helloworld: 최신*
- *acr-helloworld: v1*
- *acr-helloworld: v2*

리포지토리 이름에 [네임스페이스](container-registry-best-practices.md#repository-namespaces)가 포함될 수도 있습니다. 네임 스페이스를 사용 하면 슬래시 (/)로 구분 된 이름을 사용 하 여 조직에서 관련 리포지토리 및 아티팩트 소유권을 식별할 수 있습니다. 그러나 레지스트리는 계층 구조가 아닌 독립적으로 모든 리포지토리를 관리 합니다. 예를 들면 다음과 같습니다.

- *marketing/campaign10-18/웹: v2*
- *marketing/campaign10-18/api: v3*
- *marketing/campaign10-18/전자 메일-보낸 사람: v2*
- *제품-반환/웹 제출: 20180604*
- *제품 반환/레거시-통합자: 20180715*

리포지토리 이름에는 소문자 영숫자, 마침표, 대시, 밑줄 및 슬래시만 포함할 수 있습니다. 

전체 리포지토리 명명 규칙은 [Open Container 이니셔티브 배포 사양](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)을 참조 하세요.

## <a name="artifact"></a>아티팩트

컨테이너 이미지 또는 레지스트리 내의 다른 아티팩트는 하나 이상의 태그와 연결 되 고 하나 이상의 계층이 있으며 매니페스트로 식별 됩니다. 이러한 구성 요소가 서로 어떻게 관련 되는지에 대 한 이해는 레지스트리를 효과적으로 관리 하는 데 도움이 될 수 있습니다.

### <a name="tag"></a>태그

이미지 또는 다른 아티팩트의 *태그* 는 해당 버전을 지정 합니다. 리포지토리 내의 단일 아티팩트에는 하나 이상의 태그를 할당할 수 있으며 "태그가 지정 되지 않은" 경우도 있습니다. 즉, 이미지의 모든 태그를 삭제할 수 있는 반면 이미지의 데이터 (레이어)는 레지스트리에 남아 있습니다.

리포지토리(또는 리포지토리 및 네임스페이스)와 태그는 이미지의 이름을 정의합니다. 밀어넣기 및 끌어오기 작업에서 해당 이름을 지정하여 이미지를 밀어넣고 끌어올 수 있습니다. 태그는 `latest` Docker 명령에서 제공 하지 않는 경우 기본적으로 사용 됩니다.

컨테이너 이미지에 태그를 표시 하는 방법은 시나리오에 따라 개발 하거나 배포 하는 방법을 안내 합니다. 예를 들어 기본 이미지를 유지 하 고 이미지 배포를 위한 고유한 태그를 유지 하는 데 안정적인 태그를 사용할 수 있습니다. 자세한 내용은 [컨테이너 이미지 태그 지정 및 버전 관리에 대 한 권장 사항](container-registry-image-tag-version.md)을 참조 하세요.

태그 명명 규칙에 대해서는 [Docker 설명서](https://docs.docker.com/engine/reference/commandline/tag/)를 참조 하세요.

### <a name="layer"></a>계층

컨테이너 이미지와 아티팩트는 하나 이상의 *계층* 으로 구성 됩니다. 아티팩트 형식이 다르면 레이어를 다르게 정의 합니다. 예를 들어 Docker 컨테이너 이미지에서 각 레이어는 이미지를 정의 하는 Dockerfile의 줄에 해당 합니다.

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="컨테이너 이미지의 계층":::

레지스트리의 아티팩트는 저장소 효율성을 높여 주는 공통 계층을 공유 합니다. 예를 들어 다른 리포지토리의 여러 이미지는 공통 ASP.NET Core 기본 계층을 가질 수 있지만 해당 계층의 복사본 하나만 레지스트리에 저장 됩니다. 또한 계층 공유는 공용 계층을 공유 하는 여러 아티팩트가 있는 노드에 계층 배포를 최적화 합니다. 노드에 이미 있는 이미지에 ASP.NET Core 계층이 기본으로 포함 된 경우 동일한 계층을 참조 하는 다른 이미지의 후속 끌어오기는 계층을 노드로 전송 하지 않습니다. 대신, 노드에 이미 존재하는 계층을 참조합니다.

보안 격리 및 잠재적 계층 조작 으로부터 보호를 제공 하기 위해 레이어는 레지스트리를 통해 공유 되지 않습니다.

### <a name="manifest"></a>file:///

컨테이너 레지스트리에 푸시되는 각 컨테이너 이미지 또는 아티팩트는 *매니페스트와* 연결 됩니다. 콘텐츠를 푸시할 때 레지스트리에서 생성 된 매니페스트는 아티팩트를 고유 하 게 식별 하 고 계층을 지정 합니다. Azure CLI 명령 [az acr repository show-manifest][az-acr-repository-show-manifests]를 사용 하 여 리포지토리에 대 한 매니페스트를 나열할 수 있습니다. 

Linux 이미지에 대 한 기본 매니페스트는 `hello-world` 다음과 유사 합니다.

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

Azure CLI 명령 [az acr repository show-manifests][az-acr-repository-show-manifests]를 사용하여 리포지토리에 대한 매니페스트를 나열할 수 있습니다.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

예를 들어 "acr-helloworld" 리포지토리의 매니페스트를 나열 합니다.

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

### <a name="manifest-digest"></a>매니페스트 다이제스트

매니페스트는 고유한 SHA-256 해시 또는 *매니페스트 다이제스트* 에 의해 식별됩니다. 각 이미지 또는 아티팩트-태그가 지정 되었는지 아니면 그렇지 않은지는 다이제스트로 식별 됩니다. 다이제스트 값은 아티팩트의 계층 데이터가 다른 아티팩트의 값과 동일한 경우에도 고유 합니다. 이 메커니즘에 따르면 동일하게 태그가 지정된 이미지를 레지스트리에 반복적으로 밀어넣을 수 있습니다. 예를 들어, 각 이미지는 고유한 다이제스트로 식별되므로 `myimage:latest`(을)를 레지스트리에 오류 없이 반복적으로 밀어넣을 수 있습니다.

끌어오기 작업에서 해당 다이제스트를 지정 하 여 레지스트리에서 아티팩트를 끌어올 수 있습니다. 일부 시스템은 동일한 태그가 지정 된 이미지를 나중에 레지스트리에 푸시하는 경우에도 해당 이미지 버전을 가져올 수 있으므로 다이제스트를 통해 가져오도록 구성할 수 있습니다.

> [!IMPORTANT]
> 동일한 태그를 사용 하 여 수정 된 아티팩트를 반복 해 서 푸시하는 경우에는 태그가 없는 "고아"를 만들 수 있지만 여전히 레지스트리에 공간이 사용 됩니다. 태그가 지정되지 않은 이미지는 태그를 기준으로 이미지를 나열하거나 표시할 경우 Azure CLI 또는 Azure Portal에 표시되지 않습니다. 그러나 해당 계층은 여전히 존재하며 레지스트리에서 공간을 차지합니다. 태그가 없는 이미지를 삭제 하면 매니페스트가 유일한 경우 또는 마지막 인 경우 특정 계층을 가리키는 레지스트리 공간을 해제 합니다. 태그 없는 이미지에서 사용 되는 공간을 확보 하는 방법에 대 한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조 하세요.

## <a name="addressing-an-artifact"></a>아티팩트 주소 지정

Docker 또는 다른 클라이언트 도구를 사용 하 여 푸시 및 끌어오기 작업에 대 한 레지스트리 아티팩트를 해결 하려면 정규화 된 레지스트리 이름, 리포지토리 이름 (해당 하는 경우 네임 스페이스 경로 포함), 아티팩트 태그 또는 매니페스트 다이제스트를 조합 합니다. 이러한 용어에 대 한 설명은 이전 섹션을 참조 하세요.

  **태그별 주소**: `[loginServerUrl]/[repository][:tag]`
    
  **다이제스트 기준 주소**: `[loginServerUrl]/[repository@sha256][:digest]`  

Docker 또는 다른 클라이언트 도구를 사용 하 여 아티팩트를 Azure container registry로 끌어오거나 푸시하는 경우 *로그인 서버* 이름이 라고도 하는 레지스트리의 정규화 된 URL을 사용 합니다. Azure cloud에서 Azure container registry의 정규화 된 URL은 `myregistry.azurecr.io` (모두 소문자) 형식으로 되어 있습니다.

> [!NOTE]
> * 레지스트리 로그인 서버 URL (예:)에서 포트 번호를 지정할 수 없습니다 `myregistry.azurecr.io:443` . 
> * 태그를 `latest` 명령에 제공 하지 않으면 기본적으로 태그가 사용 됩니다.  

   
### <a name="push-by-tag"></a>태그로 푸시

예제: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>태그별로 가져오기

예제: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>매니페스트 다이제스트로 끌어오기


예제:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>다음 단계

Azure Container Registry에서 [레지스트리 저장소](container-registry-storage.md) 및 [지원 되는 콘텐츠 형식](container-registry-image-formats.md) 에 대해 자세히 알아보세요.

Azure Container Registry에서 [이미지를 푸시하고 가져오는](container-registry-get-started-docker-cli.md) 방법에 대해 알아봅니다.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


