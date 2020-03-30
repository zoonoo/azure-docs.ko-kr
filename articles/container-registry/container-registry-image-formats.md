---
title: 지원되는 콘텐츠 형식
description: Docker 호환 컨테이너 이미지, 헬름 차트, OCI 이미지 및 OCI 아티팩트를 포함하여 Azure 컨테이너 레지스트리에서 지원하는 콘텐츠 형식에 대해 알아봅니다.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247008"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry에서 지원되는 콘텐츠 서식

Azure Container Registry에서 프라이빗 리포지토리를 사용하여 다음 콘텐츠 서식 중 하나를 관리합니다. 

## <a name="docker-compatible-container-images"></a>Docker 호환 컨테이너 이미지

다음 Docker 컨테이너 이미지 형식이 지원됩니다.

* [Docker 이미지 매니페스트 V2, 스키마 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 이미지 매니페스트 V2, 스키마 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 레지스트리가 단일 "image:tag" 참조에서 다중 플랫폼 이미지를 저장할 수 있는 매니페스트 목록이 포함됩니다.

## <a name="oci-images"></a>OCI 이미지

Azure 컨테이너 레지스트리는 [OCI(개방형 컨테이너 이니셔티브) 이미지 형식 사양을](https://github.com/opencontainers/image-spec/blob/master/spec.md)충족하는 이미지를 지원합니다. 패키징 형식에는 [특이점 이미지 형식(SIF)이](https://github.com/sylabs/sif)포함됩니다.

## <a name="oci-artifacts"></a>OCI 유물

Azure 컨테이너 레지스트리는 컨테이너 이미지 및 기타 콘텐츠 유형(아티팩트)을 저장, 공유, 보안 및 배포하는 공급업체 중립적인 클라우드 에 구애받지 않는 SPEC인 [OCI 배포 사양을](https://github.com/opencontainers/distribution-spec)지원합니다. 이 사양을 통해 레지스트리는 컨테이너 이미지 외에도 다양한 아티팩트를 저장할 수 있습니다. 아티팩트에 적합한 툴링을 사용하여 아티팩트를 밀고 당깁니다. 예를 들어 [Azure 컨테이너 레지스트리를 사용하여 OCI 아티팩트를 푸시하고 끌어올릴](container-registry-oci-artifacts.md)수 있습니다.

OCI 아티팩트에 대한 자세한 내용은 [OCI 레지스트리를 저장소(ORAS)](https://github.com/deislabs/oras) 리포지토리로, GitHub의 [OCI 아티팩트 리포지토리를](https://github.com/opencontainers/artifacts) 참조하십시오.

## <a name="helm-charts"></a>Helm 차트

Azure 컨테이너 레지스트리는 Kubernetes에 대한 응용 프로그램을 신속하게 관리하고 배포하는 데 사용되는 패키징 형식인 [Helm 차트에](https://helm.sh/)대한 리포지토리를 호스팅할 수 있습니다. [Helm 클라이언트](https://docs.helm.sh/using_helm/#installing-helm) 버전 2(2.11.0 이상)가 지원됩니다.

## <a name="next-steps"></a>다음 단계

* Azure Container Registry를 사용하여 이미지를 [푸시하고 끌어오는](container-registry-get-started-docker-cli.md) 방법을 참조하세요.

* [ACR 작업](container-registry-tasks-overview.md)을 사용하여 컨테이너 이미지를 빌드하고 테스트합니다. 

* [ BuildKit](https://github.com/moby/buildkit)를 사용하여 OCI 형식으로 컨테이너를 빌드하고 패키징합니다.

* Azure Container Registry에서 호스트되는 [Helm 리포지토리](container-registry-helm-repos.md)를 설정합니다. 


