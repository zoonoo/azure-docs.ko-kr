---
title: 사용자 지정 컨테이너에 대한 CI/CD
description: Azure App Service에서 사용자 지정 Windows 또는 Linux 컨테이너에 지속적인 배포를 설정합니다.
keywords: Azure App Service, Linux, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 6519f3fe7335ed41f4d5ef67771aaa738a33e4a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782606"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Azure App Service에서 사용자 지정 컨테이너를 사용한 지속적인 배포

이 자습서에서는 관리되는 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 리포지토리 또는 [Docker 허브](https://hub.docker.com)에서 사용자 지정 컨테이너 이미지에 대한 연속 배포를 구성합니다.

## <a name="1-go-to-deployment-center"></a>1. 배포 센터로 이동

[Azure Portal](https://portal.azure.com)에서 App Service 앱의 관리 페이지로 이동합니다.

왼쪽 메뉴에서 **배포 센터** > **설정** 을 클릭합니다. 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. 배포 원본 선택

배포 원본 **선택** 은 시나리오에 따라 달라집니다.
- **컨테이너 레지스트리** 는 컨테이너 레지스트리와 App Service 사이에 CI/CD를 설정합니다.
- **GitHub Actions** 옵션은 GitHub에서 컨테이너 이미지에 대해 소스 코드를 유지 관리하는 경우에 사용됩니다. GitHub 리포지토리에 대한 새 커밋으로 트리거되는 배포 작업은 컨테이너 레지스트리에 직접 `docker build` 및 `docker push`를 실행한 후 새 이미지를 실행하도록 App Service 앱을 업데이트할 수 있습니다. 자세한 내용은 [GitHub Actions에서 CI/CD의 작동 방법](#how-cicd-works-with-github-actions)을 참조하세요.
- **Azure Pipelines** 를 사용하여 CI/CD를 설정하려면 [Azure Pipelines에서 Azure 웹앱 컨테이너 배포](/azure/devops/pipelines/targets/webapp-on-container-linux)를 참조하세요.

> [!NOTE]
> Docker Compose 앱의 경우 **컨테이너 레지스트리** 를 선택합니다.

GitHub Actions를 선택한 경우 **권한 부여** 를 **클릭** 하고 권한 부여 프롬프트를 따릅니다. 이전에 GitHub로 이미 권한 부여된 경우 **계정 변경** 을 클릭하여 다른 사용자의 리포지토리에서 배포할 수 있습니다.

GitHub로 Azure 계정에 권한 부여한 후 배포할 **조직**, **리포지토리** 및 **분기** 를 **선택** 합니다.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. 레지스트리 설정 구성
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. 레지스트리 설정 구성

다중 컨테이너(Docker Compose) 앱을 배포하려면 **컨테이너 유형** 에서 **Docker Compose** 를 **선택** 합니다.

**컨테이너 유형** 드롭다운이 표시되지 않으면 다시 **원본** 으로 스크롤하고 **컨테이너 레지스트리** 를 **선택** 합니다.
::: zone-end

**레지스트리 원본** 에서 컨테이너 레지스트리가 있는 위치를 **선택** 합니다. Azure Container Registry 또는 Docker Hub도 아닌 경우에는 **프라이빗 레지스트리** 를 **선택** 합니다.

::: zone pivot="container-linux"
> [!NOTE]
> 다중 컨테이너(Docker Compose) 앱에 사용되는 프라이빗 이미지가 1개를 초과하는 경우 프라이빗 이미지가 동일한 프라이빗 레지스트리에 있고 동일한 사용자 자격 증명으로 액세스할 수 있는지 확인합니다. 다중 컨테이너 앱에 퍼블릭 이미지만 사용되는 경우에는 일부 이미지가 Docker Hub에 있지 않아도 **Docker Hub** 를 **선택** 합니다.
::: zone-end  

선택과 일치하는 탭을 선택하여 다음 단계를 수행합니다.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

**레지스트리** 드롭다운에는 앱과 동일한 구독의 레지스트리가 표시됩니다. 원하는 레지스트리를 **선택** 합니다.

> [!NOTE]
> 다른 구독에 있는 레지스트리에서 배포하려면 대신 **레지스트리 원본** 에서 **프라이빗 레지스트리** 를 **선택** 합니다.

::: zone pivot="container-windows"
배포할 **이미지** 및 **태그** 를 **선택** 합니다. 원하는 경우 **시작 파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end
::: zone pivot="container-linux"
**컨테이너 유형** 에 따라 다음 단계를 수행합니다.
- **Docker Compose** 의 경우 프라이빗 이미지에 대해 레지스트리를 **선택** 합니다. **파일 선택** 을 **클릭** 하여 [Docker Compose 파일](https://docs.docker.com/compose/compose-file/)을 업로드하거나 단순히 Docker Compose 파일의 내용을 **구성** 에 **붙여넣습니다**.
- **단일 컨테이너** 의 경우 배포할 **이미지** 및 **태그** 를 **선택** 합니다. 원하는 경우 **시작 파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end

App Service는 컨테이너를 시작할 때 **시작 파일** 의 문자열을 [`docker run` 명령의 끝(`[COMMAND] [ARG...]` 세그먼트)](https://docs.docker.com/engine/reference/run/)에 추가합니다.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
**리포지토리 액세스** 에서 배포할 이미지가 퍼블릭 또는 프라이빗인지 **선택** 합니다.
::: zone-end
::: zone pivot="container-linux"
**리포지토리 액세스** 에서 배포할 이미지가 퍼블릭 또는 프라이빗인지 **선택** 합니다. 프라이빗 이미지가 하나 이상인 Docker Compose 앱의 경우 **프라이빗** 을 **선택** 합니다.
::: zone-end

프라이빗 이미지를 선택하는 경우 Docker 계정의 **로그인**(사용자 이름) 및 **암호** 를 **지정** 합니다.

::: zone pivot="container-windows"
**전체 이미지 이름 및 태그** 에 `:`으로 구분된 이미지 및 태그 이름을 **제공** 합니다(예: `nginx:latest`). 원하는 경우 **시작 파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end
::: zone pivot="container-linux"
**컨테이너 유형** 에 따라 다음 단계를 수행합니다.
- **Docker Compose** 의 경우 프라이빗 이미지에 대해 레지스트리를 **선택** 합니다. **파일 선택** 을 **클릭** 하여 [Docker Compose 파일](https://docs.docker.com/compose/compose-file/)을 업로드하거나 단순히 Docker Compose 파일의 내용을 **구성** 에 **붙여넣습니다**.
- **단일 컨테이너** 의 경우 **전체 이미지 이름 및 태그** 에 `:`으로 구분된 이미지 및 태그 이름을 **제공** 합니다(예: `nginx:latest`). 원하는 경우 **시작 파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end

App Service는 컨테이너를 시작할 때 **시작 파일** 의 문자열을 [`docker run` 명령의 끝(`[COMMAND] [ARG...]` 세그먼트)](https://docs.docker.com/engine/reference/run/)에 추가합니다.

# <a name="private-registry"></a>[프라이빗 레지스트리](#tab/private)

**서버 URL** 에 **https://** 로 시작하는 서버의 URL을 **입력** 합니다.

**로그인** 및 **암호** 에 개인 레지스트리에 대한 로그인 자격 증명을 **입력** 합니다.

::: zone pivot="container-windows"
**전체 이미지 이름 및 태그** 에 `:`으로 구분된 이미지 및 태그 이름을 **제공** 합니다(예: `nginx:latest`). 원하는 경우 **시작 파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end
::: zone pivot="container-linux"
**컨테이너 유형** 에 따라 다음 단계를 수행합니다.
- **Docker Compose** 의 경우 프라이빗 이미지에 대해 레지스트리를 **선택** 합니다. **파일 선택** 을 **클릭** 하여 [Docker Compose 파일](https://docs.docker.com/compose/compose-file/)을 업로드하거나 단순히 Docker Compose 파일의 내용을 **구성** 에 **붙여넣습니다**.
- **단일 컨테이너** 의 경우 **전체 이미지 이름 및 태그** 에 `:`으로 구분된 이미지 및 태그 이름을 **제공** 합니다(예: `nginx:latest`). 원하는 경우 **시작 파일** 에 시작 명령을 **입력** 합니다. 
::: zone-end

App Service는 컨테이너를 시작할 때 **시작 파일** 의 문자열을 [`docker run` 명령의 끝(`[COMMAND] [ARG...]` 세그먼트)](https://docs.docker.com/engine/reference/run/)에 추가합니다.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. CI/CD 사용
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. CI/CD 사용
::: zone-end

App Service는 Azure Container Registry 및 Docker Hub와의 CI/CD 통합을 지원합니다. 이를 사용하도록 설정하려면 **지속적인 배포** 에서 **켜기** 를 **선택** 합니다.

::: zone pivot="container-linux"
> [!NOTE]
> **원본** 에서 **GitHub Actions** 를 선택하면 CI/CD가 GitHub Actions에서 직접 처리되므로 이 옵션이 표시되지 않습니다. 대신 **워크플로 구성** 섹션이 표시되고, 여기에서 **파일 미리 보기** 를 **클릭** 하여 워크플로 파일을 검사할 수 있습니다. Azure는 선택한 GitHub 원본 리포지토리에 이 파일을 커밋하여 빌드 및 배포 작업을 처리합니다. 자세한 내용은 [GitHub Actions에서 CI/CD의 작동 방법](#how-cicd-works-with-github-actions)을 참조하세요.
::: zone-end

이 옵션을 사용하도록 설정하면 App Service가 Azure Container Registry 또는 Docker Hub의 레지스트리에 웹후크를 추가합니다. 선택한 이미지가 `docker push`로 업데이트될 때마다 리포지토리가 이 웹후크에 게시합니다. 이 웹후크를 통해 App Service 앱이 재시작되고 `docker pull`을 실행하여 업데이트된 이미지를 가져옵니다.

**다른 프라이빗 레지스트리** 의 경우에는 웹후크에 수동으로 또는 CI/CD 파이프라인의 한 단계로 게시할 수 있습니다. **웹후크 URL** 에서 **복사** 단추를 **클릭** 하여 웹후크 URL을 가져옵니다.

::: zone pivot="container-linux"
> [!NOTE]
> 다중 컨테이너(Docker Compose) 앱에 대한 지원은 제한됩니다. 
> - Azure Container Registry의 경우 App Service가 선택한 레지스트리에서 레지스트리를 범위로 사용하여 웹후크를 만듭니다. Docker Compose 파일에서 참조되지 않는 항목을 포함하여 레지스트리에 있는 모든 리포지토리에 대한 `docker push`는 앱 다시 시작을 트리거합니다. 범위를 좁히기 위해 [웹후크를 수정](../container-registry/container-registry-webhook.md)해야 할 수 있습니다.
> - Docker Hub는 레지스트리 수준에서 웹후크를 지원하지 않습니다. Docker Compose 파일에 지정된 이미지에 웹후크를 수동으로 **추가** 해야 합니다.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. 설정 저장
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. 설정 저장
::: zone-end

**저장** 을 **클릭** 합니다.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>GitHub Actions에서 CI/CD의 작동 방법

**원본** 에서 **GitHub Actions** 를 선택하면([배포 원본 선택](#2-choose-deployment-source) 참조), App Service가 다음 방식으로 CI/CD를 설정합니다.

- App Service에 대한 빌드 및 배포 작업을 처리하기 위해 GitHub Actions 워크플로 파일을 GitHub 리포지토리에 저장합니다.
- 프라이빗 레지스트리에 대한 자격 증명을 GitHub 비밀로 추가합니다. 생성된 워크플로 파일은 [Azure/docker-login](https://github.com/Azure/docker-login) 작업을 실행하여 프라이빗 레지스트리로 로그인한 후 `docker push`를 실행하여 여기에 배포합니다.
- 앱의 게시 프로필을 GitHub 비밀로 추가합니다. 생성된 워크플로 파일은 이 비밀을 사용하여 App Service에 인증을 수행한 후 [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) 작업을 실행하여 업데이트된 이미지를 구성합니다. 이것은 업데이트된 이미지를 가져오도록 앱 다시 시작을 트리거합니다.
- [워크플로 실행 로그](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs)에서 정보를 캡처하여 앱 **배포 센터** 의 **로그** 탭에 표시합니다.

다음과 같은 방법으로 GitHub Actions 빌드 공급자를 사용자 지정할 수 있습니다.

- 워크플로 파일이 GitHub 리포지토리에서 생성된 후 이를 사용자 지정합니다. 자세한 내용은 [GitHub Actions의 워크플로 구문](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)을 참조하세요. 앱 다시 시작을 트리거하기 위해 워크플로가 [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) 작업으로 종료되는지 확인합니다.
- 선택한 분기가 보호되는 경우 구성을 저장하지 않고 워크플로 파일을 계속 미리 볼 수 있고, 이후 이것과 필요한 GitHub 비밀을 리포지토리에 수동으로 추가할 수 있습니다. 이 방법은 Azure Portal과의 로그 통합을 제공하지 않습니다.
- 게시 프로필 대신 Azure Active Directory에서 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 사용하여 배포합니다.

#### <a name="authenticate-with-a-service-principal"></a>서비스 주체를 사용하여 인증

이 선택적 구성은 생성된 워크플로 파일의 게시 프로필로 기본 인증을 대체합니다.

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 서비스 주체를 **생성** 합니다. 다음 예제에서는 *\<subscription-id>* , *\<group-name>* 및 *\<app-name>* 값을 사용자 고유의 값으로 바꿉니다. 다음 단계에 대한 전체 JSON 출력을 최상위 수준(`{}`)을 포함하여 **저장** 합니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> 보안을 위해 서비스 주체에 필요한 최소한의 액세스 권한을 부여합니다. 이전 예의 범위는 전체 리소스 그룹이 아닌 특정 App Service 앱으로 제한되어 있습니다.

[GitHub](https://github.com/)에서 리포지토리로 **이동** 한 후 **설정 > 비밀 > 새 비밀 추가** 를 **선택** 합니다. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 **붙여넣습니다**. 비밀 이름을 **지정** 합니다(예: `AZURE_CREDENTIALS`).

**배포 센터** 에서 생성된 워크플로 파일에서 다음 예시와 같이 코드를 사용하여 `azure/webapps-deploy` 단계를 **수정** 합니다.

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

컨테이너 레지스트리 및 Docker 이미지를 구성하려면 [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set)를 **실행** 합니다.

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

# <a name="private-registry"></a>[프라이빗 레지스트리](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
다중 컨테이너(Docker Compose) 앱을 구성하려면 Docker Compose 파일을 로컬로 **준비** 한 후 `--multicontainer-config-file` 매개 변수를 사용하여 [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set)를 **실행** 합니다. Docker Compose 파일에 프라이빗 이미지가 포함된 경우 이전 예시에 표시된 것처럼 `--docker-registry-server-*` 매개 변수를 **추가** 합니다.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

컨테이너 레지스트리에서 앱으로의 CI/CD를 구성하려면 `--enable-cd` 매개 변수를 사용하여 [az webapp deployment container config](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config)를 **실행** 합니다. 이 명령은 웹후크 URL을 출력하지만 개별 단계로 레지스트리에 웹후크를 수동으로 만들어야 합니다. 다음 예제는 앱에서 CI/CD를 사용하도록 설정한 후 출력에서 웹후크 URL을 사용하여 Azure Container Registry에 웹후크를 만듭니다.

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
