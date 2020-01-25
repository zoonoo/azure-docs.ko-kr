---
title: Azure Dev Spaces에서 개인 투구 리포지토리를 사용 하는 방법
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Azure Dev 공간에서 개인 투구 리포지토리를 사용 합니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너, 투구
manager: gwallace
ms.openlocfilehash: b1579adc00540a429170027b66c5d3e508bcb5d4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718739"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Azure Dev Spaces에서 개인 투구 리포지토리 사용

[투구][helm] 는 Kuberentes 패키지 관리자입니다. 투구는 [차트][helm-chart] 형식을 사용 하 여 종속성을 패키지 합니다. 투구 차트는 공개 또는 비공개 일 수 있는 리포지토리에 저장 됩니다. Azure Dev Spaces는 응용 프로그램을 실행할 때 공용 리포지토리에서 투구 차트를 검색 합니다. 투구 리포지토리가 개인 이거나 액세스할 수 Azure Dev Spaces 없는 경우 해당 리포지토리의 차트를 응용 프로그램에 직접 추가할 수 있습니다. 차트를 직접 추가 하면 개인 투구 리포지토리에 액세스할 필요 없이 응용 프로그램을 실행할 Azure Dev Spaces 있습니다.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>로컬 컴퓨터에 개인 투구 리포지토리 추가

[투구 리포지토리 추가][helm-repo-add] 및 [투구 리포지토리 업데이트][helm-repo-update] 를 사용 하 여 로컬 컴퓨터에서 개인 투구 리포지토리에 액세스할 수 있습니다.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>응용 프로그램에 차트 추가

프로젝트 디렉터리로 이동 하 여 `azds prep`를 실행 합니다.

```cmd
azds prep --public
```

> [!TIP]
> `prep` 명령은 프로젝트에 대해 [Dockerfile 및 Helm 차트](../how-dev-spaces-works.md#prepare-your-code)를 생성하려고 합니다. Azure Dev Spaces는 이러한 파일을 사용하여 코드를 빌드하고 실행하지만 프로젝트를 빌드하고 실행하는 방법을 변경하려면 이러한 파일을 수정할 수 있습니다.

응용 프로그램의 차트 디렉터리에 차트를 사용 하 여 [요구 사항 .yaml][helm-requirements] 파일을 만듭니다. 예를 들어 응용 프로그램 이름이 *app1*인 경우 *차트/app1/요구 사항을 만듭니다. yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

응용 프로그램의 차트 디렉터리로 이동 하 고 [투구 종속성 업데이트][helm-dependency-update] 를 사용 하 여 응용 프로그램에 대 한 투구 종속성을 업데이트 하 고 개인 리포지토리에서 차트를 다운로드 합니다.

```cmd
helm dependency update
```

*Tgz* 파일이 있는 *차트* 하위 디렉터리가 응용 프로그램의 차트 디렉터리에 추가 되었는지 확인 합니다. 예: *차트/app1/차트/mychart-0.1.0. tgz*.

개인 투구 리포지토리의 차트가 다운로드 되어 프로젝트에 추가 되었습니다. Dev 공간에서이 종속성을 업데이트 하지 않도록 *요구 사항 .yaml* 파일을 제거 합니다.

## <a name="run-your-application"></a>애플리케이션 실행

프로젝트의 루트 디렉터리로 이동 하 고 `azds up`를 실행 하 여 개발 공간에서 응용 프로그램이 성공적으로 실행 되는지 확인 합니다.

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

[투구 및 작동 방식][helm]에 대해 자세히 알아보세요.

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies