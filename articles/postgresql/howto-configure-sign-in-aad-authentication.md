---
title: Azure Active Directory 사용 - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL - 단일 서버 인증을 위해 AAD(Azure Active Directory)를 설정하는 방법을 알아봅니다.
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: e813459ddf516b170e7f429646dad38452188335
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102381"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>PostgreSQL 인증에 Azure Active Directory 사용

이 문서에서는 Azure Database for PostgreSQL을 사용하여 Azure Active Directory 액세스를 구성하는 방법 및 Azure AD 토큰을 사용하여 연결하는 방법을 단계적으로 알아봅니다.

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD 관리 사용자 설정

Azure AD 관리자 사용자만 Azure AD 기반 인증을 위한 사용자를 만들거나 사용하도록 설정할 수 있습니다. Azure AD 관리자는 높은 사용자 권한(예: CREATEDB)을 가지므로 일반 데이터베이스 작업에는 해당 관리자를 사용하지 않는 것이 좋습니다.

사용자 또는 그룹을 사용하여 Azure AD 관리자를 설정하려면 다음 단계를 수행하세요.

1. Azure Portal에서 Azure AD에 사용하도록 설정하려는 Azure Database for PostgreSQL 인스턴스를 선택합니다.
2. 설정에서 Active Directory 관리자를 선택합니다.

![azure ad 관리자 설정][2]

3. 고객 테넌트에서 유효한 Azure AD 사용자를 Azure AD 관리자로 선택합니다.

> [!IMPORTANT]
> 관리자를 설정하면 Azure Database for PostgreSQL 서버에 전체 관리자 권한이 있는 새 사용자가 추가됩니다. Azure Database for PostgreSQL의 Azure AD 관리 사용자에게 `azure_ad_admin` 역할이 있습니다.

PostgreSQL 서버당 하나의 Azure AD 관리자만 만들 수 있으며 다른 사용자를 선택하면 서버에 구성된 기존 Azure AD 관리자를 덮어씁니다. 개별 사용자 대신 여러 관리자가 포함된 Azure AD 그룹을 지정할 수 있습니다. 그러면 관리 목적으로 그룹 이름으로 로그인됩니다.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Azure AD를 사용하여 Azure Database for PostgreSQL에 연결

다음 개략적인 다이어그램으로 Azure Database for PostgreSQL과 Azure AD 인증을 사용하는 워크플로를 요약할 수 있습니다.

![인증 흐름][1]

Azure AD를 인식하지 못하고 PostgreSQL에 연결할 때 사용자 이름 및 암호 지정만 지원하는 psql과 같은 일반적인 PostgreSQL 도구에서 작동하도록 Azure AD 통합을 디자인했습니다. 위 그림에 표시된 대로 Azure AD 토큰을 암호로 전달합니다.

현재 다음 클라이언트를 테스트했습니다.

- psql 명령줄(PGPASSWORD 변수를 이용하여 토큰 전달, 아래 참조)
- Azure Data Studio(PostgreSQL 확장 사용)
- 기타 libpq 기반 클라이언트(예: 일반적인 애플리케이션 프레임워크 및 ORM)

> [!NOTE]
> pgAdmin과 함께 Azure AD 토큰을 사용하는 기능의 경우 암호에 대한 256자의 하드 코드된 제한이 있기 때문에 현재 지원되지 않습니다.

다음은 사용자/애플리케이션에서 Azure AD로 인증해야 하는 단계입니다.

### <a name="prerequisites"></a>필수 구성 요소

Azure Cloud Shell, Azure VM 또는 로컬 컴퓨터에서 수행할 수 있습니다. [Azure CLI가 설치](/cli/azure/install-azure-cli)되어 있는지 확인합니다.

### <a name="step-1-authenticate-with-azure-ad"></a>1단계: Azure AD를 사용하여 인증

Azure CLI 도구를 사용 하 여 Azure AD를 인증 하는 것으로 시작 합니다. Azure Cloud Shell에는이 단계가 필요 하지 않습니다.

```
az login
```

이 명령은 Azure AD 인증 페이지에 대 한 브라우저 창을 시작 합니다. Azure AD 사용자 ID와 암호를 제공해야 합니다.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2단계: Azure AD 액세스 토큰 검색

Azure Database for PostgreSQL에 액세스하려면 Azure CLI 도구를 호출하여 1단계에서 Azure AD 인증된 사용자의 액세스 토큰을 가져옵니다.

예(퍼블릭 클라우드의 경우):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

위의 리소스 값은 표시된 대로 정확하게 지정해야 합니다. 다른 클라우드의 경우 다음을 사용하여 리소스 값을 조회할 수 있습니다.

```azurecli-interactive
az cloud show
```

Azure CLI 버전 2.0.71 이상은 모든 클라우드에 대해 다음과 같은 더 편리한 버전으로 명령을 지정할 수 있습니다.

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

인증에 성공하면 Azure AD가 액세스 토큰을 반환합니다.

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

토큰은 Azure Database for PostgreSQL 서비스를 대상으로 하고 인증된 사용자에 관한 모든 정보를 인코딩하는 Base 64 문자열입니다.

> [!NOTE]
> 액세스 토큰의 유효 기간은 5분에서 60분 사이입니다. Azure Database for PostgreSQL에 로그인하기 직전에 액세스 토큰을 가져오는 것이 좋습니다.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>3단계: PostgreSQL로 로그인하는 데 암호로 토큰 사용

연결할 때 액세스 토큰을 PostgreSQL 사용자 암호로 사용해야 합니다.

액세스 토큰은 `psql`이 직접 수락할 수 있는 암호 길이를 초과하므로 `psql` 명령줄 클라이언트를 사용하는 경우 `PGPASSWORD` 환경 변수를 통해 액세스 토큰을 전달해야 합니다.

Windows 예제:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Linux/macOS 예제:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

이제 일반적인 방식으로 Azure Database for PostgreSQL과 연결을 시작할 수 있습니다.

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

연결할 때 중요 한 고려 사항:

* `user@tenant.onmicrosoft.com`연결 하려는 Azure AD 사용자 또는 그룹의 이름입니다.
* 항상 Azure AD 사용자/그룹 이름 뒤에 서버 이름을 추가 합니다 (예: `@mydb` ).
* Azure AD 사용자 또는 그룹 이름에 대 한 정확한 방법을 사용 해야 합니다.
* Azure AD 사용자 및 그룹 이름은 대/소문자를 구분 합니다.
* 그룹으로 연결 하는 경우 그룹 이름 (예:)만 사용 합니다. `GroupName@mydb`
* 이름에 공백이 포함 되어 있으면 `\` 각 공간 앞에를 사용 하 여 이스케이프 합니다.

이제 Azure AD 인증을 사용하여 PostgreSQL 서버에 인증됩니다.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 Azure AD 사용자 만들기

Azure Database for PostgreSQL 데이터베이스에 Azure AD 사용자를 추가하려면 연결 후 다음 단계를 수행합니다(연결하는 방법은 다음 섹션 참조).

1. 먼저 Azure AD 사용자 `<user>@yourtenant.onmicrosoft.com`이 Azure AD 테넌트의 유효한 사용자인지 확인합니다.
2. Azure AD 관리 사용자로 Azure Database for PostgreSQL 인스턴스에 로그인합니다.
3. Azure Database for PostgreSQL에 `<user>@yourtenant.onmicrosoft.com` 역할을 만듭니다.
4. `<user>@yourtenant.onmicrosoft.com`을 azure_ad_user 역할의 멤버로 지정합니다. 이 멤버는 Azure AD 사용자에게만 제공되어야 합니다.

**예:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Azure AD를 통해 사용자를 인증해도 Azure Database for PostgreSQL 데이터베이스 내 개체에 액세스할 권한이 사용자에게 부여되지 않습니다. 사용자에게 필요한 권한을 수동으로 부여해야 합니다.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에 Azure AD 그룹 만들기

데이터베이스 액세스에 Azure AD 그룹을 사용하려면 사용자에 사용하는 동일한 메커니즘을 사용하지만 그룹 이름을 지정합니다.

**예제:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

로그인할 때 그룹 멤버는 개인용 액세스 토큰을 사용하지만 사용자 이름으로 지정된 그룹 이름으로 서명합니다.

## <a name="token-validation"></a>토큰 유효성 검사

Azure Database for PostgreSQL의 Azure AD 인증으로 사용자가 PostgreSQL 서버에 존재하는지 확인하고 토큰 콘텐츠의 유효성을 검사하여 토큰의 유효 기간을 확인합니다. 다음 토큰 유효성 검사 단계를 수행합니다.

- 토큰이 Azure AD에 의해 서명되고 변조되지 않았는지 검사
- 서버와 연결된 테넌트에 Azure AD에서 토큰을 발급했는지 검사
- 토큰이 만료되지 않았는지 검사
- 토큰이 다른 Azure 리소스가 아닌 Azure Database for PostgreSQL 리소스 용인지 검사

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>기존 PostgreSQL 사용자를 Azure AD 기반 인증으로 마이그레이션

기존 사용자에 Azure AD 인증을 사용할 수 있습니다. 다음 두 가지 사례를 고려해야 합니다.

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>사례 1: PostgreSQL 사용자 이름이 Azure AD 사용자 계정 이름과 일치함

기존 사용자가 Azure AD 사용자 이름과 이미 일치하는 경우는 거의 없지만 Azure AD 인증에 해당 사용자를 사용할 수 있도록 `azure_ad_user` 역할을 부여할 수 있습니다.

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

이제 이전에 구성된 PostgreSQL 사용자 암호를 사용하는 대신 Azure AD 자격 증명으로 로그인할 수 있습니다.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>사례 2: PostgreSQL 사용자 이름이 Azure AD 사용자 계정 이름과 다름

PostgreSQL 사용자가 Azure AD에 없거나 다른 사용자 이름을 사용하는 경우 Azure AD 그룹을 사용하여 이 PostgreSQL 사용자로 인증할 수 있습니다. PostgreSQL 사용자와 일치하는 이름으로 Azure AD 그룹을 만든 다음, 기존 PostgreSQL 사용자에게 azure_ad_user 역할을 부여하여 기존 Azure Database for PostgreSQL 사용자를 Azure AD로 마이그레이션할 수 있습니다.

```sql
GRANT azure_ad_user TO "DBReadUser";
```

여기서는 Azure AD에서 “DBReadUser” 그룹을 만들었다고 가정합니다. 해당 그룹에 속한 사용자는 이제 이 사용자로 데이터베이스에 로그인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for PostgreSQL - 단일 서버를 사용한 Azure Active Directory 인증](concepts-aad-authentication.md)에 관한 전반적인 개념 검토

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
