---
title: FTP/S를 사용 하 여 콘텐츠 배포
description: FTP 또는 FTPS를 사용하여 Azure App Service에 앱을 배포하는 방법을 알아봅니다. 암호화 되지 않은 FTP를 사용 하지 않도록 설정 하 여 웹 사이트 보안 향상
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045805"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S를 사용하여 앱에 Azure App Service에 배포

이 문서는 FTP 또는 FTPS를 사용하여 웹앱, 모바일 앱 백 엔드 또는 API 앱을 [Azure App Service](./overview.md)에 배포하는 방법을 보여 줍니다.

앱에 대한 FTP/S 엔드포인트는 이미 활성화되어 있습니다. FTP/S 배포를 사용하도록 설정하는 데 필요한 구성은 없습니다.

> [!NOTE]
> 이전 배포 환경인 Azure Portal의 **개발 센터 (클래식)** 페이지는 2021 년 3 월부터 더 이상 사용 되지 않습니다. 이 변경 내용은 앱의 기존 배포 설정에 영향을 주지 않으며, **배포 센터** 페이지에서 계속 해 서 앱 배포를 관리할 수 있습니다.

## <a name="get-deployment-credentials"></a>배포 자격 증명 가져오기

1. [Azure App Service에 대 한 배포 자격 증명 구성의](deploy-configure-credentials.md) 지침에 따라 응용 프로그램 범위 자격 증명을 복사 하거나 사용자 범위 자격 증명을 설정 합니다. 자격 증명 중 하나를 사용 하 여 앱의 FTP/S 끝점에 연결할 수 있습니다.

1. 선택한 자격 증명 범위에 따라 다음과 같은 형식으로 FTP 사용자 이름을 지정 합니다.

    | 응용 프로그램 범위 | 사용자 범위 |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    App Service FTP/S 끝점이 앱 간에 공유 됩니다. 사용자 범위 자격 증명은 특정 리소스에 연결 되지 않기 때문에 위와 같이 사용자 범위 사용자 이름을 앱 이름 앞에 추가 해야 합니다.

## <a name="get-ftps-endpoint"></a>FTP/S 끝점 가져오기
    
# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

배포 자격 증명 (**배포 센터**  >  **FTP 자격 증명**)을 복사한 앱에 대 한 동일한 관리 페이지에서 **FTPS 끝점** 을 복사 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[Az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) 명령을 실행 합니다. 다음 예에서는 [JMES 경로](https://jmespath.org/) 를 사용 하 여 출력에서 FTP/S 끝점을 추출 합니다.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

각 앱에는 두 개의 FTP/S 끝점이 있습니다. 하나는 읽기/쓰기이 고 다른 하나는 읽기 전용 ( `profileName` 포함 `ReadOnly` )이 고 데이터 복구 시나리오를 위한 것입니다. FTP를 사용 하 여 파일을 배포 하려면 읽기/쓰기 끝점의 URL을 복사 합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) 명령을 실행 합니다. 다음 예에서는 XML 출력에서 FTP/S 끝점을 추출 합니다.

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
> [Git 기반 배포](deploy-local-git.md) 및 [Zip 배포](deploy-zip.md)와 달리 FTP 배포는 다음과 같은 빌드 자동화를 지원 하지 않습니다. 
>
> - 종속성 복원(예: NuGet, NPM, PIP 및 Composer Automation)
> - .NET 이진 파일 컴파일
> - web.config 생성([Node.js 예제](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps) 참조)
> 
> 로컬 컴퓨터에서 이러한 필요한 파일을 수동으로 생성한 후 앱과 함께 배포합니다.
>

## <a name="enforce-ftps"></a>FTPS 적용

보안 강화를 위해 TLS/SSL을 통해 FTP를 허용 해야 합니다. FTP 배포를 사용하지 않을 경우 FTP 및 FTPS를 둘 다 사용하지 않도록 설정할 수도 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)의 앱 리소스 페이지에서 왼쪽 탐색 영역에 있는 **구성**  >  **일반 설정** 을 선택 합니다.

2. 암호화 되지 않은 FTP를 사용 하지 않도록 설정 하려면 **ftp 상태** **에서만 FTPS** 를 선택 합니다. FTP와 FTPS를 모두 사용 하지 않도록 설정 하려면 **사용 안 함** 을 선택 합니다. 완료되면 **저장** 을 클릭합니다. **FTPS만** 사용 하는 경우에는 웹 앱의 **tls/SSL 설정** 블레이드로 이동 하 여 tls 1.2 이상을 적용 해야 합니다. TLS 1.0 및 1.1은 **FTPS만** 으로 지원되지 않습니다.

    ![FTP/S 사용 안 함](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

인수를 사용 하 여 [az webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) 명령을 실행 합니다 `--ftps-state` .

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

에 사용할 수 있는 값 `--ftps-state` 은 `AllAllowed` (FTP 및 ftps 사용), `Disabled` (ftp 및 ftps 사용 안 함) 및 `FtpsOnly` (FTPS 전용)입니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

매개 변수를 사용 하 여 [AzWebApp](/powershell/module/az.websites/set-azwebapp) 명령을 실행 합니다 `-FtpsState` .

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

에 사용할 수 있는 값 `--ftps-state` 은 `AllAllowed` (FTP 및 ftps 사용), `Disabled` (ftp 및 ftps 사용 안 함) 및 `FtpsOnly` (FTPS 전용)입니다.

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP 배포 문제 해결

- [FTP 배포 문제를 어떻게 해결할 수 있나요?](#how-can-i-troubleshoot-ftp-deployment)
- [FTP를 수행할 수 없으며 내 코드를 게시할 수 없습니다. 문제를 해결 하려면 어떻게 해야 하나요?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [수동 모드를 통해 Azure App Service에서 FTP에 연결하려면 어떻게 해야 하나요?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP 배포 문제를 어떻게 해결할 수 있나요?

FTP 배포 문제를 해결하는 첫 번째 단계는 런타임 애플리케이션 문제에서 배포 문제를 격리하는 것입니다.

배포 문제가 발생하면 일반적으로 앱에 파일이 배포되지 않거나 잘못된 파일이 배포됩니다. FTP 배포를 조사하거나 대체 배포 경로(예: 소스 제어)를 선택하여 문제를 해결할 수 있습니다.

런타임 애플리케이션 문제가 발생하면 일반적으로 앱에 올바른 파일 집합이 배포되기는 하지만 앱이 올바르지 않게 동작합니다. 런타임의 코드 동작에 중점을 두고 구체적인 실패 경로를 조사하여 문제를 해결할 수 있습니다.

배포 또는 런타임 문제를 확인하려면 [배포 문제 및 런타임 문제](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)를 참조하세요.

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>FTP를 수행할 수 없으며 코드를 게시할 수 없습니다. 이 문제는 어떻게 해결할 수 있나요?
올바른 [호스트 이름](#get-ftps-endpoint) 및 [자격 증명](#get-deployment-credentials)을 입력 했는지 확인 합니다. 또한 컴퓨터의 다음 FTP 포트가 방화벽에 의해 차단되지 않는지 확인합니다.

- FTP 제어 연결 포트: 21, 990
- FTP 데이터 연결 포트: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>수동 모드를 통해 Azure App Service에서 FTP에 연결하려면 어떻게 해야 하나요?
Azure App Service는 활성 및 수동 모드를 통한 연결을 모두 지원합니다. 배포 컴퓨터는 일반적으로 방화벽 뒤에 있으므로(운영 체제에서 또는 가정용/회사 네트워크의 일부로) 수동 모드가 선호됩니다. [WinSCP 설명서의 예제](https://winscp.net/docs/ui_login_connection)를 참조하세요. 

## <a name="more-resources"></a>추가 리소스

* [Azure App Service에 로컬 Git 배포](deploy-local-git.md)
* [Azure App Service 배포 자격 증명](deploy-configure-credentials.md)
* [샘플: FTP (Azure CLI)를 사용 하 여 웹 앱을 만들고 파일을 배포](./scripts/cli-deploy-ftp.md)합니다.
* [샘플: FTP (PowerShell)를 사용 하 여 웹 앱에 파일을 업로드](./scripts/powershell-deploy-ftp.md)합니다.
