---
title: 원본에서 Azure Red Hat OpenShift로 애플리케이션 배포
description: S2I(Source-to-Image) 빌드 전략을 사용하여 소스 코드에서 Azure Red Hat OpenShift에 애플리케이션을 배포하는 방법 알아보기
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, s2i, 원본에서 이미지로
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558700"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>원본에서 Azure Red Hat OpenShift로 애플리케이션 배포

이 문서에서는 S2I(source-to-image) 빌드를 사용하여 소스 코드에서 Azure Red Hat OpenShift 클러스터에 애플리케이션을 배포합니다. [S2I(source-to-image)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds)는 소스 코드에서 재현 가능한 컨테이너 이미지를 빌드하기 위한 빌드 프로세스입니다. S2I는 소스 코드를 컨테이너 이미지에 주입하고 컨테이너가 실행을 위해 해당 소스 코드를 준비하도록 하여 바로 실행 가능한 이미지를 생성합니다. OpenShift가 원본에서 애플리케이션을 빌드하여 배포하도록 할 수 있으므로 변경될 때마다 컨테이너를 직접 구성할 필요가 없습니다. 그런 다음 OpenShift는 소스 코드 변경 알림을 받으면 새 버전을 자동으로 빌드하고 배포할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>프로젝트 만들기

`demoproject`라는 새 프로젝트를 작성하려면 다음 명령을 실행합니다.

```azurecli-interactive
oc new-project demoproject
```

다음과 비슷한 내용이 출력됩니다.

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>웹 콘솔 시작

다음을 실행하여 클러스터 웹 콘솔 URL을 찾습니다.

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

다음과 유사한 URL이 표시되어야 합니다.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

브라우저에서 콘솔 URL을 시작하고 `kubeadmin` 자격 증명을 사용하여 로그인합니다.

:::image type="content" source="media/login.png" alt-text="Azure Red Hat OpenShift 로그인 화면":::

왼쪽 메뉴에서 *관리자* 큐브 뷰 대신 *개발자* 큐브 뷰로 전환하고 프로젝트 목록에서 `demoproject`를 선택합니다. 그러면 프로젝트의 *토폴로지* 페이지가 표시됩니다.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift 프로젝트 토폴로지":::

프로젝트가 비어 있으므로 워크로드를 찾을 수 없으며 애플리케이션을 배포할 수 있는 방법에 대한 다양한 옵션이 표시됩니다.

## <a name="deploying-using-the-web-console"></a>웹 콘솔을 사용하여 배포

애플리케이션 배포를 위해 표시된 옵션에서 *Git에서* 를 선택합니다. 그러면 *Git에서 가져오기* 페이지가 표시됩니다. `https://github.com/sclorg/django-ex.git`를 **Git Repo URL** 로 사용합니다. 샘플 웹 애플리케이션은 Python 프로그래밍 언어를 사용하여 구현됩니다.

:::image type="content" source="media/s2i/from-git.png" alt-text="Git의 Azure Red Hat OpenShift 프로젝트":::

> [!NOTE]
> OpenShift는 이것이 Python 프로젝트임을 검색하고 적절한 작성기 이미지를 선택합니다.

*고급 옵션* 까지 아래로 스크롤하고 **애플리케이션에 대한 경로 만들기** 가 선택되어 있는지 확인합니다. 이 작업은 외부에서 연결할 수 있는 호스트 이름을 제공하여 서비스를 노출하는 방법인 OpenShift 경로를 만듭니다.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Git의 Azure Red Hat OpenShift 프로젝트 - 경로 설정":::

준비가 되면 페이지 맨 아래에서 **만들기** 를 클릭합니다. 그러면 애플리케이션의 빌드 및 배포를 관리하는 리소스가 만들어집니다. 그런 다음 프로젝트에 대한 토폴로지 개요로 리디렉션됩니다.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Git의 Azure Red Hat OpenShift 프로젝트 - 토폴로지":::

토폴로지 개요는 배포한 애플리케이션의 시각적 표시를 제공합니다. 이 보기를 통해 전체 애플리케이션 구조를 볼 수 있습니다.

Git 아이콘을 클릭하여 애플리케이션의 소스 코드가 빌드된 Git 리포지토리로 이동할 수 있습니다. 왼쪽 아래에 표시된 아이콘은 애플리케이션의 빌드 상태를 보여 줍니다. 이 아이콘을 클릭하면 빌드 세부 정보 섹션으로 이동합니다. 애플리케이션에 노출된 경로가 있는 경우 오른쪽 위에 있는 아이콘을 클릭하여 만든 애플리케이션 경로에 대한 URL을 열 수 있습니다.

애플리케이션의 규모를 스케일 업 또는 스케일 다운하는 동안 롤아웃을 시작하고 pod를 다시 만들면 토폴로지 보기의 애플리케이션 표시에 애니메이션이 적용되어 진행 상황을 실시간으로 볼 수 있습니다.

애플리케이션 아이콘을 클릭하면 아래와 같이 자세한 정보가 표시됩니다.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Git의 Azure Red Hat OpenShift 프로젝트 - 세부 정보":::

## <a name="viewing-the-builder-logs"></a>작성기 로그 보기

빌드가 시작되면 *리소스* 패널에 표시된 *로그 보기* 링크를 클릭합니다.

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Git의 Azure Red Hat OpenShift 프로젝트 - 빌드 로그":::

이렇게 하면 실행 중인 빌드의 진행 상황을 모니터링할 수 있습니다. 작성기 이미지(이 경우 Python)는 애플리케이션 소스 코드를 OpenShift 내부 이미지 레지스트리로 푸시하기 전에 최종 이미지에 삽입합니다. "푸시 성공"의 최종 메시지가 표시되면 빌드가 성공적으로 완료된 것입니다.

## <a name="accessing-the-application"></a>애플리케이션 액세스

애플리케이션 이미지의 빌드가 완료되면 배포됩니다.

왼쪽 메뉴 모음에서 *토폴로지* 를 클릭하여 프로젝트의 토폴로지 보기로 돌아갑니다. 웹 콘솔을 사용하여 애플리케이션을 만들 때 애플리케이션에 대한 *경로* 가 자동으로 만들어지고 클러스터 외부에 노출됩니다. 웹 브라우저에서 애플리케이션에 액세스하는 데 사용할 수 있는 URL은 이전에 본 애플리케이션의 *리소스* 탭에 표시됩니다.

토폴로지 보기에서 링 오른쪽 위에 있는 아이콘을 클릭하여 배포된 애플리케이션의 URL을 가져올 수 있습니다. 배포가 완료된 후 아이콘을 클릭하면 배포한 애플리케이션이 표시되어야 합니다.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Git의 Azure Red Hat OpenShift 프로젝트 - 앱 찾아보기":::

## <a name="deploying-using-the-command-line"></a>명령줄을 사용하여 배포

웹 콘솔을 사용하여 애플리케이션을 배포하는 방법을 배웠습니다. 이제 동일한 웹 애플리케이션을 배포할 수 있지만 이번에는 `oc` 명령줄 도구를 사용합니다.

다음 명령을 실행하여 프로젝트를 삭제하고 다시 시작합니다.

```azurecli-interactive
oc delete project demoproject
```

`demoproject`가 삭제되었다는 확인 메시지가 표시되어야 합니다.

```output
project.project.openshift.io "demoproject" deleted
```

다음을 실행하여 `demoproject`를 다시 만듭니다.

```azurecli-interactive
oc new-project demoproject
```

프로젝트 내에서 GitHub의 원본을 사용하여 새 애플리케이션을 만들고 제공된 최신 버전의 Python에 대한 S2I 작성기를 지정합니다.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

다음과 유사한 출력이 표시되어야 합니다.

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift는 Git 리포지토리의 이름을 애플리케이션의 이름으로 사용합니다. 다음을 실행하여 빌드 및 배포 상태를 검토합니다.

```azurecli-interactive
oc status
```

빌드 및 배포가 완료되면 다음과 유사한 출력이 표시되어야 합니다.

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

OpenShift 클러스터 외부에 애플리케이션을 노출하려면 다음을 실행하여 경로를 만들어야 합니다.

```azurecli-interactive
oc expose service/django-ex
```

확인 메시지가 표시되어야 합니다.

```output
route.route.openshift.io/django-ex exposed
```

다음을 실행하여 URL을 검색합니다.

```azurecli-interactive
oc get route django-ex
```

배포된 애플리케이션을 검색하는 데 사용할 수 있는 경로에 할당된 호스트 이름이 다시 표시되어야 합니다.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>새 이진 빌드 트리거

애플리케이션에서 작업을 수행하는 동안 변경 내용을 적용하고 배포되는 것을 확인하고 싶을 수 있습니다. 모든 코드 커밋과 함께 새 빌드 및 배포를 트리거하는 웹후크를 쉽게 설정할 수 있습니다. 그러나 모든 코드 변경 내용을 리포지토리에 푸시하지 않고도 변경 내용을 확인하려는 경우에는 이 방법이 바람직하지 않을 수 있습니다.

변경 내용을 신속하게 반복하는 경우 이진 빌드를 사용할 수 있습니다. 이 시나리오를 시연하려면 다음을 실행하여 애플리케이션의 Git 저장소를 로컬로 복제합니다.

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

그러면 애플리케이션의 소스 코드가 포함된 하위 디렉터리 `django-ex`가 만들어집니다.

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

하위 디렉터리로 변경합니다.

```azurecli-interactive
cd django-ex
```

통합된 Azure Cloud Shell 편집기를 엽니다.

```azurecli-interactive
code welcome/templates/welcome/index.html
```

아래로 스크롤하여 `Welcome to your Django application on OpenShift`라고 표시된 줄을 `Welcome to Azure Red Hat OpenShift`로 변경합니다. 오른쪽 위의 `...` 메뉴를 통해 파일을 저장하고 편집기를 닫습니다.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Git의 Azure Red Hat OpenShift 프로젝트 - Azure Cloud Shell 편집기에서 애플리케이션 편집":::

다음 명령을 실행하여 새 빌드를 시작합니다.

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

`--from-dir=.` 플래그를 전달하면 OpenShift 명령줄이 지정된 디렉터리에서 소스 코드를 업로드한 다음 빌드 및 배포 프로세스를 시작합니다. 아래와 유사한 출력이 표시되고 몇 분 후에 빌드가 완료되어야 합니다.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

애플리케이션이 있는 브라우저를 새로 고치면 업데이트된 제목이 표시되어야 합니다.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Git의 Azure Red Hat OpenShift 프로젝트 - 업데이트된 앱 찾아보기":::

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션을 완료하고 나면 다음 명령을 실행하여 프로젝트를 삭제할 수 있습니다.

```azurecli-interactive
oc delete project demoproject
```

[자습서: Azure Red Hat OpenShift 4 클러스터 삭제](./tutorial-delete-cluster.md)의 지침에 따라 클러스터를 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 지침에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
>
> * 프로젝트 만들기
> * 웹 콘솔을 사용하여 소스 코드에서 애플리케이션 배포
> * OpenShift 명령줄을 사용하여 소스 코드에서 애플리케이션 배포
> * OpenShift 명령줄을 사용하여 이진 빌드 트리거

S2I(Source-to-Image) 및 [기타 빌드 전략](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)을 사용하여 애플리케이션을 빌드하고 배포하는 방법에 대해 자세히 알아봅니다.
