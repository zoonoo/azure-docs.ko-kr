---
title: 관리 ID를 사용하여 SQL Database 연결 보호 - Azure App Service | Microsoft Docs
description: 관리 ID를 사용하여 데이터베이스 연결을 보다 안전하게 만드는 방법과 이를 다른 Azure 서비스에 적용하는 방법에 대해 알아봅니다.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481008"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>자습서: 관리 ID를 사용하여 App Service에서 Azure SQL Database 연결 보호

[App Service](overview.md)는 Azure에서 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 또한 [Azure SQL Database](/azure/sql-database/) 및 기타 Azure 서비스에 대한 액세스를 보호하기 위한 턴키 솔루션인 [관리 ID](overview-managed-identity.md)를 앱에 제공합니다. App Service의 관리 ID는 연결 문자열의 자격 증명과 같은 비밀을 앱에서 제거하여 앱의 보안을 보다 강화합니다. 이 자습서에서는 [자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)에서 중단한 위치부터 계속됩니다. 완료되면 샘플 앱은 사용자 이름과 암호 없이도 안전하게 SQL Database에 연결됩니다.

> [!NOTE]
> 이 시나리오는 현재 .NET Framework 4.7.2 이상에서 지원됩니다. [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2)는 이 시나리오를 지원하지만, 아직 App Service의 기본 이미지에 포함되지 않습니다. 
>

다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 관리 ID 사용
> * 관리 ID에 SQL Database 액세스 권한 부여
> * SQL Database에서 Azure AD 인증을 사용하도록 Entity Framework 구성
> * Visual Studio에서 Azure AD 인증을 사용하여 SQL Database에 연결

> [!NOTE]
>Azure AD 인증은 온-프레미스 Active Directory(AD DS)의 [통합 Windows 인증](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))과 _다릅니다_. AD DS 및 Azure AD는 완전히 다른 인증 프로토콜을 사용합니다. 자세한 내용은 [Azure AD Domain Services 설명서](https://docs.microsoft.com/azure/active-directory-domain-services/)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 문서는 [자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)에서 중단한 위치부터 계속됩니다. 아직 하지 않은 경우 먼저 해당 자습서를 따릅니다. 또는 SQL Database를 사용하여 해당 ASP.NET 앱에 맞게 단계를 조정할 수 있습니다.

SQL Database를 백 엔드로 사용하여 앱을 디버깅하려면 [컴퓨터에서 클라이언트 연결을 허용](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer)했는지 확인합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Azure AD 사용자에게 데이터베이스에 대한 액세스 권한 부여

먼저 Azure AD 사용자를 SQL Database 서버의 Active Directory 관리자로 할당하여 SQL Database에 대한 Azure AD 인증을 사용하도록 설정합니다. 이 사용자는 Azure 구독에 가입하는 데 사용한 Microsoft 계정과 다릅니다. 자신이 만들거나, Azure AD로 가져오거나, 동기화하거나, 초대한 사용자여야 합니다. 허용된 Azure AD 사용자에 대한 자세한 내용은 [Azure AD 기능 및 SQL Database의 제한 사항](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)을 참조하세요. 

[`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list)를 사용하여 Azure AD 사용자의 개체 ID를 찾고 *\<user-principal-name>* 을 바꿉니다. 결과는 변수에 저장됩니다.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Azure AD의 모든 사용자 계정 이름 목록을 보려면 `az ad user list --query [].userPrincipalName`을 실행합니다.
>

Cloud Shell에서 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) 명령을 사용하여 이 Azure AD 사용자를 Active Directory 관리자로 추가합니다. 다음 명령에서 *\<server_name>* 을 바꿉니다.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Active Directory 관리자를 추가하는 방법에 대한 자세한 내용은 [Azure SQL Database Server의 Azure Active Directory 관리자 프로비저닝](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)을 참조하세요.

## <a name="set-up-visual-studio"></a>Visual Studio 설정

Visual Studio에서 개발 및 디버깅을 사용하도록 설정하려면 Visual Studio의 메뉴에서 **파일** > **계정 설정**을 선택하여 Azure AD 사용자를 추가하고 **계정 추가**를 클릭합니다.

Azure 서비스 인증의 Azure AD 사용자를 설정하려면 메뉴에서 **도구** > **옵션**을 선택한 후 **Azure 서비스 인증** > **계정 선택**을 선택합니다. 추가한 Azure AD 사용자를 선택하고 **확인**을 클릭합니다.

이제 백 엔드 SQL Database에서 Azure AD 인증을 사용하여 앱을 개발하고 디버그할 준비가 되었습니다.

## <a name="modify-aspnet-project"></a>ASP.NET 프로젝트 수정

Visual Studio에서 패키지 관리자 콘솔을 열고, [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) NuGet 패키지를 추가합니다.

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

*Web.config*에서 파일 위쪽부터 다음과 같은 변경 작업을 수행합니다.

- `<configSections>`에서 다음과 같은 섹션 선언을 추가합니다.

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- 닫는 `</configSections>` 태그 아래에서 `<SqlAuthenticationProviders>`에 다음 XML 코드를 추가합니다.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- `MyDbConnection`이라는 연결 문자열을 찾고 `connectionString` 값을 `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`로 바꿉니다. _\<server-name>_ 및 _\<db-name>_ 을 서버 이름 및 데이터베이스 이름으로 바꿉니다.

`Ctrl+F5`를 입력하여 앱을 다시 실행합니다. 이제 브라우저에서 동일한 CRUD 앱이 Azure AD 인증을 사용하여 Azure SQL Database에 직접 연결합니다. 이 설정을 사용하면 데이터베이스 마이그레이션을 실행할 수 있습니다. 나중에 변경 내용을 App Service에 배포할 때 동일한 설정이 앱의 관리 ID와 함께 작동합니다.

## <a name="use-managed-identity-connectivity"></a>관리 ID 연결 사용

다음으로, 시스템에서 할당한 관리 ID를 사용하여 SQL Database에 연결하도록 App Service 앱을 구성합니다.

### <a name="enable-managed-identity-on-app"></a>앱에서 관리 ID 사용

Azure 앱의 관리 ID를 사용하려면 Cloud Shell에서 [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 명령을 사용합니다. 다음 명령에서 *\<app-name>* 을 바꿉니다.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

다음은 출력의 예입니다.

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Azure AD 그룹에 관리 ID 추가

이 ID에 SQL Database에 대한 액세스 권한을 부여하려면 [Azure AD 그룹](../active-directory/fundamentals/active-directory-manage-groups.md)에 해당 ID를 추가해야 합니다. Cloud Shell에서 다음 스크립트에 표시된 _myAzureSQLDBAccessGroup_이라는 새 그룹에 ID를 추가합니다.

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

각 명령에 대한 전체 JSON 출력을 표시하려는 경우 매개 변수 `--query objectId --output tsv`를 삭제합니다.

### <a name="grant-permissions-to-azure-ad-group"></a>Azure AD 그룹에 권한 부여

Cloud Shell에서 SQLCMD 명령을 사용하여 SQL Database에 로그인합니다. _\<server-name>_ 을 SQL Database 서버 이름으로 바꾸고, _\<db-name>_ 을 앱에서 사용하는 데이터베이스 이름으로 바꾸고, _\<aad-user-name>_ 및 _\<aad-password>_ 를 Azure AD 사용자의 자격 증명으로 바꿉니다.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

원하는 데이터베이스의 SQL 프롬프트에서 다음 명령을 실행하여 Azure AD 그룹을 추가하고 앱에 필요한 권한을 부여합니다. 예를 들면 다음과 같습니다. 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

`EXIT`를 입력하여 Cloud Shell 프롬프트로 돌아갑니다.

### <a name="modify-connection-string"></a>연결 문자열 수정

`Web.config`에서 변경한 내용은 관리 ID와 함께 작동하므로 Visual Studio가 처음으로 앱을 배포할 때 만든 기존 연결 문자열을 앱에서 제거하기만 하면 됩니다. 다음 명령에서 *\<app-name>* 을 앱의 이름으로 바꾸어 사용합니다.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>변경 내용 게시

이제 Azure에 변경 내용을 게시하면 됩니다.

**솔루션 탐색기**에서 **DotNetAppSqlDb** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

![솔루션 탐색기에서 게시](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

게시 페이지에서 **게시**를 클릭합니다. 새 웹 페이지에 할 일 목록이 표시되면 앱이 관리 ID를 사용하여 데이터베이스에 연결합니다.

![Code First 마이그레이션 후 Azure 앱](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

이제 이전처럼 할 일 목록을 편집할 수 있어야 합니다.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 관리 ID 사용
> * 관리 ID에 SQL Database 액세스 권한 부여
> * SQL Database에서 Azure AD 인증을 사용하도록 Entity Framework 구성
> * Visual Studio에서 Azure AD 인증을 사용하여 SQL Database에 연결

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
