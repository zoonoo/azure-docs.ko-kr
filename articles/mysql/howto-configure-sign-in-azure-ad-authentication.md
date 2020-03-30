---
title: MySQL에 Azure 활성 디렉터리 - Azure 데이터베이스 사용
description: MySQL용 Azure 데이터베이스를 사용하여 인증을 위해 Azure Active Directory(Azure AD)를 설정하는 방법에 대해 알아봅니다.
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299008"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>MySQL로 인증하기 위해 Azure Active Directory 사용

이 문서에서는 MySQL용 Azure 데이터베이스로 Azure Active Directory 액세스를 구성하는 방법과 Azure AD 토큰을 사용하여 연결하는 방법을 설명합니다.

> [!IMPORTANT]
> MySQL용 Azure 데이터베이스에 대한 Azure AD 인증은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD 관리자 사용자 설정

Azure AD 관리자 사용자만 Azure AD 기반 인증을 위해 사용자를 만들/활성화할 수 있습니다. Azure AD 관리자 사용자를 만들려면 다음 단계를 따르십시오.

1. Azure 포털에서 Azure AD에 대해 사용할 Azure 데이터베이스의 인스턴스를 선택합니다.
2. 설정에서 활성 디렉터리 관리자를 선택합니다.

![Azure 광고 관리자 설정][2]

3. 고객 테넌트에서 유효한 Azure AD 사용자를 Azure AD 관리자로 선택합니다.

> [!IMPORTANT]
> 관리자를 설정할 때 전체 관리자 권한이 있는 MySQL 서버용 Azure 데이터베이스에 새 사용자가 추가됩니다.

MySQL 서버당 하나의 Azure AD 관리자만 만들 수 있으며 다른 관리자를 선택하면 서버에 대해 구성된 기존 Azure AD 관리자를 덮어씁니다.

이후 릴리스에서는 개별 사용자가 여러 관리자를 갖도록 지정하는 대신 Azure AD 그룹을 지정하는 것을 지원하지만 현재는 지원되지 않습니다.

관리자를 구성한 후 이제 로그인할 수 있습니다.

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Azure AD를 사용하여 MySQL용 Azure 데이터베이스에 연결

다음 상위 수준 다이어그램은 MySQL용 Azure 데이터베이스에서 Azure AD 인증을 사용하는 워크플로를 요약합니다.

![인증 흐름][1]

Azure AD 통합은 Azure AD가 인식하지 못하고 MySQL에 연결할 때 사용자 이름과 암호를 지정하는 것만 지원하는 mysql CLI와 같은 일반적인 MySQL 도구와 함께 작동하도록 설계되었습니다. 위의 그림과 같이 Azure AD 토큰을 암호로 전달합니다.

우리는 현재 다음과 같은 클라이언트를 테스트했습니다 :

- 마이SQL워크벤치 
- 마이sql CLI

우리는 또한 가장 일반적인 응용 프로그램 드라이버를 테스트했습니다, 당신은이 페이지의 끝에서 세부 사항을 볼 수 있습니다.

다음은 사용자/응용 프로그램이 아래에 설명된 Azure AD를 사용하여 인증해야 하는 단계입니다.

### <a name="step-1-authenticate-with-azure-ad"></a>1단계: Azure AD로 인증

[Azure CLI가 설치되어](/cli/azure/install-azure-cli)있는지 확인합니다.

Azure CLI 도구를 호출하여 Azure AD로 인증합니다. Azure AD 사용자 ID와 암호를 제공해야 합니다.

```
az login
```

이 명령은 Azure AD 인증 페이지에 대한 브라우저 창을 시작합니다.

> [!NOTE]
> Azure 클라우드 셸을 사용하여 이러한 단계를 수행할 수도 있습니다.
> Azure Cloud Shell에서 Azure AD 액세스 토큰을 검색할 때 명시적으로 `az login` 호출하고 다시 로그인해야 합니다(코드가 있는 별도의 창에서). 해당 로그인 후에 `get-access-token` 명령에 예상대로 작동합니다.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2단계: Azure AD 액세스 토큰 검색

Azure CLI 도구를 호출하여 1단계에서 Azure AD 인증 사용자에 대한 액세스 토큰을 획득하여 MySQL용 Azure 데이터베이스에 액세스합니다.

예제(퍼블릭 클라우드용):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

위의 리소스 값은 표시된 대로 정확하게 지정해야 합니다. 다른 클라우드의 경우 다음을 사용하여 리소스 값을 조회할 수 있습니다.

```shell
az cloud show
```

Azure CLI 버전 2.0.71 이상에 대 한 명령을 모든 클라우드에 대 한 다음 보다 편리한 버전에 지정할 수 있습니다.

```shell
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

토큰은 인증된 사용자에 대한 모든 정보를 인코딩하고 MySQL 서비스에 대한 Azure 데이터베이스를 대상으로 하는 기본 64 문자열입니다.

> [!NOTE]
> 액세스 토큰 유효 자격은 5분에서 60분 사이입니다. MySQL용 Azure 데이터베이스에 로그인을 시작하기 직전에 액세스 토큰을 받는 것이 좋습니다.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>3 단계 : MySQL로 로그인할 때 토큰을 암호로 사용

연결할 때 액세스 토큰을 MySQL 사용자 암호로 사용해야 합니다. MySQLWorkbench와 같은 GUI 클라이언트를 사용하는 경우 위의 방법을 사용하여 토큰을 검색할 수 있습니다. 

CLI를 사용하는 경우 이 단축된 방법을 사용하여 연결할 수 있습니다. 

**예제(Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

"사용-지우기-플러그인" 설정 - 다른 클라이언트와 유사한 구성을 사용하여 토큰이 해시되지 않고 서버로 전송되도록 해야 합니다.

이제 Azure AD 인증을 사용하여 MySQL 서버에 인증됩니다.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에서 Azure AD 사용자 만들기

MySQL 데이터베이스에 대한 Azure AD 사용자를 Azure 데이터베이스에 추가하려면 연결 후 다음 단계를 수행합니다(연결 방법에 대한 이후 섹션 참조).

1. 먼저 Azure AD 사용자가 `<user>@yourtenant.onmicrosoft.com` Azure AD 테넌트에서 유효한 사용자인지 확인합니다.
2. Azure AD 관리자 사용자로 MySQL 인스턴스에 대한 Azure 데이터베이스에 로그인합니다.
3. MySQL용 Azure 데이터베이스에서 사용자를 `<user>@yourtenant.onmicrosoft.com` 만듭니다.

**예제:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

32자를 초과하는 사용자 이름의 경우 연결할 때 별칭을 사용하는 것이 좋습니다. 

예제:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Azure AD를 통해 사용자를 인증한다고 해서 사용자에게 MySQL 데이터베이스용 Azure 데이터베이스 내의 개체에 액세스할 수 있는 권한이 부여되지는 않습니다. 사용자에게 필요한 권한을 수동으로 부여해야 합니다.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에서 Azure AD 그룹 만들기

데이터베이스에 액세스할 수 있도록 Azure AD 그룹을 사용하려면 사용자와 동일한 메커니즘을 사용하지만 대신 그룹 이름을 지정합니다.

**예제:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

로그인할 때 그룹의 구성원은 개인 액세스 토큰을 사용하지만 사용자 이름으로 지정된 그룹 이름으로 서명합니다.

## <a name="token-validation"></a>토큰 유효성 검사

MySQL용 Azure 데이터베이스의 Azure AD 인증은 사용자가 MySQL 서버에 있는지 확인하고 토큰의 내용을 확인하여 토큰의 유효성을 검사합니다. 다음 토큰 유효성 검사 단계가 수행됩니다.

-   토큰은 Azure AD에 의해 서명되고 변조되지 않았습니다.
-   서버와 연결된 테넌트에 대해 Azure AD에서 토큰을 발급했습니다.
-   토큰이 만료되지 않았습니다.
-   토큰은 MySQL 리소스용 Azure 데이터베이스용입니다(다른 Azure 리소스가 아님).

## <a name="compatibility-with-application-drivers"></a>응용 프로그램 드라이버와의 호환성

대부분의 드라이버는 지원되지만 암호를 일반 텍스트로 전송하기 위한 설정을 사용해야 하므로 토큰은 수정없이 전송됩니다.

* C/C++
  * libmysqlclient: 지원
  * mysql 커넥터-c++: 지원
* Java
  * 커넥터/J(mysql-커넥터-java): 지원되는 설정을 `useSSL` 사용해야 합니다.
* Python
  * 커넥터/파이썬: 지원
* Ruby
  * mysql2: 지원
* .NET
  * mysql 커넥터 그물: 지원, mysql_clear_password 대 한 플러그인을 추가 해야 합니다.
  * mysql-net/MySqlConnector: 지원
* Node.js
  * mysqljs: 지원되지 않음 (패치없이 cleartext로 토큰을 보내지 않음)
  * 노드-mysql2: 지원
* Perl
  * DBD::mysql: 지원
  * 인터넷:::MySQL: 지원 되지 않음
* Go
  * 이동 sql-드라이버: 지원, `?tls=true&allowCleartextPasswords=true` 연결 문자열에 추가

## <a name="next-steps"></a>다음 단계

* [MySQL용 Azure 데이터베이스를 사용하여 Azure Active Directory 인증에 대한](concepts-azure-ad-authentication.md) 전반적인 개념 검토

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
