---
title: 자습서 - .NET에서 Azure 웹앱을 통해 Azure Key Vault 사용
description: 이 자습서에서는 키 자격 증명 모음에서 비밀을 읽도록 ASP.NET Core 애플리케이션에서 Azure 웹앱을 구성합니다.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 6bb1aafd942046faa77072d99af043ebd43b4a8a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589970"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>자습서: 관리 ID를 사용하여 .NET에서 Key Vault를 Azure 웹앱에 연결

[Azure Key Vault](./overview.md)는 보안이 강화된 자격 증명 및 기타 비밀을 저장하는 방법을 제공합니다. 그러나 코드는 Key Vault에 인증하여 이러한 항목을 검색해야 합니다. [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공하여 이 문제를 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 표시할 필요가 없습니다.

이 자습서에서는 관리 ID를 사용하여 Azure 키 자격 증명 모음을 통해 Azure 웹앱을 인증합니다. [.NET용 Azure Key Vault 비밀 클라이언트 라이브러리](/dotnet/api/overview/azure/key-vault) 및 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용합니다. 선택한 개발 언어, Azure PowerShell 및/또는 Azure Portal을 사용하는 경우에도 동일한 기본 원칙이 적용됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

* Azure 구독 [체험 계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK(이상)](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* [Git](https://www.git-scm.com/downloads) 설치
* [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) 키 자격 증명 모음은 [Azure Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md) 또는 [Azure PowerShell](quick-create-powershell.md)을 사용하여 만들 수 있습니다.
* Key Vault [비밀](../secrets/about-secrets.md). 비밀은 [Azure Portal](../secrets/quick-create-portal.md), [PowerShell](../secrets/quick-create-powershell.md) 또는 [Azure CLI](../secrets/quick-create-cli.md)를 사용하여 만들 수 있습니다.

## <a name="create-a-net-core-app"></a>.NET Core 앱 만들기
이 단계에서는 로컬 .NET Core 프로젝트를 설정합니다.

컴퓨터의 터미널 창에서 `akvwebapp`이라는 디렉터리를 만들고, 이를 현재 디렉터리로 만듭니다.

```bash
mkdir akvwebapp
cd akvwebapp
```

[dotnet new web](/dotnet/core/tools/dotnet-new) 명령을 사용하여 .NET Core 앱을 만듭니다.

```bash
dotnet new web
```

애플리케이션을 로컬로 실행하여 Azure에 이를 배포할 때 표시되는 모양을 확인합니다.

```bash
dotnet run
```

웹 브라우저에서 `http://localhost:5000`에 있는 앱으로 이동합니다.

페이지에 표시된 샘플 앱의 "Hello World!" 메시지가 표시됩니다.

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

이 단계에서는 로컬 Git을 사용하여 .NET Core 애플리케이션을 Azure App Service에 배포합니다. 애플리케이션을 만들고 배포하는 방법에 대한 자세한 내용은 [Azure에서 ASP.NET Core 웹앱 만들기](../../app-service/quickstart-dotnetcore.md)를 참조하세요.

### <a name="configure-the-local-git-deployment"></a>로컬 Git 배포 구성

터미널 창에서 **Ctrl+C** 를 선택하여 웹 서버를 닫습니다.  .NET Core 프로젝트에 대한 Git 리포지토리를 초기화합니다.

```bash
git init
git add .
git commit -m "first commit"
```

FTP 및 로컬 Git에서 *배포 사용자* 를 사용하여 Azure 웹앱을 배포할 수 있습니다. 배포 사용자가 구성되면 이를 모든 Azure 배포에 사용할 수 있습니다. 계정 수준 배포 사용자 이름 및 암호는 Azure 구독 자격 증명과 다릅니다. 

배포 사용자를 구성하려면 [az webapp deployment user set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) 명령을 실행합니다. 다음 지침을 준수하는 사용자 이름과 암호를 선택합니다. 

- 사용자 이름은 Azure 내에서 고유해야 합니다. 로컬 Git 푸시의 경우 at 기호(@)는 포함할 수 없습니다. 
- 암호는 8자 이상이어야 하며 문자, 숫자 및 기호의 세 가지 요소 중 두 가지를 포함해야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON 출력에는 암호가 `null`로 나옵니다. `'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. `'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다. 

웹앱을 배포하는 데 사용할 수 있도록 사용자 이름 및 암호를 기록해 둡니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리적 컨테이너입니다. [az group create](/cli/azure/group?#az-group-create) 명령을 사용하여 키 자격 증명 모음과 웹앱을 모두 포함하는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

Azure CLI [az appservice plan create](/cli/azure/appservice/plan) 명령을 사용하여 [App Service 요금제](../../app-service/overview-hosting-plans.md)를 만듭니다. 다음 예제에서는 `FREE` 가격 책정 계층에서 `myAppServicePlan`이라는 App Service 계획을 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service 요금제가 만들어지면 Azure CLI에서 아래와 비슷한 정보를 표시합니다.

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

자세한 내용은 [Azure에서 App Service 계획 관리](../../app-service/app-service-plan-manage.md)를 참조하세요.

### <a name="create-a-web-app"></a>웹앱 만들기

`myAppServicePlan` App Service 계획에서 [Azure 웹앱](../../app-service/overview.md)을 만듭니다. 

> [!Important]
> 키 자격 증명 모음과 마찬가지로 Azure 웹앱에는 고유한 이름이 있어야 합니다. 다음 예제에서 `<your-webapp-name>`을 웹앱의 이름으로 바꿉니다.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

웹앱이 만들어지면 Azure CLI에서 아래와 비슷한 출력을 표시합니다.

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


Git 원격의 URL은 `deploymentLocalGitUrl` 속성에서 `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` 형식으로 표시됩니다. 이 URL을 저장합니다. 나중에 필요합니다.

다음 명령을 사용하여 새 앱으로 이동합니다. `<your-webapp-name>`을 앱 이름으로 바꿉니다.

```bash
https://<your-webapp-name>.azurewebsites.net
```

새 Azure 웹앱에 대한 기본 웹 페이지가 표시됩니다.

### <a name="deploy-your-local-app"></a>로컬 앱 배포

로컬 터미널 창으로 돌아와서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. 다음 명령에서 `<deploymentLocalGitUrl-from-create-step>`을 [웹앱 만들기](#create-a-web-app) 섹션에서 저장한 Git 원격의 URL로 바꿉니다.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

다음 명령을 사용하여 앱을 배포할 Azure 원격에 푸시합니다. Git 자격 증명 관리자에서 자격 증명을 요구하는 메시지가 표시되면 [로컬 Git 배포 구성](#configure-the-local-git-deployment) 섹션에서 만든 자격 증명을 사용합니다.

```bash
git push azure main
```

이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 실행되는 동안 아래와 비슷한 정보가 표시됩니다.
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

웹 브라우저를 사용하여 배포된 애플리케이션으로 이동하거나 해당 애플리케이션을 새로 고칩니다.

```bash
http://<your-webapp-name>.azurewebsites.net
```

이전에 `http://localhost:5000`을 방문했을 때 확인한 "Hello World!" 메시지가 표시됩니다.
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>웹앱을 구성하여 Key Vault에 연결

이 섹션에서는 Key Vault에 대한 웹 액세스를 구성하고, Key Vault에서 비밀을 검색하도록 애플리케이션 코드를 업데이트합니다.

### <a name="create-and-assign-a-managed-identity"></a>관리 ID 만들기 및 할당

이 자습서에서는 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 Key Vault에 인증합니다. 관리 ID는 애플리케이션 자격 증명을 자동으로 관리합니다.

Azure CLI에서 애플리케이션에 대한 ID를 만들려면 [az webapp-identity assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) 명령을 실행합니다.

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

이 명령은 다음 JSON 코드 조각을 반환합니다.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

키 자격 증명 모음에서 **get** 및 **list** 작업을 수행할 수 있는 권한을 웹앱에 부여하려면 `principalId`를 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) 명령에 전달합니다.

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

또한 [Azure Portal](./assign-access-policy-portal.md) 또는 [PowerShell](./assign-access-policy-powershell.md)을 사용하여 액세스 정책을 할당할 수 있습니다.

### <a name="modify-the-app-to-access-your-key-vault"></a>키 자격 증명 모음에 액세스하도록 앱 수정

이 자습서에서는 [Azure Key Vault 비밀 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.secrets-readme)를 데모용으로 사용합니다. 또한 [Azure Key Vault 인증서 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.certificates-readme) 또는 [Azure Key Vault 키 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/security.keyvault.keys-readme)를 사용할 수 있습니다.

#### <a name="install-the-packages"></a>패키지 설치

터미널 창에서 .NET용 Azure Key Vault 비밀 클라이언트 라이브러리 및 Azure ID 클라이언트 라이브러리 패키지를 설치합니다.

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>코드 업데이트

akvwebapp 프로젝트에서 Startup.cs 파일을 찾아서 엽니다. 

다음 줄을 헤더에 추가합니다.

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

다음 줄을 `app.UseEndpoints` 호출 앞에 추가하여 키 자격 증명 모음의 `vaultUri`를 반영하도록 URI를 업데이트합니다. 이 코드는 [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential)을 사용하여 Key Vault에 인증하며, Key Vault는 관리 ID의 토큰을 사용하여 인증합니다. Key Vault에 인증하는 방법에 대한 자세한 내용은 [개발자 가이드](./developers-guide.md#authenticate-to-key-vault-in-code)를 참조하세요. 또한 Key Vault가 제한되는 경우 이 코드는 지수 백오프를 다시 시도에 사용합니다. Key Vault 트랜잭션 제한에 대한 자세한 내용은 [Azure Key Vault 제한 지침](./overview-throttling.md)을 참조하세요.

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

`await context.Response.WriteAsync("Hello World!");` 줄을 다음 줄과 같이 업데이트합니다.

```csharp
await context.Response.WriteAsync(secretValue);
```

다음 단계를 계속하기 전에 변경 내용을 저장해야 합니다.

#### <a name="redeploy-your-web-app"></a>웹앱 다시 배포

이제 코드가 업데이트되었으므로 다음 Git 명령을 사용하여 이를 Azure에 다시 배포할 수 있습니다.

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>완성된 웹앱으로 이동

```bash
http://<your-webapp-name>.azurewebsites.net
```

이제 비밀 값이 이전에 "Hello World!"가 표시된 위치에 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [.NET에서 가상 머신에 배포된 애플리케이션을 통해 Azure Key Vault 사용](./tutorial-net-virtual-machine.md)
- [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보기
- [개발자 가이드](./developers-guide.md) 살펴보기
- [Key vault에 대한 액세스 보안](./secure-your-key-vault.md)
