---
title: Azure Dev Spaces에서 프라이빗 Helm 리포지토리를 사용하는 방법
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Azure Dev Spaces에서 프라이빗 Helm 리포지토리를 사용합니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너, Helm
manager: gwallace
ms.openlocfilehash: 7c5f28595df2e552fd48033b44e4e1f0ea4ec306
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91960340"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Azure Dev Spaces에서 프라이빗 Helm 리포지토리 사용

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

[Helm][helm]은 Kubernetes 패키지 관리자입니다. Helm은 [차트][helm-chart] 형식을 사용하여 종속성을 패키지합니다. Helm 차트는 퍼블릭 또는 프라이빗일 수 있는 리포지토리에 저장됩니다. Azure Dev Spaces는 애플리케이션을 실행할 때 퍼블릭 리포지토리에서만 Helm 차트를 검색합니다. Helm 리포지토리가 프라이빗이거나 Azure Dev Spaces에서 액세스할 수 없는 경우 해당 리포지토리의 차트를 애플리케이션에 직접 추가할 수 있습니다. 차트를 직접 추가하면 Azure Dev Spaces에서 프라이빗 Helm 리포지토리에 액세스하지 않고도 애플리케이션을 실행할 수 있습니다.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>로컬 머신에 프라이빗 Helm 리포지토리 추가

[helm repo add][helm-repo-add] 및 [helm repo update][helm-repo-update]를 사용하여 로컬 머신에서 프라이빗 Helm 리포지토리에 액세스할 수 있습니다.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>애플리케이션에 차트 추가

프로젝트 디렉터리로 이동한 다음 `azds prep`을 실행합니다.

```cmd
azds prep --enable-ingress
```

> [!TIP]
> `prep` 명령은 프로젝트에 대해 [Dockerfile 및 Helm 차트](../how-dev-spaces-works-prep.md#prepare-your-code)를 생성하려고 합니다. Azure Dev Spaces는 이러한 파일을 사용하여 코드를 빌드하고 실행하지만 프로젝트를 빌드하고 실행하는 방법을 변경하려면 이러한 파일을 수정할 수 있습니다.

애플리케이션의 chart 디렉터리에 있는 차트를 사용하여 [requirements.yaml][helm-requirements] 파일을 만듭니다. 예를 들어 애플리케이션 이름이 *app1* 이면 *charts/app1/requirements.yaml* 을 만듭니다.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

애플리케이션의 chart 디렉터리로 이동한 다음 [helm dependency update][helm-dependency-update]를 사용하여 애플리케이션의 Helm 종속성을 업데이트하고 프라이빗 리포지토리에서 차트를 다운로드합니다.

```cmd
helm dependency update
```

*tgz* 파일이 있는 *charts* 하위 디렉터리가 애플리케이션의 chart 디렉터리에 추가되었는지 확인합니다. 예를 들어 *charts/app1/charts/mychart-0.1.0.tgz* 입니다.

프라이빗 Helm 리포지토리의 차트가 다운로드되어 프로젝트에 추가되었습니다. *requirements.yaml* 파일을 제거하여 Dev Spaces에서 해당 종속성을 업데이트하지 않도록 합니다.

## <a name="run-your-application"></a>애플리케이션 실행

프로젝트의 루트 디렉터리로 이동한 다음 `azds up`을 실행하여 개발 공간에서 애플리케이션이 성공적으로 실행되는지 확인합니다.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>다음 단계

[Helm 및 작동 방식][helm]을 자세히 알아봅니다.

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
