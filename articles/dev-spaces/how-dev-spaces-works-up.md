---
title: Azure Dev Spaces 작업을 사용하여 코드를 실행하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces를 사용하여 Azure Kubernetes Service에서 코드를 실행하는 프로세스 설명
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014434"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Azure Dev Spaces 작업을 사용하여 코드를 실행하는 방법

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces를 사용하면 여러 가지 방법을 통해 Kubernetes 애플리케이션을 신속하게 반복하고 디버그할 수 있으며, AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업이 가능합니다. [프로젝트를 개발 공간에서 실행할 준비][how-it-works-prep]가 되면 Dev Spaces를 사용하여 AKS 클러스터에서 프로젝트를 빌드하고 실행할 수 있습니다.

이 문서에서는 Dev Spaces를 사용하여 AKS에서 코드를 실행하는 과정을 설명합니다.

## <a name="run-your-code"></a>코드 실행

개발 공간에서 코드를 실행하려면 `azds.yaml` 파일과 동일한 디렉터리에서 `up` 명령을 실행합니다.

```cmd
azds up
```

`up` 명령은 프로젝트를 빌드하고 실행하는 데 필요한 애플리케이션 원본 파일 및 기타 아티팩트를 개발 공간에 업로드합니다. 여기에서 개발 공간의 컨트롤러는 다음과 같습니다.

1. 애플리케이션을 배포하는 Kubernetes 개체를 만듭니다.
1. 애플리케이션의 컨테이너를 빌드합니다.
1. 개발 공간에 애플리케이션을 배포합니다.
1. 구성된 경우 애플리케이션 엔드포인트에 대해 공개적으로 액세스할 수 있는 DNS 이름을 만듭니다.
1. *port-forward* 를 통해 http://localhost 를 사용하는 애플리케이션 엔드포인트에 대한 액세스를 제공합니다.
1. Stdout 및 Stderr을 클라이언트 쪽 도구에 전달합니다.


## <a name="starting-a-service"></a>서비스 시작

개발 공간에서 서비스를 시작하면 클라이언트 쪽 도구와 컨트롤러가 함께 작동하여 원본 파일을 동기화하고 컨테이너와 Kubernetes 개체를 만들고 애플리케이션을 실행합니다.

보다 세분화된 수준에서 `azds up`을 실행하면 다음과 같은 결과가 발생합니다.

1. 사용자의 컴퓨터에서 사용자의 AKS 클러스터에 고유한 Azure 파일 스토리지로 [파일이 동기화됩니다][sync-section]. 소스 코드, Helm 차트 및 구성 파일이 업로드됩니다.
1. 컨트롤러가 새 세션을 시작하는 요청을 만듭니다. 이 요청에는 고유 ID, 공간 이름, 소스 코드 경로 및 디버깅 플래그를 비롯한 여러 속성이 포함됩니다.
1. 컨트롤러는 Helm 차트에서 *$ (tag)* 자리 표시자를 고유한 세션 ID로 바꾸고 서비스에 대한 Helm 차트를 설치합니다. 고유한 세션 ID에 대한 참조를 Helm 차트에 추가하면 이 특정 세션의 AKS 클러스터에 배포된 컨테이너가 세션 요청 및 관련 정보에 다시 연결될 수 있습니다.
1. Helm 차트를 설치하는 동안 Kubernetes 웹후크 승인 서버는 애플리케이션의 Pod에 추가 컨테이너를 추가하여 프로젝트의 소스 코드에 대한 계측 및 액세스를 수행합니다. devspaces-proxy 및 devspaces-proxy-init 컨테이너가 추가되어 HTTP 추적 및 공간 라우팅을 제공합니다. devspaces-build 컨테이너가 추가되어 애플리케이션의 컨테이너를 빌드하기 위한 Docker 인스턴스 및 프로젝트 소스 코드에 대한 액세스 권한을 Pod에 제공합니다.
1. 애플리케이션의 Pod가 시작되면 devspaces-build 컨테이너와 devspaces-proxy-init 컨테이너가 사용되어 애플리케이션 컨테이너를 빌드합니다. 그러면 애플리케이션 컨테이너와 devspaces-proxy 컨테이너가 시작됩니다.
1. 애플리케이션 컨테이너가 시작되면 클라이언트 쪽 기능은 Kubernetes *port-forward* 기능을 사용하여 http://localhost 를 통해 애플리케이션에 대한 HTTP 액세스를 제공합니다. 이 포트 전달은 개발 컴퓨터를 개발 공간의 서비스에 연결합니다.
1. Pod의 모든 컨테이너가 시작되면 서비스가 실행됩니다. 이 시점에서 클라이언트 쪽 기능은 HTTP 추적, stdout 및 stderr의 스트리밍을 시작합니다. 이 정보는 개발자를 위한 클라이언트 쪽 기능으로 표시됩니다.

## <a name="updating-a-running-service"></a>실행 중인 서비스 업데이트

서비스를 실행하는 동안 프로젝트 원본 파일이 변경될 경우 Azure Dev Spaces는 해당 서비스를 업데이트할 수 있습니다. 또한 변경되는 파일 형식에 따라 서비스 업데이트를 다르게 처리합니다. Dev Spaces는 다음 세 가지 방법으로 실행 중인 서비스를 업데이트할 수 있습니다.

* 파일 직접 업데이트
* 실행 중인 애플리케이션의 컨테이너 내에서 애플리케이션의 프로세스 다시 빌드 및 다시 시작
* 애플리케이션의 컨테이너 다시 빌드 및 다시 배포

![Azure Dev Spaces 파일 동기화](media/how-dev-spaces-works/file-sync.svg)

html, css 및 cshtml 파일과 같은 정적 자산에 해당하는 특정 프로젝트 파일은 아무것도 다시 시작하지 않고 애플리케이션의 컨테이너에서 직접 업데이트할 수 있습니다. 정적 자산이 변경되면 새 파일이 개발 공간에 동기화되고 실행 중인 컨테이너에서 사용됩니다.

소스 코드 또는 애플리케이션 구성 파일과 같은 파일의 변경 내용은 실행 중인 컨테이너 내에서 애플리케이션의 프로세스를 다시 시작하면 적용됩니다. 이러한 파일이 동기화되면 *devhostagent* 프로세스를 사용하여 실행 중인 컨테이너 내에서 애플리케이션의 프로세스가 다시 시작됩니다. 애플리케이션의 컨테이너를 처음 만들 때 컨트롤러는 애플리케이션의 시작 명령을 *devhostagent* 라는 다른 프로세스로 바꿉니다. 그러면 애플리케이션의 실제 프로세스가 *devhostagent* 에서 자식 프로세스로 실행되고 해당 출력은 *devhostagent* 의 출력을 사용하여 전달됩니다. *devhostagent* 프로세스 또한 Dev Spaces의 일부이며 Dev Spaces를 대신하여 실행 중인 컨테이너에서 명령을 실행할 수 있습니다. 다시 시작하는 경우 *devhostagent* 는 다음을 수행합니다.

* 현재 프로세스 또는 애플리케이션과 관련된 프로세스 중지
* 애플리케이션 다시 빌드
* 현재 프로세스 또는 애플리케이션과 관련된 프로세스 다시 시작

*devhostagent* 가 이전 단계를 실행하는 방식은 [`azds.yaml`에서 구성][azds-yaml-section]됩니다.

Dockerfiles, csproj 파일 또는 Helm 차트의 일부와 같은 프로젝트 파일을 업데이트하려면 애플리케이션의 컨테이너를 다시 빌드하고 다시 배포해야 합니다. 이러한 파일 중 하나가 개발 공간에 동기화되면 컨트롤러는 [helm upgrade][helm-upgrade] 명령을 실행하고 애플리케이션의 컨테이너가 다시 작성되고 다시 배포됩니다.

## <a name="file-synchronization"></a>파일 동기화

개발 공간에서 애플리케이션이 처음 시작될 때 모든 애플리케이션의 원본 파일이 업로드됩니다. 애플리케이션이 실행되고 나중에 다시 시작될 때 변경된 파일만 업로드됩니다. 이 프로세스를 조정하는 데 사용되는 두 파일은 클라이언트 쪽 파일과 컨트롤러 쪽 파일입니다.

클라이언트 쪽 파일은 임시 디렉터리에 저장되며 Dev Spaces에서 실행 중인 프로젝트 디렉터리 해시를 기반으로 이름이 지정됩니다. 예를 들어 Windows에서는 프로젝트에 대해 *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* 와 같은 파일이 있습니다. Linux에서는 클라이언트 쪽 파일이 */tmp* 디렉터리에 저장됩니다. macOS에서는 `echo $TMPDIR` 명령을 실행하여 디렉터리를 찾을 수 있습니다.

이 파일은 다음을 포함하는 JSON 형식입니다.

* 개발 공간과 동기화된 각 프로젝트 파일의 항목
* 동기화 ID
* 마지막 동기화 작업의 타임스탬프

각 프로젝트 파일 항목에는 파일 경로 및 해당 타임스탬프가 포함됩니다.

컨트롤러 쪽 파일은 AKS 클러스터에 저장됩니다. 여기에는 마지막 동기화의 동기화 ID와 타임스탬프가 포함됩니다.

동기화는 동기화 타임스탬프가 클라이언트 쪽 파일과 컨트롤러 쪽 파일 간에 일치하지 않을 경우 발생합니다. 동기화하는 동안 클라이언트 쪽 도구는 클라이언트 쪽 파일의 파일 항목을 반복합니다. 파일의 타임스탬프가 동기화 타임스탬프 이후이면 해당 파일이 개발 공간에 동기화됩니다. 동기화가 완료되면 클라이언트 쪽 파일 및 컨트롤러 쪽 파일 모두에서 동기화 타임스탬프가 업데이트됩니다.

클라이언트 쪽 파일이 없는 경우 모든 프로젝트 파일이 동기화됩니다. 이 동작을 통해 클라이언트 쪽 파일을 삭제하여 전체 동기화를 강제로 수행할 수 있습니다.

## <a name="how-running-your-code-is-configured"></a>코드 실행 구성 방법

Azure Dev Spaces는 `azds.yaml` 파일을 사용하여 서비스를 설치하고 구성합니다. 컨트롤러는 `azds.yaml` 파일의 `install` 속성을 사용하여 Helm 차트를 설치하고 Kubernetes 개체를 만듭니다.

```yaml
...
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
...
```

기본적으로 `prep` 명령은 Helm 차트를 생성합니다. 또한 *install.chart* 속성을 Helm 차트의 디렉터리로 설정합니다. 다른 위치에서 Helm 차트를 사용하려면 이 속성을 업데이트하여 해당 위치를 사용할 수 있습니다.

Helm 차트를 설치할 때 Azure Dev Spaces는 Helm 차트에서 값을 재정의하는 방법을 제공합니다. Helm 차트의 기본값은 `charts/APP_NAME/values.yaml`에 있습니다.

*install.values* 속성을 사용하여 Helm 차트에서 바꿀 값을 정의하는 파일을 하나 이상 나열할 수 있습니다. 예를 들어 특별히 개발 공간에서 애플리케이션을 실행할 때 호스트 이름 또는 데이터베이스 구성을 원하는 경우 이 재정의 기능을 사용할 수 있습니다. 또한 *?* 를 파일 이름 끝에 추가하여 선택 사항으로 설정할 수 있습니다.

*install.set* 속성을 사용하면 Helm 차트에서 바꿀 값을 하나 이상 구성할 수 있습니다. *install.set* 에 구성된 모든 값은 *install.values* 에 나열된 파일에 구성된 값을 재정의합니다. *install.set* 아래의 속성은 Helm 차트의 값에 따라 달라지며 생성된 Helm 차트에 따라 다를 수 있습니다.

위의 예제에서 *install.set.replicaCount* 속성은 개발 공간에서 실행할 애플리케이션의 인스턴스 수를 컨트롤러에 알려줍니다. 시나리오에 따라 이 값을 늘릴 수 있지만 애플리케이션의 Pod에 디버거를 연결하는 데 영향을 줍니다. 자세한 내용은 [문제 해결 문서][troubleshooting]를 참조하세요.

생성된 Helm 차트에서 컨테이너 이미지는 *{{ .Values.image.repository }}:{{ .Values.image.tag }}* 로 설정됩니다. `azds.yaml` 파일은 기본적으로 *install.set.image.tag* 속성을 *{{ .Values.image.tag }}* 의 값으로 사용되는 *$(tag)* 로 정의합니다. 이러한 방식으로 *install.set.image.tag* 속성을 설정하면 Azure Dev Spaces를 실행할 때 애플리케이션의 컨테이너 이미지에 고유한 방식으로 태그를 지정할 수 있습니다. 이렇게 특별한 경우 이미지에는 *\<value from image.repository>:$(tag)* 로 태그가 지정됩니다. Dev Spaces가 AKS 클러스터에서 컨테이너를 인식하고 찾으려면 *$(tag)* 변수를 *install.set.image.tag* 값으로 사용해야 합니다.

위의 예제에서는 `azds.yaml`이 *install.set.ingress.hosts* 를 정의합니다. *install.set.ingress.hosts* 속성은 공용 엔드포인트의 호스트 이름 형식을 정의합니다. 이 속성은 컨트롤러에서 제공하는 값인 *$(spacePrefix)* , *$(rootSpacePrefix)* 및 *$(hostSuffix)* 도 사용합니다.

*$(spacePrefix)* 는 *SPACENAME.s* 형식으로 사용되는 자식 개발 공간의 이름입니다. *$(rootSpacePrefix)* 는 부모 공간의 이름입니다. 예를 들어 *azureuser* 가 *default* 의 자식 공간이면 *$(rootSpacePrefix)* 의 값은 *default* 이고 *$(spacePrefix)* 의 값은 *azureuser.s* 입니다. 공간이 자식 공간이 아니면 *$(spacePrefix)* 가 비어 있습니다. 예를 들어 *default* 공간에 부모 공간이 없으면 *$(rootSpacePrefix)* 의 값은 *default* 이고 *$(spacePrefix)* 의 값은 비어 있습니다. *$(hostSuffix)* 는 AKS 클러스터에서 실행되는 Azure Dev Spaces 수신 컨트롤러를 가리키는 DNS 접미사입니다. 이 DNS 접미사는 Azure Dev Spaces 컨트롤러가 AKS 클러스터에 추가될 때 생성된 와일드카드 DNS 항목에 해당합니다(예: *\*.RANDOM_VALUE.eus.azds.io*).

위의 `azds.yaml` 파일에서 *install.set.ingress.hosts* 를 업데이트하여 애플리케이션의 호스트 이름을 변경할 수도 있습니다. 예를 들어 애플리케이션의 호스트 이름을 *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* 에서 *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)* 로 간소화하려는 경우입니다.

애플리케이션의 컨테이너를 빌드하기 위해 컨트롤러는 `azds.yaml` 구성 파일의 아래 섹션을 사용합니다.

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

컨트롤러는 Dockerfile을 사용하여 애플리케이션을 빌드하고 실행합니다.

*build.context* 속성은 Dockerfiles가 있는 디렉터리를 나열합니다. *build.dockerfile* 속성은 애플리케이션의 프로덕션 버전을 빌드하기 위한 Dockerfile의 이름을 정의합니다. *configurations.develop.build.dockerfile* 속성은 애플리케이션 개발 버전의 Dockerfile 이름을 구성합니다.

개발 및 프로덕션에 다른 Dockerfiles를 사용하면 개발 중에 특정 항목을 사용하도록 설정하고 프로덕션 배포에 대해 해당 항목을 사용하지 않도록 설정할 수 있습니다. 예를 들어 개발 중에 디버깅 또는 더욱 자세한 로깅을 사용하도록 설정하고 프로덕션 환경에서는 사용하지 않도록 설정할 수 있습니다. Dockerfiles의 이름이 다르게 지정되거나 다른 위치에 있는 경우 이러한 속성을 업데이트할 수도 있습니다.

개발 중에 신속하게 반복할 수 있도록 Azure Dev Spaces는 로컬 프로젝트의 변경 내용을 동기화하고 애플리케이션을 점진적으로 업데이트합니다. `azds.yaml` 구성 파일의 아래 섹션은 동기화 및 업데이트를 구성하는 데 사용됩니다.

```yaml
...
configurations:
  develop:
    ...
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
...
```

변경 내용을 동기화할 파일 및 디렉터리가 *configurations.develop.container.sync* 속성에 나열됩니다. 이러한 디렉터리들은 `up` 명령을 실행할 때와 변경 내용이 검색될 때 처음 동기화됩니다. 개발 공간에 동기화하려는 추가 디렉터리 또는 다른 디렉터리가 있는 경우 이 속성을 변경할 수 있습니다.

*configurations.develop.container.iterate.buildCommands* 속성은 개발 시나리오에서 애플리케이션을 빌드하는 방법을 지정합니다. *configurations.develop.container.command* 속성은 개발 시나리오에서 애플리케이션을 실행하는 명령을 제공합니다. 개발 중에 사용하려는 추가 빌드, 런타임 플래그 또는 매개 변수가 있는 경우 이러한 속성 중 하나를 업데이트할 수 있습니다.

*configurations.develop.container.iterate.processesToKill* 은 애플리케이션을 중지하기 위해 종료할 프로세스를 나열합니다. 개발 중에 애플리케이션의 다시 시작 동작을 변경하려는 경우 이 속성을 업데이트할 수 있습니다. 예를 들어 *configurations.develop.container.iterate.buildCommands* 또는 *configurations.develop.container.command* 속성을 업데이트하여 애플리케이션을 빌드하거나 시작하는 방법을 변경한 경우 중지되는 프로세스를 변경해야 할 수 있습니다.

`azds prep` 명령을 사용하여 코드를 준비할 때 `--enable-ingress` 플래그를 추가하는 옵션이 있습니다. `--enable-ingress` 플래그를 추가하면 애플리케이션에 대해 공개적으로 액세스할 수 있는 URL이 만들어집니다. 이 플래그를 생략하면 클러스터 내에서 또는 localhost 터널을 통해서만 애플리케이션에 액세스할 수 있습니다. `azds prep` 명령을 실행한 후 `charts/APPNAME/values.yaml`에서 *ingress.enabled* 속성을 수정하여 이 설정을 변경할 수 있습니다.

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>다음 단계

네트워킹 및 Azure Dev Spaces에서 요청이 라우팅되는 방법에 대해 자세히 알아보려면 [Azure Dev Spaces에서 라우팅이 작동하는 방법][how-it-works-routing]을 참조하세요.

Kubernetes를 사용하여 신속하게 반복하고 개발하는 방법을 자세히 알아보려면 [Bridge to Kubernetes 작동 방법][how-it-works-bridge-to-kubernetes] 및 [Azure Dev Spaces로 코드를 원격 디버깅하는 방법][how-it-works-remote-debugging]을 참조하세요.


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md