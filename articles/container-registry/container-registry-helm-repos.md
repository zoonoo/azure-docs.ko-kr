---
title: 매장 투구 차트
description: Azure Container Registry 리포지토리를 사용 하 여 Kubernetes 응용 프로그램에 대 한 투구 차트를 저장 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 26588bb4dc3cf50656103b50d5d0559908a1ccb7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524634"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Azure container registry에 투구 차트 푸시 및 끌어오기

Kubernetes에 대 한 응용 프로그램을 신속 하 게 관리 하 고 배포 하기 위해 [오픈 소스 투구 패키지 관리자][helm]를 사용할 수 있습니다. 투구를 사용 하는 경우 응용 프로그램 패키지는 [차트로](https://helm.sh/docs/topics/charts/)수집 되 고, [투구 차트 리포지토리에](https://helm.sh/docs/topics/chart_repository/)저장 됩니다.

이 문서에서는 투구 3 또는 투구 2 설치를 사용 하 여 Azure container registry의 리포지토리에서 투구 차트를 호스트 하는 방법을 보여 줍니다. 이 예에서는 공용 투구 *안정적인* 리포지토리의 기존 투구 차트를 저장 합니다. 대부분의 시나리오에서 개발 하는 응용 프로그램에 대 한 고유한 차트를 빌드하고 업로드 합니다. 사용자 고유의 투구 차트를 작성 하는 방법에 대 한 자세한 내용은 [차트 템플릿 개발자 가이드][develop-helm-charts]를 참조 하세요.

> [!IMPORTANT]
> Azure Container Registry의 투구 차트는 현재 미리 보기로 제공 됩니다. 추가 [사용 약관][terms-of-use]에 동의 하는 조건에 따라 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="helm-3-or-helm-2"></a>투구 3 또는 투구 2?

투구 차트를 저장, 관리 및 설치 하려면 투구 클라이언트 및 투구 CLI를 사용 합니다. 투구 클라이언트의 주요 릴리스에는 투구 3 및 투구 2가 포함 됩니다. 투구 3은 새로운 차트 형식을 지원 하며 더 이상 Tiller 서버 쪽 구성 요소를 설치 하지 않습니다. 버전 차이점에 대 한 자세한 내용은 [버전 FAQ](https://helm.sh/docs/faq/)를 참조 하십시오. 이전에 투구 2 차트를 배포한 경우 [v3로 투구 V2 마이그레이션](https://helm.sh/docs/topics/v2_v3_migration/)을 참조 하세요.

투구 3 또는 투구 2를 사용 하 여 각 버전에 특정 한 워크플로와 함께 Azure Container Registry에서 투구 차트를 호스트할 수 있습니다.

* [투구 3 클라이언트](#use-the-helm-3-client) -`helm chart` 명령을 사용 하 여 레지스트리에서 차트를 [OCI 아티팩트](container-registry-image-formats.md#oci-artifacts) 로 관리 합니다.
* [투구 2 클라이언트](#use-the-helm-2-client) -Azure CLI에서 [az acr 투구][az-acr-helm] 명령을 사용 하 여 컨테이너 레지스트리를 투구 차트 리포지토리로 추가 하 고 관리 합니다.

### <a name="additional-information"></a>추가 정보

* 차트를 OCI 아티팩트로 관리 하는 기본 `helm chart` 명령을 사용 하 여 투구 3 워크플로를 사용 하는 것이 좋습니다.
* 투구 3 클라이언트 및 차트에서 레거시 [az acr 투구][az-acr-helm] Azure CLI 명령 및 워크플로를 사용할 수 있습니다. 그러나 `az acr helm list`와 같은 특정 명령은 투구 3 차트와 호환 되지 않습니다.
* 투구 3부터 [az acr 투구][az-acr-helm] 명령은 주로 투구 2 클라이언트 및 차트 형식과의 호환성을 위해 지원 됩니다. 이러한 명령에 대 한 향후 개발은 현재 계획 되지 않았습니다.

## <a name="use-the-helm-3-client"></a>투구 3 클라이언트 사용

### <a name="prerequisites"></a>사전 요구 사항

- Azure 구독에 있는 **azure container registry** 필요한 경우 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용 하 여 레지스트리를 만듭니다.
- **투구 클라이언트 버전 3.0.0 이상** -`helm version`를 실행 하 여 현재 버전을 찾습니다. 투구 설치 및 업그레이드 방법에 대 한 자세한 내용은 [투구 설치][helm-install]를 참조 하세요.
- 투구 차트를 설치 하는 **Kubernetes 클러스터** 입니다. 필요한 경우 [Azure Kubernetes 서비스 클러스터][aks-quickstart]를 만듭니다. 
- **Azure CLI 버전 2.0.71 이상** -`az --version`를 실행 하 여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="high-level-workflow"></a>개략적인 워크플로

**투구 3** 사용:

* Azure container registry에서 하나 이상의 투구 리포지토리를 만들 수 있습니다.
* 레지스트리에서 투구 3 차트를 [OCI 아티팩트](container-registry-image-formats.md#oci-artifacts)로 저장 합니다. 현재 OCI에 대 한 투구 3 지원은 *실험적*으로 간주 됩니다.
* 투구 CLI에서 직접 `helm chart` 명령을 사용 하 여 레지스트리에서 투구 차트를 푸시, 풀 및 관리 합니다.
* 레지스트리 URI 및 자격 증명을 사용 하 여 자동으로 투구 클라이언트를 업데이트 하는 Azure CLI를 통해 레지스트리를 사용 하 여 인증 합니다. 이 레지스트리 정보를 수동으로 지정할 필요가 없으므로 자격 증명이 명령 기록에 노출 되지 않습니다.
* `helm install`를 사용 하 여 로컬 리포지토리 캐시에서 Kubernetes 클러스터에 차트를 설치 합니다.

예제는 다음 섹션을 참조 하세요.

### <a name="enable-oci-support"></a>OCI 지원 사용

다음 환경 변수를 설정 하 여 투구 3 클라이언트에서 OCI 지원을 사용 하도록 설정 합니다. 현재이 지원은 실험적입니다. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>기존 투구 패키지 가져오기

`stable` 투구 차트 리포지토리를 아직 추가 하지 않은 경우 `helm repo add` 명령을 실행 합니다.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

`stable` 리포지토리에서 차트 패키지를 로컬로 끌어옵니다. 예를 들어 *~/acr-helm*와 같은 로컬 디렉터리를 만든 다음 기존 *스테이블/wordpress* 차트 패키지를 다운로드 합니다. 이 문서의이 예제 및 기타 명령은 Bash 셸에 대해 형식이 지정 됩니다.

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

`helm pull stable/wordpress` 명령은 특정 버전을 지정 하지 않았으므로 *최신* 버전은 `wordpress` 하위 디렉터리에서 끌어오고 압축을 풀었습니다.

### <a name="save-chart-to-local-registry-cache"></a>로컬 레지스트리 캐시에 차트 저장

디렉터리를 투구 차트 파일이 포함 된 `wordpress` 하위 디렉터리로 변경 합니다. 그런 다음 `helm chart save`를 실행 하 여 차트의 복사본을 로컬에 저장 하 고 레지스트리의 정규화 된 이름과 대상 리포지토리 및 태그를 사용 하 여 별칭을 만듭니다. 

다음 예제에서 레지스트리 이름은 *mycontainerregistry*이 고, 대상 리포지토리는 *wordpress*이며, 대상 차트 태그는 *최신*이지만 환경에 대 한 값을 대체 합니다.

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

`helm chart list`를 실행 하 여 로컬 레지스트리 캐시에 차트를 저장 했는지 확인 합니다. 출력은 다음과 비슷합니다.

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>Azure Container Registry에 차트 푸시

투구 3 CLI에서 `helm chart push` 명령을 실행 하 여 투구 차트를 Azure container registry의 리포지토리로 푸시합니다. 존재 하지 않는 경우 리포지토리가 만들어집니다.

먼저 Azure CLI 명령 [az acr login][az-acr-login] 을 사용 하 여 레지스트리에 인증 합니다.

```azurecli
az acr login --name mycontainerregistry
```

다음과 같이 정규화 된 대상 리포지토리에 차트를 푸시합니다.

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

푸시가 성공적으로 완료 되 면 출력은 다음과 유사 합니다.

```console
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>리포지토리의 차트 목록 표시

Azure container registry에 저장 된 이미지와 마찬가지로 [az acr repository][az-acr-repository] 명령을 사용 하 여 차트를 호스트 하는 리포지토리와 차트 태그 및 매니페스트를 표시할 수 있습니다. 

예를 들어, [az acr repository show][az-acr-repository-show] 를 실행 하 여 이전 단계에서 만든 리포지토리의 속성을 볼 수 있습니다.

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
```

출력은 다음과 비슷합니다.

```console
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

[Az acr repository show-manifest][az-acr-repository-show-manifests] 명령을 실행 하 여 리포지토리에 저장 된 차트의 세부 정보를 확인 합니다. 다음은 그 예입니다.

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

이 예에서 간략하게 설명 하는 출력은 `application/vnd.cncf.helm.config.v1+json``configMediaType`을 보여 줍니다.

```console
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>로컬 캐시로 차트 끌어오기

Kubernetes에 투구 차트를 설치 하려면 차트가 로컬 캐시에 있어야 합니다. 이 예에서는 먼저 `helm chart remove`를 실행 하 여 `mycontainerregistry.azurecr.io/helm/wordpress:latest`이라는 기존 로컬 차트를 제거 합니다.

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

`helm chart pull`를 실행 하 여 Azure container registry에서 로컬 캐시로 차트를 다운로드 합니다.

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>투구 차트 내보내기

차트를 추가로 사용 하려면 `helm chart export`를 사용 하 여 로컬 디렉터리로 내보냅니다. 예를 들어 끌어온 차트를 `install` 디렉터리로 내보냅니다.

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

리포지토리의 내보낸 차트에 대 한 정보를 보려면 차트를 내보낸 디렉터리에서 `helm inspect chart` 명령을 실행 합니다.

```console
cd install
helm inspect chart wordpress
```

버전 번호를 제공하지 않으면 *최신* 버전이 사용됩니다. 투구는 다음 압축 된 출력과 같이 차트에 대 한 자세한 정보를 반환 합니다.

```
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
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
```

### <a name="install-helm-chart"></a>투구 차트 설치

`helm install`를 실행 하 여 로컬 캐시로 끌어온 투구 차트를 설치 하 고 내보낼 수 있습니다. 릴리스 이름을 지정 하거나 `--generate-name` 매개 변수를 전달 하십시오. 다음은 그 예입니다.

```console
helm install wordpress --generate-name
```

설치가 진행 되는 동안 명령 출력의 지침에 따라 WorPress Url 및 자격 증명을 확인 합니다. `kubectl get pods` 명령을 실행 하 여 투구 차트를 통해 배포 된 Kubernetes 리소스를 확인할 수도 있습니다.

```console
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>리포지토리에서 Helm 차트 삭제

리포지토리에서 차트를 삭제 하려면 [az acr repository delete][az-acr-repository-delete] 명령을 사용 합니다. 다음 명령을 실행 하 고 메시지가 표시 되 면 작업을 확인 합니다.

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>투구 2 클라이언트 사용

### <a name="prerequisites"></a>사전 요구 사항

- Azure 구독에 있는 **azure container registry** 필요한 경우 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용 하 여 레지스트리를 만듭니다.
- **Helm 클라이언트 버전 2.11.0(RC 버전 아님) 이상** - `helm version`을 실행하여 현재 버전을 찾습니다. Kubernetes 클러스터 내에서 Helm 서버(Tiller)도 초기화해야 합니다. 필요한 경우 [Azure Kubernetes 서비스 클러스터][aks-quickstart]를 만듭니다. 투구 설치 및 업그레이드 방법에 대 한 자세한 내용은 [투구 설치][helm-install-v2]를 참조 하세요.
- **Azure CLI 버전 2.0.46 이상** - `az --version`을 실행하여 버전을 확인합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="high-level-workflow"></a>개략적인 워크플로

**투구 2** 를 사용 하 여 다음을 수행 합니다.

* Azure container registry를 *단일* 투구 차트 리포지토리로 구성 합니다. Azure Container Registry는 리포지토리에 차트를 추가 하 고 제거할 때 인덱스 정의를 관리 합니다.
* Azure CLI의 [az acr 투구][az-acr-helm] 명령을 사용 하 여 Azure container Registry를 투구 차트 리포지토리로 추가 하 고 차트를 푸시 및 관리 합니다. 이러한 Azure CLI 명령은 투구 2 클라이언트 명령을 래핑합니다.
* Azure container registry의 차트 리포지토리를 로컬 투구 리포지토리 인덱스에 추가 하 고 차트 검색을 지원 합니다.
* Azure CLI를 통해 Azure container registry로 인증 한 다음 레지스트리 URI 및 자격 증명을 사용 하 여 자동으로 투구 클라이언트를 업데이트 합니다. 이 레지스트리 정보를 수동으로 지정할 필요가 없으므로 자격 증명이 명령 기록에 노출 되지 않습니다.
* `helm install`를 사용 하 여 로컬 리포지토리 캐시에서 Kubernetes 클러스터에 차트를 설치 합니다.

예제는 다음 섹션을 참조 하세요.

### <a name="add-repository-to-helm-client"></a>투구 클라이언트에 리포지토리 추가

[Az acr 투구 리포지토리 add][az-acr-helm-repo-add] 명령을 사용 하 여 투구 클라이언트에 Azure Container Registry 투구 차트 리포지토리를 추가 합니다. 이 명령은 Helm 클라이언트에 사용되는 Azure Container Registry용 인증 토큰을 가져옵니다. 인증 토큰은 3 시간 동안 유효 합니다. `docker login`과 마찬가지로 이 명령도 이후 CLI 세션에서 실행하여 Azure Container Registry Helm 차트 리포지토리에 Helm 클라이언트를 인증할 수 있습니다.

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>리포지토리에 차트 추가

먼저 *~/acr-baa*에서 로컬 디렉터리를 만든 다음 기존 *안정적인/wordpress* 차트를 다운로드 합니다.

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

`ls`를 입력 하 여 다운로드 한 차트를 나열 하 고 파일 이름에 포함 된 Wordpress 버전을 확인 합니다. `helm fetch stable/wordpress` 명령은 특정 버전을 지정하지 않았으므로 *최신* 버전을 가져왔습니다. 다음 예제 출력에서 Wordpress 차트는 version *8.1.0*입니다.

```
wordpress-8.1.0.tgz
```

Azure CLI에서 [az acr 투구 push][az-acr-helm-push] 명령을 사용 하 여 Azure Container Registry에서 투구 차트 리포지토리로 차트를 푸시합니다. 이전 단계에서 다운로드 한 투구 차트의 이름 (예: *wordpress-8.1.0. tgz*)을 지정 합니다.

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

몇 분 후에 Azure CLI은 다음 예제 출력과 같이 차트가 저장 되었음을 보고 합니다.

```
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>리포지토리의 차트 목록 표시

이전 단계에서 업로드한 차트를 사용하려면 로컬 Helm 리포지토리 인덱스를 업데이트해야 합니다. Helm 클라이언트를 리포지토리를 다시 인덱싱할 수도 있고 Azure CLI를 사용하여 리포지토리 인덱스를 업데이트할 수도 있습니다. 리포지토리에 차트를 추가할 때마다 이 단계를 완료해야 합니다.

```azurecli
az acr helm repo add --name mycontainerregistry
```

리포지토리에 차트가 저장되어 있고 업데이트된 인덱스를 로컬에서 사용할 수 있으면 일반 Helm 클라이언트 명령을 사용해 차트를 검색하거나 설치할 수 있습니다. 리포지토리의 모든 차트를 보려면 `helm search` 명령을 사용 하 여 고유한 Azure Container Registry 이름을 제공 합니다.

```console
helm search mycontainerregistry
```

다음 예제 출력과 같이 이전 단계에서 푸시한 Wordpress 차트가 나열됩니다.

```
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

[Az acr 투구 list][az-acr-helm-list]를 사용 하 여 Azure CLI 차트를 나열할 수도 있습니다.

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Helm 차트의 정보 표시

리포지토리의 특정 차트에 대 한 정보를 보려면 `helm inspect` 명령을 사용 하면 됩니다.

```console
helm inspect mycontainerregistry/wordpress
```

버전 번호를 제공하지 않으면 *최신* 버전이 사용됩니다. 다음의 축소된 예제 출력과 같이 Helm이 차트에 대한 세부 정보를 반환합니다.

```
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

Azure CLI [az acr 투구 show][az-acr-helm-show] 명령을 사용 하 여 차트에 대 한 정보를 표시할 수도 있습니다. 이 명령을 사용하는 경우에도 마찬가지로 차트의 *최신* 버전이 기본적으로 반환됩니다. *8.1.0*와 같은 특정 버전의 차트를 나열 `--version` 추가할 수 있습니다.

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>리포지토리에서 Helm 차트 설치

리포지토리의 투구 차트는 리포지토리 이름 및 차트 이름을 지정 하 여 설치 됩니다. Helm 클라이언트를 사용하여 Wordpress 차트를 설치하려면 다음 명령을 사용합니다.

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Azure Container Registry Helm 차트 리포지토리로 푸시한 후 나중에 새 CLI 세션으로 돌아오는 경우 로컬 Helm 클라이언트에 업데이트된 인증 토큰이 필요합니다. 새 인증 토큰을 얻으려면 [az acr 투구 리포지토리 add][az-acr-helm-repo-add] 명령을 사용 합니다.

설치 프로세스 중에는 다음 단계가 완료됩니다.

- Helm 클라이언트가 로컬 리포지토리 인덱스를 검색합니다.
- Azure Container Registry 리포지토리에서 해당 차트가 다운로드됩니다.
- Kubernetes 클러스터의 Tiller를 사용하여 차트가 배포됩니다.

설치가 진행 되는 동안 명령 출력의 지침에 따라 WorPress Url 및 자격 증명을 확인 합니다. `kubectl get pods` 명령을 실행 하 여 투구 차트를 통해 배포 된 Kubernetes 리소스를 확인할 수도 있습니다.

```
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>리포지토리에서 Helm 차트 삭제

리포지토리에서 차트를 삭제 하려면 [az acr 투구 delete][az-acr-helm-delete] 명령을 사용 합니다. *Wordpress*와 같은 차트의 이름과 삭제할 버전 (예: *8.1.0*)을 지정 합니다.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

이름을 지정한 차트의 모든 버전을 삭제하려는 경우 `--version` 매개 변수를 제거합니다.

`helm search`를 실행 하면 차트가 계속 반환 됩니다. 여기서도 Helm 클라이언트는 리포지토리에서 사용 가능한 차트 목록을 자동으로 업데이트하지 않습니다. 투구 클라이언트 리포지토리 인덱스를 업데이트 하려면 [az acr 투구 리포지토리 add][az-acr-helm-repo-add] 명령을 다시 사용 합니다.

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 공용 *stable* 리포지토리의 기존 Helm 차트를 사용했습니다. 투구 차트를 만들고 배포 하는 방법에 대 한 자세한 내용은 [투구 차트 개발][develop-helm-charts]을 참조 하세요.

Helm 차트는 컨테이너 빌드 프로세스의 일부로 사용할 수 있습니다. 자세한 내용은 [Azure Container Registry 작업 사용][acr-tasks]을 참조 하세요.

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
