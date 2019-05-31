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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.custom: seodec18
ms.openlocfilehash: b879036dcd79901cb634fa197932e833cb22d12a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "65956088"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service에 대한 로컬 Git 배포

이 방법 가이드에서는 로컬 컴퓨터의 Git 리포지토리에서 [Azure App Service](overview.md) 에 코드를 배포하는 방법을 보여 줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 수행하려면

* [Git를 설치](https://www.git-scm.com/downloads)합니다.
* 배포할 코드가 있는 로컬 Git 리포지토리를 가지고 있어야 합니다.

샘플 리포지토리를 사용해서 따라하려면 로컬 터미널 창에서 다음 명령을 실행합니다.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-builds"></a>Kudu 빌드를 사용 하 여 배포

Kudu 빌드 서버로 앱에 대한 로컬 Git 배포를 사용하도록 설정하는 가장 쉬운 방법은 Cloud Shell을 사용하는 것입니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Kudu로 로컬 Git을 사용하도록 설정

Kudu 빌드 서버로 앱에 대한 로컬 Git 배포를 사용하도록 설정하려면 Cloud Shell에서 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git)을 실행합니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

대신 Git 지원 앱을 만들려면 `--deployment-local-git` 매개 변수를 사용하여 Cloud Shell에서 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)를 실행합니다.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` 명령은 다음과 유사한 출력을 표시합니다.

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>프로젝트 배포

_로컬 터미널 창_으로 돌아와서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. _\<url>_ 을 [앱에 대해 Git 사용](#enable-local-git-with-kudu)에서 가져온 Git 원격의 URL로 바꿉니다.

```bash
git remote add azure <url>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. 암호를 입력하라는 메시지가 표시되는 경우 Azure Portal에 로그인할 때 사용하는 암호가 아닌, [배포 사용자 구성](#configure-a-deployment-user)에서 만든 암호를 입력해야 합니다.

```bash
git push azure master
```

출력에 ASP.NET용 MSBuild, Node.js용 `npm install` 및 Python용 `pip install`과 같은 런타임 관련 자동화가 표시될 수 있습니다. 

앱으로 이동하여 콘텐츠가 배포되었는지 확인합니다.

## <a name="deploy-with-azure-devops-builds"></a>Azure DevOps 빌드를 사용 하 여 배포

> [!NOTE]
> App Service가 Azure DevOps Services 조직에서 필요한 Azure Pipelines를 만들려면 사용자의 Azure 계정에 Azure 구독의 **소유자**의 역할이 있어야 합니다.
>

Kudu 빌드 서버로 앱에 대한 로컬 Git 배포를 사용하도록 설정하려면 [Azure Portal](https://portal.azure.com)에서 앱으로 이동합니다.

앱 페이지의 왼쪽 탐색 영역에서 **배포 센터** > **로컬 Git** > **계속**을 클릭합니다.

![](media/app-service-deploy-local-git/portal-enable.png)

클릭 **Azure 파이프라인 (미리 보기)** > **계속**합니다.

![](media/app-service-deploy-local-git/pipeline-builds.png)

에 **구성** 페이지에서 새 Azure DevOps 조직에서 구성 하거나 기존 조 직을 지정 합니다. 작업을 마쳤으면 **계속**을 클릭합니다.

> [!NOTE]
> 나열 되지 않은 기존 Azure DevOps 조 직을 사용 하려는 경우 해야 [Azure 구독에 Azure DevOps 서비스 조직 연결할](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)합니다.

App Service 계획의 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/plans/)에 따라 **스테이징에 배포** 페이지가 표시될 수 있습니다. 배포 슬롯을 활성화할지 여부를 선택한 다음, **계속**을 클릭합니다.

**요약** 페이지에서 옵션을 확인하고 **마침**을 클릭합니다.

Azure DevOps Services 조직을 준비하는 데 몇 분 정도 걸립니다. 준비되면 배포 센터에서 Git 리포지토리 URL을 복사합니다.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

_로컬 터미널 창_으로 돌아와서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. _\<url>_ 을 마지막 단계에서 가져온 URL로 바꿉니다.

```bash
git remote add vsts <url>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. Git 자격 증명 관리자에서 메시지가 표시되면 visualstudio.com 사용자를 사용하여 로그인합니다. 추가 인증 방법은 [Azure DevOps Services authentication overview](/vsts/git/auth-overview?view=vsts)(Azure DevOps Services 인증 개요)를 참조하세요.

```bash
git push vsts master
```

배포가 완료된 후에 `https://<vsts_account>.visualstudio.com/<project_name>/_build`에서 빌드 진행률을, `https://<vsts_account>.visualstudio.com/<project_name>/_release`에서 배포 진행률을 확인할 수 있습니다.

앱으로 이동하여 콘텐츠가 배포되었는지 확인합니다.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Kudu 배포 문제 해결

다음은 Git을 사용하여 Azure에서 App Service 앱을 게시할 때 일반적으로 발생하는 문제 또는 오류입니다.

---
**증상**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**원인**: 이 오류는 앱이 가동 및 실행되지 않는 경우에 발생할 수 있습니다.

**해결 방법**: Azure Portal에서 앱을 시작합니다. 웹앱이 중지되면 Git 배포를 사용할 수 없습니다.

---
**증상**: `Couldn't resolve host 'hostname'`

**원인**: 이 오류는 ‘azure’ 원격을 만들 때 입력한 주소 정보가 올바르지 않을 경우에 발생할 수 있습니다.

**해결 방법**: `git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드세요.

---
**증상**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**원인**: 이 오류는 `git push` 중에 분기를 지정하지 않거나 `.gitconfig`에 `push.default` 값을 설정하지 않은 경우 발생할 수 있습니다.

**해결 방법**: `git push`를 다시 실행하고 마스터 분기를 지정합니다. 예를 들면 다음과 같습니다.

```bash
git push azure master
```

---
**증상**: `src refspec [branchname] does not match any.`

**원인**: 이 오류는 ‘azure’ 원격의 마스터가 아닌 다른 분기에 푸시하려는 경우 발생할 수 있습니다.

**해결 방법**: `git push`를 다시 실행하고 마스터 분기를 지정합니다. 예를 들면 다음과 같습니다.

```bash
git push azure master
```

---
**증상**: `RPC failed; result=22, HTTP code = 5xx.`

**원인**: 이 오류는 HTTPS를 통해 큰 git 리포지토리를 푸시하려고 시도하는 경우 발생할 수 있습니다.

**해결 방법**: 더 큰 postBuffer를 만들도록 로컬 머신에서 git 구성을 변경합니다.

```bash
git config --global http.postBuffer 524288000
```

---
**증상**: `Error - Changes committed to remote repository but your web app not updated.`

**원인**: 이 오류는 추가 필수 모듈을 지정하는 _package.json_ 파일이 있는 Node.js 앱을 배포하는 경우에 발생할 수 있습니다.

**해결 방법**: ‘npm ERR!’을 포함하는 추가 메시지를 이 오류 이전에 기록해야 합니다. 이러한 메시지는 오류 원인에 대한 추가 내용을 제공할 수 있습니다. 다음은 이 오류 및 해당 'npm ERR!' 메시지의 알려진 원인입니다. 메시지:

* **잘못된 package.json 파일**: npm ERR! 종속성을 읽을 수 없음
* **Windows용 바이너리 배포가 없는 네이티브 모듈**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      또는
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>추가 리소스

* [프로젝트 Kudu 설명서](https://github.com/projectkudu/kudu/wiki)
* [Azure App Service에 연속 배포](deploy-continuous-deployment.md)
* [샘플: 로컬 Git 리포지토리에서 웹앱 만들기 및 코드 배포(Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [샘플: 로컬 Git 리포지토리에서 웹앱 만들기 및 코드 배포(PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
