---
title: 헬름 차트 저장
description: Azure 컨테이너 레지스트리의 리포지토리를 사용하여 Kubernetes 응용 프로그램에 대한 Helm 차트를 저장하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399384"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>헬름 차트를 Azure 컨테이너 레지스트리로 푸시및 끌어올릴 수 있습니다.

Kubernetes용 애플리케이션을 빠르게 관리하고 배포하려는 경우 [오픈 소스 Helm 패키지 관리자][helm]를 사용할 수 있습니다. Helm을 사용하면 응용 프로그램 패키지가 [차트로](https://helm.sh/docs/topics/charts/)정의되며, 이 차트는 [Helm 차트 리포지토리에](https://helm.sh/docs/topics/chart_repository/)수집되고 저장됩니다.

이 문서에서는 Helm 3 또는 Helm 2 설치를 사용하여 Azure 컨테이너 레지스트리에서 Helm 차트 리포지토리를 호스트하는 방법을 보여 주며 이 문서에서는 이를 보여 주실 수 있습니다. 대부분의 시나리오에서는 개발한 응용 프로그램에 대한 고유한 차트를 빌드하고 업로드합니다. 고유한 헬름 차트를 작성하는 방법에 대한 자세한 내용은 [차트 템플릿 개발자 가이드를][develop-helm-charts]참조하십시오. 다른 투구 리포지토리에서 기존 투구 차트를 저장할 수도 있습니다.

> [!IMPORTANT]
> Azure 컨테이너 레지스트리의 Helm 차트에 대한 지원은 현재 미리 보기 상태입니다. 미리 보기는 추가 [사용 약관에][terms-of-use]동의하는 조건으로 제공됩니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="helm-3-or-helm-2"></a>투구 3 또는 투구 2?

Helm 차트를 저장, 관리 및 설치하려면 Helm 클라이언트와 Helm CLI를 사용합니다. 투구 클라이언트의 주요 릴리스는 투구 3과 투구 2를 포함한다. Helm 3은 새 차트 형식을 지원하며 더 이상 틸러 서버 측 구성 요소를 설치하지 않습니다. 버전 차이에 대한 자세한 내용은 [버전 FAQ를](https://helm.sh/docs/faq/)참조하십시오. 이전에 Helm 2 차트를 배포한 경우 [Helm v2를 v3로 마이그레이션을](https://helm.sh/docs/topics/v2_v3_migration/)참조하십시오.

Helm 3 또는 Helm 2를 사용하여 Azure 컨테이너 레지스트리에서 각 버전에 맞는 워크플로를 사용하여 Helm 차트를 호스팅할 수 있습니다.

* [Helm 3](#use-the-helm-3-client) 클라이언트 `helm chart` - Helm CLI의 명령을 사용하여 [OCI 아티팩트로 레지스트리의 차트를 관리합니다.](container-registry-image-formats.md#oci-artifacts)
* [Helm 2 클라이언트](#use-the-helm-2-client) - Azure CLI에서 [az acr helm][az-acr-helm] 명령을 사용하여 헬름 차트 리포지토리로 컨테이너 레지스트리를 추가하고 관리합니다.

### <a name="additional-information"></a>추가 정보

* 대부분의 시나리오에서 기본 `helm chart` 명령이 있는 Helm 3 워크플로를 사용하여 차트를 OCI 아티팩트로 관리하는 것이 좋습니다.
* Helm 3 클라이언트 및 차트에서 레거시 [az acr helm][az-acr-helm] Azure CLI 명령 및 워크플로를 사용할 수 있습니다. 그러나 Helm 3 차트와 호환되지 않는 특정 명령은 다음과 같이 `az acr helm list` 표시됩니다.
* Helm 3에서 [az acr helm][az-acr-helm] 명령은 주로 Helm 2 클라이언트 및 차트 형식과의 호환성을 위해 지원됩니다. 이러한 명령의 향후 개발은 현재 계획되지 않습니다.

## <a name="use-the-helm-3-client"></a>헬름 3 클라이언트 사용

### <a name="prerequisites"></a>사전 요구 사항

- **Azure** 구독의 Azure 컨테이너 레지스트리입니다. 필요한 경우 [Azure 포털](container-registry-get-started-portal.md) 또는 [Azure CLI를](container-registry-get-started-azure-cli.md)사용하여 레지스트리를 만듭니다.
- **헬름 클라이언트 버전 3.1.0 이상** - 실행하여 `helm version` 현재 버전을 찾습니다. Helm을 설치하고 업그레이드하는 방법에 대한 자세한 내용은 [Helm 설치][helm-install]를 참조하세요.
- 투구 차트를 설치할 **Kubernetes 클러스터입니다.** 필요한 경우 [Azure Kubernetes 서비스 클러스터를][aks-quickstart]만듭니다. 
- **Azure CLI 버전 2.0.71** `az --version` 이상 - 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="high-level-workflow"></a>높은 수준의 워크플로우

**투구 3** 당신:

* Azure 컨테이너 레지스트리에서 하나 이상의 Helm 리포지토리를 만들 수 있습니다.
* OCI 아티팩트로 레지스트리에 Helm 3 [차트를 저장합니다.](container-registry-image-formats.md#oci-artifacts) 현재 OCI에 대한 투구 3 지원은 *실험적입니다.*
* 명령을 사용하여 레지스트리로 `helm registry login` 인증합니다.
* 헬름 CLI의 명령을 사용하여 `helm chart` 레지스트리에서 Helm 차트를 푸시, 당기기 및 관리합니다.
* 로컬 `helm install` 리포지토리 캐시에서 Kubernetes 클러스터에 차트를 설치하는 데 사용합니다.

예제는 다음 섹션을 참조하십시오.

### <a name="enable-oci-support"></a>OCI 지원 사용

Helm 3 클라이언트에서 OCI 지원을 사용하도록 설정하려면 다음 환경 변수를 설정합니다. 현재 이 지원은 실험적입니다. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>샘플 차트 만들기

다음 명령을 사용하여 테스트 차트를 만듭니다.

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

기본 예로 디렉터리를 `templates` 폴더로 변경하고 먼저 다음 내용을 삭제합니다.

```console
cd hello-world/templates
rm -rf *
```

폴더에서 `templates` 다음 내용으로 `configmap.yaml` 호출된 파일을 만듭니다.

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

이 예제를 만들고 실행하는 자세한 내용은 Helm 문서에서 [시작하기](https://helm.sh/docs/chart_template_guide/getting_started/) 를 참조하십시오.

### <a name="save-chart-to-local-registry-cache"></a>차트를 로컬 레지스트리 캐시에 저장

디렉터리를 하위 `hello-world` 디렉터리로 변경합니다. 그런 다음 `helm chart save` 차트의 복사본을 로컬로 저장하고 레지스트리(모든 소문자) 및 대상 리포지토리 및 태그의 정규화된 이름이 있는 별칭을 만듭니다. 

다음 예제에서 레지스트리 이름은 *mycontainerregistr,* 대상 리포지는 *hello-world,* 대상 차트 태그는 *v1이지만*사용자 환경에 대한 대체 값입니다.

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

로컬 `helm chart list` 레지스트리 캐시에 차트를 저장 확인 하려면 실행 합니다. 출력은 다음과 비슷합니다.

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>레지스트리로 인증

Helm `helm registry login` 3 CLI에서 명령을 실행하여 시나리오에 적합한 자격 증명을 사용하여 [레지스트리를 인증합니다.](container-registry-authentication.md)

예를 들어, 레지스트리에 끌어오기 및 푸시 권한(AcrPush 역할)을 사용하여 Azure Active Directory [서비스 주체를](container-registry-auth-service-principal.md#create-a-service-principal) 만듭니다. 그런 다음 서비스 주체 `helm registry login`자격 증명을 에 공급합니다. 다음 예제는 환경 변수를 사용하여 암호를 제공합니다.

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Azure 컨테이너 레지스트리에 차트 푸시

Helm `helm chart push` 3 CLI에서 명령을 실행하여 차트를 정규화된 대상 리포지토리로 푸시합니다.

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

푸시에 성공한 후 출력은 다음과 유사합니다.

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>리포지토리의 차트 목록 표시

Azure 컨테이너 레지스트리에 저장된 이미지와 마찬가지로 [az acr 리포지토리][az-acr-repository] 명령을 사용하여 차트를 호스팅하는 리포지토리및 차트 태그 및 매니페스트를 표시할 수 있습니다. 

예를 들어 [az acr 리포지토리 쇼를][az-acr-repository-show] 실행하여 이전 단계에서 만든 리포지토리의 속성을 확인합니다.

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

출력은 다음과 비슷합니다.

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

az [acr 리포지토리 쇼 매니페스트][az-acr-repository-show-manifests] 명령을 실행하여 리포지토리에 저장된 차트의 세부 정보를 확인합니다. 다음은 그 예입니다.

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

이 예제에서 축약된 출력은 `configMediaType` 다음을 보여 주며 다음을 `application/vnd.cncf.helm.config.v1+json`보여 주며 다음과 같은 것을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은 것을 보여 주며,

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>로컬 캐시로 차트 를 당기기

Kubernetes에 Helm 차트를 설치하려면 차트가 로컬 캐시에 있어야 합니다. 이 예제에서는 먼저 `helm chart remove` 다음이라는 `mycontainerregistry.azurecr.io/helm/hello-world:v1`기존 로컬 차트를 제거합니다.

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Azure `helm chart pull` 컨테이너 레지스트리에서 로컬 캐시로 차트를 다운로드하려면 실행합니다.

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>내보내기 투구 차트

차트를 더 자세히 사용하려면 을 사용하여 `helm chart export`로컬 디렉터리로 내보냅니다. 예를 들어, `install` 가져온 차트를 디렉터리로 내보내십시오.

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

리포지토리에서 내보낸 차트에 대한 정보를 `helm show chart` 보려면 차트를 내보낸 디렉터리에서 명령을 실행합니다.

```console
cd install
helm show chart hello-world
```

Helm은 다음 샘플 출력과 같이 차트의 최신 버전에 대한 자세한 정보를 반환합니다.

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>헬름 차트 설치

로컬 `helm install` 캐시로 가져와 내보낸 Helm 차트를 설치하려면 실행합니다. *myhelmtest*와 같은 릴리스 이름을 지정하거나 매개 변수를 전달합니다. `--generate-name` 다음은 그 예입니다.

```console
helm install myhelmtest ./hello-world
```

성공적인 차트 설치 후 출력은 다음과 유사합니다.

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

설치를 확인하려면 명령을 `helm get manifest` 실행합니다. 명령은 템플릿 파일에서 YAML `configmap.yaml` 데이터를 반환합니다.

클러스터에서 차트 릴리스를 제거하려면 실행합니다. `helm uninstall`

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>리포지토리에서 Helm 차트 삭제

리포지토리에서 차트를 삭제하려면 [az acr 리포지토리 삭제][az-acr-repository-delete] 명령을 사용합니다. 다음 명령을 실행하고 메시지가 표시되면 작업을 확인합니다.

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>헬름 2 클라이언트 사용

### <a name="prerequisites"></a>사전 요구 사항

- **Azure** 구독의 Azure 컨테이너 레지스트리입니다. 필요한 경우 [Azure 포털](container-registry-get-started-portal.md) 또는 [Azure CLI를](container-registry-get-started-azure-cli.md)사용하여 레지스트리를 만듭니다.
- **Helm 클라이언트 버전 2.11.0(RC 버전 아님) 이상** - `helm version`을 실행하여 현재 버전을 찾습니다. Kubernetes 클러스터 내에서 Helm 서버(Tiller)도 초기화해야 합니다. 필요한 경우 [Azure Kubernetes 서비스 클러스터를][aks-quickstart]만듭니다. Helm을 설치하고 업그레이드하는 방법에 대한 자세한 내용은 [Helm 설치][helm-install-v2]를 참조하세요.
- **Azure CLI 버전 2.0.46 이상** - `az --version`을 실행하여 버전을 확인합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="high-level-workflow"></a>높은 수준의 워크플로우

**투구 2** 당신과 함께 :

* Azure 컨테이너 레지스트리를 *단일* Helm 차트 리포지토리로 구성합니다. Azure 컨테이너 레지스트리는 리포지토리에 차트를 추가하고 제거할 때 인덱스 정의를 관리합니다.
* Azure CLI를 통해 Azure 컨테이너 레지스트리를 인증한 다음 Helm 클라이언트를 레지스트리 URI 및 자격 증명으로 자동으로 업데이트합니다. 이 레지스트리 정보를 수동으로 지정할 필요가 없으므로 명령 기록에 자격 증명이 노출되지 않습니다.
* Azure CLI의 [az acr helm][az-acr-helm] 명령을 사용하여 Azure 컨테이너 레지스트리를 Helm 차트 리포지토리로 추가하고 차트를 푸시및 관리합니다. 이러한 Azure CLI 명령은 Helm 2 클라이언트 명령을 래핑합니다.
* Azure 컨테이너 레지스트리의 차트 리포지토리를 로컬 Helm 리포지토리 인덱스에 추가하여 차트 검색을 지원합니다.
* 로컬 `helm install` 리포지토리 캐시에서 Kubernetes 클러스터에 차트를 설치하는 데 사용합니다.

예제는 다음 섹션을 참조하십시오.

### <a name="add-repository-to-helm-client"></a>Helm 클라이언트에 리포지토리 추가

[az acr helm repo 추가][az-acr-helm-repo-add] 명령을 사용하여 Azure 컨테이너 레지스트리 헬름 차트 리포지토리를 Helm 클라이언트에 추가합니다. 이 명령은 Helm 클라이언트에 사용되는 Azure Container Registry용 인증 토큰을 가져옵니다. 인증 토큰은 3시간 동안 유효합니다. `docker login`과 마찬가지로 이 명령도 이후 CLI 세션에서 실행하여 Azure Container Registry Helm 차트 리포지토리에 Helm 클라이언트를 인증할 수 있습니다.

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>리포지토리에 샘플 차트 추가

먼저 *~ /acr-helm에서*로컬 디렉토리를 만든 다음 기존 *안정 / 워드 프레스* 차트를 다운로드하십시오.

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

다운로드한 차트를 나열하려면 입력하고 `ls` 파일 이름에 포함된 워드프레스 버전을 기록합니다. `helm fetch stable/wordpress` 명령은 특정 버전을 지정하지 않았으므로 *최신* 버전을 가져왔습니다. 다음 예제 출력에서 워드 프레스 차트는 버전 *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Azure CLI의 [az acr helm 푸시][az-acr-helm-push] 명령을 사용하여 Azure 컨테이너 레지스트리의 Helm 차트 리포지토리로 차트를 푸시합니다. *워드 프레스-8.1.0.tgz와*같은 이전 단계에서 다운로드 한 Helm 차트의 이름을 지정합니다.

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

잠시 후 Azure CLI는 다음 예제 출력과 같이 차트가 저장된다고 보고합니다.

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>리포지토리의 차트 목록 표시

이전 단계에서 업로드한 차트를 사용하려면 로컬 Helm 리포지토리 인덱스를 업데이트해야 합니다. Helm 클라이언트를 리포지토리를 다시 인덱싱할 수도 있고 Azure CLI를 사용하여 리포지토리 인덱스를 업데이트할 수도 있습니다. 리포지토리에 차트를 추가할 때마다 이 단계를 완료해야 합니다.

```azurecli
az acr helm repo add --name mycontainerregistry
```

리포지토리에 차트가 저장되어 있고 업데이트된 인덱스를 로컬에서 사용할 수 있으면 일반 Helm 클라이언트 명령을 사용해 차트를 검색하거나 설치할 수 있습니다. 리포지토리의 모든 차트를 보려면 `helm search` 명령을 사용하여 고유한 Azure 컨테이너 레지스트리 이름을 제공합니다.

```console
helm search mycontainerregistry
```

다음 예제 출력과 같이 이전 단계에서 푸시한 Wordpress 차트가 나열됩니다.

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

Azure CLI에서 [az acr helm list][az-acr-helm-list]를 사용하여 차트 목록을 표시할 수도 있습니다.

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Helm 차트의 정보 표시

리포지토리에서 특정 차트에 대한 정보를 보려면 `helm inspect` 명령을 사용할 수 있습니다.

```console
helm inspect mycontainerregistry/wordpress
```

버전 번호를 제공하지 않으면 *최신* 버전이 사용됩니다. 다음의 축소된 예제 출력과 같이 Helm이 차트에 대한 세부 정보를 반환합니다.

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Azure CLI [az acr helm show][az-acr-helm-show] 명령을 사용하여 차트의 정보를 표시할 수도 있습니다. 이 명령을 사용하는 경우에도 마찬가지로 차트의 *최신* 버전이 기본적으로 반환됩니다. `--version` *8.1.0과*같은 특정 버전의 차트를 나열하기 위해 추가할 수 있습니다.

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>리포지토리에서 Helm 차트 설치

리포지토리의 Helm 차트는 리포지토리 이름과 차트 이름을 지정하여 설치됩니다. Helm 클라이언트를 사용하여 Wordpress 차트를 설치하려면 다음 명령을 사용합니다.

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Azure Container Registry Helm 차트 리포지토리로 푸시한 후 나중에 새 CLI 세션으로 돌아오는 경우 로컬 Helm 클라이언트에 업데이트된 인증 토큰이 필요합니다. 새 인증 토큰을 받으려면 [az acr helm repo add][az-acr-helm-repo-add] 명령을 사용합니다.

설치 프로세스 중에는 다음 단계가 완료됩니다.

- Helm 클라이언트가 로컬 리포지토리 인덱스를 검색합니다.
- Azure Container Registry 리포지토리에서 해당 차트가 다운로드됩니다.
- Kubernetes 클러스터의 Tiller를 사용하여 차트가 배포됩니다.

설치가 진행됨에 따라 명령 출력의 지침에 따라 WorPress URL 및 자격 증명을 확인합니다. `kubectl get pods` 명령을 실행하여 Helm 차트를 통해 배포된 Kubernetes 리소스를 볼 수도 있습니다.

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>리포지토리에서 Helm 차트 삭제

리포지토리에서 차트를 삭제하려면 [az acr helm delete][az-acr-helm-delete] 명령을 사용합니다. 차트 이름(예: *워드프레스*및 *삭제할 버전)(예: 8.1.0)을*지정합니다.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

이름을 지정한 차트의 모든 버전을 삭제하려는 경우 `--version` 매개 변수를 제거합니다.

를 실행할 `helm search`때 차트가 계속 반환됩니다. 여기서도 Helm 클라이언트는 리포지토리에서 사용 가능한 차트 목록을 자동으로 업데이트하지 않습니다. Helm 클라이언트 리포지토리 인덱스를 업데이트하려면 다시 [az acr helm repo add][az-acr-helm-repo-add] 명령을 사용합니다.

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>다음 단계

* Helm 차트를 만들고 배포하는 방법에 대한 자세한 내용은 [Helm 차트 개발][develop-helm-charts]을 참조하세요.
* [Azure Kubernetes 서비스(AKS)에서](../aks/kubernetes-helm.md)Helm을 사용 하 여 응용 프로그램 설치에 대해 자세히 알아봅니다.
* Helm 차트는 컨테이너 빌드 프로세스의 일부로 사용할 수 있습니다. 자세한 내용은 [Azure 컨테이너 레지스트리 작업 사용을][acr-tasks]참조하십시오.

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
