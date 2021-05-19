---
title: FTP/S를 사용하여 콘텐츠 배포
description: FTP 또는 FTPS를 사용하여 Azure App Service에 앱을 배포하는 방법을 알아봅니다. 암호화되지 않은 FTP를 사용하지 않으면 웹사이트 보안이 강화됩니다.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: fd856ee47c1100292f7558bb0fa2a1772951a3cb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832372"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S를 사용하여 앱에 Azure App Service에 배포

이 문서는 FTP 또는 FTPS를 사용하여 웹앱, 모바일 앱 백 엔드 또는 API 앱을 [Azure App Service](./overview.md)에 배포하는 방법을 보여 줍니다.

앱에 대한 FTP/S 엔드포인트는 이미 활성화되어 있습니다. FTP/S 배포를 사용하도록 설정하는 데 필요한 구성은 없습니다.

> [!NOTE]
> 이전 배포 환경인 Azure Portal의 **개발 센터(클래식)** 페이지는 2021년 3월부터 더 이상 사용되지 않습니다. 이 변경 내용은 앱의 기존 배포 설정에 영향을 주지 않으며, **배포 센터** 페이지에서 계속해서 앱 배포를 관리할 수 있습니다.

## <a name="get-deployment-credentials"></a>배포 자격 증명 가져오기

1. [Azure App Service의 배포 자격 증명 구성](deploy-configure-credentials.md)의 지침에 따라 애플리케이션 범위 자격 증명을 복사하거나 사용자 범위 자격 증명을 설정합니다. 자격 증명 중 하나를 사용하여 앱의 FTP/S 엔드포인트에 연결할 수 있습니다.

1. 선택한 자격 증명 범위에 따라 FTP 사용자 이름을 다음 형식으로 만듭니다.

    | 애플리케이션 범위 | 사용자 범위 |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    App Service에서 FTP/S 엔드포인트는 앱 간에 공유됩니다. 사용자 범위 자격 증명은 특정 리소스에 연결되지 않으므로 다음과 같이 사용자 범위 사용자 이름 앞에 앱 이름을 추가해야 합니다.

## <a name="get-ftps-endpoint"></a>FTP/S 엔드포인트 가져오기
    
# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

배포 자격 증명을 복사한 앱의 동일한 관리 페이지에서(**배포 센터** > **FTP 자격 증명**) **FTPS 엔드포인트** 를 복사합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) 명령을 실행합니다. 다음 예제에서는 [JMES 경로](https://jmespath.org/)를 사용하여 출력에서 FTP/S 엔드포인트를 추출합니다.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

각 앱에는 FTP/S 엔드포인트가 두 개 있습니다. 하나는 읽기-쓰기이며 다른 하나는 읽기 전용(`ReadOnly`가 있는 `profileName`)이고 데이터 복구 시나리오용입니다. FTP를 사용하여 파일을 배포하려면 읽기-쓰기 엔드포인트의 URL을 복사합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) 명령을 실행합니다. 다음 예제에서는 XML 출력에서 FTP/S 엔드포인트를 추출합니다.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Azure에 파일 배포

1. FTP 클라이언트(예: [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/) 또는 [WinSCP](https://winscp.net/index.php))에서 수집한 연결 정보를 사용하여 앱에 연결합니다.
2. 파일 및 해당 디렉터리 구조를 Azure의 [**/site/wwwroot** 디렉터리](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)(또는 WebJobs의 경우 **/site/wwwroot/App_Data/Jobs/** 디렉터리)에 복사합니다.
3. 앱의 URL을 찾아 앱이 제대로 실행하는지 확인합니다. 

> [!NOTE] 
> [Git 기반 배포](deploy-local-git.md) 및 [Zip 배포](deploy-zip.md)와 달리 FTP 배포는 다음과 같은 빌드 자동화를 지원하지 않습니다. 
>
> - 종속성 복원(예: NuGet, NPM, PIP 및 Composer Automation)
> - .NET 이진 파일 컴파일
> - web.config 생성([Node.js 예제](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps) 참조)
> 
> 로컬 컴퓨터에서 이러한 필요한 파일을 수동으로 생성한 후 앱과 함께 배포합니다.
>

## <a name="enforce-ftps"></a>FTPS 적용

보안 강화를 위해 FTPS(FTP over TLS/SSL)만 허용해야 합니다. FTP 배포를 사용하지 않을 경우 FTP 및 FTPS를 둘 다 사용하지 않도록 설정할 수도 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)의 앱 리소스 페이지 왼쪽 탐색 창에서 **구성** > **일반 설정** 을 선택합니다.

2. 암호화되지 않은 FTP를 사용하지 않으려면 **FTP 상태** 에서 **FTPS만** 을 선택합니다. FTP 및 FTPS를 둘 다 완전히 사용하지 않으려면 **사용 안 함** 을 선택합니다. 완료되면 **저장** 을 클릭합니다. **FTPS만** 을 사용할 경우 웹앱의 **TLS/SSL 설정** 블레이드로 이동하여 TLS 1.2 이상을 적용해야 합니다. TLS 1.0 및 1.1은 **FTPS만** 으로 지원되지 않습니다.

    ![FTP/S 사용 안 함](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

`--ftps-state` 인수와 함께 [az webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) 명령을 실행합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

`--ftps-state`에 가능한 값은 `AllAllowed`(FTP 및 FTPS 사용), `Disabled`(FTP 및 FTPS 사용 안 함) 및 `FtpsOnly`(FTPS만)입니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

`-FtpsState` 매개 변수와 함께 [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) 명령을 실행합니다.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

`--ftps-state`에 가능한 값은 `AllAllowed`(FTP 및 FTPS 사용), `Disabled`(FTP 및 FTPS 사용 안 함) 및 `FtpsOnly`(FTPS만)입니다.

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP 배포 문제 해결

- [FTP 배포 문제를 어떻게 해결할 수 있나요?](#how-can-i-troubleshoot-ftp-deployment)
- [FTP를 수행할 수 없으며 코드를 게시할 수 없습니다. 이 문제를 어떻게 해결할 수 있나요?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [수동 모드를 통해 Azure App Service에서 FTP에 연결하려면 어떻게 해야 하나요?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP 배포 문제를 어떻게 해결할 수 있나요?

FTP 배포 문제를 해결하는 첫 번째 단계는 런타임 애플리케이션 문제에서 배포 문제를 격리하는 것입니다.

배포 문제가 발생하면 일반적으로 앱에 파일이 배포되지 않거나 잘못된 파일이 배포됩니다. FTP 배포를 조사하거나 대체 배포 경로(예: 소스 제어)를 선택하여 문제를 해결할 수 있습니다.

런타임 애플리케이션 문제가 발생하면 일반적으로 앱에 올바른 파일 집합이 배포되기는 하지만 앱이 올바르지 않게 동작합니다. 런타임의 코드 동작에 중점을 두고 구체적인 실패 경로를 조사하여 문제를 해결할 수 있습니다.

배포 또는 런타임 문제를 확인하려면 [배포 문제 및 런타임 문제](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)를 참조하세요.

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>FTP를 수행할 수 없으며 코드를 게시할 수 없습니다. 이 문제는 어떻게 해결할 수 있나요?
올바른 [호스트 이름](#get-ftps-endpoint)과 [자격 증명](#get-deployment-credentials)을 입력했는지 확인합니다. 또한 컴퓨터의 다음 FTP 포트가 방화벽에 의해 차단되지 않는지 확인합니다.

- FTP 제어 연결 포트: 21, 990
- FTP 데이터 연결 포트: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>수동 모드를 통해 Azure App Service에서 FTP에 연결하려면 어떻게 해야 하나요?
Azure App Service는 활성 및 수동 모드를 통한 연결을 모두 지원합니다. 배포 컴퓨터는 일반적으로 방화벽 뒤에 있으므로(운영 체제에서 또는 가정용/회사 네트워크의 일부로) 수동 모드가 선호됩니다. [WinSCP 설명서의 예제](https://winscp.net/docs/ui_login_connection)를 참조하세요. 

## <a name="more-resources"></a>추가 리소스

* [Azure App Service에 로컬 Git 배포](deploy-local-git.md)
* [Azure App Service 배포 자격 증명](deploy-configure-credentials.md)
* [샘플: FTP(Azure CLI)를 사용하여 웹앱을 만들고 파일을 배포합니다](./scripts/cli-deploy-ftp.md).
* [샘플: FTP(PowerShell)를 사용하여 웹앱에 파일을 업로드합니다](./scripts/powershell-deploy-ftp.md).
