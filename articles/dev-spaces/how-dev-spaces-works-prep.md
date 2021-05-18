---
title: Azure Dev Spaces에 대한 프로젝트 준비 소개
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces를 사용한 프로젝트 준비가 어떻게 작동하는지를 설명합니다.
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: d2da69dd8a8c2683ff584dfd0ffc61cb023f2ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91968160"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Azure Dev Spaces에 대한 프로젝트 준비 소개

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces를 사용하면 여러 가지 방법을 통해 Kubernetes 애플리케이션을 신속하게 반복하고 디버그할 수 있으며, AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업이 가능합니다. Dev Spaces는 프로젝트의 Dockerfile 및 Helm 차트를 생성할 수 있습니다. 또한 Dev Spaces는 구성 파일을 만들고 AKS에서 Kubernetes 애플리케이션을 배포, 실행, 디버그하는 데 사용합니다. 모든 파일은 애플리케이션 코드와 함께 상주하며 버전 제어 시스템에 추가할 수 있습니다.

이 문서에서는 Dev Spaces를 사용하여 AKS에서 실행되도록 프로젝트를 준비하면 어떻게 되는지를 설명합니다.

## <a name="prepare-your-code"></a>코드 준비

개발 공간에서 애플리케이션을 실행하려면 애플리케이션을 컨테이너화해야 하며, Kubernetes에 배포하는 방법을 정의해야 합니다. 애플리케이션을 컨테이너화하려면 Dockerfile이 필요합니다. Kubernetes에 애플리케이션을 배포하는 방법을 정의하려면 [Helm 차트](https://docs.helm.sh/)가 필요합니다. 애플리케이션의 Dockerfile 및 Helm 차트 생성을 지원하기 위해 클라이언트 쪽 도구는 `prep` 명령을 제공합니다.

```cmd
azds prep --enable-ingress
```

`prep` 명령은 프로젝트의 파일을 확인하고 Kubernetes에서 애플리케이션을 실행하는 데 필요한 Dockerfile 및 Helm 차트를 만들려고 합니다. 현재 `prep` 명령은 다음 언어로 Dockerfile 및 Helm 차트를 생성합니다.

* Java
* Node.js
* .NET Core

소스 코드가 포함된 디렉터리에서 `prep` 명령을 ‘실행해야’ 합니다. 올바른 디렉터리에서 `prep` 명령을 실행하면 클라이언트 쪽 도구가 언어를 식별하고 적절한 Dockerfile을 만들어 애플리케이션을 컨테이너화할 수 있습니다. Java 프로젝트용 *pom.xml* 파일이 포함된 디렉터리에서 `prep` 명령을 실행할 수도 있습니다.

소스 코드가 포함되지 않은 디렉터리에서 `prep` 명령을 실행하면 클라이언트 쪽 도구가 Dockerfile을 생성하지 않습니다. ‘지원되지 않는 언어로 인해 Dockerfile을 생성할 수 없습니다.’라는 오류도 표시됩니다. 클라이언트 쪽 도구가 프로젝트 형식을 인식할 수 없는 경우에도 이 오류가 발생합니다.

`prep` 명령을 실행할 때 `--enable-ingress` 플래그를 지정할 수 있습니다. 이 플래그는 인터넷에서 액세스할 수 있는 서비스 엔드포인트를 만들도록 컨트롤러에 지시합니다. 이 플래그를 지정하지 않으면 서비스는 클러스터 내에서만 액세스할 수 있거나 클라이언트 쪽 도구로 만든 localhost 터널을 통해서만 액세스할 수 있습니다. `prep` 명령을 실행한 후 생성된 Helm 차트를 업데이트하여 이 동작을 사용하거나 사용하지 않도록 설정할 수 있습니다.

`prep` 명령은 프로젝트의 기존 Dockerfiles 또는 Helm 차트를 바꾸지 않습니다. 기존 Dockerfile 또는 Helm 차트에서 `prep` 명령을 통해 생성된 파일과 동일한 명명 규칙을 사용하는 경우 `prep` 명령은 해당 파일 생성을 건너뜁니다. 그렇지 않으면 `prep` 명령은 기존 파일과 별도로 자체 Dockerfile 또는 Helm 차트를 생성합니다.

> [!IMPORTANT]
> Azure Dev Spaces는 프로젝트의 Dockerfile 및 Helm 차트를 사용하여 코드를 빌드하고 실행하지만, 프로젝트를 빌드하고 실행하는 방법을 변경하려는 경우 해당 파일을 수정할 수 있습니다.

`prep` 명령은 프로젝트의 루트에 `azds.yaml` 파일도 생성합니다. Azure Dev Spaces는 이 파일을 사용하여 애플리케이션을 빌드, 설치, 구성, 실행합니다. 이 구성 파일은 Dockerfile 및 Helm 차트의 위치를 나열하고 해당 아티팩트를 토대로 추가 구성도 제공합니다.

다음은 [.Net Core 샘플 애플리케이션](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)을 사용하여 만든 예제 azds.yaml 파일입니다.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`prep` 명령을 통해 생성된 `azds.yaml` 파일은 간단한 단일 프로젝트 개발 시나리오에 사용할 수 있습니다. 특정 프로젝트의 복잡성이 증가한 경우에는 `prep` 명령을 실행한 후 해당 파일을 업데이트해야 할 수도 있습니다. 예를 들어 개발 또는 디버깅 요구 사항에 따라 프로젝트에서 빌드 또는 시작 프로세스를 변경해야 할 수 있습니다. 프로젝트에 여러 빌드 프로세스나 다른 빌드 콘텐츠가 필요한 여러 애플리케이션이 있을 수도 있습니다.

## <a name="next-steps"></a>다음 단계

개발 공간에서 코드를 실행하는 방법에 대한 자세한 내용은 [Azure Dev Spaces를 사용한 코드 실행 소개][how-it-works-up]를 참조하세요.

[how-it-works-up]: how-dev-spaces-works-up.md