---
title: 로컬 Git 리포지토리에서 배포 - Azure App Service
description: Azure App Service에 로컬 Git 배포를 사용하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836870"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service에 로컬 Git 배포

이 방법 가이드에서는 앱을 배포 하는 방법을 보여 줍니다 [Azure App Service](overview.md) 로컬 컴퓨터의 Git 리포지토리에서 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드의 단계를 수행하려면

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git를 설치](https://www.git-scm.com/downloads)합니다.
  
- 배포 하려는 코드를 사용 하 여 로컬 Git 리포지토리를 경우 샘플 리포지토리를 다운로드 하려면 로컬 터미널 창에서 다음 명령을 실행 합니다.
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Kudu 빌드 서버를 사용 하 여 배포

App Service Kudu 빌드 서버를 사용 하 여 앱에 대 한 로컬 Git 배포를 사용 하도록 설정 하는 가장 쉬운 방법은 Azure Cloud Shell을 사용 하는 것입니다. 

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>배포 URL 가져오기

기존 앱에 대 한 로컬 Git 배포를 사용 하도록 설정 하려면 URL을 가져오려면 실행 [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) Cloud Shell에서. 바꿉니다 \<앱 이름 > 및 \<그룹-이름 > 앱 및 해당 Azure 리소스 그룹의 이름입니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

새 Git 지원 앱을 만들려면 다음을 실행 하거나 [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 사용 하 여 Cloud Shell에서는 `--deployment-local-git` 매개 변수입니다. 대체 \<앱 이름 >, \<그룹-이름 >, 및 \<계획 이름 > 새 Git 앱, 해당 Azure 리소스 그룹 및 해당 Azure App Service 계획에 대 한 이름입니다.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

같은 URL을 반환 하는 두 가지 명령 모두: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`합니다. 다음 단계에서 앱을 배포 하려면이 URL을 사용 합니다.

이 계정 수준 URL을 사용 하는 대신 앱 수준 자격 증명을 사용 하 여 로컬 Git을 사용할 수 있습니다. Azure App Service는 자동으로 모든 앱에 대 한 이러한 자격 증명을 생성합니다. 

Cloud Shell에서 다음 명령을 실행 하 여 앱 자격 증명을 가져옵니다. 바꿉니다 \<앱 이름 > 및 \<그룹-이름 > 앱의 이름 및 Azure 리소스 그룹 이름을 사용 합니다.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

다음 단계에서 앱 배포를 반환 하는 URL을 사용 합니다.

### <a name="deploy-the-web-app"></a>웹앱 배포

1. 로컬 Git 리포지토리에 로컬 터미널 창을 열고 Azure 원격을 추가 합니다. 다음 명령에서 대체 \<url > 배포 사용자 특정 URL 또는 이전 단계에서 가져온 앱 별 URL을 사용 하 여 합니다.
   
   ```bash
   git remote add azure <url>
   ```
   
1. 사용 하 여 원격 Azure로 푸시하여 `git push azure master`합니다. 
   
1. 에 **Git 자격 증명 관리자** 창에서 입력에 [배포 사용자 암호가](#configure-a-deployment-user), Azure 로그인 암호가 아닌 합니다.
   
1. 출력을 검토합니다. ASP.NET 용 MSBuild와 같은 런타임 관련 자동화 표시 될 수 있습니다 `npm install` Node.js 용 및 `pip install` Python에 대 한 합니다. 
   
1. 콘텐츠가 배포 되었는지 확인 하려면 Azure portal에서 앱으로 이동 합니다.

## <a name="deploy-with-azure-pipelines-builds"></a>Azure 파이프라인 빌드를 사용 하 여 배포

계정에 필요한 권한이 있는 경우 앱에 대 한 로컬 Git 배포를 사용 하도록 설정 하려면 Azure 파이프라인 (미리 보기)를 설정할 수 있습니다. 

- Azure 계정 서비스를 만들고 Azure Active Directory에 쓰기 권한이 있어야 합니다. 
  
- Azure 계정에 있어야 합니다 **소유자** Azure 구독에 역할입니다.

- 사용 하려는 Azure DevOps 프로젝트 관리자 여야 합니다.

사용 하려면 Azure 파이프라인 (미리 보기)를 사용 하 여 앱에 대 한 로컬 Git 배포:

1. Azure App Service 앱 페이지에 이동 합니다 [Azure portal](https://portal.azure.com), 선택한 **배포 센터** 왼쪽된 메뉴에서.
   
1. 에 **배포 센터** 페이지에서 **로컬 Git**를 선택한 후 **계속**합니다. 
   
   ![로컬 Git을 선택한 후 계속](media/app-service-deploy-local-git/portal-enable.png)
   
1. 에 **빌드 공급자** 페이지에서 **Azure 파이프라인 (미리 보기)** 를 선택한 후 **계속**합니다. 
   
   ![Azure 파이프라인 (미리 보기)를 선택한 다음 계속을 선택 합니다.](media/app-service-deploy-local-git/pipeline-builds.png)

1. 에 **구성** 페이지, 새 Azure DevOps 조직에서 구성 하 고 또는 기존 조직의 지정 하 고 선택한 **계속**합니다.
   
   > [!NOTE]
   > 기존 Azure DevOps 조직 목록에 없으면 Azure 구독에 연결 해야 합니다. 자세한 내용은 [CD 릴리스 파이프라인 정의](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)합니다.
   
1. App Service 계획에 따라 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/plans/)에 표시 될 수 있습니다를 **스테이징에 배포** 페이지입니다. 선택 것인지 [배포 슬롯을 사용 하도록 설정](deploy-staging-slots.md)를 선택한 후 **계속**합니다.
   
1. 에 **요약** 페이지에서 설정을 검토 하 고 선택한 **마침**합니다.
   
1. Azure 파이프라인 준비 되 면에서 Git 리포지토리 URL을 복사 합니다 **배포 센터** 다음 단계에서 사용 하는 페이지입니다. 
   
   ![Git 리포지토리 URL 복사](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. 로컬 터미널 창에서 로컬 Git 리포지토리에 Azure 원격을 추가 합니다. 명령에서 대체 \<url > 이전 단계에서 가져온 Git 리포지토리의 URL을 사용 하 여 합니다.
   
   ```bash
   git remote add azure <url>
   ```
   
1. 사용 하 여 원격 Azure로 푸시하여 `git push azure master`합니다. 
   
1. 에 **Git Credential Manager** 페이지, visualstudio.com 사용자 이름을 사용 하 여 로그인 합니다. 다른 인증 방법을 참조 하세요 [Azure DevOps 서비스 인증 개요](/vsts/git/auth-overview?view=vsts)합니다.
   
1. 배포가 완료 되 면 보기에서 빌드 진행 상황 `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`, 및에서 배포 진행률 `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`합니다.
   
1. 콘텐츠가 배포 되었는지 확인 하려면 Azure portal에서 앱으로 이동 합니다.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>배포 문제 해결

Git를 사용 하 여 Azure에서 App Service 앱을 게시할 때와 같은 일반적인 오류 메시지가 나타날 수 있습니다.

|메시지|원인|해결 방법
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|앱을 실행 하지 않습니다.|Azure Portal에서 앱을 시작합니다. 웹 앱 중지 되 면 Git 배포를 사용할 수 없습니다.|
|`Couldn't resolve host 'hostname'`|'Azure' 원격에 대 한 주소 정보가 올바르지 않습니다.|`git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드세요.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|중에 분기를 지정 하지 않은 `git push`를 설정 하지 않은 또는 `push.default` 값 `.gitconfig`합니다.|실행할 `git push` 마스터 분기를 다시 지정: `git push azure master`합니다.|
|`src refspec [branchname] does not match any.`|마스터가 아닌 다른 분기에 'azure' 원격 푸시 하려고 했습니다.|실행할 `git push` 마스터 분기를 다시 지정: `git push azure master`합니다.|
|`RPC failed; result=22, HTTP code = 5xx.`|이 오류는 HTTPS를 통해 큰 git 리포지토리를 푸시하려고 시도하는 경우 발생할 수 있습니다.|확인 하기 위해 로컬 컴퓨터에서 git 구성을 변경 합니다 `postBuffer` 큰 합니다. 예를 들어 `git config --global http.postBuffer 524288000`을 참조하십시오.|
|`Error - Changes committed to remote repository but your web app not updated.`|Node.js 앱을 배포 하는 _package.json_ 추가 필수 모듈을 지정 하는 파일입니다.|검토를 `npm ERR!` 이 오류는 실패 시 더 많은 컨텍스트를 이전 오류 메시지입니다. 다음은이 오류 및 해당의 알려진된 원인 `npm ERR!` 메시지:<br /><br />**잘못 된 package.json 파일**: `npm ERR! Couldn't read dependencies.`<br /><br />**Windows에 대 한 네이티브 모듈 바이너리 배포가 없는**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />로 구분하거나 여러 <br />' npm ERR! [modulename@version] preinstall: \make || gmake\`|

## <a name="additional-resources"></a>추가 자료

- [프로젝트 Kudu 설명서](https://github.com/projectkudu/kudu/wiki)
- [Azure App Service에 연속 배포](deploy-continuous-deployment.md)
- [샘플: 웹 앱 만들기 및 로컬 Git 리포지토리 (Azure CLI)에서 코드 배포](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [샘플: 웹 앱 만들기 및 로컬 Git 리포지토리 (PowerShell)의 코드 배포](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
