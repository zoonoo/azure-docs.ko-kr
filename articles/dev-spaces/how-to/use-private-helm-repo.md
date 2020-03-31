---
title: Azure 개발자 공간에서 개인 헬름 리포지토리를 사용하는 방법
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Azure 개발자 공간에서 개인 헬름 리포지토리를 사용합니다.
keywords: 도커, 쿠베네츠, Azure, AKS, Azure 컨테이너 서비스, 컨테이너, 헬름
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240464"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Azure 개발자 공간에서 개인 헬름 리포지토리 사용

[헬름은][helm] 쿠베네츠의 패키지 매니저입니다. Helm은 [차트][helm-chart] 형식을 사용하여 종속성을 패키지합니다. helm 차트는 공용 또는 비공개일 수 있는 리포지토리에 저장됩니다. Azure 개발자 공백은 응용 프로그램을 실행할 때 공용 리포지토리에서만 Helm 차트를 검색합니다. Helm 리포지토리가 비공개이거나 Azure 개발자 공간에서 액세스할 수 없는 경우 해당 리포지토리에서 응용 프로그램에 직접 차트를 추가할 수 있습니다. 차트를 직접 추가하면 Azure 개발자 공간에서 개인 Helm 리포지토리에 액세스할 필요 없이 응용 프로그램을 실행할 수 있습니다.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>로컬 컴퓨터에 개인 Helm 리포지토리 추가

[helm 리포지토리 추가][helm-repo-add] 및 투구 [리포지토리 업데이트를][helm-repo-update] 사용하여 로컬 컴퓨터에서 개인 Helm 리포지토리에 액세스할 수 있습니다.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>응용 프로그램에 차트 추가

프로젝트 디렉터리로 이동하여 실행합니다. `azds prep`

```cmd
azds prep --enable-ingress
```

> [!TIP]
> `prep` 명령은 프로젝트에 대해 [Dockerfile 및 Helm 차트](../how-dev-spaces-works-prep.md#prepare-your-code)를 생성하려고 합니다. Azure Dev Spaces는 이러한 파일을 사용하여 코드를 빌드하고 실행하지만 프로젝트를 빌드하고 실행하는 방법을 변경하려면 이러한 파일을 수정할 수 있습니다.

응용 프로그램의 차트 디렉토리에 차트가 있는 [requirements.yaml][helm-requirements] 파일을 만듭니다. 예를 들어 응용 프로그램 이름이 *app1인*경우 *차트/app1/requirements.yaml을 만듭니다.*

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

응용 프로그램의 차트 디렉토리로 이동하여 [helm 종속성 업데이트를][helm-dependency-update] 사용하여 응용 프로그램에 대한 Helm 종속성을 업데이트하고 개인 리포지토리에서 차트를 다운로드합니다.

```cmd
helm dependency update
```

*tgz* 파일이 있는 *차트* 하위 디렉터리가 응용 프로그램의 차트 디렉터리에 추가되었는지 확인합니다. 예를 들어 *차트/app1/차트/마이차트-0.1.0.tgz.*

개인 Helm 리포지토리의 차트가 다운로드되어 프로젝트에 추가되었습니다. 개발자 공백이 이 종속성을 업데이트하지 않도록 *요구 사항.yaml* 파일을 제거합니다.

## <a name="run-your-application"></a>애플리케이션 실행

프로젝트의 루트 디렉토리로 이동하여 `azds up` 실행하여 개발 공간에서 응용 프로그램이 성공적으로 실행되는지 확인합니다.

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

[Helm및 작동 방식에][helm]대해 자세히 알아보십시오.

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
