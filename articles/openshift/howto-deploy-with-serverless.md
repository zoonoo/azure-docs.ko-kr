---
title: OpenShift Serverless를 사용하여 Azure Red Hat OpenShift에 애플리케이션 배포
description: OpenShift Serverless를 사용하여 Azure Red Hat OpenShift에 애플리케이션을 배포하는 방법을 알아봅니다.
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, serverless
ms.openlocfilehash: 0cd6ac8ae31c43bf1fe1be1d42ec27f183512442
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110063629"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>OpenShift Serverless를 사용하여 Azure Red Hat OpenShift에 애플리케이션 배포

이 문서에서는 [OpenShift Serverless](https://www.openshift.com/learn/topics/serverless)를 사용하여 Azure Red Hat OpenShift 클러스터에 애플리케이션을 배포합니다. OpenShift Serverless를 사용하면 개발자는 스케일 업 또는 0으로 스케일링할 애플리케이션을 배포하고 실행할 수 있으므로 사용되고 있지 않을 때 리소스 사용이 제거됩니다.

애플리케이션 코드는 적절한 런타임과 함께 컨테이너에 패키지할 수 있으며 이벤트가 애플리케이션 컨테이너를 트리거하면 서버리스 기능이 해당 컨테이너를 시작합니다. 애플리케이션의 이벤트, 여러 공급자의 클라우드 서비스, SaaS(Software as a Service) 시스템 및 기타 서비스와 같은 다양한 이벤트 원본이 애플리케이션을 트리거할 수 있습니다.

서버리스 방식으로 컨테이너를 배포하는 모든 측면을 관리하는 기능은 OpenShift 인터페이스에 직접 빌드됩니다. 개발자는 이벤트 매개 변수를 수정하는 여러 가지 방법으로 컨테이너화된 애플리케이션의 시작을 구동하는 이벤트를 시각적으로 식별할 수 있습니다. OpenShift Serverless 애플리케이션은 OpenShift Pipelines, Service Mesh 및 Monitoring과 같은 다른 OpenShift 서비스와 통합하여 완벽한 서버리스 애플리케이션 개발 및 배포 환경을 제공할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Knative 명령줄 도구(kn) 설치

<https://github.com/knative/client/releases/>에서 현재 사용하는 머신에 맞는 최신 CLI 릴리스를 다운로드할 수 있습니다.

Azure Cloud Shell에서 명령을 실행하는 경우 Linux용 최신 Knative CLI를 다운로드합니다.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>웹 콘솔 시작

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

## <a name="install-the-openshift-serverless-operator"></a>OpenShift Serverless 운영자 설치

OpenShift 웹 콘솔에 로그인한 후 ‘관리자’ 큐브 뷰에 있는지 확인합니다. *Operator Hub* 를 열고 **OpenShift Serverless** 운영자를 찾고 선택합니다.

![OpenShift Serverless 운영자 검색](media/serverless/serverless-operatorhub.png)

그런 다음, **설치** 를 클릭하여 운영자 설치 페이지를 엽니다.

![[설치]를 클릭하여 운영자 설치](media/serverless/serverless-clickinstall.png)

Azure Red Hat OpenShift의 클러스터 버전에 적절한 ‘업데이트 채널’을 선택하고 `openshift-serverless` 네임스페이스에 운영자를 설치합니다. 아래로 스크롤하고 **설치** 를 클릭합니다.

![운영자 설치 페이지](media/serverless/serverless-installpage.png)

몇 분 후에 운영자가 설치되고 사용할 준비가 되었음이 상태 페이지에 반영됩니다. **운영자 보기** 단추를 클릭하여 계속 진행합니다.

![운영자가 설치되고 사용할 준비가 됨](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Knative 서비스 설치

OpenShift Serverless에서 서버리스 방식으로 컨테이너를 실행하는 기능은 업스트림 Knative를 사용하여 구현할 수 있습니다. Knative는 서버리스 방법을 사용하여 최신 애플리케이션을 배포, 실행, 관리하기 위한 구성 요소 세트를 제공하도록 Kubernetes를 확장합니다.

### <a name="create-an-instance-of-the-knative-serving"></a>Knative 서비스 인스턴스 만들기

왼쪽 위에서 프로젝트 드롭다운 목록을 클릭하여 `knative-serving` 네임스페이스로 전환한 다음, ‘제공된 API’ 아래 ‘Knative 서비스’ 카드에서 **인스턴스 만들기** 를 클릭합니다. 

![Knative 서비스 인스턴스를 만들려면 클릭](media/serverless/serverless-createknativeserving.png)

기본값을 유지하고 ‘Knative 서비스 만들기’ 페이지에서 아래로 스크롤하여 **만들기** 단추를 클릭합니다.

![기본값을 유지하고 만들기 클릭](media/serverless/serverless-createknativeserving2.png)

‘상태’ 열에 **준비** 가 표시될 때까지 기다리면 OpenShift Serverless가 설치되고 OpenShift Serverless 프로젝트를 만들 준비가 됩니다.

![Knative 서비스 준비](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>서버리스 프로젝트 만들기

`demoserverless`라는 새 프로젝트를 만들려면 다음 명령을 실행합니다.

```azurecli-interactive
oc new-project demoserverless
```

다음과 같은 출력이 표시됩니다.

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

왼쪽 메뉴에서 ‘관리자’ 큐브 뷰 대신 ‘개발자’ 큐브 뷰로 전환하고 프로젝트 목록에서 `demoserverless`를 선택합니다.  그러면 프로젝트의 ‘토폴로지’ 페이지가 표시됩니다.

![Azure Red Hat OpenShift 프로젝트 토폴로지](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>웹 콘솔을 사용하여 배포

애플리케이션 배포를 위해 표시된 옵션에서 *Git에서* 를 선택합니다. 그러면 *Git에서 가져오기* 페이지가 표시됩니다. `https://github.com/sclorg/django-ex.git`를 **Git Repo URL** 로 사용합니다. 샘플 웹 애플리케이션은 Python 프로그래밍 언어를 사용하여 구현됩니다.

![Git의 Azure Red Hat OpenShift 프로젝트](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift는 Python 프로젝트임을 검색하고 적절한 작성기 이미지를 선택합니다.

‘리소스’까지 아래로 스크롤하고 **Knative 서비스** 가 생성할 리소스 종류로 선택되어 있는지 확인합니다. 이 작업은 유휴 상태일 때 OpenShift Serverless를 0으로 스케일링할 수 있는 배포 유형인 Knative 서비스를 만듭니다.

![Azure Red Hat OpenShift 프로젝트 - Knative](media/serverless/serverless-knative.png)


준비가 되면 페이지 아래쪽에서 **만들기** 를 클릭합니다. 그러면 애플리케이션의 빌드 및 배포를 관리하는 리소스가 만들어집니다. 그런 다음, 프로젝트의 토폴로지 개요로 리디렉션됩니다.

토폴로지 개요는 배포한 애플리케이션을 시각적으로 표시합니다. 이 보기를 통해 전체 애플리케이션 구조를 볼 수 있습니다.

빌드가 완료될 때까지 기다립니다. 몇 분이 걸릴 수 있습니다. 빌드가 완료되면 서비스의 왼쪽 아래에 녹색 확인 표시가 나타납니다.

![Azure Red Hat OpenShift 프로젝트 토폴로지 - 빌드 성공](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>애플리케이션 스케일링 확인

토폴로지 보기 위쪽의 ‘표시 옵션’ 목록에서 ‘Pod 개수’를 클릭합니다.  Pod 개수가 0개 Pod로 스케일 다운될 때까지 기다립니다. 스케일 다운에는 몇 분이 걸릴 수 있습니다.

![Azure Red Hat OpenShift 프로젝트 토폴로지 - 0으로 스케일링됨](media/serverless/serverless-scaledtozero.png)

Knative 서비스 패널의 오른쪽 위에 있는 ‘URL 열기’ 아이콘을 클릭합니다. 애플리케이션이 새 탭에서 열립니다. 새 브라우저 탭을 닫고 토폴로지 보기로 돌아갑니다. 토폴로지 보기에서 애플리케이션이 요청을 수용하도록 1개 Pod로 스케일 업된 것을 확인할 수 있습니다. 몇 분 후에 애플리케이션은 다시 0개 Pod로 스케일 다운됩니다.

![Azure Red Hat OpenShift 프로젝트 토폴로지 - 1로 스케일링됨](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>새 수정 버전 적용 및 트래픽 배포 설정

서비스 구성이 업데이트될 때마다 서비스의 수정 버전이 새로 생성됩니다. 서비스 경로에서는 기본적으로 모든 트래픽이 최신 준비 버전을 가리킵니다. 트래픽 일부를 가져오는 수정 버전을 정의하여 이 동작을 변경할 수 있습니다. Knative 서비스는 트래픽 매핑을 허용합니다. 즉, 서비스의 수정 버전을 트래픽의 할당된 부분에 매핑할 수 있습니다. 트래픽 매핑은 특정 수정 버전의 고유 URL을 만드는 옵션도 제공합니다.

‘토폴로지’에서 서비스 내에서 수정 버전을 클릭하여 세부 정보를 확인합니다. Pod 링 아래 및 세부 정보 패널 위쪽에 있는 배지는 `(REV)`입니다. 사이드 패널의 ‘리소스’ 탭에서 아래로 스크롤하여 서비스와 연결된 구성을 클릭합니다.

![Azure Red Hat OpenShift 프로젝트 토폴로지 - 수정 버전](media/serverless/serverless-revdetails.png)

*YAML* 탭으로 전환하고 아래로 스크롤하여 `timeoutSeconds` 값을 `301`로 편집하여 구성 업데이트를 적용합니다. **저장** 을 클릭하여 업데이트된 구성을 저장합니다. 실제 시나리오에서는 컨테이너 이미지 태그를 업데이트하여 해당 구성 업데이트를 트리거할 수도 있습니다.

![구성을 업데이트하여 새 수정 버전 적용](media/serverless/serverless-confupdate.png)

‘토폴로지’ 보기로 돌아가면 새 수정 버전이 배포된 것을 볼 수 있습니다. `(KSVC)` 배지로 끝나는 서비스를 클릭하고 **트래픽 분산 설정** 단추를 클릭하면 이제 서비스의 여러 수정 버전 간에 트래픽을 나눌 수 있습니다.

![트래픽 분산 설정](media/serverless/serverless-trafficdist.png)

이제 ‘토폴로지’ 보기는 두 개의 수정 버전 간 트래픽이 분산되는 방식을 보여 줍니다.

![트래픽 분산 검토](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Knative 명령줄 도구(kn) 사용

이전 단계에서는 OpenShift 웹 콘솔을 사용하여 애플리케이션을 만들고 OpenShift Serverless에 배포했습니다. OpenShift Serverless는 바로 아래에 Knative를 실행하고 있으므로 Knative 명령줄 도구(kn)를 사용하여 Knative 서비스를 만들 수도 있습니다.

> [!NOTE]
> `kn` CLI를 아직 설치하지 않은 경우 이 문서의 필수 조건 섹션에 있는 단계를 수행해야 합니다. 또한 OpenShift 명령줄 도구 `oc`를 사용하여 로그인했는지 확인합니다.

`quay.io/rhdevelopers/knative-tutorial-greeter`에서 이미 빌드된 컨테이너 이미지를 사용하려고 합니다.

### <a name="deploy-a-service"></a>서비스 배포

서비스를 배포하려면 다음 명령을 실행합니다.

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

다음과 유사한 출력이 표시됩니다.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

프로젝트에서 경로 목록을 검색하려면 다음을 실행합니다.

```azurecli-interactive
kn route list
```

네임스페이스의 경로 목록이 다시 표시됩니다. 브라우저에서 URL을 열어 배포된 서비스를 확인합니다.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>새 버전의 서비스 배포

아래 명령을 실행하고 `:latest` 이미지 태그 및 환경 변수 `MESSAGE_PREFIX`를 전달하여 새 버전의 애플리케이션을 배포합니다.

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

새 수정 버전 `greeter-v2`가 배포되었다는 확인이 다시 표시됩니다.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

모든 수정 버전 및 해당 트래픽 분산 목록을 보려면 다음 명령을 실행합니다.

```azurecli-interactive
kn revision list
```

다음과 같은 목록이 표시됩니다. 새 수정 버전은 트래픽 100%를 가져옵니다.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>파란색/녹색 및 카나리아 배포

기본적으로 새 수정 버전이 배포되면 트래픽 100%를 가져옵니다. 이전 버전의 애플리케이션으로 빠르게 롤백할 수 있는 파란색/녹색 배포 전략을 구현하려는 경우를 살펴보겠습니다. Knative를 사용하면 쉽게 구현할 수 있습니다.

트래픽 100%를 전송하는 동안 다음 세 개의 트래픽 태그를 만들도록 서비스를 업데이트합니다.

- **current**: 현재 배포된 버전을 가리킵니다.
- **prev**: 이전 버전을 가리킵니다.
- **최신**: 항상 최신 버전을 가리킵니다.

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

다음과 같은 확인이 표시됩니다.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

다음 명령을 사용하여 경로를 나열합니다.

```azurecli-interactive
kn route describe greeter
```

트래픽 분산과 함께 각 태그의 URL을 보여 주는 출력이 표시됩니다.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

이전 버전으로 빠르게 롤백하려는 경우 이전 태그로 트래픽 100%를 전송하도록 트래픽 분산을 업데이트할 수 있습니다.

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

다음 명령을 통해 경로를 나열하여 다시 확인합니다.

```azurecli-interactive
kn route describe greeter
```

트래픽 분산의 100%가 이전 버전으로 이동함을 보여 주는 출력이 표시됩니다.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

브라우저에서 주 경로(이 경우 `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io`)를 새로 고치는 동안 트래픽 분산을 조정해 봅니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션을 완료하고 나면 다음 명령을 실행하여 프로젝트를 삭제할 수 있습니다.

```azurecli-interactive
oc delete project demoserverless
```

[자습서: Azure Red Hat OpenShift 4 클러스터 삭제](./tutorial-delete-cluster.md)의 지침에 따라 클러스터를 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 다음 방법을 알아보았습니다.
> [!div class="checklist"]
>
> * OpenShift Serverless 운영자 및 Knative 서비스 설치
> * 웹 콘솔을 사용하여 서버리스 프로젝트 배포
> * Knative CLI(kn)를 사용하여 서버리스 프로젝트 배포
> * Knative CLI(kn)를 사용하여 파란색/녹색 배포 및 카나리아 배포 구성

[OpenShift Serverless](https://www.openshift.com/learn/topics/serverless)를 사용하여 Azure Red Hat OpenShift에서 서버리스, 이벤트 기반 애플리케이션을 빌드 및 배포하는 방법에 관해 자세히 알아보고, [OpenShift Serverless 시작](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) 설명서와 [서버리스 애플리케이션 만들기 및 관리](https://docs.openshift.com/container-platform/4.5/serverless/serving-creating-managing-apps.html) 설명서를 참조하세요.
