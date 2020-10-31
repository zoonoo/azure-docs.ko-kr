---
title: 공용 레지스트리 콘텐츠를 관리 하는 작업 워크플로
description: 자동 Azure Container Registry 작업 워크플로를 만들어 개인 Azure Container Registry에서 공용 이미지 콘텐츠를 추적 하 고 관리 하 고 사용 합니다.
author: SteveLasker
ms.topic: article
ms.author: stevelas
ms.date: 10/29/2020
ms.custom: ''
ms.openlocfilehash: 261604b66d393723b35b472415b8840b047bc36e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133699"
---
# <a name="how-to-consume-and-maintain-public-content-with-azure-container-registry-tasks"></a>Azure Container Registry 작업을 사용 하 여 공용 콘텐츠를 사용 하 고 유지 관리 하는 방법

이 문서에서는 공용 콘텐츠를 사용 하 고 유지 관리 하는 데 도움이 되는 Azure Container Registry의 샘플 워크플로를 제공 합니다.

1. 종속 된 공용 이미지의 로컬 복사본을 가져옵니다.
1. 보안 검색 및 기능 테스트를 통해 공용 이미지의 유효성을 검사 합니다.
1. 내부 사용을 위해 이미지를 개인 레지스트리로 승격 합니다.
1. 공용 콘텐츠에 따라 응용 프로그램에 대 한 기본 이미지 업데이트를 트리거합니다.
1. [Azure Container Registry 작업](container-registry-tasks-overview.md) 을 사용 하 여이 워크플로를 자동화할 수 있습니다.

워크플로는 다음 그림에 요약 되어 있습니다.

![공용 콘텐츠 사용 워크플로](./media/tasks-consume-public-content/consuming-public-content-workflow.png)

제어 된 가져오기 워크플로는 외부에서 관리 되는 아티팩트에 대 한 조직의 종속성을 관리 하는 데 도움이 됩니다. [예를 들어][quay] [Docker 허브][docker-hub], [gcr][gcr],,, [GitHub Container Registry][ghcr], [Microsoft Container Registry][mcr]또는 기타 [Azure Container][acr]registry를 비롯 한 공용 레지스트리에서 원본 이미지를 관리할 수 있습니다. 

공용 콘텐츠에 대 한 종속성에 의해 발생 하는 위험과이를 완화 하기 위해 Azure Container Registry를 사용 하는 방법에 대 한 배경 정보는 [OCI 사용 공용 콘텐츠 블로그 게시물][oci-consuming-public-content] 및 [Azure Container Registry에서 공용 콘텐츠 관리](buffer-gate-public-content.md)를 참조 하세요.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용 하 여이 연습을 완료할 수 있습니다. Azure CLI 버전 2.10 이상을 권장 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-cli]를 참조하세요.

## <a name="scenario-overview"></a>시나리오 개요

![워크플로 구성 요소 가져오기](./media/tasks-consume-public-content/consuming-public-content-objects.png)

이 연습에서는 다음을 설정 합니다.

1. 다음을 나타내는 3 개의 **컨테이너 레지스트리**
   * 기본 이미지 변경을 지원 하기 위해 시뮬레이션 된 [Docker 허브][docker-hub] ( `publicregistry` )
   * `contoso`개인 이미지를 공유 하는 팀 레지스트리 ()
   * 가져온 공용 콘텐츠에 대 한 회사/팀 공유 레지스트리 ( `baseartifacts` )
1. 각 레지스트리의 **ACR 작업** 작업은 다음과 같습니다.  
   1. 시뮬레이션 된 공용 `node` 이미지 빌드
   1. `node`회사/팀 공유 레지스트리에 이미지 가져오기 및 유효성 검사
   1. 이미지 빌드 및 배포 `hello-world`
1. **ACR 작업 정의** (다음에 대 한 구성 포함)
1. 키 자격 증명 모음에 대 한 포인터인 **레지스트리 자격 증명** 의 컬렉션입니다.
1. **secrets** `acr-task.yaml` 키 자격 증명 모음에 대 한 포인터인 내에서 사용할 수 있는 비밀의 컬렉션입니다.
1. 내에서 사용 되는 **구성 된 값** 의 컬렉션입니다. `acr-task.yaml`
1. 모든 비밀을 보호 하는 **Azure key vault**
1. 빌드 응용 프로그램을 호스트 하는 **Azure container instance** `hello-world`

## <a name="prerequisites"></a>필수 구성 요소

다음 단계에서는 연습에서 생성 및 사용 되는 리소스에 대 한 값을 구성 합니다.

### <a name="set-environment-variables"></a>환경 변수 설정

사용자 환경에 고유한 변수를 구성 합니다. 영구 콘텐츠를 사용 하 여 리소스를 자체 리소스 그룹에 배치 하 여 실수로 인 한 삭제를 최소화 하기 위한 모범 사례를 따릅니다. 그러나 원하는 경우이를 단일 리소스 그룹에 추가할 수 있습니다.

이 문서의 예제는 bash 셸에 대해 서식 지정 됩니다.

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

환경을 시뮬레이션 하려면 다음 Git 리포지토리을 관리할 수 있는 리포지토리로 분기 합니다. 

* https://github.com/importing-public-content/base-image-node.git
* https://github.com/importing-public-content/import-baseimage-node.git
* https://github.com/importing-public-content/hello-world.git

그런 다음 분기 리포지토리에 대해 다음 변수를 업데이트 합니다.

`:main`Git url의 끝에 추가 된는 기본 리포지토리 분기를 나타냅니다.

```bash
GIT_BASE_IMAGE_NODE=https://github.com/<your-fork>/base-image-node.git#main
GIT_NODE_IMPORT=https://github.com/<your-fork>/import-baseimage-node.git#main
GIT_HELLO_WORLD=https://github.com/<your-fork>/hello-world.git#main
```

ACR 작업에서 Git webhook를 복제 하 고 설정 하는 데 사용 되는 [GitHub 액세스 토큰 (PAT)][git-token] 이 필요 합니다. 개인 리포지토리에 필요한 권한이 있는 토큰을 만드는 단계는 [GitHub 액세스 토큰 만들기](container-registry-tutorial-build-task.md#create-a-github-personal-access-token)를 참조 하세요. 

```bash
GIT_TOKEN=<set-git-token-here>
```

### <a name="docker-hub-credentials"></a>Docker 허브 자격 증명  
Docker 허브에서 이미지를 끌어올 때 제한 및 id 요청을 방지 하려면 [Docker 허브 토큰][docker-hub-tokens]을 만듭니다. 그런 다음, 다음 환경 변수를 설정 합니다.

```bash
REGISTRY_DOCKERHUB_USER=<yourusername>
REGISTRY_DOCKERHUB_PASSWORD=<yourtoken>
```

### <a name="create-registries"></a>레지스트리 만들기

Azure CLI 명령을 사용 하 여 각각 자체 리소스 그룹에 3 개의 프리미엄 계층 컨테이너 레지스트리를 만듭니다.

```azurecli-interactive
az group create --name $REGISTRY_PUBLIC_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_PUBLIC_RG --name $REGISTRY_PUBLIC --sku Premium

az group create --name $REGISTRY_BASE_ARTIFACTS_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_BASE_ARTIFACTS_RG --name $REGISTRY_BASE_ARTIFACTS --sku Premium

az group create --name $REGISTRY_RG --location $RESOURCE_GROUP_LOCATION
az acr create --resource-group $REGISTRY_RG --name $REGISTRY --sku Premium
```

### <a name="create-key-vault-and-set-secrets"></a>주요 자격 증명 모음 만들기 및 암호 설정

키 자격 증명 모음을 만듭니다.

```azurecli-interactive
az group create --name $AKV_RG --location $RESOURCE_GROUP_LOCATION
az keyvault create --resource-group $AKV_RG --name $AKV
```

키 자격 증명 모음에서 Docker 허브 사용자 이름 및 토큰을 설정 합니다.

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

Key vault에서 Git PAT를 설정 하 고 확인 합니다.

```azurecli-interactive
az keyvault secret set --vault-name $AKV --name github-token --value $GIT_TOKEN

az keyvault secret show --vault-name $AKV --name github-token --query value -o tsv
```

### <a name="create-resource-group-for-an-azure-container-instance"></a>Azure container instance에 대 한 리소스 그룹 만들기

이 리소스 그룹은 이후 작업에서 이미지를 배포할 때 사용 됩니다 `hello-world` .

```azurecli-interactive
az group create --name $ACI_RG --location $RESOURCE_GROUP_LOCATION
```

## <a name="create-public-node-base-image"></a>공용 `node` 기본 이미지 만들기

`node`Docker 허브에서 이미지를 시뮬레이션 하려면 공용 이미지를 빌드하고 유지 관리 하는 [ACR 작업][acr-task] 을 만듭니다. 이렇게 설정 하면 이미지 유지 관리자 변경 내용을 시뮬레이션할 수 있습니다 `node` .

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

Docker 제한을 방지 하려면 작업에 [Docker 허브 자격 증명][docker-hub-tokens] 을 추가 합니다. [Acr 작업 자격 증명][acr-task-credentials] 명령은 docker 허브를 비롯 한 모든 레지스트리에 docker 자격 증명을 전달 하는 데 사용할 수 있습니다.

```azurecli-interactive
az acr task credential add \
  -n node-public \
  -r $REGISTRY_PUBLIC \
  --login-server $REGISTRY_DOCKERHUB_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-dockerhub-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-dockerhub-password \
  --use-identity [system]
```

키 자격 증명 모음에서 값을 읽을 수 있는 권한을 태스크에 부여 합니다.

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

작업은 Git 커밋, 기본 이미지 업데이트, 타이머 또는 수동 실행에 의해 [트리거될 수 있습니다][acr-task-triggers] . 

수동으로 작업을 실행 하 여 이미지를 생성 합니다 `node` .

```azurecli-interactive
az acr task run -r $REGISTRY_PUBLIC -n node-public
```

시뮬레이션 된 공용 레지스트리에서 이미지를 나열 합니다.

```azurecli-interactive
az acr repository show-tags -n $REGISTRY_PUBLIC --repository node
```

## <a name="create-the-hello-world-image"></a>이미지 만들기 `hello-world`

시뮬레이션 된 공용 `node` 이미지를 기반으로 이미지를 작성 `hello-world` 합니다.

### <a name="create-token-for-pull-access-to-simulated-public-registry"></a>시뮬레이션 된 공용 레지스트리에 대 한 끌어오기 액세스용 토큰 만들기

로 범위가 지정 된 시뮬레이션 된 공용 레지스트리에 대 한 [액세스 토큰][acr-tokens] 을 만듭니다 `pull` . 그런 다음 키 자격 증명 모음에 설정 합니다.

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

### <a name="create-token-for-pull-access-by-azure-container-instances"></a>Azure Container Instances에서 끌어오기 액세스용 토큰 만들기

풀로 범위가 지정 된 이미지를 호스트 하는 레지스트리에 대 한 [액세스 토큰][acr-tokens] 을 만듭니다 `hello-world` . 그런 다음 키 자격 증명 모음에 설정 합니다.

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

### <a name="create-task-to-build-and-maintain-hello-world-image"></a>이미지를 작성 하 고 유지 관리 하는 작업 만들기 `hello-world`

다음 명령은 리포지토리의에 정의 된 작업을 만듭니다 `acr-tasks.yaml` `hello-world` . 작업 단계에서는 이미지를 빌드한 `hello-world` 다음 Azure Container Instances에 배포 합니다. Azure Container Instances에 대 한 리소스 그룹은 이전 섹션에서 만들었습니다. 의 `az container create` 차이로만 작업에서를 호출 하 여 `image:tag` 이 연습에서 작업을 동일한 인스턴스에 배포 합니다.

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

시뮬레이션 된 공용 레지스트리에 대 한 작업에 자격 증명을 추가 합니다.

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

키 자격 증명 모음에서 값을 읽을 수 있는 권한을 태스크에 부여 합니다.

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

리소스 그룹에 대 한 액세스 권한을 부여 하 여 Azure Container Instances를 만들고 관리할 수 있는 권한을 작업에 부여 합니다.

```azurecli-interactive
az role assignment create \
  --assignee $(az acr task show \
  --name hello-world \
  --registry $REGISTRY \
  --query identity.principalId --output tsv) \
  --scope $(az group show -n $ACI_RG --query id -o tsv) \
  --role owner
```

작업을 만들고 구성한 상태에서 작업을 실행 하 여 이미지를 빌드하고 배포 합니다 `hello-world` .

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

만든 후에는 이미지를 호스트 하는 컨테이너의 IP 주소를 가져옵니다 `hello-world` .

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

브라우저에서 IP 주소로 이동 하 여 실행 중인 응용 프로그램을 확인 합니다.

## <a name="update-the-base-image-with-a-questionable-change"></a>"의심 스러운" 변경 내용으로 기본 이미지 업데이트

이 섹션에서는 환경에서 문제를 일으킬 수 있는 기본 이미지에 대 한 변경 내용을 시뮬레이션 합니다.

1. `Dockerfile`분기 리포지토리에서를 엽니다 `base-image-node` .
1. `BACKGROUND_COLOR`변경을 시뮬레이트하려면를로 변경 `Orange` 합니다.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Orange
```

변경을 커밋하고 ACR 작업을 감시 하 여 자동으로 빌드를 시작 합니다.

실행을 시작할 작업을 감시 합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

결국 `Succeeded` 트리거를 기반으로 하는 상태가 표시 됩니다 `Commit` .

```azurecli-interactive
RUN ID    TASK      PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------  ----------  ---------  ---------  --------------------  ----------
ca4       hub-node  linux       Succeeded  Commit     2020-10-24T05:02:29Z  00:00:22
```

**Ctrl + C** 를 입력 하 여 watch 명령을 종료 한 다음 가장 최근 실행에 대 한 로그를 확인 합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

`node`이미지가 완료 되 면 `watch` ACR 작업에서 이미지 빌드를 자동으로 시작 합니다 `hello-world` .

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

결국 `Succeeded` 트리거를 기반으로 하는 상태가 표시 됩니다 `Image Update` .

```azurecli-interactive
RUN ID    TASK         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  -----------  ----------  ---------  ------------  --------------------  ----------
dau       hello-world  linux       Succeeded  Image Update  2020-10-24T05:08:45Z  00:00:31
```

**Ctrl + C** 를 입력 하 여 watch 명령을 종료 한 다음 가장 최근 실행에 대 한 로그를 확인 합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY
```

완료 되 면 업데이트 된 이미지를 호스트 하는 사이트의 IP 주소를 가져옵니다 `hello-world` .

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

브라우저에서 주황색 (불확실) 배경의 사이트로 이동 합니다.

### <a name="checking-in"></a>체크 인

이 시점에서 `hello-world` Git 커밋 및 기본 이미지 변경에 자동으로 빌드되는 이미지를 만들었습니다 `node` . 이 예제에서 작업은 Azure Container Registry의 기본 이미지를 기반으로 하지만 지원 되는 모든 레지스트리를 사용할 수 있습니다.

기본 이미지 업데이트는 이미지가 업데이트 될 때 작업 실행을 자동으로 retriggers 합니다 `node` . 여기에 표시 된 것 처럼 일부 업데이트는 필요 하지 않습니다.

## <a name="gated-imports-of-public-content"></a>공용 콘텐츠의 제어 된 가져오기

업스트림 변경이 중요 한 작업을 중단 하지 않도록 방지 하기 위해 보안 검색 및 기능 테스트가 추가 될 수 있습니다.

이 섹션에서는 다음에 대 한 ACR 작업을 만듭니다.

* 테스트 이미지 빌드
* `./test.sh`테스트 이미지에 대해 기능적 테스트 스크립트 실행
* 이미지가 성공적으로 테스트 되 면 **baseimages** 레지스트리로 공용 이미지를 가져옵니다.

### <a name="add-automation-testing"></a>자동화 테스트 추가

업스트림 콘텐츠를 게이트 하기 위해 자동화 된 테스트가 구현 됩니다. 이 예제에서는를 `test.sh` 확인 하는이 제공 됩니다 `$BACKGROUND_COLOR` . 테스트가 실패할 경우 `EXIT_CODE` 의 `1` 이 반환 되어 ACR 작업 단계가 실패 하 고 작업 실행이 종료 됩니다. 테스트는 로깅 결과를 포함 하 여 모든 형태의 도구로 확장할 수 있습니다. 이 게이트는 스크립트의 pass/fail 응답에 의해 관리 되며 여기에서 재현 됩니다.

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

리포지토리의를 검토 하 여 `acr-task.yaml` `import-baseimage-node` 다음 단계를 수행 합니다.

1. 다음 Dockerfile을 사용 하 여 테스트 기본 이미지를 빌드합니다.
    ```dockerfile
    ARG REGISTRY_FROM_URL=
    FROM ${REGISTRY_FROM_URL}node:15-alpine
    WORKDIR /test
    COPY ./test.sh .
    CMD ./test.sh
    ```
1. 완료 되 면 실행 되는 컨테이너를 실행 하 여 이미지의 유효성을 검사 합니다. `./test.sh`
1. 성공적으로 완료 된 경우에만 다음을 사용 하 여 제어 되는 가져오기 단계를 실행 합니다. `when: ['validate-base-image']`

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

### <a name="create-task-to-import-and-test-base-image"></a>기본 이미지를 가져오고 테스트 하기 위한 작업 만들기

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

시뮬레이션 된 공용 레지스트리에 대 한 작업에 자격 증명을 추가 합니다.

```azurecli-interactive
az acr task credential add \
  -n base-import-node \
  -r $REGISTRY_BASE_ARTIFACTS \
  --login-server $REGISTRY_PUBLIC_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_PUBLIC}-password \
  --use-identity [system]
```

키 자격 증명 모음에서 값을 읽을 수 있는 권한을 태스크에 부여 합니다.

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

가져오기 작업을 실행 합니다.

```azurecli-interactive
az acr task run -n base-import-node -r $REGISTRY_BASE_ARTIFACTS
```

> [!NOTE]
> 로 인해 태스크가 실패 하는 경우 `./test.sh: Permission denied` 스크립트에 실행 권한이 있는지 확인 하 고 Git 리포지토리로 다시 커밋합니다.
>```bash
>chmod +x ./test.sh
>```

## <a name="update-hello-world-image-to-build-from-gated-node-image"></a>`hello-world`제어 된 이미지에서 빌드하기 위한 이미지 업데이트 `node`

저장소에서로 범위가 지정 된 기본 아티팩트 레지스트리에 액세스 하기 위한 [액세스 토큰][acr-tokens] 을 만듭니다 `read` `node` . 그런 다음 키 자격 증명 모음에서를 설정 합니다.

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

기본 아티팩트 레지스트리에 대 한 **hello-세계** 작업에 자격 증명을 추가 합니다.

```azurecli-interactive
az acr task credential add \
  -n hello-world \
  -r $REGISTRY \
  --login-server $REGISTRY_BASE_ARTIFACTS_URL \
  -u https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-user \
  -p https://${AKV}.vault.azure.net/secrets/registry-${REGISTRY_BASE_ARTIFACTS}-password \
  --use-identity [system]
```

`REGISTRY_FROM_URL`레지스트리를 사용 하도록를 변경 하려면 작업을 업데이트 합니다. `BASE_ARTIFACTS`

```azurecli-interactive
az acr task update \
  -n hello-world \
  -r $REGISTRY \
  --set KEYVAULT=$AKV \
  --set REGISTRY_FROM_URL=${REGISTRY_BASE_ARTIFACTS_URL}/ \
  --set ACI=$ACI \
  --set ACI_RG=$ACI_RG
```

**Hello 세계** 작업을 실행 하 여 기본 이미지 종속성을 변경 합니다.

```azurecli-interactive
az acr task run -r $REGISTRY -n hello-world
```

## <a name="update-the-base-image-with-a-valid-change"></a>"올바른" 변경 내용으로 기본 이미지 업데이트

1. 리포지토리에서를 `Dockerfile` 엽니다 `base-image-node` .
1. `BACKGROUND_COLOR` `Green` 올바른 변경을 시뮬레이트하려면를로 변경 합니다.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Green
```

변경 내용 커밋 및 업데이트 시퀀스 모니터링:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

실행 되 면 **Ctrl + C** 를 입력 하 고 로그를 모니터링 합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

완료 되 면 **기본 이미지 가져오기** 작업을 모니터링 합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

실행 되 면 **Ctrl + C** 를 입력 하 고 로그를 모니터링 합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

완료 되 면 **hello 세계** 작업을 모니터링 합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY -o table
```

실행 되 면 **Ctrl + C** 를 입력 하 고 로그를 모니터링 합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY
```

완료 되 면 업데이트 된 이미지를 호스트 하는 사이트의 IP 주소를 가져옵니다 `hello-world` .

```azurecli-interactive
az container show \
  --resource-group $ACI_RG \
  --name ${ACI} \
  --query ipAddress.ip \
  --out tsv
```

브라우저에서 사이트로 이동 합니다. 여기에는 녹색 (유효한) 배경이 있어야 합니다.

### <a name="view-the-gated-workflow"></a>제어 된 워크플로 보기

배경 색이 빨강 인 이전 섹션의 단계를 다시 수행 합니다.

1. 리포지토리에서를 `Dockerfile` 엽니다. `base-image-node`
1. 잘못 된 `BACKGROUND_COLOR` `Red` 변경 내용을 시뮬레이트하려면를로 변경 합니다.

```Dockerfile
ARG REGISTRY_NAME=
FROM ${REGISTRY_NAME}node:15-alpine
ENV NODE_VERSION 15-alpine
ENV BACKGROUND_COLOR Red
```

변경 내용 커밋 및 업데이트 시퀀스 모니터링:

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_PUBLIC -o table
```

실행 되 면 **Ctrl + C** 를 입력 하 고 로그를 모니터링 합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_PUBLIC
```

완료 되 면 **기본 이미지 가져오기** 작업을 모니터링 합니다.

```azurecli-interactive
watch -n1 az acr task list-runs -r $REGISTRY_BASE_ARTIFACTS -o table
```

실행 되 면 **Ctrl + C** 를 입력 하 고 로그를 모니터링 합니다.

```azurecli-interactive
az acr task logs -r $REGISTRY_BASE_ARTIFACTS
```

이 시점에서 **기본-노드** 작업 실패 유효성 검사가 표시 되 고 업데이트를 게시 하는 시퀀스가 중지 됩니다 `hello-world` . 출력은 다음과 비슷합니다.

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

### <a name="publish-an-update-to-hello-world"></a>업데이트 게시 대상 `hello-world`

이미지에 대 한 변경 내용은 `hello-world` 마지막으로 유효성을 검사 한 이미지를 계속 사용 `node` 합니다.

`node`제어 된 유효성 검사를 통과 하는 기본 이미지를 추가로 변경 하면 이미지에 대 한 기본 이미지 업데이트가 트리거됩니다 `hello-world` .

## <a name="cleaning-up"></a>정리

더 이상 필요 하지 않은 경우이 문서에 사용 된 리소스를 삭제 합니다.

```azurecli-interactive
az group delete -n $REGISTRY_RG --no-wait -y
az group delete -n $REGISTRY_PUBLIC_RG --no-wait -y
az group delete -n $REGISTRY_BASE_ARTIFACTS_RG --no-wait -y
az group delete -n $AKV_RG --no-wait -y
az group delete -n $ACI_RG --no-wait -y
```

## <a name="next-steps"></a>다음 단계

문서를 참조 하세요. ACR 작업을 사용 하 여 자동 제어 워크플로를 만들어 사용자 환경에 업데이트 된 기본 이미지를 도입할 수 있습니다. Azure Container Registry에서 이미지를 관리 하려면 관련 정보를 참조 하세요.


* [컨테이너 이미지 태그 지정 및 버전 관리에 대 한 권장 사항](container-registry-image-tag-version.md)
* [Azure container registry에서 컨테이너 이미지 잠그기](container-registry-image-lock.md)

[install-cli]:                  /cli/azure/install-azure-cli
[acr]:                          https://aka.ms/acr
[acr-repo-permissions]:         https://aka.ms/acr/repo-permissions
[acr-task]:                     https://aka.ms/acr/tasks
[acr-task-triggers]:            container-registry-tasks-overview.md#task-scenarios
[acr-task-credentials]:       container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task
[acr-tokens]:                   https://aka.ms/acr/tokens
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
[oci-artifacts]:                https://aka.ms/acr/artifacts
[oci-consuming-public-content]: https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/
[opa]:                          https://www.openpolicyagent.org/
[quay]:                         https://quay.io




