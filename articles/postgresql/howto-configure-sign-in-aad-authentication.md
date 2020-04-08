---
title: Azure 활성 디렉터리 사용 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: PostgreSQL - 단일 서버에 대한 Azure 데이터베이스를 사용하여 인증을 위해 Azure Active Directory(AAD)를 설정하는 방법에 대해 알아봅니다.
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5588503825281f407ddbbc2c1c57cd94a9c7ee6
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804710"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>PostgreSQL을 사용하여 인증하기 위해 Azure Active Directory사용

이 문서에서는 PostgreSQL에 대한 Azure 데이터베이스로 Azure Active Directory 액세스를 구성하는 방법과 Azure AD 토큰을 사용하여 연결하는 방법을 설명합니다.

> [!IMPORTANT]
> PostgreSQL용 Azure 데이터베이스에 대한 Azure AD 인증은 현재 공개 미리 보기상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD 관리자 사용자 설정

Azure AD 관리자 사용자만 Azure AD 기반 인증을 위해 사용자를 만들/활성화할 수 있습니다. 사용자 권한이 증가했기 때문에 일반 데이터베이스 작업에는 Azure AD 관리자를 사용하지 않는 것이 좋습니다(예: CREATEDB).

Azure AD 관리자(사용자 또는 그룹을 사용할 수 있음)를 설정하려면 다음 단계를 따르십시오.

1. Azure 포털에서 Azure AD에 대해 사용할 Azure 데이터베이스의 인스턴스를 PostgreSQL에 대해 선택합니다.
2. 설정에서 활성 디렉터리 관리자를 선택합니다.

![Azure 광고 관리자 설정][2]

3. 고객 테넌트에서 유효한 Azure AD 사용자를 Azure AD 관리자로 선택합니다.

> [!IMPORTANT]
> 관리자를 설정할 때 새 사용자가 전체 관리자 권한이 있는 PostgreSQL 서버용 Azure 데이터베이스에 추가됩니다. PostgreSQL용 Azure 데이터베이스의 Azure AD 관리자 `azure_ad_admin`사용자에게 는 역할이 있습니다.

PostgreSQL 서버당 하나의 Azure AD 관리자만 만들 수 있으며 다른 관리자를 선택하면 서버에 대해 구성된 기존 Azure AD 관리자가 덮어씁니다. 여러 관리자를 갖도록 개별 사용자 대신 Azure AD 그룹을 지정할 수 있습니다. 그런 다음 관리 목적으로 그룹 이름으로 로그인합니다.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Azure AD를 사용하여 PostgreSQL용 Azure 데이터베이스에 연결

다음 상위 수준 다이어그램은 PostgreSQL에 대한 Azure 데이터베이스와 함께 Azure AD 인증을 사용하는 워크플로를 요약합니다.

![인증 흐름][1]

Azure AD 통합을 Azure AD 인식되지 않고 PostgreSQL에 연결할 때 사용자 이름과 암호 지정만 지원하는 psql과 같은 일반적인 PostgreSQL 도구에서 작동하도록 Azure AD 통합을 설계했습니다. 위의 그림과 같이 Azure AD 토큰을 암호로 전달합니다.

우리는 현재 다음과 같은 클라이언트를 테스트했습니다 :

- psql 명령줄 (PGPASSWORD 변수를 사용하여 토큰을 전달, 아래 참조)
- Azure 데이터 스튜디오(PostgreSQL 확장 사용)
- 기타 libpq 기반 클라이언트(예: 공통 응용 프로그램 프레임워크 및 ORM)

> [!NOTE]
> azure AD 토큰을 pgAdmin과 함께 사용하는 것은 현재 지원되지 않습니다.

다음은 사용자/응용 프로그램이 아래에 설명된 Azure AD를 사용하여 인증해야 하는 단계입니다.

### <a name="step-1-authenticate-with-azure-ad"></a>1단계: Azure AD로 인증

[Azure CLI가 설치되어](/cli/azure/install-azure-cli)있는지 확인합니다.

Azure CLI 도구를 호출하여 Azure AD로 인증합니다. Azure AD 사용자 ID와 암호를 제공해야 합니다.

```azurecli-interactive
az login
```

이 명령은 Azure AD 인증 페이지에 대한 브라우저 창을 시작합니다.

> [!NOTE]
> Azure 클라우드 셸을 사용하여 이러한 단계를 수행할 수도 있습니다.
> Azure Cloud Shell에서 Azure AD 액세스 토큰을 검색할 때 명시적으로 `az login` 호출하고 다시 로그인해야 합니다(코드가 있는 별도의 창에서). 해당 로그인 후에 `get-access-token` 명령에 예상대로 작동합니다.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2단계: Azure AD 액세스 토큰 검색

Azure CLI 도구를 호출하여 1단계에서 Azure AD 인증 사용자에 대한 액세스 토큰을 획득하여 PostgreSQL용 Azure 데이터베이스에 액세스합니다.

예제(퍼블릭 클라우드용):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

위의 리소스 값은 표시된 대로 정확하게 지정해야 합니다. 다른 클라우드의 경우 다음을 사용하여 리소스 값을 조회할 수 있습니다.

```azurecli-interactive
az cloud show
```

Azure CLI 버전 2.0.71 이상에 대 한 명령을 모든 클라우드에 대 한 다음 보다 편리한 버전에 지정할 수 있습니다.

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

인증에 성공하면 Azure AD는 액세스 토큰을 반환합니다.

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

토큰은 인증된 사용자에 대한 모든 정보를 인코딩하고 PostgreSQL 서비스에 대한 Azure 데이터베이스를 대상으로 하는 기본 64 문자열입니다.

> [!NOTE]
> 액세스 토큰 유효 자격은 5분에서 60분 사이입니다. PostgreSQL에 대한 Azure 데이터베이스에 로그인을 시작하기 직전에 액세스 토큰을 받는 것이 좋습니다.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>3 단계: PostgreSQL로 로그인할 때 토큰을 암호로 사용

연결할 때 액세스 토큰을 PostgreSQL 사용자 암호로 사용해야 합니다.

명령줄 `psql` 클라이언트를 사용하는 경우 액세스 토큰이 직접 `PGPASSWORD` 허용할 수 있는 `psql` 암호 길이를 초과하므로 액세스 토큰은 환경 변수를 통해 전달되어야 합니다.

윈도우 예제:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

리눅스 /맥OS 예 :

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

이제 평소와 같이 PostgreSQL용 Azure 데이터베이스와의 연결을 시작할 수 있습니다.

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

이제 Azure AD 인증을 사용하여 PostgreSQL 서버에 인증됩니다.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 Azure AD 사용자 만들기

PostgreSQL 데이터베이스에 대한 Azure AD 사용자를 Azure 데이터베이스에 추가하려면 연결 후 다음 단계를 수행합니다(연결 방법에 대한 이후 섹션 참조).

1. 먼저 Azure AD 사용자가 `<user>@yourtenant.onmicrosoft.com` Azure AD 테넌트에서 유효한 사용자인지 확인합니다.
2. Azure AD 관리자 사용자로 PostgreSQL 인스턴스에 대한 Azure 데이터베이스에 로그인합니다.
3. PostgreSQL에 대한 Azure 데이터베이스에서 역할을 `<user>@yourtenant.onmicrosoft.com` 만듭니다.
4. 역할의 구성원을 azure_ad_user. `<user>@yourtenant.onmicrosoft.com` Azure AD 사용자에게만 이 방법을 제공해야 합니다.

**예제:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Azure AD를 통해 사용자를 인증한다고 해서 사용자에게 PostgreSQL 데이터베이스용 Azure 데이터베이스 내의 개체에 액세스할 수 있는 권한이 부여되지는 않습니다. 사용자에게 필요한 권한을 수동으로 부여해야 합니다.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>PostgreSQL에 대한 Azure 데이터베이스에서 Azure AD 그룹 만들기

데이터베이스에 액세스할 수 있도록 Azure AD 그룹을 사용하려면 사용자와 동일한 메커니즘을 사용하지만 대신 그룹 이름을 지정합니다.

**예제:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

로그인할 때 그룹의 구성원은 개인 액세스 토큰을 사용하지만 사용자 이름으로 지정된 그룹 이름으로 서명합니다.

## <a name="token-validation"></a>토큰 유효성 검사

PostgreSQL용 Azure 데이터베이스의 Azure AD 인증은 사용자가 PostgreSQL 서버에 있는지 확인하고 토큰의 내용을 확인하여 토큰의 유효성을 검사합니다. 다음 토큰 유효성 검사 단계가 수행됩니다.

- 토큰은 Azure AD에 의해 서명되고 변조되지 않았습니다.
- 서버와 연결된 테넌트에 대해 Azure AD에서 토큰을 발급했습니다.
- 토큰이 만료되지 않았습니다.
- 토큰은 PostgreSQL 리소스에 대한 Azure 데이터베이스용입니다(다른 Azure 리소스가 아님).

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>기존 PostgreSQL 사용자를 Azure AD 기반 인증으로 마이그레이션

기존 사용자에 대해 Azure AD 인증을 사용하도록 설정할 수 있습니다. 고려해야 할 두 가지 경우는 다음과 같습니다.

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>사례 1: PostgreSQL 사용자 이름이 Azure AD 사용자 주 이름과 일치합니다.

드문 경우지만 기존 사용자가 Azure AD 사용자 이름과 이미 일치하는 `azure_ad_user` 경우 Azure AD 인증을 사용하도록 설정하기 위해 해당 사용자에게 역할을 부여할 수 있습니다.

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

이제 이전에 구성된 PostgreSQL 사용자 암호를 사용하는 대신 Azure AD 자격 증명으로 로그인할 수 있습니다.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>사례 2: PostgreSQL 사용자 이름은 Azure AD 사용자 주 이름과 다릅니다.

PostgreSQL 사용자가 Azure AD에 없거나 다른 사용자 이름이 있는 경우 Azure AD 그룹을 사용하여 이 PostgreSQL 사용자로 인증할 수 있습니다. PostgreSQL 사용자와 일치하는 이름으로 Azure AD 그룹을 만든 다음 기존 PostgreSQL 사용자에게 azure_ad_user 역할을 부여하여 PostgreSQL 사용자를 위한 기존 Azure 데이터베이스를 Azure AD로 마이그레이션할 수 있습니다.

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Azure AD에서 그룹 "DBReadUser"를 만들었다고 가정합니다. 이제 해당 그룹에 속한 사용자가 이 사용자로 데이터베이스에 로그인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* PostgreSQL - [단일 서버에 대한 Azure 데이터베이스에서 Azure Active Directory 인증에 대한](concepts-aad-authentication.md) 전반적인 개념 검토

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
