---
title: About repositories & images
description: Introduction to key concepts of Azure container registries, repositories, and container images.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455281"
---
# <a name="about-registries-repositories-and-images"></a>About registries, repositories, and images

This article introduces the key concepts of container registries, repositories, and container images and related artifacts. 

## <a name="registry"></a>레지스트리

컨테이너 *레지스트리*는 컨테이너 이미지를 저장하고 배포하는 서비스입니다. Docker Hub is a public container registry that supports the open source community and serves as a general catalog of images. Azure Container Registry provides users with direct control of their images, with integrated authentication, [geo-replication](container-registry-geo-replication.md) supporting global distribution and reliability for network-close deployments, [virtual network and firewall configuration](container-registry-vnet.md), [tag locking](container-registry-image-lock.md), and many other enhanced features. 

In addition to Docker container images, Azure Container Registry supports related [content artifacts](container-registry-image-formats.md) including Open Container Initiative (OCI) image formats.

## <a name="content-addressable-elements-of-an-artifact"></a>Content addressable elements of an artifact

The address of an artifact in an Azure container registry includes the following elements. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** - The fully qualified name of the registry host. The registry host in an Azure container registry is in the format *myregistry*.azurecr.io (all lowercase). You must specify the loginUrl when using Docker or other client tools to pull or push artifacts to an Azure container registry. 
* **namespace** - Slash-delimited logical grouping of related images or artifacts - for example, for a workgroup or app
* **artifact** - The name of a repository for a particular image or artifact
* **tag** - A specific version of an image or artifact stored in a repository


For example, the full name of an image in an Azure container registry might look like:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

See the following sections for details about these elements.

## <a name="repository-name"></a>리포지토리 이름

Container registries manage *repositories*, collections of container images or other artifacts with the same name, but different tags. 예를 들어, 다음 3개의 이미지가 "helloworld acr" 리포지토리에 있습니다.

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

리포지토리 이름에 [네임스페이스](container-registry-best-practices.md#repository-namespaces)가 포함될 수도 있습니다. Namespaces allow you to group images using forward slash-delimited repository names, for example:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>이미지

A container image or other artifact within a registry is associated with one or more tags, has one or more layers, and is identified by a manifest. Understanding how these components relate to each other can help you manage your registry effectively.

### <a name="tag"></a>태그

The *tag* for an image or other artifact specifies its version. A single artifact within a repository can be assigned one or many tags, and may also be "untagged." That is, you can delete all tags from an image, while the image's data (its layers) remain in the registry.

리포지토리(또는 리포지토리 및 네임스페이스)와 태그는 이미지의 이름을 정의합니다. 밀어넣기 및 끌어오기 작업에서 해당 이름을 지정하여 이미지를 밀어넣고 끌어올 수 있습니다.

How you tag container images is guided by your scenarios to develop or deploy them. For example, stable tags are recommended for maintaining your base images, and unique tags for deploying images. For more information, see [Recommendations for tagging and versioning container images](container-registry-image-tag-version.md).

### <a name="layer"></a>계층

Container images are made up of one or more *layers*, each corresponding to a line in the Dockerfile that defines the image. 레지스트리의 이미지는 공용 계층을 공유하므로 스토리지 효율성이 높아집니다. 예를 들어, 다른 리포지토리에 있는 여러 이미지가 동일한 Alpine Linux 기본 계층을 공유할 수 있지만 해당 계층의 복사본 하나만 레지스트리에 저장됩니다.

계층 공유는 여러 개의 이미지가 공통 계층을 공유하는 방식으로 노드에 대한 계층 분산을 최적화합니다. 예를 들어, 노드에 이미 있는 이미지가 맨 아래에 Alpine Linux 계층을 포함하는 경우 이후에 같은 계층을 참조하는 다른 이미지를 끌어올 경우 해당 계층이 노드로 전송되지 않습니다. 대신, 노드에 이미 존재하는 계층을 참조합니다.

To provide secure isolation and protection from potential layer manipulation, layers are not shared across registries.

### <a name="manifest"></a>매니페스트

Each container image or artifact pushed to a container registry is associated with a *manifest*. 이미지를 밀어넣을 때 레지스트리에서 생성된 매니페스트는 이미지를 고유하게 식별하고 해당 계층을 지정합니다. You can list the manifests for a repository with the Azure CLI command [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

For example, list the manifests for the "acr-helloworld" repository:

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

### <a name="manifest-digest"></a>매니페스트 다이제스트

매니페스트는 고유한 SHA-256 해시 또는 *매니페스트 다이제스트*에 의해 식별됩니다. Each image or artifact--whether tagged or not--is identified by its digest. 다이제스트 값은 이미지의 계층 데이터가 다른 이미지의 계층 데이터와 동일하더라도 고유합니다. 이 메커니즘에 따르면 동일하게 태그가 지정된 이미지를 레지스트리에 반복적으로 밀어넣을 수 있습니다. 예를 들어, 각 이미지는 고유한 다이제스트로 식별되므로 `myimage:latest`(을)를 레지스트리에 오류 없이 반복적으로 밀어넣을 수 있습니다.

끌어오기 작업에서 해당 다이제스트를 지정하여 레지스트리에서 이미지를 끌어올 수 있습니다. 일부 시스템은 다이제스트별로 끌어오도록 구성이 가능합니다. 동일하게 태그가 지정된 이미지를 나중에 레지스트리로 밀어넣더라도 해당 이미지 버전 끌어오기가 보장되기 때문입니다.

For example, pull an image from the "acr-helloworld" repository by manifest digest:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 동일한 태그가 지정된 이미지를 수정 후 반복적으로 밀어넣을 경우 태그가 지정되지 않은 이미지는 별도로 분리되어 생성될 수 있지만 이 이미지는 여전히 레지스트리에서 공간을 차지합니다. 태그가 지정되지 않은 이미지는 태그를 기준으로 이미지를 나열하거나 표시할 경우 Azure CLI 또는 Azure Portal에 표시되지 않습니다. 그러나 해당 계층은 여전히 존재하며 레지스트리에서 공간을 차지합니다. Deleting an untagged image frees registry space when the manifest is the only one, or the last one, pointing to a particular layer. For information about freeing space used by untagged images, see [Delete container images in Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>다음 단계

Learn more about [image storage](container-registry-storage.md) and [supported content formats](container-registry-image-formats.md) in Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


