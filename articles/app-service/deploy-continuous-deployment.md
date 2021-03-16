---
title: 지속적 배포 구성
description: GitHub, BitBucket, Azure Repos 또는 기타 리포지토리에서 Azure App Service 하는 CI/CD를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 요구 사항에 맞는 빌드 파이프라인을 선택 합니다.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 52f0db739cff9614dc4e9f5ef71d582e926fc65a
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470271"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service에 지속적인 배포

[Azure App Service](overview.md) 는 최신 업데이트를 끌어와 [GitHub](https://help.github.com/articles/create-a-repo), [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)및 [Azure Repos](/azure/devops/repos/git/creatingrepo) 리포지토리에서 지속적인 배포를 가능 하 게 합니다.

> [!NOTE]
> 이전 배포 환경인 Azure Portal의 **개발 센터 (클래식)** 페이지는 2021 년 3 월부터 더 이상 사용 되지 않습니다. 이 변경 내용은 앱의 기존 배포 설정에 영향을 주지 않으며, **배포 센터** 페이지에서 계속 해 서 앱 배포를 관리할 수 있습니다.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>배포 원본 구성

1. [Azure Portal](https://portal.azure.com)에서 App Service 앱에 대 한 관리 페이지로 이동 합니다.

1. 왼쪽 메뉴에서 **배포 센터**  >  **설정** 을 클릭 합니다. 

1. **원본** 에서 CI/CD 옵션 중 하나를 선택 합니다.

    ![배포 센터에서 Azure App Service에 대 한 배포 원본을 선택 하는 방법을 보여 줍니다.](media/app-service-continuous-deployment/choose-source.png)

단계에 대 한 선택 항목에 해당 하는 탭을 선택 합니다.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub 작업](#how-the-github-actions-build-provider-works) 은 기본 빌드 공급자입니다. 변경 하려면 **공급자**  >  **App Service 빌드 서비스** (Kudu) > **확인** 을 클릭 합니다.

    > [!NOTE]
    > App Service 앱에 대 한 빌드 공급자로 Azure Pipelines를 사용 하려면 App Service에서 구성 하지 마십시오. 대신 Azure Pipelines에서 직접 CI/CD를 구성 합니다. **Azure Pipelines** 옵션은 바로 오른쪽 방향으로 가리킵니다.

1. 처음으로 GitHub에서 배포 하는 경우 **권한 부여를 클릭 하** 고 권한 부여 프롬프트를 따릅니다. 다른 사용자의 리포지토리에서 배포 하려면 **계정 변경** 을 클릭 합니다.

1. Azure 계정에 GitHub에 대 한 권한을 부여한 후에는 **조직**, **리포지토리** 및 **분기** 를 선택 하 여에 대 한 CI/CD를 구성 합니다.

1. GitHub 작업이 선택한 빌드 공급자 인 경우 **런타임 스택** 및 **버전** 드롭다운을 사용 하 여 원하는 워크플로 파일을 선택할 수 있습니다. Azure는이 워크플로 파일을 선택한 GitHub 리포지토리에 커밋 하 여 빌드 및 배포 작업을 처리 합니다. 변경 내용을 저장 하기 전에 파일을 보려면 **파일 미리 보기** 를 클릭 합니다.

    > [!NOTE]
    > App Service는 앱의 [언어 스택 설정을](configure-common.md#configure-language-stack-settings) 검색 하 고 가장 적합 한 워크플로 템플릿을 선택 합니다. 다른 템플릿을 선택 하는 경우 제대로 실행 되지 않는 앱을 배포할 수 있습니다. 자세한 내용은 [GitHub 작업 빌드 공급자의 작동 방식](#how-the-github-actions-build-provider-works)을 참조 하세요.

1. **저장** 을 클릭합니다.
   
    선택한 리포지토리 및 분기의 새 커밋이 이제 App Service 앱에 지속적으로 배포됩니다. **로그** 탭에서 커밋 및 배포를 추적할 수 있습니다.

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

BitBucket 통합에서는 빌드 자동화를 위해 Kudu (App Service 빌드 서비스)를 사용 합니다.

4. BitBucket에서 처음으로 배포 하는 경우 **권한 부여를 클릭 하** 고 권한 부여 프롬프트를 따릅니다. 다른 사용자의 리포지토리에서 배포 하려면 **계정 변경** 을 클릭 합니다.

1. Bitbucket의 경우 지속적으로 배포 하려는 Bitbucket **팀**, **리포지토리** 및 **분기** 를 선택 합니다.

1. **저장** 을 클릭합니다.
   
    선택한 리포지토리 및 분기의 새 커밋이 이제 App Service 앱에 지속적으로 배포됩니다. **로그** 탭에서 커밋 및 배포를 추적할 수 있습니다.
   
# <a name="local-git"></a>[로컬 Git](#tab/local)

[Azure App Service에 대 한 로컬 Git 배포를](deploy-local-git.md)참조 하세요.

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> 배포 원본으로 Azure Repos는 Windows 앱에 대 한 지원입니다.
>

4. Kudu (App Service 빌드 서비스)는 기본 빌드 공급자입니다.

    > [!NOTE]
    > App Service 앱에 대 한 빌드 공급자로 Azure Pipelines를 사용 하려면 App Service에서 구성 하지 마십시오. 대신 Azure Pipelines에서 직접 CI/CD를 구성 합니다. **Azure Pipelines** 옵션은 바로 오른쪽 방향으로 가리킵니다.

1. 지속적으로 배포 하려는 **Azure DevOps 조직**, **프로젝트**, **리포지토리** 및 **분기** 를 선택 합니다. 

    DevOps 조직이 나열 되지 않은 경우 아직 Azure 구독에 연결 되지 않은 것입니다. 자세한 내용은 [Azure 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure)를 참조 하세요.

-----

## <a name="disable-continuous-deployment"></a>지속적 배포 사용 안 함

1. [Azure Portal](https://portal.azure.com)에서 App Service 앱에 대 한 관리 페이지로 이동 합니다.

1. 왼쪽 메뉴에서 **배포 센터**  >  **설정**  >  **연결 끊기** 를 클릭 합니다. 

    ![Azure Portal에서 App Service 앱과 클라우드 폴더 동기화를 분리 하는 방법을 보여 줍니다.](media/app-service-continuous-deployment/disable.png)

1. 기본적으로 GitHub 작업 워크플로 파일은 리포지토리에 유지 되지만 앱에 대 한 배포는 계속 트리거됩니다. 저장소에서 삭제 하려면 **워크플로 파일 삭제** 를 선택 합니다.

1. **확인** 을 클릭합니다.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>GitHub 작업 빌드 공급자의 작동 방식

GitHub 작업 빌드 공급자는 [github의 ci/cd](#configure-deployment-source)에 대 한 옵션이 며 CI/cd를 설정 하려면 다음을 수행 합니다.

- App Service에 빌드 및 배포 작업을 처리 하기 위해 github 작업 워크플로 파일을 GitHub 리포지토리에 예치금.
- 앱에 대 한 게시 프로필을 GitHub 암호로 추가 합니다. 워크플로 파일은이 암호를 사용 하 여 App Service 인증 합니다.
- [워크플로 실행 로그](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) 에서 정보를 캡처하고 앱 **배포 센터** 의 **로그** 탭에 표시 합니다.

다음과 같은 방법으로 GitHub 작업 빌드 공급자를 사용자 지정할 수 있습니다.

- GitHub 리포지토리에서 생성 된 후에 워크플로 파일을 사용자 지정 합니다. 자세한 내용은 [GitHub 동작에 대 한 워크플로 구문](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)을 참조 하세요. 워크플로가 [azure/webapps-배포](https://github.com/Azure/webapps-deploy) 작업을 사용 하 여 App Service에 배포 되는지 확인 합니다.
- 선택한 분기가 보호 되는 경우에도 구성을 저장 하지 않고도 워크플로 파일을 미리 본 다음 리포지토리에 수동으로 추가할 수 있습니다. 이 메서드는 Azure Portal와의 로그 통합을 제공 하지 않습니다.
- 게시 프로필 대신 Azure Active Directory에서 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 를 사용 하 여 배포 합니다.

#### <a name="authenticate-with-a-service-principal"></a>서비스 주체를 사용 하 여 인증

이 선택적 구성은 생성 된 워크플로 파일의 게시 프로필을 사용 하 여 기본 인증을 대체 합니다.

1. [Azure CLI](/cli/azure/)에서 [az ad sp create-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) 명령을 사용 하 여 서비스 사용자를 생성 합니다. 다음 예제에서는 *\<subscription-id>* , *\<group-name>* 및를 *\<app-name>* 사용자 고유의 값으로 바꿉니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > 보안을 위해 서비스 주체에 필요한 최소한의 액세스 권한을 부여 합니다. 이전 예제의 범위는 전체 리소스 그룹이 아니라 특정 App Service 앱으로 제한 됩니다.
    
1. 최상위 수준을 포함 하 여 다음 단계에 대 한 전체 JSON 출력을 저장 합니다 `{}` .

1. [GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가** 합니다.

1. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 암호에와 같은 이름을 지정 합니다 `AZURE_CREDENTIALS` .

1. **Deployment Center** 에 의해 생성 된 워크플로 파일에서 `azure/webapps-deploy` 다음 예제와 같은 코드를 사용 하 여 단계를 수정 합니다 (Node.js 워크플로 파일에서 수정 됨).

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
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>다른 리포지토리에서 배포

Windows 앱의 경우 포털이 직접 지원 하지 않는 클라우드 Git 또는 Mercurial 리포지토리에서 (예: [Gitlab](https://gitlab.com/)) 연속 배포를 수동으로 구성할 수 있습니다. **원본** 드롭다운에서 외부 Git을 선택 하 여이 작업을 수행 합니다. 자세한 내용은 [수동 단계를 사용 하 여 연속 배포 설정](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)을 참조 하세요.

## <a name="more-resources"></a>추가 리소스

* [Azure Pipelines에서 Azure 앱 서비스로 배포](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [연속 배포에 대 한 일반적인 문제 조사](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell 사용](/powershell/azure/)
* [프로젝트 Kudu](https://github.com/projectkudu/kudu/wiki)
