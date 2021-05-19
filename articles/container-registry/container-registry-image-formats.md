---
title: 지원되는 콘텐츠 형식
description: Docker 호환 컨테이너 이미지, Helm 차트, OCI 이미지 및 OCI 아티팩트를 비롯하여 Azure Container Registry에서 지원하는 콘텐츠 형식에 대해 알아봅니다.
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 218d98f3f16e8d0ca76a24692afbb2b69606564b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223067"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry에서 지원되는 콘텐츠 서식

Azure Container Registry에서 프라이빗 리포지토리를 사용하여 다음 콘텐츠 서식 중 하나를 관리합니다. 

## <a name="docker-compatible-container-images"></a>Docker 호환 컨테이너 이미지

다음 Docker 컨테이너 이미지 형식이 지원됩니다.

* [Docker 이미지 매니페스트 V2, 스키마 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 이미지 매니페스트 V2, 스키마 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 레지스트리가 단일 `image:tag` 참조 아래에 [다중 아키텍처 이미지](push-multi-architecture-images.md)를 저장할 수 있는 매니페스트 목록을 포함합니다.

## <a name="oci-images"></a>OCI 이미지

Azure Container Registry는 선택적 [이미지 인덱스](https://github.com/opencontainers/image-spec/blob/master/image-index.md) 사양을 비롯하여 [OCI(Open Container Initiative) 이미지 형식 사양](https://github.com/opencontainers/image-spec/blob/master/spec.md)을 충족하는 이미지를 지원합니다. 패키징 형식에는 [SIF(Singularity Image Format)](https://github.com/sylabs/sif)가 포함됩니다.

## <a name="oci-artifacts"></a>OCI 아티팩트

Azure Container Registry는 컨테이너 이미지 및 기타 콘텐츠 형식(아티팩트)을 저장, 공유, 보호 및 배포할 수 있는 공급 업체 중립적 클라우드에 구애받지 않는 사양인 [OCI 배포 사양](https://github.com/opencontainers/distribution-spec)을 지원합니다. 사양을 통해 레지스트리는 컨테이너 이미지 외에도 광범위한 아티팩트를 저장할 수 있습니다. 아티팩트를 푸시하고 풀하는 데 적합한 도구를 사용합니다. 예제는 [Azure 컨테이너 레지스트리를 사용하여 OCI 아티팩트 푸시 및 끌어오기](container-registry-oci-artifacts.md)를 참조하세요.

OCI 아티팩트에 대해 자세히 알아보려면 [ORAS(OCI Registry as Storage)](https://github.com/deislabs/oras) 리포지토리 및 GitHub의 [OCI 아티팩트](https://github.com/opencontainers/artifacts) 리포지토리를 참조하세요.

## <a name="helm-charts"></a>Helm 차트

Azure Container Registry는 Kubernetes용 애플리케이션을 신속하게 관리하고 배포하는 데 사용되는 패키징 형식인 [Helm 차트](https://helm.sh/)에 대한 리포지토리를 호스팅할 수 있습니다. [Helm 클라이언트](https://docs.helm.sh/using_helm/#installing-helm) 버전 3을 사용하는 것이 좋습니다. [Azure 컨테이너 레지스트리에 Helm 차트 푸시 및 끌어오기](container-registry-helm-repos.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Container Registry를 사용하여 이미지를 [푸시하고 끌어오는](container-registry-get-started-docker-cli.md) 방법을 참조하세요.

* [ACR 작업](container-registry-tasks-overview.md)을 사용하여 컨테이너 이미지를 빌드하고 테스트합니다. 

* [ BuildKit](https://github.com/moby/buildkit)를 사용하여 OCI 형식으로 컨테이너를 빌드하고 패키징합니다.

* Azure Container Registry에서 호스트되는 [Helm 리포지토리](container-registry-helm-repos.md)를 설정합니다. 


