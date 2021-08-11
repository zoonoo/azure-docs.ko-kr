---
title: 퍼블릭 레지스트리 콘텐츠를 관리하는 작업 워크플로
description: 자동화된 Azure Container Registry 작업 워크플로를 만들어 프라이빗 Azure Container Registry에서 퍼블릭 이미지 콘텐츠를 추적, 관리, 사용합니다.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 4fba6290b4973e797c13943fc9be4fadb19f3274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349285"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Azure Container Registry 작업을 사용하여 퍼블릭 콘텐츠를 사용하고 유지 관리하는 방법

이 문서에서는 퍼블릭 콘텐츠를 사용하고 유지 관리하는 데 도움이 되는 Azure Container Registry의 샘플 워크플로를 제공합니다.

1. 종속 퍼블릭 이미지의 로컬 복사본을 가져옵니다.
1. 보안 검사와 기능 테스트를 통해 퍼블릭 이미지의 유효성을 검사합니다.
1. 내부 사용을 위해 이미지를 프라이빗 레지스트리로 승격합니다.
1. 퍼블릭 콘텐츠에 종속된 애플리케이션의 기본 이미지 업데이트를 트리거합니다.
1. [Azure Container Registry 작업](container-registry-tasks-overview.md)을 사용하여 이 워크플로를 자동화합니다.

워크플로는 다음 이미지에 요약되어 있습니다.

![퍼블릭 콘텐츠 사용 워크플로](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

제어된 가져오기 워크플로를 사용하여 외부에서 관리형 아티팩트에 대한 조직의 종속성을 관리할 수 있습니다(예: [Docker Hub][docker-hub], [GCR][gcr], [Quay][quay], [GitHub Container Registry][ghcr], [Microsoft Container Registry][mcr] 또는 기타 [Azure 컨테이너 레지스트리][acr]도 포함하여 퍼블릭 레지스트리에서 소싱된 이미지). 

퍼블릭 콘텐츠에 대한 종속성으로 인해 발생하는 위험과 Azure Container Registry를 사용하여 위험을 완화하는 방법에 관한 배경은 [OCI 퍼블릭 콘텐츠 사용 블로그 게시물][oci-consuming-public-content] 및 [Azure Container Registry를 사용하여 퍼블릭 콘텐츠 관리](buffer-gate-public-content.md)를 참조하세요.

Azure Cloud Shell을 사용하거나 Azure CLI의 로컬 설치를 사용하여 이 연습을 완료할 수 있습니다. Azure CLI 버전 2.10 이상이 권장됩니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-cli]를 참조하세요.

## <a name="scenario-overview"></a>시나리오 개요

![가져오기 워크플로 구성 요소](./media/tasks-consume-public-content/consuming-public-content-objects.png)

이 연습에서는 다음을 설정합니다.

1. 다음을 나타내는 **컨테이너 레지스트리** 3개:
   * 기본 이미지 변경을 지원하기 위한 시뮬레이션된 [Docker Hub][docker-hub](`publicregistry`)
   * 프라이빗 이미지를 공유하기 위한 팀 레지스트리(`contoso`)
   * 가져온 퍼블릭 콘텐츠에 대한 회사/팀 공유 레지스트리(`baseartifacts`)
1. 각 레지스트리의 **ACR 작업**. 작업은 다음을 수행합니다.  
   1. 시뮬레이션된 퍼블릭 `node` 이미지 빌드
   1. `node` 이미지를 회사/팀 공유 레지스트리로 가져오고 유효성 검사
   1. `hello-world` 이미지 빌드 및 배포
1. 다음의 구성을 포함한 **ACR 작업 정의**:
1. 키 자격 증명 모음에 대한 포인터인 **레지스트리 자격 증명** 의 컬렉션
1. `acr-task.yaml` 내에서 사용할 수 있는 키 자격 증명 모음에 대한 포인터인 **비밀** 의 컬렉션
1. `acr-task.yaml` 내에서 사용되는 **구성된 값** 의 컬렉션
1. 모든 비밀을 보호하기 위한 **Azure Key Vault**
1. `hello-world` 빌드 애플리케이션을 호스트하는 **Azure Container Instance**

## <a name="prerequisites"></a>사전 요구 사항

다음 단계에서는 연습에서 만들고 사용한 리소스의 값을 구성합니다.

### <a name="set-environment-variables"></a>환경 변수 설정

환경에 고유한 변수를 구성합니다. 실수로 인한 삭제를 최소화하기 위해 지속형 콘텐츠가 있는 리소스를 자체 리소스 그룹에 배치하는 모범 사례를 따릅니다. 그러나 원하는 경우 해당 리소스를 단일 리소스 그룹에 배치할 수 있습니다.

이 문서의 예제는 Bash 셸용으로 형식이 지정되어 있습니다.

```bash
# Set the three registry names, must be globally unique:
REGISTRY_PUBLIC=publicregistry
REGISTRY_BASE_ARTIFACTS=contosobaseartifacts
REGISTRY=contoso

# set the location all resources will be created in:
RESOURCE_GROUP_LOCATION=eastus

# default resource groups
REGISTRY_PUBLIC_RG=${REGISTRY_PUBLIC}-rg
REGISTRY_BASE_ARTIFACTS_RG=${REGISTRY_BASE_ARTIFACTS}-rg
REGISTRY_RG=${REGISTRY}-rg

# fully qualified registry urls
REGISTRY_DOCKERHUB_URL=docker.io
REGISTRY_PUBLIC_URL=${REGISTRY_PUBLIC}.azurecr.io
REGISTRY_BASE_ARTIFACTS_URL=${REGISTRY_BASE_ARTIFACTS}.azurecr.io
REGISTRY_URL=${REGISTRY}.azurecr.io

# Azure key vault for storing secrets, name must be globally unique
AKV=acr-task-credentials
AKV_RG=${AKV}-rg

# ACI for hosting the deployed application
ACI=hello-world-aci
ACI_RG=${ACI}-rg
```

### <a name="git-repositories-and-tokens"></a>Git 리포지토리 및 토큰

환경을 시뮬레이션하려면 다음 Git 리포지토리를 각각 관리 가능한 리포지토리로 포크합니다. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

그런 다음, 포크된 리포지토리에 대해 다음 변수를 업데이트합니다.

Git URL의 끝에 추가된 `:main`은 기본 리포지토리 분기를 나타냅니다.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

ACR 작업이 Git 웹후크를 복제하고 설정하려면 [GitHub 액세스 토큰(PAT)][git-token]이 필요합니다. 프라이빗 리포지토리에 필요한 권한이 있는 토큰을 만드는 단계는 [GitHub 액세스 토큰 만들기](container-registry-tutorial-build-task.md#create-a-github-personal-access-token)를 참조하세요. 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Docker Hub 자격 증명  
Docker Hub에서 이미지를 끌어올 때 제한 및 ID 요청을 방지하려면 [Docker Hub 토큰][docker-hub-tokens]을 만듭니다. 다음으로, 다음 환경 변수를 설정합니다.

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>레지스트리 만들기

Azure CLI 명령을 사용하여 각각 자체 리소스 그룹에 세 개의 프리미엄 계층 컨테이너 레지스트리를 만듭니다.

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>키 자격 증명 모음 만들기 및 비밀 설정

키 자격 증명 모음을 만듭니다.

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

키 자격 증명 모음에서 Docker Hub 사용자 이름과 토큰을 설정합니다.

```azurecli-interactive
az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-user \
--value $REGISTRY_DOCKERHUB_USER

az keyvault secret set \
--vault-name $AKV \
--name registry-dockerhub-password \
--value $REGISTRY_DOCKERHUB_PASSWORD
```

키 자격 증명 모음에서 Git PAT를 설정하고 확인합니다.

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Azure Container Instance의 리소스 그룹 만들기

이 리소스 그룹은 `hello-world` 이미지를 배포할 때 이후 작업에서 사용됩니다.

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>퍼블릭 `node` 기본 이미지 만들기

Docker Hub에서 `node` 이미지를 시뮬레이션하려면 퍼블릭 이미지를 빌드하고 유지 관리하는 [ACR 작업][acr-task]을 만듭니다. 이 설정을 사용하면 `node` 이미지 유지 관리자에 의한 변경 내용을 시뮬레이션할 수 있습니다.

```azurecli-interactive
az acr task create \
  --name node-public \
  -r $REGISTRY_PUBLIC \
  -f acr-task.yaml \
  --context $GIT_BASE_IMAGE_NODE \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_DOCKERHUB_URL}/ \
  --assign-identity
```

Docker 제한을 방지하려면 [Docker Hub 자격 증명][docker-hub-tokens]을 작업에 추가합니다. [acr task credentials][acr-task-credentials] 명령을 사용하여 Docker Hub를 포함한 모든 레지스트리에 Docker 자격 증명을 전달할 수 있습니다.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

키 자격 증명 모음에서 값을 읽을 수 있는 액세스 권한을 작업에 부여합니다.

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name node-public \
                  --registry $REGISTRY_PUBLIC \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

Git 커밋, 기본 이미지 업데이트, 타이머 또는 수동 실행을 통해 [작업을 트리거할 수 있습니다][acr-task-triggers]. 

수동으로 작업을 실행하여 `node` 이미지를 생성합니다.

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

시뮬레이션된 퍼블릭 레지스트리에 이미지를 나열합니다.

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>`hello-world` 이미지 만들기

시뮬레이션된 퍼블릭 `node` 이미지를 기반으로 `hello-world` 이미지를 빌드합니다.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>시뮬레이션된 퍼블릭 레지스트리에 대한 풀 액세스를 위한 토큰 만들기

범위가 `pull`로 지정된 시뮬레이션된 퍼블릭 레지스트리에 대한 [액세스 토큰][acr-tokens]을 만듭니다. 그런 다음, 키 자격 증명 모음에서 액세스 토큰을 설정합니다.

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-user" \
  --value "registry-${REGISTRY_PUBLIC}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_PUBLIC}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_PUBLIC}-user" \
              --registry $REGISTRY_PUBLIC \
              --scope-map _repositories_pull \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Azure Container Instances의 풀 액세스를 위한 토큰 만들기

풀로 범위가 지정된 `hello-world` 이미지를 호스트하는 레지스트리에 대한 [액세스 토큰][acr-tokens]을 만듭니다. 그런 다음, 키 자격 증명 모음에서 액세스 토큰을 설정합니다.

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-user" \
  --value "registry-${REGISTRY}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY}-user" \
              --registry $REGISTRY \
              --repository hello-world content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>`hello-world` 이미지를 빌드하고 유지 관리하는 작업 만들기

다음 명령은 `hello-world` 리포지토리의 `acr-tasks.yaml`에 있는 정의에서 작업을 만듭니다. 작업 단계에서는 `hello-world` 이미지를 빌드한 다음, Azure Container Instances에 배포합니다. Azure Container Instances의 리소스 그룹은 이전 섹션에서 만들었습니다. `image:tag`만 다른 작업에서 `az container create`를 호출하면 작업은 이 연습 전체에서 동일한 인스턴스에 배포합니다.

```azurecli-interactive
az acr task create \
  -n hello-world \
  -r $REGISTRY \
  -f acr-task.yaml \
  --context $GIT_HELLO_WORLD \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --set KEYVAULT=$AKV \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG \
  --assign-identity
```

시뮬레이션된 퍼블릭 레지스트리에 대한 작업에 자격 증명을 추가합니다.

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

키 자격 증명 모음에서 값을 읽을 수 있는 액세스 권한을 작업에 부여합니다.

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name hello-world \
                  --registry $REGISTRY \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

리소스 그룹에 대한 액세스 권한을 부여하여 Azure Container Instances를 만들고 관리할 수 있는 액세스 권한을 작업에 부여합니다.

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

작업이 생성되고 구성되면 `hello-world` 이미지를 빌드하고 배포하는 작업을 실행합니다.

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

생성된 후 `hello-world` 이미지를 호스트하는 컨테이너의 IP 주소를 가져옵니다.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

브라우저에서 IP 주소로 이동하여 실행 중인 애플리케이션을 확인합니다.

## <a name="update-the-base-image-with-a-questionable-change"></a>“의심 스러운” 변경 내용으로 기본 이미지 업데이트

이 섹션에서는 환경에서 문제를 일으킬 수 있는 기본 이미지의 변경 내용을 시뮬레이션합니다.

1. 포크된 `base-image-node` 리포지토리에서 `Dockerfile`을 엽니다.
1. `BACKGROUND_COLOR`를 `Orange`로 변경하여 변경 내용을 시뮬레이션합니다.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

변경 내용을 커밋하고 ACR 작업이 자동으로 빌드를 시작하는지 감시합니다.

작업이 실행을 시작하는지 감시합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

결과적으로 `Commit`의 트리거를 기반으로 하는 상태 `Succeeded`가 표시됩니다.

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

**Ctrl+C** 를 입력하여 watch 명령을 종료한 다음, 가장 최근 실행의 로그를 봅니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

`node` 이미지가 완료되면 ACR 작업이 `hello-world` 이미지 빌드를 자동으로 시작하는지 `watch`합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

결과적으로 `Image Update`의 트리거를 기반으로 하는 상태 `Succeeded`가 표시됩니다.

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

**Ctrl+C** 를 입력하여 watch 명령을 종료한 다음, 가장 최근 실행의 로그를 봅니다.

```azurecli-interactive
az acr task logs -r $REGISTRY
```

완료되면 업데이트된 `hello-world` 이미지를 호스트하는 사이트의 IP 주소를 가져옵니다.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

브라우저에서 주황색(의심스러운) 배경이 있는 사이트로 이동합니다.

### <a name="checking-in"></a>체크 인

이제 Git 커밋과 기본 `node` 이미지의 변경 내용을 기반으로 자동으로 빌드되는 `hello-world` 이미지를 만들었습니다. 이 예제에서 작업은 Azure Container Registry의 기본 이미지에 대해 빌드되지만 지원되는 모든 레지스트리를 사용할 수 있습니다.

기본 이미지 업데이트는 `node` 이미지가 업데이트될 때 작업 실행을 자동으로 다시 트리거합니다. 여기서 볼 수 있듯이 일부 업데이트는 필요하지 않습니다.

## <a name="gated-imports-of-public-content"></a>퍼블릭 콘텐츠의 제어된 가져오기

업스트림 변경으로 인해 중요한 워크로드가 중단되는 것을 방지하기 위해 보안 검사 및 기능 테스트를 추가할 수 있습니다.

이 섹션에서는 다음을 수행하는 ACR 작업을 만듭니다.

* 테스트 이미지 빌드
* 테스트 이미지에 대해 기능 테스트 스크립트 `./test.sh` 실행
* 이미지가 성공적으로 테스트되면 퍼블릭 이미지를 **baseimages** 레지스트리로 가져오기

### <a name="add-automation-testing"></a>자동화 테스트 추가

업스트림 콘텐츠를 제어하기 위해 자동화된 테스트를 구현합니다. 이 예제에서는 `$BACKGROUND_COLOR`를 확인하는 `test.sh`가 제공됩니다. 테스트에 실패하면 `1`의 `EXIT_CODE`가 반환되어 ACR 작업 단계가 실패하고 결국 작업 실행이 종료됩니다. 로깅 결과를 포함하여 모든 형태의 도구에서 테스트를 확장할 수 있습니다. 게이트는 여기서 재현되는 스크립트의 통과/실패 응답에 의해 관리됩니다.

```bash
if [ ""$(echo $BACKGROUND_COLOR | tr '[:lower:]' '[:upper:]') = 'RED' ]; then
    echo -e "\e[31mERROR: Invalid Color:\e[0m" ${BACKGROUND_COLOR}
    EXIT_CODE=1
else
  echo -e "\e[32mValidation Complete - No Known Errors\e[0m"
fi
exit ${EXIT_CODE}
```
### <a name="task-yaml"></a>작업 YAML 

다음 단계를 수행하는 `import-baseimage-node` 리포지토리의 `acr-task.yaml`을 검토합니다.

1. 다음 Dockerfile을 사용하여 테스트 기본 이미지를 빌드합니다.
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. 완료되면 `./test.sh`를 실행하는 컨테이너를 실행하여 이미지의 유효성을 검사합니다.
1. 성공적으로 완료된 경우에만 `when: ['validate-base-image']`를 사용하여 제어되는 가져오기 단계를 실행합니다.

```yaml
version: v1.1.0
steps:
  - id: build-test-base-image
    # Build off the base image we'll track
    # Add a test script to do unit test validations
    # Note: the test validation image isn't saved to the registry
    # but the task logs captures log validation results
    build: >
      --build-arg REGISTRY_FROM_URL={{.Values.REGISTRY_FROM_URL}}
      -f ./Dockerfile
      -t {{.Run.Registry}}/node-import:test
      .
  - id: validate-base-image
    # only continues if node-import:test returns a non-zero code
    when: ['build-test-base-image']
    cmd: "{{.Run.Registry}}/node-import:test"
  - id: pull-base-image
    # import the public image to base-artifacts
    # Override the stable tag,
    # and create a unique tag to enable rollback
    # to a previously working image
    when: ['validate-base-image']
    cmd: >
        docker pull {{.Values.REGISTRY_FROM_URL}}node:15-alpine
  - id: retag-base-image
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine
  - id: retag-base-image-unique-tag
    when: ['pull-base-image']
    cmd: docker tag {{.Values.REGISTRY_FROM_URL}}node:15-alpine {{.Run.Registry}}/node:15-alpine-{{.Run.ID}}
  - id: push-base-image
    when: ['retag-base-image', 'retag-base-image-unique-tag']
    push:
    - "{{.Run.Registry}}/node:15-alpine"
    - "{{.Run.Registry}}/node:15-alpine-{{.Run.ID}}"
```

### <a name="create-task-to-import-and-test-base-image"></a>기본 이미지를 가져오고 테스트하는 작업 만들기

```azurecli-interactive
  az acr task create \
  --name base-import-node \
  -f acr-task.yaml \
  -r $REGISTRY_BASE_ARTIFACTS \
  --context $GIT_NODE_IMPORT \
  --git-access-token $(az keyvault secret show \
                        --vault-name $AKV \
                        --name github-token \
                        --query value -o tsv) \
  --set REGISTRY_FROM_URL=${REGISTRY_PUBLIC_URL}/ \
  --assign-identity
```

시뮬레이션된 퍼블릭 레지스트리에 대한 작업에 자격 증명을 추가합니다.

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

키 자격 증명 모음에서 값을 읽을 수 있는 액세스 권한을 작업에 부여합니다.

```azurecli-interactive
az keyvault set-policy \
  --name $AKV \
  --resource-group $AKV_RG \
  --object-id $(az acr task show \
                  --name base-import-node \
                  --registry $REGISTRY_BASE_ARTIFACTS \
                  --query identity.principalId --output tsv) \
  --secret-permissions get
```

가져오기 작업을 실행합니다.

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> `./test.sh: Permission denied`로 인해 작업이 실패하는 경우 스크립트에 실행 권한이 있는지 확인하고 Git 리포지토리로 다시 커밋합니다.
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>제어된 `node` 이미지에서 빌드하도록 `hello-world` 이미지 업데이트

`node` 리포지토리에서 `read`로 범위가 지정된 base-artifacts 레지스트리에 액세스하기 위한 [액세스 토큰][acr-tokens]을 만듭니다. 그런 다음, 키 자격 증명 모음에서 다음을 설정합니다.

```azurecli-interactive
az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
  --value "registry-${REGISTRY_BASE_ARTIFACTS}-user"

az keyvault secret set \
  --vault-name $AKV \
  --name "registry-${REGISTRY_BASE_ARTIFACTS}-password" \
  --value $(az acr token create \
              --name "registry-${REGISTRY_BASE_ARTIFACTS}-user" \
              --registry $REGISTRY_BASE_ARTIFACTS \
              --repository node content/read \
              -o tsv \
              --query credentials.passwords[0].value)
```

기본 아티팩트 레지스트리에 대한 **hello-world** 작업에 자격 증명을 추가합니다.

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

`BASE_ARTIFACTS` 레지스트리를 사용하도록 `REGISTRY_FROM_URL`을 변경하는 작업을 업데이트합니다.

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

**hello-world** 작업을 실행하여 기본 이미지 종속성을 변경합니다.

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>“유효한” 변경 내용으로 기본 이미지 업데이트

1. `base-image-node` 리포지토리에서 `Dockerfile`을 엽니다.
1. `BACKGROUND_COLOR`를 `Green`으로 변경하여 유효한 변경 내용을 시뮬레이션합니다.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

변경 내용을 커밋하고 업데이트 시퀀스를 모니터링합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

실행되면 **Ctrl+C** 를 입력하고 로그를 모니터링합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

완료되면 **base-image-import** 작업을 모니터링합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

실행되면 **Ctrl+C** 를 입력하고 로그를 모니터링합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

완료되면 **hello-world** 작업을 모니터링합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

실행되면 **Ctrl+C** 를 입력하고 로그를 모니터링합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY
```

완료되면 업데이트된 `hello-world` 이미지를 호스트하는 사이트의 IP 주소를 가져옵니다.

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

브라우저에서 녹색(유효한) 배경이 있는 사이트로 이동합니다.

### <a name="view-the-gated-workflow"></a>제어된 워크플로 보기

배경색이 빨간색인 이전 섹션의 단계를 다시 수행합니다.

1. `base-image-node` 리포지토리에서 `Dockerfile`을 엽니다.
1. `BACKGROUND_COLOR`를 `Red`로 변경하여 잘못된 변경 내용을 시뮬레이션합니다.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

변경 내용을 커밋하고 업데이트 시퀀스를 모니터링합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

실행되면 **Ctrl+C** 를 입력하고 로그를 모니터링합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

완료되면 **base-image-import** 작업을 모니터링합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

실행되면 **Ctrl+C** 를 입력하고 로그를 모니터링합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

이제 **base-import-node** 작업이 유효성 검사에 실패하고 `hello-world` 업데이트를 게시하는 시퀀스가 중지됩니다. 출력은 다음과 비슷합니다.

```console
[...]
2020/10/30 03:57:39 Launching container with name: validate-base-image
Validating Image
NODE_VERSION: 15-alpine
BACKGROUND_COLOR: Red
ERROR: Invalid Color: Red
2020/10/30 03:57:40 Container failed during run: validate-base-image. No retries remaining.
failed to run step ID: validate-base-image: exit status 1
```

### <a name="publish-an-update-to-hello-world"></a>`hello-world` 업데이트 게시

`hello-world` 이미지의 변경 내용은 마지막으로 유효성이 검사된 `node` 이미지를 계속 사용합니다.

제어된 유효성 검사를 통과하는 기본 `node` 이미지의 추가 변경 내용은 `hello-world` 이미지의 기본 이미지 업데이트를 트리거합니다.

## <a name="cleaning-up"></a>정리

더 이상 필요하지 않은 경우 이 문서에서 사용된 리소스를 삭제합니다.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>다음 단계

이 문서의 내용. ACR 작업을 사용하여 환경에 업데이트된 기본 이미지를 도입하는 자동화된 제어 워크플로를 만들었습니다. Azure Container Registry에서 이미지를 관리하기 위한 관련 정보를 확인합니다.


* [컨테이너 이미지 태그 지정 및 버전 관리에 대한 권장 사항](container-registry-image-tag-version.md)
* [Azure 컨테이너 레지스트리의 컨테이너 이미지 잠금](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         ./container-registry-repository-scoped-permissions.md
[acr-task]:                     ./container-registry-tasks-overview.md
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   ./container-registry-repository-scoped-permissions.md
[aci]:                          https://aka.ms/aci
[alpine-public-image]:          https://hub.docker.com/_/alpine
[docker-hub]:                   https://hub.docker.com
[docker-hub-tokens]:            https://hub.docker.com/settings/security
[git-token]:                    https://github.com/settings/tokens
[gcr]:                          https://cloud.google.com/container-registry
[ghcr]:                         https://docs.github.com/en/free-pro-team@latest/packages/getting-started-with-github-container-registry/about-github-container-registry
[helm-charts]:                  https://helm.sh
[mcr]:                          https://aka.ms/mcr
[nginx-public-image]:           https://hub.docker.com/_/nginx
[oci-artifacts]:                ./container-registry-oci-artifacts.md
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io