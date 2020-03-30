---
title: 로컬 Git 리포지토리에서 배포
description: Azure App Service에 로컬 Git 배포를 사용하는 방법에 대해 알아봅니다. 로컬 컴퓨터에서 코드를 배포하는 가장 간단한 방법 중 하나입니다.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152995"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure 앱 서비스에 로컬 Git 배포

이 방법 가이드는 로컬 컴퓨터의 Git 리포지토리에서 [Azure 앱 서비스에](overview.md) 앱을 배포하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드의 단계를 수행하려면

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git을 설치합니다.](https://www.git-scm.com/downloads)
  
- 배포할 코드가 있는 로컬 Git 리포지토리를 갖습니다. 샘플 리포지토리를 다운로드하려면 로컬 터미널 창에서 다음 명령을 실행합니다.
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>쿠두 빌드 서버로 배포

Kudu 앱 서비스 빌드 서버를 사용하여 앱에 대한 로컬 Git 배포를 활성화하는 가장 쉬운 방법은 Azure Cloud Shell을 사용하는 것입니다. 

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>배포 URL 받기

기존 앱에 대한 로컬 Git 배포를 사용하도록 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) 설정하는 URL을 얻으려면 클라우드 셸에서 실행합니다. 앱 \<이름> \<및 그룹 이름> 앱 및 해당 Azure 리소스 그룹의 이름으로 바꿉니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Linux 앱 서비스 계획을 사용하는 경우 이 매개 변수를 추가해야 합니다.


또는 새 Git 지원 앱을 만들려면 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 매개 변수를 `--deployment-local-git` 사용하여 Cloud Shell에서 실행합니다. 앱 \<이름>, \<그룹 이름> \<및 계획 이름> 새 Git 앱, Azure 리소스 그룹 및 Azure 앱 서비스 계획의 이름으로 바꿉니다.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

두 명령 중 하나는 `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`다음과 같은 URL을 반환합니다. 이 URL을 사용하여 다음 단계에서 앱을 배포합니다.

이 계정 수준 URL을 사용하는 대신 앱 수준 자격 증명을 사용하여 로컬 Git을 활성화할 수도 있습니다. Azure App Service는 모든 앱에 대해 이러한 자격 증명을 자동으로 생성합니다. 

클라우드 셸에서 다음 명령을 실행하여 앱 자격 증명을 가져옵니다. 앱 \<이름> \<및 그룹 이름> 앱 이름 및 Azure 리소스 그룹 이름으로 바꿉니다.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

반환되는 URL을 사용하여 다음 단계에서 앱을 배포합니다.

### <a name="deploy-the-web-app"></a>웹앱 배포

1. 로컬 Git 리포지토리에 로컬 터미널 창을 열고 Azure 리모컨을 추가합니다. 다음 명령에서 url \<> 이전 단계에서 가져온 배포 사용자별 URL 또는 앱별 URL로 바꿉습니다.
   
   ```bash
   git remote add azure <url>
   ```
   
1. `git push azure master`을 사용하여 Azure 리모컨으로 푸시합니다. 
   
1. **Git 자격 증명 관리자** 창에서 Azure 로그인 [암호가](#configure-a-deployment-user)아닌 배포 사용자 암호를 입력합니다.
   
1. 출력을 검토합니다. ASP.NET 위한 MSBuild, `npm install` Node.js 및 Python용과 `pip install` 같은 런타임별 자동화가 표시될 수 있습니다. 
   
1. Azure 포털에서 앱을 찾아 콘텐츠가 배포되었는지 확인합니다.

## <a name="deploy-with-azure-pipelines-builds"></a>Azure 파이프라인 빌드를 통해 배포

계정에 필요한 권한이 있는 경우 앱에 대한 로컬 Git 배포를 사용하도록 Azure 파이프라인(미리 보기)을 설정할 수 있습니다. 

- Azure 계정에는 Azure Active Directory에 쓰고 서비스를 만들 수 있는 권한이 있어야 합니다. 
  
- Azure 계정에 Azure 구독의 **소유자** 역할이 있어야 합니다.

- 사용하려는 Azure DevOps 프로젝트의 관리자여야 합니다.

Azure 파이프라인(미리 보기)을 사용하여 앱에 대한 로컬 Git 배포를 사용하려면 다음을 수행할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com) **앱 서비스를**검색하고 선택합니다. 

1. Azure 앱 서비스 앱을 선택하고 왼쪽 메뉴에서 **배포 센터를** 선택합니다.
   
1. 배포 **센터** 페이지에서 **로컬 git**을 선택한 다음 **계속을**선택합니다. 
   
   ![로컬 Git을 선택한 다음 계속을 선택합니다.](media/app-service-deploy-local-git/portal-enable.png)
   
1. 공급자 **빌드** 페이지에서 **Azure 파이프라인(미리 보기)을**선택한 다음 **계속을**선택합니다. 
   
   ![Azure 파이프라인(미리 보기)을 선택한 다음 계속을 선택합니다.](media/app-service-deploy-local-git/pipeline-builds.png)

1. **구성** 페이지에서 새 Azure DevOps 조직을 구성하거나 기존 조직을 지정한 다음 **계속을**선택합니다.
   
   > [!NOTE]
   > 기존 Azure DevOps 조직이 나열되지 않은 경우 Azure 구독에 연결해야 할 수 있습니다. 자세한 내용은 [CD 릴리스 파이프라인 정의를](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)참조하십시오.
   
1. 앱 서비스 요금 제 [가격 책정 계층에](https://azure.microsoft.com/pricing/details/app-service/plans/)따라 스테이징 페이지로 **배포가** 표시될 수 있습니다. [배포 슬롯을 사용하도록 설정할지](deploy-staging-slots.md)여부를 선택한 다음 **계속을**선택합니다.
   
1. **요약** 페이지에서 설정을 검토한 다음 **완료를**선택합니다.
   
1. Azure 파이프라인이 준비되면 **배포 센터** 페이지에서 Git 리포지토리 URL을 복사하여 다음 단계에서 사용할 수 있습니다. 
   
   ![Git 리포지토리 URL 복사](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. 로컬 터미널 창에서 로컬 Git 리포지토리에 Azure 리모컨을 추가합니다. 명령에서 url \<> 이전 단계에서 가져온 Git 리포지토리의 URL로 바꿉습니다.
   
   ```bash
   git remote add azure <url>
   ```
   
1. `git push azure master`을 사용하여 Azure 리모컨으로 푸시합니다. 
   
1. **Git 자격 증명 관리자** 페이지에서 visualstudio.com 사용자 이름으로 로그인합니다. 다른 인증 방법에 대한 [참조 Azure DevOps 서비스 인증 개요](/vsts/git/auth-overview?view=vsts).
   
1. 배포가 완료되면 에서 `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`빌드 진행 률과 에서 `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`배포 진행 률을 봅니다.
   
1. Azure 포털에서 앱을 찾아 콘텐츠가 배포되었는지 확인합니다.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>배포 문제 해결

Git을 사용하여 Azure의 앱 서비스 앱에 게시할 때 다음과 같은 일반적인 오류 메시지가 표시될 수 있습니다.

|메시지|원인|해결 방법
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|앱이 실행되고 있지 않습니다.|Azure Portal에서 앱을 시작합니다. 웹 앱이 중지되면 Git 배포를 사용할 수 없습니다.|
|`Couldn't resolve host 'hostname'`|'azure' 원격의 주소 정보가 올바르지 않습니다.|`git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드세요.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|의 에서 `git push` `push.default` 값을 `.gitconfig`설정하지 않은 경우 분기를 지정하지 않았거나|마스터 `git push` 분기를 지정하여 다시 `git push azure master`실행합니다.|
|`src refspec [branchname] does not match any.`|'azure' 리모컨에서 마스터가 아닌 다른 분기로 푸시하려고 했습니다.|마스터 `git push` 분기를 지정하여 다시 `git push azure master`실행합니다.|
|`RPC failed; result=22, HTTP code = 5xx.`|이 오류는 HTTPS를 통해 큰 git 리포지토리를 푸시하려고 시도하는 경우 발생할 수 있습니다.|로컬 컴퓨터의 git 구성을 변경하여 `postBuffer` 더 크게 만듭니다. 예: `git config --global http.postBuffer 524288000`|
|`Error - Changes committed to remote repository but your web app not updated.`|추가 필수 모듈을 지정하는 _package.json_ 파일이 있는 Node.js 앱을 배포했습니다.|오류 `npm ERR!` 전에 오류 메시지를 검토하여 오류에 대한 자세한 컨텍스트를 참조하십시오. 다음은 이 오류의 알려진 원인과 해당 `npm ERR!` 메시지입니다.<br /><br />**잘못된 패키지.json 파일**:`npm ERR! Couldn't read dependencies.`<br /><br />**네이티브 모듈에는 Windows용 이진 분포가 없습니다.**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />또는 <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>추가 리소스

- [프로젝트 Kudu 설명서](https://github.com/projectkudu/kudu/wiki)
- [Azure 앱 서비스에 대한 지속적인 배포](deploy-continuous-deployment.md)
- [샘플: 로컬 Git 리포지토리(Azure CLI)에서 웹 앱을 만들고 코드를 배포합니다.](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [샘플: 웹 앱을 만들고 로컬 Git 리포지토리(PowerShell)에서 코드를 배포합니다.](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
