---
title: 사용자 지정 컨테이너에 대 한 CI/CD
description: Azure App Service에서 사용자 지정 Windows 또는 Linux 컨테이너에 대 한 연속 배포를 설정 합니다.
keywords: Azure App Service, Linux, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: bc36325b55f049eebef823d836768fccc39a7615
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472168"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Azure App Service에서 사용자 지정 컨테이너를 사용 하 여 연속 배포

이 자습서에서는 관리 되는 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 리포지토리 또는 [Docker 허브](https://hub.docker.com)에서 사용자 지정 컨테이너 이미지에 대 한 연속 배포를 구성 합니다.

## <a name="1-go-to-deployment-center"></a>1. Deployment Center로 이동

[Azure Portal](https://portal.azure.com)에서 App Service 앱에 대 한 관리 페이지로 이동 합니다.

왼쪽 메뉴에서 **배포 센터**  >  **설정** 을 클릭 합니다. 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. 배포 원본 선택

배포 원본 **선택** 은 시나리오에 따라 달라 집니다.
- Container **registry** 는 컨테이너 레지스트리와 App Service 사이에 CI/CD를 설정 합니다.
- Github의 컨테이너 이미지에 대 한 소스 코드를 유지 관리 하는 경우 **Github 작업** 옵션을 사용할 수 있습니다. GitHub 리포지토리에 대 한 새로운 커밋에 의해 트리거된 배포 작업은 `docker build` `docker push` 컨테이너 레지스트리를 실행 하 고, 새 이미지를 실행 하기 위해 App Service 앱을 업데이트할 수 있습니다. 자세한 내용은 [GitHub 작업을 사용 하 여 CI/CD의 작동 방식](#how-cicd-works-with-github-actions)을 참조 하세요.
- **Azure Pipelines** 를 사용 하 여 CI/CD를 설정 하려면 [Azure Pipelines에서 Azure 웹 앱 컨테이너 배포](/devops/pipelines/targets/webapp-on-container-linux)를 참조 하세요.

> [!NOTE]
> Docker Compose 앱의 경우 **Container Registry** 를 선택 합니다.

GitHub 작업을 선택 하는 경우 권한 부여를 **클릭** **하 고 권한** 부여 프롬프트를 따릅니다. 이전에 GitHub에 대 한 권한이 이미 부여 된 경우 **계정 변경** 을 클릭 하 여 다른 사용자의 리포지토리에서 배포할 수 있습니다.

GitHub를 사용 하 여 Azure 계정에 권한을 부여 하 고 나면 배포할 **조직**, **리포지토리** 및 **분기** 를 **선택** 합니다.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. 레지스트리 설정 구성
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. 레지스트리 설정 구성

다중 컨테이너 (Docker Compose) 앱을 배포 하려면 **컨테이너 형식** 에서 **Docker Compose** 를 **선택** 합니다.

**컨테이너 유형** 드롭다운이 표시 되지 않으면 **원본** 으로 다시 스크롤하고 **Container Registry** 를 **선택** 합니다.
::: zone-end

**레지스트리 원본** 에서 컨테이너 레지스트리가 있는 위치를 **선택** 합니다. Azure Container Registry 또는 Docker 허브가 아닌 경우 **개인 레지스트리** 를 **선택** 합니다.

::: zone pivot="container-linux"
> [!NOTE]
> 다중 컨테이너 (Docker Compose) 앱에서 둘 이상의 개인 이미지를 사용 하는 경우 개인 이미지가 동일한 개인 레지스트리에 있고 동일한 사용자 자격 증명을 사용 하 여 액세스할 수 있는지 확인 합니다. 다중 컨테이너 앱에서 공용 이미지만 사용 하는 경우 일부 이미지가 Docker 허브에 있지 않더라도 **Docker 허브** 를 **선택** 합니다.
::: zone-end  

선택한 항목과 일치 하는 탭을 선택 하 여 다음 단계를 수행 합니다.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

레지스트리 드롭다운에서 앱과 동일한 구독의 **레지스트리** 를 표시 합니다. 원하는 레지스트리를 **선택** 합니다.

> [!NOTE]
> 다른 구독의 레지스트리에서 배포 하려면 대신 **레지스트리 원본** 에서 **개인 레지스트리** 를 **선택** 합니다.

::: zone pivot="container-windows"
배포할 **이미지** 및 **태그** 를 **선택** 합니다. 원하는 경우 시작 **파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end
::: zone pivot="container-linux"
**컨테이너 유형에** 따라 다음 단계를 수행 합니다.
- **Docker Compose** 의 경우 개인 이미지에 대 한 레지스트리를 **선택** 합니다.  **파일 선택** 을 클릭 하 여 [Docker Compose 파일](https://docs.docker.com/compose/compose-file/)을 업로드 하거나 Docker Compose 파일의 내용을 **구성** 에 **붙여넣습니다** .
- **단일 컨테이너** 에 대해 배포할 **이미지** 및 **태그** 를 **선택** 합니다. 원하는 경우 시작 **파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end

컨테이너를 시작할 때 **시작 파일** 의 문자열을 [ `docker run` `[COMMAND] [ARG...]` 세그먼트와 같이 명령 끝](https://docs.docker.com/engine/reference/run/) 에 추가 App Service 합니다.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
**리포지토리 액세스** 에서 배포할 이미지를 공용 또는 개인으로 **선택** 합니다.
::: zone-end
::: zone pivot="container-linux"
**리포지토리 액세스** 에서 배포할 이미지를 공용 또는 개인으로 **선택** 합니다. 하나 이상의 개인 이미지가 있는 Docker Compose 앱에 대해 **개인** 을 **선택** 합니다.
::: zone-end

개인 이미지를 선택 하는 경우 Docker 계정의 **로그인** (사용자 이름) 및 **암호** 를 **지정** 합니다.

::: zone pivot="container-windows"
로 구분 된 **전체 이미지 이름 및 태그** 에 이미지 및 태그 이름을 **제공** `:` 합니다 (예: `nginx:latest` ). 원하는 경우 시작 **파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end
::: zone pivot="container-linux"
**컨테이너 유형에** 따라 다음 단계를 수행 합니다.
- **Docker Compose** 의 경우 개인 이미지에 대 한 레지스트리를 **선택** 합니다.  **파일 선택** 을 클릭 하 여 [Docker Compose 파일](https://docs.docker.com/compose/compose-file/)을 업로드 하거나 Docker Compose 파일의 내용을 **구성** 에 **붙여넣습니다** .
- **단일 컨테이너** 의 경우 이미지 및 태그 이름을로 구분 하 여 **전체 이미지 이름 및 태그** 에 **입력** `:` 합니다 (예: `nginx:latest` ). 원하는 경우 시작 **파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end

컨테이너를 시작할 때 **시작 파일** 의 문자열을 [ `docker run` `[COMMAND] [ARG...]` 세그먼트와 같이 명령 끝](https://docs.docker.com/engine/reference/run/) 에 추가 App Service 합니다.

# <a name="private-registry"></a>[개인 레지스트리](#tab/private)

**서버 url** 에 **https://** 로 시작 하는 서버의 url을 **입력** 합니다.

**로그인** 및 **암호** 에 개인 레지스트리에 대 한 로그인 자격 증명을 **입력** 합니다.

::: zone pivot="container-windows"
로 구분 된 **전체 이미지 이름 및 태그** 에 이미지 및 태그 이름을 **제공** `:` 합니다 (예: `nginx:latest` ). 원하는 경우 시작 **파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end
::: zone pivot="container-linux"
**컨테이너 유형에** 따라 다음 단계를 수행 합니다.
- **Docker Compose** 의 경우 개인 이미지에 대 한 레지스트리를 **선택** 합니다.  **파일 선택** 을 클릭 하 여 [Docker Compose 파일](https://docs.docker.com/compose/compose-file/)을 업로드 하거나 Docker Compose 파일의 내용을 **구성** 에 **붙여넣습니다** .
- **단일 컨테이너** 의 경우 이미지 및 태그 이름을로 구분 하 여 **전체 이미지 이름 및 태그** 에 **입력** `:` 합니다 (예: `nginx:latest` ). 원하는 경우 시작 **파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end

컨테이너를 시작할 때 **시작 파일** 의 문자열을 [ `docker run` `[COMMAND] [ARG...]` 세그먼트와 같이 명령 끝](https://docs.docker.com/engine/reference/run/) 에 추가 App Service 합니다.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. CI/CD 사용
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. CI/CD를 사용 하도록 설정
::: zone-end

App Service Azure Container Registry 및 Docker 허브와의 CI/CD 통합을 지원 합니다. 이 기능을 사용 하도록 설정 하려면 **연속 배포** 에서 **켜기** 를 **선택** 합니다.

::: zone pivot="container-linux"
> [!NOTE]
> **원본** 에서 **github 작업** 을 선택 하는 경우 GITHUB 작업에서 직접 CI/CD를 처리 하기 때문에이 옵션을 사용할 수 없습니다. 대신 워크플로 **구성** 섹션이 표시 됩니다. 여기에서 **파일 미리 보기** 를 **클릭** 하 여 워크플로 파일을 검사할 수 있습니다. Azure는이 파일을 선택한 GitHub 원본 리포지토리로 커밋 하 여 빌드 및 배포 작업을 처리 합니다. 자세한 내용은 [GitHub 작업을 사용 하 여 CI/CD의 작동 방식](#how-cicd-works-with-github-actions)을 참조 하세요.
::: zone-end

이 옵션을 사용 하도록 설정 하면 App Service Azure Container Registry 또는 Docker 허브의 리포지토리에 webhook를 추가 합니다. 선택한 이미지가로 업데이트 될 때마다 리포지토리가이 webhook에 게시 됩니다 `docker push` . 웹 후크는 App Service 앱을 다시 시작 하 고 실행 하 여 `docker pull` 업데이트 된 이미지를 가져옵니다.

**다른 개인 레지스트리** 는 수동으로 webhook에 게시 하거나 CI/CD 파이프라인의 단계로 게시할 수 있습니다. **WEBHOOK url** 에서 **복사** 단추를 **클릭** 하 여 웹 후크 url을 가져옵니다.

::: zone pivot="container-linux"
> [!NOTE]
> 다중 컨테이너 (Docker Compose) 앱에 대 한 지원은 제한 됩니다. 
> - App Service Azure Container Registry의 경우 선택 된 레지스트리에서 레지스트리를 범위로 사용 하 여 webhook를 만듭니다. `docker push`Docker Compose 파일에서 참조 하지 않는 리포지토리를 포함 하 여 레지스트리의 모든 리포지토리에 대 한는 앱 다시 시작을 트리거합니다. 웹 후크를 보다 좁은 범위로 [수정할](../container-registry/container-registry-webhook.md) 수 있습니다.
> - Docker 허브는 레지스트리 수준에서 웹 후크를 지원 하지 않습니다. Docker Compose 파일에 지정 된 이미지에 웹 후크를 수동으로 **추가** 해야 합니다.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. 설정 저장
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. 설정 저장
::: zone-end

 **저장** 을 클릭 합니다.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>GitHub 작업과 CI/CD가 작동 하는 방식

**원본** 에서 **GitHub 작업** ( [배포 원본 선택](#2-choose-deployment-source)참조)을 선택 하는 경우 다음과 같은 방법으로 CI/CD를 설정 App Service 합니다.

- App Service에 빌드 및 배포 작업을 처리 하기 위해 github 작업 워크플로 파일을 GitHub 리포지토리에 예치금.
- 개인 레지스트리에 대 한 자격 증명을 GitHub 비밀로 추가 합니다. 생성 된 워크플로 파일은 [Azure/docker 로그인](https://github.com/Azure/docker-login) 작업을 실행 하 여 개인 레지스트리로 로그인 한 다음를 실행 하 여 `docker push` 배포 합니다.
- 앱에 대 한 게시 프로필을 GitHub 암호로 추가 합니다. 생성 된 워크플로 파일은이 암호를 사용 하 여 App Service 인증 한 다음, [Azure/webapps-배포](https://github.com/Azure/webapps-deploy) 작업을 실행 하 여 업데이트 된 이미지를 구성 합니다. 그러면 업데이트 된 이미지를 가져오기 위해 앱을 다시 시작 하는 작업을 트리거합니다.
- [워크플로 실행 로그](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) 에서 정보를 캡처하고 앱 **배포 센터** 의 **로그** 탭에 표시 합니다.

다음과 같은 방법으로 GitHub 작업 빌드 공급자를 사용자 지정할 수 있습니다.

- GitHub 리포지토리에서 생성 된 후에 워크플로 파일을 사용자 지정 합니다. 자세한 내용은 [GitHub 동작에 대 한 워크플로 구문](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)을 참조 하세요. 워크플로가 [Azure/webapps-배포](https://github.com/Azure/webapps-deploy) 작업으로 끝나는 지 확인 하 여 앱 다시 시작을 트리거합니다.
- 선택한 분기가 보호 되는 경우 구성을 저장 하지 않고 워크플로 파일을 미리 본 다음,이를 리포지토리에 추가 하 고 필요한 GitHub 암호를 수동으로 추가할 수 있습니다. 이 메서드는 Azure Portal와의 로그 통합을 제공 하지 않습니다.
- 게시 프로필 대신 Azure Active Directory에서 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 를 사용 하 여 배포 합니다.

#### <a name="authenticate-with-a-service-principal"></a>서비스 주체를 사용 하 여 인증

이 선택적 구성은 생성 된 워크플로 파일의 게시 프로필을 사용 하 여 기본 인증을 대체 합니다.

[Azure CLI](/cli/azure/)에서 [az ad sp create-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 명령을 사용 하 여 서비스 사용자를 **생성** 합니다. 다음 예제에서는 *\<subscription-id>* , *\<group-name>* 및를 *\<app-name>* 사용자 고유의 값으로 바꿉니다. 최상위 수준을 포함 하 여 다음 단계에 대 한 전체 JSON 출력을 **저장** 합니다 `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> 보안을 위해 서비스 주체에 필요한 최소한의 액세스 권한을 부여 합니다. 이전 예제의 범위는 전체 리소스 그룹이 아니라 특정 App Service 앱으로 제한 됩니다.

[GitHub](https://github.com/)에서 리포지토리로 **이동한** 다음, 설정 > 비밀을 **선택** 하 **> 새 비밀을 추가** 합니다. Azure CLI 명령의 전체 JSON 출력을 암호의 값 필드에 **붙여넣습니다** . 암호에와 같은 이름을 **지정** 합니다 `AZURE_CREDENTIALS` .

**Deployment Center** 에 의해 생성 된 워크플로 파일에서 다음  예제와 `azure/webapps-deploy` 같은 코드를 사용 하 여 단계를 수정 합니다.

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>CLI로 자동화

컨테이너 레지스트리와 Docker 이미지를 구성 하려면 [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set)를 **실행** 합니다.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[개인 레지스트리](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
다중 컨테이너 (Docker Compose) 앱을 구성 하려면 Docker Compose 파일을 로컬로 **준비한** 다음 매개 변수를 사용 하 여 [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) 를 **실행** `--multicontainer-config-file` 합니다. Docker Compose 파일에 전용 이미지가 포함 되어 있는  경우 `--docker-registry-server-*` 앞의 예제와 같이 매개 변수를 추가 합니다.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

컨테이너 레지스트리에서 앱으로 CI/CD를 구성 하려면 매개 변수를 사용 하 여 [az webapp deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) 를 **실행** `--enable-cd` 합니다. 명령은 webhook URL을 출력 하지만 별도의 단계에서 수동으로 webhook를 레지스트리에 만들어야 합니다. 다음 예제에서는 앱에서 CI/CD를 사용 하도록 설정한 다음 출력에서 webhook URL을 사용 하 여 Azure Container Registry에 webhook를 만듭니다.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>추가 리소스

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Linux의 App Service에서 .NET Core 웹앱 만들기](quickstart-dotnetcore.md)
* [빠른 시작: App Service에서 사용자 지정 컨테이너 실행](quickstart-custom-container.md)
* [Linux의 App Service FAQ](faq-app-service-linux.md)
* [사용자 지정 컨테이너 구성](configure-custom-container.md)
* [Azure에 배포할 작업 워크플로](https://github.com/Azure/actions-workflow-samples)
