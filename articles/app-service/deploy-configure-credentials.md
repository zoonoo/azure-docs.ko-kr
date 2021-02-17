---
title: 배포 자격 증명 구성
description: Azure App Service에 있는 배포 자격 증명의 유형과 이를 구성하고 사용하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 2a53ecb1b3411561da50f7dbf3be79f9d70b42bc
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560422"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service의 배포 자격 증명 구성
로컬 컴퓨터에서 앱 배포를 보호 하기 위해 [Azure App Service](./overview.md) 는 [로컬 Git 배포](deploy-local-git.md) 및 [FTP/S 배포](deploy-ftp.md)에 대 한 두 가지 유형의 자격 증명을 지원 합니다. 이러한 자격 증명은 Azure 구독 자격 증명과 동일하지 않습니다.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>사용자 범위 자격 증명 구성

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[Az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) 명령을 실행 합니다. \<username> 및 \<password>를 배포 사용자 이름 및 암호로 바꿉니다. 

- 사용자 이름은 Azure 내에서 고유해야 하고, 로컬 Git 푸시의경우 ' @' 기호를 포함하면 안 됩니다. 
- 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 출력에는 암호가 `null`로 나옵니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell를 사용 하 여 사용자 범위 자격 증명을 구성할 수 없습니다. 다른 방법을 사용 하거나 [응용 프로그램 범위 자격 증명을 사용 하](#appscope)는 것이 좋습니다. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

모든 앱의 [리소스 페이지](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)에서 사용자 범위 자격 증명을 구성할 수 있습니다. 이러한 자격 증명을 구성 하는 앱에 관계 없이 Azure 계정의 모든 구독에 대 한 모든 앱에 적용 됩니다. 

[Azure Portal](https://portal.azure.com)에서 배포 자격 증명 페이지에 액세스 하려면 앱이 하나 이상 있어야 합니다. 사용자 범위 자격 증명을 구성 하려면:

1. 앱의 왼쪽 메뉴에서 **Deployment center**  >  **FTPS 자격 증명** 또는 **로컬 Git/FTPS 자격 증명**>를 선택 합니다.

    ![Azure 앱 Services의 배포 센터에서 FTP 대시보드를 선택 하는 방법을 보여 줍니다.](./media/app-service-deployment-credentials/access-no-git.png)

2. **사용자 범위** 까지 아래로 스크롤하고 사용자 **이름** 및 **암호** 를 구성한 후 **저장** 을 선택 합니다.

배포 자격 증명을 설정하면 앱의 **개요** 페이지에서 *Git* 배포 사용자 이름을 찾을 수 있습니다.

![앱의 개요 페이지에서 Git 배포 사용자 이름을 찾는 방법을 보여 줍니다.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git 배포가 구성된 경우 페이지에 **Git/배포 사용자 이름** 이 표시됩니다. 그렇지 않으면 **FTP/배포 사용자 이름** 이 표시됩니다.

> [!NOTE]
> Azure는 사용자 범위 배포 암호를 표시 하지 않습니다. 암호를 잊은 경우 이 섹션의 단계에 따라 자격 증명을 다시 설정할 수 있습니다.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>FTP/FTPS와 함께 사용자 범위 자격 증명 사용

사용자 범위 자격 증명을 사용 하 여 FTP/FTPS 끝점을 인증 하려면 다음 형식의 사용자 이름이 필요 합니다. `<app-name>\<user-name>`

사용자 범위 자격 증명은 특정 리소스가 아니라 사용자에 게 연결 되기 때문에 로그인 작업을 올바른 앱 끝점으로 보낼 수 있도록 사용자 이름이이 형식 이어야 합니다.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>응용 프로그램 범위 자격 증명 가져오기

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[Az webapp deployment](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) 명령을 사용 하 여 응용 프로그램 범위 자격 증명을 가져옵니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

[로컬 Git 배포](deploy-local-git.md)의 경우, 응용 프로그램 범위 자격 증명이 이미 포함 된 상태에서 [az webapp deployment list-publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) 명령을 사용 하 여 앱에 대 한 Git 원격 URI를 가져올 수도 있습니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) 명령을 사용 하 여 응용 프로그램 범위 자격 증명을 가져옵니다. 예를 들면 다음과 같습니다.

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. 앱의 왼쪽 메뉴에서 **배포 센터**  >  **FTPS 자격 증명** 또는 **로컬 Git/FTPS 자격 증명** 을 선택 합니다.

    ![Azure 앱 Services의 배포 센터에서 FTP 대시보드를 선택 하는 방법을 보여 줍니다.](./media/app-service-deployment-credentials/access-no-git.png)

2. **응용 프로그램 범위** 섹션에서 **복사** 링크를 선택 하 여 사용자 이름 또는 암호를 복사 합니다.

-----

## <a name="reset-application-scope-credentials"></a>응용 프로그램 범위 자격 증명 다시 설정

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[Az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) 명령을 사용 하 여 응용 프로그램 범위 자격 증명을 다시 설정 합니다.

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 명령을 사용 하 여 응용 프로그램 범위 자격 증명을 다시 설정 합니다.

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. 앱의 왼쪽 메뉴에서 **배포 센터**  >  **FTPS 자격 증명** 또는 **로컬 Git/FTPS 자격 증명** 을 선택 합니다.

    ![Azure 앱 Services의 배포 센터에서 FTP 대시보드를 선택 하는 방법을 보여 줍니다.](./media/app-service-deployment-credentials/access-no-git.png)

2. **응용 프로그램 범위** 섹션에서 **다시 설정** 을 선택 합니다.

-----

## <a name="disable-basic-authentication"></a>기본 인증 사용 안 함

일부 조직에서는 보안 요구 사항을 충족 해야 하며 FTP 또는 WebDeploy를 통해 액세스를 사용 하지 않도록 설정 해야 합니다. 이러한 방식으로 조직의 구성원은 Azure Active Directory (Azure AD)에 의해 제어 되는 Api를 통해서만 App Services에 액세스할 수 있습니다.

### <a name="ftp"></a>FTP

사이트에 대 한 FTP 액세스를 사용 하지 않도록 설정 하려면 다음 CLI 명령을 실행 합니다. 자리 표시자를 리소스 그룹 및 사이트 이름으로 바꿉니다. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

FTP 액세스가 차단 되었는지 확인 하려면 FileZilla와 같은 FTP 클라이언트를 사용 하 여 인증을 시도할 수 있습니다. 게시 자격 증명을 검색 하려면 사이트의 개요 블레이드로 이동 하 고 게시 프로필 다운로드를 클릭 합니다. 파일의 FTP 호스트 이름, 사용자 이름 및 암호를 사용 하 여 인증 하 고, 사용자에 게 권한이 없음을 나타내는 401 오류 응답을 받게 됩니다.

### <a name="webdeploy-and-scm"></a>WebDeploy 및 SCM

WebDeploy 포트 및 SCM 사이트에 대 한 기본 인증 액세스를 사용 하지 않도록 설정 하려면 다음 CLI 명령을 실행 합니다. 자리 표시자를 리소스 그룹 및 사이트 이름으로 바꿉니다. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

WebDeploy에서 게시 프로필 자격 증명이 차단 되었는지 확인 하려면 [Visual Studio 2019을 사용 하 여 웹 앱을 게시](/visualstudio/deployment/quickstart-deploy-to-azure)해 보세요.

### <a name="disable-access-to-the-api"></a>API에 대 한 액세스 사용 안 함

이전 섹션의 API는 azure RBAC (역할 기반 액세스 제어)를 지원 합니다. 즉, [사용자 지정 역할을 만들고](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) priveldged 사용자를 역할에 할당 하 여 사이트에서 기본 인증을 사용 하도록 설정할 수 없습니다. 사용자 지정 역할을 구성 하려면 [다음 지침을 따르세요](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

또한 [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) 를 사용 하 여 성공한 인증 요청을 감사 하 고 [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) 를 사용 하 여 구독의 모든 사이트에 대해이 구성을 적용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이러한 자격 증명을 사용하여 [로컬 Git](deploy-local-git.md)에서 또는 [FTP/S](deploy-ftp.md)를 통해 앱을 배포하는 방법을 알아봅니다.
