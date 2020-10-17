---
title: 배포 자격 증명 구성
description: Azure App Service에 있는 배포 자격 증명의 유형과 이를 구성하고 사용하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 653110b953b6947254d5063a9e389505d45ea4cb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149023"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service의 배포 자격 증명 구성
[Azure App Service](./overview.md)는 [로컬 Git 배포](deploy-local-git.md) 및 [FTP/S 배포](deploy-ftp.md)를 위해 두 가지 유형의 자격 증명을 지원합니다. 이러한 자격 증명은 Azure 구독 자격 증명과 동일하지 않습니다.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>사용자 수준 자격 증명 구성

모든 앱의 [리소스 페이지](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)에서 사용자 수준 자격 증명을 구성할 수 있습니다. 이러한 자격 증명을 구성하는 앱에 관계 없이 Azure 계정의 모든 앱과 모든 구독에 적용됩니다. 

### <a name="in-the-cloud-shell"></a>Cloud Shell에서

[Cloud Shell](https://shell.azure.com)에서 배포 사용자를 구성하려면 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 명령을 실행합니다. \<username> 및 \<password>를 배포 사용자 이름 및 암호로 바꿉니다. 

- 사용자 이름은 Azure 내에서 고유해야 하고, 로컬 Git 푸시의경우 ' @' 기호를 포함하면 안 됩니다. 
- 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 출력에는 암호가 `null`로 나옵니다. `'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. `'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다. 

### <a name="in-the-portal"></a>포털에서

Azure Portal에서 배포 자격 증명 페이지에 액세스하려면 앱이 하나 이상 있어야 합니다. 사용자 수준 자격 증명을 구성하려면

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **App Services**  >  **\<any_app>**  >  **Deployment center**  >  **FTP**  >  **대시보드**를 선택 합니다.

    ![Azure 앱 Services의 배포 센터에서 FTP 대시보드를 선택 하는 방법을 보여 줍니다.](./media/app-service-deployment-credentials/access-no-git.png)

    또는 Git 배포를 이미 구성한 경우 **App Services** >  **&lt;any_app>**  > **배포 센터** > **FTP/자격 증명**을 선택합니다.

    ![구성 된 Git 배포에 대 한 Azure 앱 Services의 배포 센터에서 FTP 대시보드를 선택 하는 방법을 보여 줍니다.](./media/app-service-deployment-credentials/access-with-git.png)

2. **사용자 자격 증명**을 선택하여 사용자 이름 및 암호를 구성한 다음, **자격 증명 저장**을 선택합니다.

배포 자격 증명을 설정하면 앱의 **개요** 페이지에서 *Git* 배포 사용자 이름을 찾을 수 있습니다.

![앱의 개요 페이지에서 Git 배포 사용자 이름을 찾는 방법을 보여 줍니다.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git 배포가 구성된 경우 페이지에 **Git/배포 사용자 이름**이 표시됩니다. 그렇지 않으면 **FTP/배포 사용자 이름**이 표시됩니다.

> [!NOTE]
> Azure에서는 사용자 수준 배포 암호가 표시되지 않습니다. 암호를 잊은 경우 이 섹션의 단계에 따라 자격 증명을 다시 설정할 수 있습니다.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>FTP/FTPS와 함께 사용자 수준 자격 증명 사용

사용자 수준 자격 증명을 사용하여 FTP/FTPS 엔드포인트를 인증하려면 `<app-name>\<user-name>` 형식의 사용자 이름이 필요합니다.

사용자 수준 자격 증명은 특정 리소스가 아니라 사용자에게 연결되므로 로그인 작업을 올바른 앱 엔드포인트로 보내려면 사용자 이름이 이 형식이어야 합니다.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>앱 수준 자격 증명 정보 가져오기 및 다시 설정
앱 수준 자격 증명을 가져오려면

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **App Services** >  **&lt;any_app>**  > **배포 센터** > **FTP/자격증명**을 선택합니다.

2. **앱 자격 증명**을 선택하고 **복사** 링크를 선택하여 사용자 이름 또는 암호를 복사합니다.

앱 수준 자격 증명을 다시 설정하려면 동일한 대화 상자에서 **자격 증명 다시 설정**을 선택합니다.

## <a name="disable-basic-authentication"></a>기본 인증 사용 안 함

일부 조직에서는 보안 요구 사항을 충족 해야 하며 FTP 또는 WebDeploy를 통해 액세스를 사용 하지 않도록 설정 해야 합니다. 이러한 방식으로 조직의 구성원은 Azure Active Directory (Azure AD)에 의해 제어 되는 Api를 통해서만 App Services에 액세스할 수 있습니다.

### <a name="ftp"></a>FTP

사이트에 대 한 FTP 액세스를 사용 하지 않도록 설정 하려면 다음 CLI 명령을 실행 합니다. 자리 표시자를 리소스 그룹 및 사이트 이름으로 바꿉니다. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

FTP 액세스가 차단 되었는지 확인 하려면 FileZilla와 같은 FTP 클라이언트를 사용 하 여 인증을 시도할 수 있습니다. 게시 자격 증명을 검색 하려면 사이트의 개요 블레이드로 이동 하 고 게시 프로필 다운로드를 클릭 합니다. 파일의 FTP 호스트 이름, 사용자 이름 및 암호를 사용 하 여 인증 하 고, 사용자에 게 권한이 없음을 나타내는 401 오류 응답을 받게 됩니다.

### <a name="webdeploy-and-scm"></a>WebDeploy 및 SCM

WebDeploy 포트 및 SCM 사이트에 대 한 기본 인증 액세스를 사용 하지 않도록 설정 하려면 다음 CLI 명령을 실행 합니다. 자리 표시자를 리소스 그룹 및 사이트 이름으로 바꿉니다. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

WebDeploy에서 게시 프로필 자격 증명이 차단 되었는지 확인 하려면 [Visual Studio 2019을 사용 하 여 웹 앱을 게시](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)해 보세요.

### <a name="disable-access-to-the-api"></a>API에 대 한 액세스 사용 안 함

이전 섹션의 API는 azure RBAC (역할 기반 액세스 제어)를 지원 합니다. 즉, [사용자 지정 역할을 만들고](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) priveldged 사용자를 역할에 할당 하 여 사이트에서 기본 인증을 사용 하도록 설정할 수 없습니다. 사용자 지정 역할을 구성 하려면 [다음 지침을 따르세요](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

또한 [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) 를 사용 하 여 성공한 인증 요청을 감사 하 고 [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) 를 사용 하 여 구독의 모든 사이트에 대해이 구성을 적용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이러한 자격 증명을 사용하여 [로컬 Git](deploy-local-git.md)에서 또는 [FTP/S](deploy-ftp.md)를 통해 앱을 배포하는 방법을 알아봅니다.