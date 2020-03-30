---
title: Azure 개발자 공간에서 코드를 실행하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure 개발자 공간을 통해 Azure Kubernetes 서비스에서 코드를 실행하는 프로세스에 대해 설명합니다.
keywords: azds.yaml, Azure 개발자 공간, 개발자 공간, 도커, 쿠베네츠, Azure, AKS, Azure Kubernetes 서비스, 컨테이너
ms.openlocfilehash: 6851c04ac0b72db1bd13c991875c16b0beadc573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241362"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Azure 개발자 공간에서 코드를 실행하는 방법

Azure 개발자 공간은 Kubernetes 응용 프로그램을 빠르게 반복 및 디버깅하고 AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업하는 여러 가지 방법을 제공합니다. 개발 [공간에서 프로젝트를 실행할 준비가][how-it-works-prep]되면 개발자 공간을 사용하여 AKS 클러스터에서 프로젝트를 빌드하고 실행할 수 있습니다.

이 문서에서는 개발자 공간을 통해 AKS에서 코드를 실행하는 작업을 설명합니다.

## <a name="run-your-code"></a>코드 실행

개발자 공간에서 코드를 실행하려면 파일과 `up` 동일한 디렉터리에서 명령을 `azds.yaml` 실행합니다.

```cmd
azds up
```

이 `up` 명령은 프로젝트를 빌드하고 개발 공간으로 실행하는 데 필요한 응용 프로그램 원본 파일 및 기타 아티팩트를 업로드합니다. 거기에서, 당신의 개발 공간에 있는 컨트롤러:

1. 응용 프로그램을 배포할 Kubernetes 개체를 만듭니다.
1. 응용 프로그램에 대한 컨테이너를 빌드합니다.
1. 개발 공간에 응용 프로그램을 배포합니다.
1. 구성된 경우 응용 프로그램 끝점에 대해 공개적으로 액세스할 수 있는 DNS 이름을 만듭니다.
1. 을 사용하여 응용 프로그램 끝점에 대한 http://localhost액세스를 제공하기 위해 *포트 포워드를* 사용합니다.
1. 클라이언트 측 툴링으로 stdout 및 stderr를 전달합니다.


## <a name="starting-a-service"></a>서비스 시작

개발 공간에서 서비스를 시작하면 클라이언트 측 툴링 및 컨트롤러가 조정되어 소스 파일을 동기화하고 컨테이너 및 Kubernetes 개체를 만들고 응용 프로그램을 실행합니다.

보다 세분화된 수준에서 실행할 `azds up`때 발생하는 일은 다음과 같습니다.

1. [파일은][sync-section] 사용자의 컴퓨터에서 사용자의 AKS 클러스터에 고유한 Azure 파일 저장소로 동기화됩니다. 소스 코드, 헬름 차트 및 구성 파일이 업로드됩니다.
1. 컨트롤러는 새 세션을 시작하도록 요청을 만듭니다. 이 요청에는 고유 ID, 공간 이름, 소스 코드 경로 및 디버깅 플래그를 비롯한 여러 속성이 포함되어 있습니다.
1. 컨트롤러는 Helm 차트의 *$(태그)* 자리 표시자를 고유 세션 ID로 바꾸고 서비스에 대한 Helm 차트를 설치합니다. Helm 차트에 고유 세션 ID에 대한 참조를 추가하면 이 특정 세션에 대해 AKS 클러스터에 배포된 컨테이너를 세션 요청 및 관련 정보에 다시 연결할 수 있습니다.
1. Helm 차트를 설치하는 동안 Kubernetes 웹후크 설치 서버는 계측 및 프로젝트 소스 코드에 대한 액세스를 위해 응용 프로그램의 포드에 추가 컨테이너를 추가합니다. HTTP 추적 및 공간 라우팅을 제공하기 위해 devspaces-프록시 및 devspace-프록시-init 컨테이너가 추가됩니다. 개발자 공간 빌드 컨테이너가 추가되어 포드에 Docker 인스턴스에 대한 액세스 권한을 제공하고 응용 프로그램의 컨테이너를 빌드하기 위한 프로젝트 소스 코드를 제공합니다.
1. 응용 프로그램의 포드가 시작되면 devspaces-build 컨테이너 및 devspaces-proxy-init 컨테이너를 사용하여 응용 프로그램 컨테이너를 빌드합니다. 그런 다음 응용 프로그램 컨테이너 및 devspaces-프록시 컨테이너가 시작됩니다.
1. 응용 프로그램 컨테이너가 시작된 후 클라이언트 측 기능은 Kubernetes *포트포워드* 기능을 사용하여 응용 http://localhost프로그램에 대한 HTTP 액세스를 위에 제공합니다. 이 포트 포워딩은 개발 컴퓨터를 개발 공간의 서비스에 연결합니다.
1. 포드의 모든 컨테이너가 시작되면 서비스가 실행되고 있습니다. 이 시점에서 클라이언트 쪽 기능은 HTTP 추적, stdout 및 stderr를 스트리밍하기 시작합니다. 이 정보는 개발자의 클라이언트 쪽 기능에 의해 표시됩니다.

## <a name="updating-a-running-service"></a>실행 중인 서비스 업데이트

서비스가 실행되는 동안 Azure 개발자 공간은 프로젝트 원본 파일이 변경되는 경우 해당 서비스를 업데이트할 수 있습니다. 또한 개발자 공백은 변경된 파일 유형에 따라 서비스 업데이트를 다르게 처리합니다. 개발자 공간에서 실행 중인 서비스를 업데이트할 수 있는 방법에는 세 가지가 있습니다.

* 파일 직접 업데이트
* 실행 중인 응용 프로그램의 컨테이너 내에서 응용 프로그램의 프로세스를 다시 빌드하고 다시 시작
* 응용 프로그램의 컨테이너 다시 빌드 및 재배포

![Azure 개발자 공간 파일 동기화](media/how-dev-spaces-works/file-sync.svg)

HTML, css 및 cshtml 파일과 같은 정적 자산인 특정 프로젝트 파일은 아무 것도 다시 시작하지 않고 응용 프로그램의 컨테이너에서 직접 업데이트할 수 있습니다. 정적 자산이 변경되면 새 파일이 개발 공간에 동기화된 다음 실행 중인 컨테이너에서 사용됩니다.

실행 중인 컨테이너 내에서 응용 프로그램의 프로세스를 다시 시작하여 소스 코드 또는 응용 프로그램 구성 파일과 같은 파일을 변경할 수 있습니다. 이러한 파일이 동기화되면 *devhostagent* 프로세스를 사용하여 실행 중인 컨테이너 내에서 응용 프로그램의 프로세스가 다시 시작됩니다. 처음에 응용 프로그램의 컨테이너를 만들 때 컨트롤러는 응용 프로그램의 시작 명령을 *devhostagent라는*다른 프로세스로 바꿉니다. 그런 다음 응용 프로그램의 실제 프로세스는 *devhostagent에서*자식 프로세스로 실행되고 출력은 *devhostagent의*출력을 사용하여 파이프아웃됩니다. *devhostagent* 프로세스는 개발자 공간의 일부이기도 하며 개발자 공간을 대신하여 실행 중인 컨테이너에서 명령을 실행할 수 있습니다. 다시 시작을 수행할 *때, 데브호스트 에이전트:*

* 응용 프로그램과 연결된 현재 프로세스 또는 프로세스 중지
* 응용 프로그램을 다시 빌드합니다.
* 응용 프로그램과 관련된 프로세스 또는 프로세스 다시 시작

*devhostagent가* 이전 단계를 실행하는 방법은 [ `azds.yaml`에서 구성됩니다. ][azds-yaml-section]

Dockerfiles, csproj 파일 또는 Helm 차트의 일부와 같은 프로젝트 파일을 업데이트하려면 응용 프로그램의 컨테이너를 다시 빌드하고 다시 배포해야 합니다. 이러한 파일 중 하나가 개발 공간에 동기화되면 컨트롤러는 [helm 업그레이드][helm-upgrade] 명령을 실행하고 응용 프로그램의 컨테이너를 다시 빌드하고 다시 배포합니다.

## <a name="file-synchronization"></a>파일 동기화

개발 공간에서 응용 프로그램을 처음 시작하면 모든 응용 프로그램의 원본 파일이 업로드됩니다. 응용 프로그램이 실행되고 나중에 다시 시작되는 동안변경된 파일만 업로드됩니다. 이 프로세스를 조정하는 데 사용되는 두 개의 파일은 클라이언트 측 파일과 컨트롤러 측 파일입니다.

클라이언트 쪽 파일은 임시 디렉터리에 저장되며 개발자 공간에서 실행 중인 프로젝트 디렉터리 해시를 기반으로 이름이 지정됩니다. 예를 들어 Windows에서는 프로젝트에 대한 *사용자\USERNAME\AppData\Local\Temp\12345677890abcdef123567890abcdef12346666666890abcdef1234567890abcdef.synclog와* 같은 파일을 갖게 됩니다. Linux에서 클라이언트 쪽 파일은 */tmp* 디렉터리에 저장됩니다. 명령을 실행하여 macOS에서 디렉터리를 `echo $TMPDIR` 찾을 수 있습니다.

이 파일은 JSON 형식으로 되어 있으며 다음을 포함합니다.

* 개발 공간과 동기화된 각 프로젝트 파일에 대한 항목
* 동기화 ID
* 마지막 동기화 작업의 타임스탬프

각 프로젝트 파일 항목에는 파일 및 해당 타임스탬프에 대한 경로가 포함되어 있습니다.

컨트롤러 측 파일은 AKS 클러스터에 저장됩니다. 동기화 ID와 마지막 동기화의 타임스탬프가 포함되어 있습니다.

동기화 타임스탬프가 클라이언트 측과 컨트롤러 측 파일 간에 일치하지 않을 때 동기화가 발생합니다. 동기화하는 동안 클라이언트 측 도구는 클라이언트 측 파일의 파일 항목을 반복해서 입력합니다. 파일의 타임스탬프가 동기화 타임스탬프 다음에 있는 경우 해당 파일은 개발 공간에 동기화됩니다. 동기화가 완료되면 동기화 타임스탬프가 클라이언트 측 및 컨트롤러 측 파일 모두에서 업데이트됩니다.

클라이언트 쪽 파일이 없는 경우 모든 프로젝트 파일이 동기화됩니다. 이 동작을 사용하면 클라이언트 쪽 파일을 삭제하여 전체 동기화를 강제할 수 있습니다.

## <a name="how-running-your-code-is-configured"></a>코드 실행 방법 구성

Azure 개발자 공간은 `azds.yaml` 파일을 사용하여 서비스를 설치하고 구성합니다. 컨트롤러는 파일의 `install` `azds.yaml` 속성을 사용하여 Helm 차트를 설치하고 Kubernetes 개체를 만듭니다.

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

기본적으로 `prep` 명령은 Helm 차트를 생성합니다. 또한 *install.chart* 속성을 Helm 차트의 디렉토리로 설정합니다. 다른 위치에서 Helm 차트를 사용하려는 경우 해당 위치를 사용하도록 이 속성을 업데이트할 수 있습니다.

헬름 차트를 설치할 때 Azure 개발자 공간은 Helm 차트에서 값을 재정의하는 방법을 제공합니다. Helm 차트의 기본값은 `charts/APP_NAME/values.yaml`에 있습니다.

*install.values* 속성을 사용하여 Helm 차트에서 대체할 값을 정의하는 하나 이상의 파일을 나열할 수 있습니다. 예를 들어 개발 공간에서 응용 프로그램을 실행할 때 호스트 이름 또는 데이터베이스 구성을 특별히 원하는 경우 이 재정의 기능을 사용할 수 있습니다. *?* 파일 이름 끝에 선택 사항으로 설정합니다.

*install.set* 속성을 사용하면 Helm 차트에서 대체할 값을 하나 이상 구성할 수 있습니다. *install.set에서* 구성된 모든 값은 *install.value*에 나열된 파일에 구성된 값을 재정의합니다. *install.set* 아래의 속성은 Helm 차트의 값에 따라 달라지며 생성된 Helm 차트에 따라 다를 수 있습니다.

위의 예에서 *install.set.replicaCount* 속성은 개발자에게 개발 공간에서 실행할 응용 프로그램의 인스턴스 수를 알려줍니다. 시나리오에 따라 이 값을 늘릴 수 있지만 응용 프로그램의 포드에 디버거를 연결하는 데 영향을 미칩니다. 자세한 내용은 문제 [해결 문서를][troubleshooting]참조하십시오.

생성된 헬름 차트에서 컨테이너 이미지는 *{{로 설정됩니다. value.image.repository }}:{{ . value.image.tag }}*. 파일은 `azds.yaml` 기본적으로 *$(tag)로* *install.set.image.tag* 속성을 정의하며, 이 속성은 {{에 대한 값으로 *사용됩니다. value.image.tag }}*. 이러한 방식으로 *install.set.image.tag* 속성을 설정하면 Azure 개발자 공간을 실행할 때 응용 프로그램의 컨테이너 이미지에 고유한 태그가 지정될 수 있습니다. 이 특정 경우 이미지는 * \<image.repository>:$(태그)에서 값으로*태그가 지정됩니다. 개발자 공간이 AKS 클러스터에서 컨테이너를 인식하고 찾으려면 *$(태그) 변수를* *install.set.image.tag* 값으로 사용해야 합니다.

위의 예에서 `azds.yaml` *install.set.ingress.hosts를 정의합니다.* *install.set.ingress.hosts* 속성은 공용 끝점에 대한 호스트 이름 형식을 정의합니다. 이 속성은 또한 *사용 $(spacePrefix)*, *$(rootSpacePrefix)* 및 *$(hostSuffix)*- 컨트롤러에서 제공하는 값입니다.

*$(spacePrefix)는* *SPACENAME.s의*형태를 취하는 자식 개발 공간의 이름입니다. *$(rootSpacePrefix)은* 상위 공간의 이름입니다. 예를 들어 *azureuser가* *기본값의*자식 공간인 경우 *$(rootSpacePrefix)의* 값은 *기본값이며* *$(spacePrefix)* 값은 *azureuser.s*입니다. 공백이 자식 공간이 아닌 경우 *$(spacePrefix)는* 비어 있습니다. 예를 들어 *기본* 공간에 상위 공간이 없는 경우 *$(rootSpacePrefix)의* 값은 *기본값이며* *$(spacePrefix)* 값은 비어 있습니다. *$(hostSuffix)은* AKS 클러스터에서 실행되는 Azure 개발자 공간 인그레스 컨트롤러를 가리키는 DNS 접미사입니다. 이 DNS 접미사는 와일드카드 * \*DNS 항목에 해당합니다. RANDOM_VALUE.eus.azds.io는*Azure 개발자 공간 컨트롤러가 AKS 클러스터에 추가되었을 때 생성되었습니다.

위의 `azds.yaml` 파일에서 *install.set.ingress.hosts를* 업데이트하여 응용 프로그램의 호스트 이름을 변경할 수도 있습니다. 예를 들어 응용 프로그램의 호스트 이름을 *$(spacePrefix)$(rootSpacePrefix)에서 $(spacePrefix)에서* *$(spacePrefix)$(hostSuffix)로 단순화하려는 경우 웹$(rootSpacePrefix)(hostSuffix)입니다.*

응용 프로그램에 대한 컨테이너를 빌드하기 위해 컨트롤러는 `azds.yaml` 구성 파일의 아래 섹션을 사용합니다.

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

컨트롤러는 Dockerfile을 사용하여 응용 프로그램을 빌드하고 실행합니다.

*build.context* 속성에는 Dockerfiles가 있는 디렉터리가 나열됩니다. *build.dockerfile* 속성은 응용 프로그램의 프로덕션 버전을 빌드하기 위한 Dockerfile의 이름을 정의합니다. *configurations.develop.build.dockerfile* 속성응용 프로그램의 개발 버전에 대 한 Dockerfile의 이름을 구성 합니다.

개발 및 프로덕션을 위해 서로 다른 Dockerfiles를 사용하면 개발 중에 특정 항목을 사용하도록 설정하고 프로덕션 배포에 대해 해당 항목을 사용하지 않도록 설정할 수 있습니다. 예를 들어 개발 중에 디버깅 또는 자세한 로깅을 사용하도록 설정하고 프로덕션 환경에서 사용하지 않도록 설정할 수 있습니다. Dockerfiles의 이름이 다르게 지정되거나 다른 위치에 있는 경우에도 이러한 속성을 업데이트할 수 있습니다.

개발 중에 빠르게 반복할 수 있도록 Azure 개발자 공간은 로컬 프로젝트의 변경 내용을 동기화하고 응용 프로그램을 점진적으로 업데이트합니다. 구성 파일의 `azds.yaml` 아래 섹션에서는 동기화 및 업데이트를 구성하는 데 사용됩니다.

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

변경 내용을 동기화할 파일 및 *디렉터리구성.develop.container.sync* 속성에 나열 됩니다. 이러한 디렉터리는 `up` 명령을 실행할 때와 변경 사항이 검색될 때 처음에 동기화됩니다. 개발 공간에 동기화하려는 추가 디렉터리 또는 다른 디렉터리가 있는 경우 이 속성을 변경할 수 있습니다.

*configurations.develop.container.iterate.buildCommands* 속성은 개발 시나리오에서 응용 프로그램을 빌드하는 방법을 지정합니다. *configurations.develop.container.command* 속성은 개발 시나리오에서 응용 프로그램을 실행하기 위한 명령을 제공합니다. 개발 중에 사용할 추가 빌드 또는 런타임 플래그 또는 매개 변수가 있는 경우 이러한 속성 중 하나를 업데이트할 수 있습니다.

*구성.develop.container.iterate.processesToKill* 응용 프로그램을 중지 죽일 프로세스를 나열 합니다. 개발 중에 응용 프로그램의 다시 시작 동작을 변경하려는 경우 이 속성을 업데이트할 수 있습니다. 예를 들어 응용 프로그램을 빌드하거나 시작하는 방법을 변경하기 위해 *configurations.develop.container.iterate.buildCommands* 또는 *configurations.develop.container.command* 속성을 업데이트한 경우 중지되는 프로세스를 변경해야 할 수 있습니다.

`azds prep` 명령을 사용하여 코드를 준비할 때 플래그를 추가할 `--enable-ingress` 수 있습니다. 플래그를 `--enable-ingress` 추가하면 응용 프로그램에 대해 공개적으로 액세스할 수 있는 URL이 만들어집니다. 이 플래그를 생략하면 클러스터 내에서만 응용 프로그램에 액세스할 수 있습니다. 명령을 실행한 후 다음 에서 *ingress.enabled* 속성을 수정하는 이 설정을 변경할 수 있습니다. `charts/APPNAME/values.yaml` `azds prep`

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>다음 단계

네트워킹 및 Azure 개발자 공간에서 요청을 라우팅하는 방법에 대해 자세히 알아보려면 [Azure 개발자 공간에서 라우팅작동 방식을][how-it-works-routing]참조하세요.

빠르게 반복하고 개발하기 위해 Azure 개발자 공간을 사용하는 방법에 대해 자세히 알아보려면 [개발 컴퓨터를 개발 공간에 연결하는 방법과][how-it-works-connect] Azure 개발자 공간에서 코드를 원격으로 [디버깅하는 방법을][how-it-works-remote-debugging]참조하세요.

Azure 개발자 공간을 사용하여 프로젝트를 실행하려면 다음 빠른 시작을 참조하세요.

* [Visual Studio 코드 및 Java를 통해 빠르게 반복 및 디버깅][quickstart-java]
* [Visual Studio 코드 및 .NET을 통해 빠르게 반복 및 디버깅][quickstart-netcore]
* [Visual Studio 코드 및 Node.js로 빠르게 반복 및 디버깅][quickstart-node]
* [Visual Studio 및 .NET Core를 통해 빠르게 반복 및 디버깅][quickstart-vs]
* [CLI를 사용하여 Kubernetes에서 응용 프로그램 개발][quickstart-cli]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md