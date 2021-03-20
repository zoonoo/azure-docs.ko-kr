---
title: 로컬 Git 리포지토리에서 배포
description: Azure App Service에 로컬 Git 배포를 사용하는 방법에 대해 알아봅니다. 로컬 컴퓨터의 코드를 배포 하는 가장 간단한 방법 중 하나입니다.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5dd6183bf88c167adb2f084c319cd90b94351dfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100560458"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service에 로컬 Git 배포

이 방법 가이드에서는 로컬 컴퓨터의 Git 리포지토리에서 [Azure App Service](overview.md) 에 앱을 배포 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드의 단계를 수행하려면

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git를 설치](https://www.git-scm.com/downloads)합니다.

- 배포 하려는 코드가 포함 된 로컬 Git 리포지토리가 있습니다. 샘플 리포지토리를 다운로드 하려면 로컬 터미널 창에서 다음 명령을 실행 합니다.
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>배포 사용자 구성

[Azure App Service에 대 한 배포 자격 증명 구성을](deploy-configure-credentials.md)참조 하세요. 사용자 범위 자격 증명이 나 응용 프로그램 범위 자격 증명 중 하나를 사용할 수 있습니다.

## <a name="create-a-git-enabled-app"></a>Git 사용 앱 만들기

App Service 앱이 이미 있고이 앱에 대 한 로컬 Git 배포를 구성 하려는 경우 대신 [기존 앱 구성](#configure-an-existing-app) 을 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[`az webapp create`](/cli/azure/webapp#az_webapp_create)옵션과 함께를 실행 `--deployment-local-git` 합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

출력에는와 같은 URL이 포함 되어 있습니다 `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . 다음 단계에서이 URL을 사용 하 여 앱을 배포 합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Git 리포지토리의 루트에서 [AzWebApp](/powershell/module/az.websites/new-azwebapp) 를 실행 합니다. 예를 들면 다음과 같습니다.

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Git 리포지토리의 디렉터리에서이 cmdlet을 실행 하면 이름이 인 사용자를 위해 App Service 앱에 대 한 Git 원격이 자동으로 만들어집니다 `azure` .

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

포털에서 먼저 앱을 만든 다음, 해당 앱에 대 한 배포를 구성 해야 합니다. [기존 앱 구성](#configure-an-existing-app)을 참조 하세요.

-----

## <a name="configure-an-existing-app"></a>기존 앱 구성

앱을 아직 만들지 않은 경우 대신 [Git 사용 앱 만들기](#create-a-git-enabled-app) 를 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) 를 실행합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

출력에는와 같은 URL이 포함 되어 있습니다 `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . 다음 단계에서이 URL을 사용 하 여 앱을 배포 합니다.

> [!TIP]
> 이 URL은 사용자 범위 배포 사용자 이름을 포함 합니다. 원하는 경우 [응용 프로그램 범위 자격 증명](deploy-configure-credentials.md#appscope) 을 대신 사용할 수 있습니다. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

`scmType` [AzResource](/powershell/module/az.resources/set-azresource) cmdlet을 실행 하 여 앱의를 설정 합니다.

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에서 앱의 관리 페이지로 이동 합니다.

1. 왼쪽 메뉴에서 **배포 센터**  >  **설정** 을 선택 합니다. **원본** 에서 **로컬 Git** 를 선택 하 고 **저장** 을 클릭 합니다.

    ![Azure Portal에서 App Service에 대 한 로컬 Git 배포를 사용 하도록 설정 하는 방법을 보여 줍니다.](./media/deploy-local-git/enable-portal.png)

1. 로컬 Git 섹션에서 나중에에 대 한 **Git Clone Uri** 를 복사 합니다. 이 Uri는 자격 증명을 포함 하지 않습니다.

-----

## <a name="deploy-the-web-app"></a>웹앱 배포

1. 로컬 터미널 창에서 Git 리포지토리의 루트로 디렉터리를 변경 하 고 앱에서 가져온 URL을 사용 하 여 Git 원격을 추가 합니다. 선택한 방법에서 URL을 제공 하지 않으면 `https://<app-name>.scm.azurewebsites.net/<app-name>.git` 에서 앱 이름으로를 사용 `<app-name>` 합니다.
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > AzWebApp를 [사용 하 여 PowerShell에서 Git 사용 앱을 만든](#create-a-git-enabled-app)경우 원격이 이미 만들어집니다.
   
1. 를 사용 하 여 Azure 원격에 푸시합니다 `git push azure master` . 
   
1. **Git 자격 증명 관리자** 창에서 Azure 로그인 자격 증명이 아닌 [사용자 범위 또는 응용 프로그램 범위 자격 증명](#configure-a-deployment-user)을 입력 합니다.

    Git 원격 URL에 사용자 이름 및 암호가 이미 포함 된 경우에는 메시지가 표시 되지 않습니다. 
   
1. 출력을 검토합니다. ASP.NET에 대 한 MSBuild, Node.js 및 Python에 대 한 런타임 관련 자동화가 표시 될 수 있습니다 `npm install` `pip install` . 
   
1. Azure Portal에서 앱으로 이동 하 여 콘텐츠가 배포 되었는지 확인 합니다.

## <a name="troubleshoot-deployment"></a>배포 문제 해결

Git를 사용 하 여 Azure에서 App Service 앱에 게시 하는 경우 다음과 같은 일반적인 오류 메시지가 표시 될 수 있습니다.

|메시지|원인|해결 방법
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|앱이 실행 되 고 있지 않습니다.|Azure Portal에서 앱을 시작합니다. 웹 앱이 중지 되 면 Git 배포를 사용할 수 없습니다.|
|`Couldn't resolve host 'hostname'`|' Azure ' 원격에 대 한 주소 정보가 잘못 되었습니다.|`git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드세요.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|중에 분기를 지정 하지 `git push` 않았거나에서 값을 설정 하지 않았습니다 `push.default` `.gitconfig` .|`git push`주 분기를 지정 하 여를 다시 실행 `git push azure main` 합니다.|
|`src refspec [branchname] does not match any.`|' Azure ' 원격에서 main 이외의 분기로 푸시 하려고 했습니다.|`git push`주 분기를 지정 하 여를 다시 실행 `git push azure main` 합니다.|
|`RPC failed; result=22, HTTP code = 5xx.`|이 오류는 HTTPS를 통해 큰 git 리포지토리를 푸시하려고 시도하는 경우 발생할 수 있습니다.|로컬 컴퓨터에서 git 구성을 변경 하 여 `postBuffer` 더 크게 만듭니다. 예를 들어 `git config --global http.postBuffer 524288000`을 참조하십시오.|
|`Error - Changes committed to remote repository but your web app not updated.`|추가 필수 모듈을 지정 하는 파일 _에package.js_ 를 사용 하 여 Node.js 앱을 배포 했습니다.|오류 `npm ERR!` 에 대 한 추가 컨텍스트를 위해이 오류 이전의 오류 메시지를 검토 합니다. 이 오류의 알려진 원인과 해당 메시지는 다음과 같습니다 `npm ERR!` .<br /><br />**파일의 package.js형식이 잘못** 되었습니다. `npm ERR! Couldn't read dependencies.`<br /><br />**네이티브 모듈에는 Windows 용 이진 배포가** 없습니다.<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />또는 <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>추가 리소스

- [App Service 빌드 서버 (Project Kudu 설명서)](https://github.com/projectkudu/kudu/wiki)
- [Azure App Service 연속 배포](deploy-continuous-deployment.md)
- [샘플: 웹 앱 만들기 및 로컬 Git 리포지토리의 코드 배포 (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [샘플: 웹 앱 만들기 및 로컬 Git 리포지토리의 코드 배포 (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
