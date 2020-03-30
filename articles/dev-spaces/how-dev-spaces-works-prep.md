---
title: Azure 개발자 공간에 대한 프로젝트 준비의 작동 방식
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure 개발자 공간을 통해 프로젝트 준비의 작동 방식에 대해 설명합니다.
keywords: azds.yaml, Azure 개발자 공간, 개발자 공간, 도커, 쿠베네츠, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241635"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Azure 개발자 공간에 대한 프로젝트 준비의 작동 방식

Azure 개발자 공간은 Kubernetes 응용 프로그램을 빠르게 반복 및 디버깅하고 AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업하는 여러 가지 방법을 제공합니다. 개발자 공간은 프로젝트에 대한 도커 파일 및 투구 차트를 생성할 수 있습니다. 또한 Dev Spaces는 AKS에서 Kubernetes 응용 프로그램을 배포, 실행 및 디버깅하기 위한 구성 파일을 만들고 사용합니다. 이러한 모든 파일은 응용 프로그램의 코드에 상주하며 버전 제어 시스템에 추가할 수 있습니다.

이 문서에서는 개발자 공간을 통해 AKS에서 실행하기 위해 프로젝트를 준비하는 상황에 대해 설명합니다.

## <a name="prepare-your-code"></a>코드 준비

개발 공간에서 응용 프로그램을 실행하려면 컨테이너화해야 하며 Kubernetes에 배포하는 방법을 정의해야 합니다. 응용 프로그램을 컨테이너화하려면 Dockerfile이 필요합니다. 응용 프로그램이 Kubernetes에 배포되는 방법을 정의하려면 [Helm 차트가](https://docs.helm.sh/)필요합니다. 응용 프로그램에 대한 Dockerfile 및 Helm 차트를 모두 만드는 데 `prep` 도움이 되는 클라이언트 쪽 도구는 다음 명령을 제공합니다.

```cmd
azds prep --enable-ingress
```

명령은 `prep` 프로젝트의 파일을 보고 Kubernetes에서 응용 프로그램을 실행하기 위한 Dockerfile 및 Helm 차트를 만들려고 합니다. 현재 `prep` 명령은 다음과 같은 언어로 Dockerfile 및 Helm 차트를 생성합니다.

* Java
* Node.js
* .NET Core

소스 코드가 `prep` 포함된 디렉터리에서 명령을 *실행해야 합니다.* 올바른 `prep` 디렉터리에서 명령을 실행하면 클라이언트 측 도구가 언어를 식별하고 적절한 Dockerfile을 만들어 응용 프로그램을 컨테이너화할 수 있습니다. Java 프로젝트에 대한 `prep` *pom.xml* 파일이 포함된 디렉터리에서 명령을 실행할 수도 있습니다.

소스 코드를 `prep` 포함하지 않는 디렉터리에서 명령을 실행하는 경우 클라이언트 측 도구는 Dockerfile을 생성하지 않습니다. 또한 *지원되지 않는 언어로 인해 Dockerfile을 생성 할 수 없다는*오류가 표시됩니다. 이 오류는 클라이언트 측 툴링이 프로젝트 유형을 인식하지 못하는 경우에도 발생합니다.

`prep` 명령을 실행하면 플래그를 지정할 수 있습니다. `--enable-ingress` 이 플래그는 컨트롤러가 이 서비스에 대해 인터넷에 액세스할 수 있는 끝점을 만들도록 지시합니다. 이 플래그를 지정하지 않으면 서비스는 클러스터 내에서 또는 클라이언트 측 툴링에 의해 생성된 localhost 터널에서만 액세스할 수 있습니다. 생성된 Helm 차트를 업데이트하여 `prep` 명령을 실행한 후 이 동작을 활성화하거나 비활성화할 수 있습니다.

이 `prep` 명령은 프로젝트에 있는 기존 Dockerfiles 또는 Helm 차트를 대체하지 않습니다. 기존 Dockerfile 또는 Helm 차트에서 `prep` 명령에 의해 생성된 파일과 동일한 `prep` 명명 규칙을 사용하는 경우 명령은 해당 파일 생성을 건너뜁니다. 그렇지 않으면 `prep` 명령은 기존 파일 옆에 자체 Dockerfile 또는 Helm 차트를 생성합니다.

> [!IMPORTANT]
> Azure 개발자 공간에서는 프로젝트에 Dockerfile 및 Helm 차트를 사용하여 코드를 빌드하고 실행하지만 프로젝트를 빌드하고 실행하는 방법을 변경하려면 이러한 파일을 수정할 수 있습니다.

명령은 `prep` 프로젝트의 루트에 `azds.yaml` 있는 파일도 생성합니다. Azure 개발자 공간에서는 이 파일을 사용하여 응용 프로그램을 빌드, 설치, 구성 및 실행합니다. 이 구성 파일은 Dockerfile 및 Helm 차트의 위치를 나열하고 이러한 아티팩트 위에 추가 구성을 제공합니다.

다음은 [.NET Core 샘플 응용 프로그램을](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)사용하여 만든 azds.yaml 파일 예제입니다.

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

명령에 의해 생성된 파일은 `azds.yaml` 간단한 단일 프로젝트 개발 시나리오에서 작동하도록 되어 있습니다. `prep` 특정 프로젝트의 복잡성이 증가한 경우 `prep` 명령을 실행한 후 이 파일을 업데이트해야 할 수 있습니다. 예를 들어 프로젝트에 개발 또는 디버깅 요구에 따라 빌드 또는 시작 프로세스를 일부 변경해야 할 수 있습니다. 또한 프로젝트에 여러 빌드 프로세스 또는 다른 빌드 콘텐츠가 필요한 여러 응용 프로그램이 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

개발 공간에서 코드를 실행하는 방법에 대해 자세히 알아보려면 [Azure 개발자 공간에서 코드를 실행하는 방법을 참조하세요.][how-it-works-up]

Azure 개발자 공간을 사용하여 Azure 개발자 공간에 대한 프로젝트를 준비하려면 다음 빠른 시작을 참조하세요.

* [Visual Studio 코드 및 Java를 통해 빠르게 반복 및 디버깅][quickstart-java]
* [Visual Studio 코드 및 .NET을 통해 빠르게 반복 및 디버깅][quickstart-netcore]
* [Visual Studio 코드 및 Node.js로 빠르게 반복 및 디버깅][quickstart-node]
* [Visual Studio 및 .NET Core를 통해 빠르게 반복 및 디버깅][quickstart-vs]
* [CLI를 사용하여 Kubernetes에서 응용 프로그램 개발][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md