---
title: 층를 사용 하 여 Azure Active Directory B2C를 구성 하는 자습서
titleSuffix: Azure AD B2C
description: 사용자 확인을 위해 whoIam를 사용 하 여 Azure AD B2C 인증을 통합 하는 방법을 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 0fd2312df31e61ae30f4c3fd04dc0991ac0f4675
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376858"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>층를 사용 하 여 온-프레미스 응용 프로그램을 보호 하기 위한 Azure AD B2C 확장을 위한 자습서

이 샘플 자습서에서는 층의 [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/)와 AZURE ACTIVE DIRECTORY (AD) B2C를 통합 하는 방법에 대해 알아봅니다.
Maverics Id Orchestrator는 온-프레미스 응용 프로그램을 보호 하기 위해 Azure AD B2C를 확장 합니다. 모든 id 시스템에 연결 하 여 투명 하 게 사용자와 자격 증명을 마이그레이션하고 정책과 구성을 동기화 하며 인증 및 세션 관리를 추상화 합니다. 층 기업을 사용 하면 응용 프로그램을 다시 작성 하지 않고도 레거시에서 Azure AD B2C로 신속 하 게 전환할 수 있습니다. 이 솔루션에는 다음과 같은 이점이 있습니다.

- **온-프레미스 하이브리드 앱에 대 한 고객 sso (Single Sign-On)** : Azure AD B2C는 Maverics id Orchestrator를 통해 고객 SSO를 지원 합니다. 사용자는 Azure AD B2C 또는 소셜 Id 공급자 (IdP)에서 호스트 되는 계정으로 로그인 합니다. Maverics는 Symantec SiteMinder와 같은 레거시 id 시스템으로 보호 된 앱에 대 한 SSO를 확장 합니다.

- **표준 기반 SSO를 다시 작성 하지 않고 앱으로 확장** : Azure AD B2C을 사용 하 여 사용자 액세스를 관리 하 고 Maverics ID Orchestrator SAML 또는 Oidc 커넥터를 사용 하 여 sso를 사용 하도록 설정 합니다.

- **간편한 구성** : Azure AD B2C는 Maverics ID Orchestrator SAML 또는 oidc 커넥터를 Azure AD B2C에 연결 하는 간단한 단계별 사용자 인터페이스를 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 입니다.

- Maverics Id Orchestrator에서 사용 되는 암호를 저장 하는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 의 인스턴스입니다. LDAP (Lightweight Directory Access Protocol) 디렉터리 또는 데이터베이스와 같은 Azure AD B2C 또는 다른 특성 공급자에 연결 하는 데 사용 됩니다.

- Azure 가상 컴퓨터 또는 사용자가 선택한 온-프레미스 서버에 설치 되 고 실행 되는 [Maverics Id Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) 의 인스턴스입니다. 소프트웨어를 다운로드 하 고 설치 및 구성 설명서에 액세스 하는 방법에 대 한 자세한 내용은 [층](https://www.strata.io/contact/) 에 문의 하세요.

- 레거시 id 시스템에서 Azure AD B2C로 전환할 온-프레미스 응용 프로그램입니다.

## <a name="scenario-description"></a>시나리오 설명

층의 Maverics 통합에는 다음 구성 요소가 포함 되어 있습니다.

- **Azure AD B2C** : 사용자의 자격 증명을 확인 해야 하는 권한 부여 서버입니다. 인증 된 사용자는 Azure AD B2C 디렉터리에 저장 된 로컬 계정을 사용 하 여 온-프레미스 앱에 액세스할 수 있습니다.

- **외부 소셜 또는 엔터프라이즈 IdP** : 모든 openid connect Connect 공급자, Facebook, Google 또는 GitHub 일 수 있습니다. Azure AD B2C에서 [외부 IdPs](https://docs.microsoft.com/azure/active-directory-b2c/technical-overview#external-identity-providers) 를 사용 하는 방법에 대 한 정보를 참조 하세요.  

- **층의 Maverics id** 오 케 스트레이 터: 사용자 로그인을 오케스트레이션 하 고 HTTP 헤더를 통해 id를 앱에 투명 하 게 전달 하는 서비스입니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![이미지 하이브리드 앱에 액세스할 수 있도록 층 Maverics와 Azure AD B2C 통합의 아키텍처를 보여 줍니다.](./media/partner-strata/strata-architecture-diagram.png)

| 단계 | Description |
|:-------|:---------------|
| 1. | 사용자가 온-프레미스 호스팅된 응용 프로그램에 대 한 액세스를 요청 합니다. Maverics Id Orchestrator는 응용 프로그램에 대 한 사용자의 요청을 프록시 합니다.|
| 2. | Orchestrator에서 사용자의 인증 상태를 확인 합니다. 세션 토큰이 수신 되지 않거나 제공 된 세션 토큰이 잘못 된 경우 인증을 위해 Azure AD B2C 사용자에 게 보냅니다.|
| 3. | Azure AD B2C는 구성 된 소셜 IdP 인증 요청을 보냅니다.|
| 4. | IdP은 사용자에 게 자격 증명을 요구 합니다. IdP에 따라 사용자는 MFA (Multi-factor authentication)를 수행 해야 할 수 있습니다.|
| 5. | IdP 인증 응답을 Azure AD B2C으로 다시 보냅니다. 필요에 따라이 단계를 수행 하는 동안 사용자가 Azure AD B2C 디렉터리에 로컬 계정을 만들 수 있습니다.|
| 6. | Azure AD B2C은 Azure AD B2C 테 넌 트에서 Orchestrator 앱 등록 중에 지정 된 끝점으로 사용자 요청을 보냅니다.|
| 7. | Orchestrator는 액세스 정책을 평가 하 고 앱에 전달 된 HTTP 헤더에 포함할 특성 값을 계산 합니다. 이 단계를 수행 하는 동안 Orchestrator는 추가 특성 공급자를 호출 하 여 헤더 값을 올바르게 설정 하는 데 필요한 정보를 검색할 수 있습니다. Orchestrator는 헤더 값을 설정 하 고 요청을 앱으로 보냅니다.|
| 8. | 이제 사용자가 인증 되 고 앱에 액세스할 수 있습니다.|

## <a name="get-maverics-identity-orchestrator"></a>Maverics Id Orchestrator 가져오기


## <a name="configure-your-azure-ad-b2c-tenant"></a>Azure AD B2C 테 넌 트 구성

1. **애플리케이션 등록**

   a. Orchestrator를 Azure AD B2C 테 넌 트의 [응용 프로그램으로 등록](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga) 합니다.
   >[!Note]
   >Orchestrator 인스턴스를 구성 하는 경우 나중에 테 넌 트 이름 및 식별자, 클라이언트 ID, 클라이언트 암호, 구성 된 클레임 및 리디렉션 URI가 필요 합니다.

   b. 응용 프로그램에 Microsoft MS Graph API 권한을 부여 합니다. 응용 프로그램에는 다음 권한이 필요 합니다. `offline_access` , `openid` .

   c. 응용 프로그램에 대 한 리디렉션 URI를 추가 합니다. 이 URI는 오 케 스트레이 터 `oauthRedirectURL` Azure AD B2C 커넥터 구성의 매개 변수와 일치 합니다 (예:) `https://example.com/oidc-endpoint` .

2. **사용자 흐름 만들기** : [서명 및 로그인 사용자 흐름](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)을 만듭니다.

3. **IdP 추가** : 로컬 계정이 나 소셜 또는 enterprise [IdP](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers)를 사용 하 여 사용자를 로그인 하도록 선택 합니다.

4. **사용자 특성 정의** : 등록 중에 수집할 특성을 정의 합니다.

5. **응용 프로그램 클레임 지정** : Orchestrator 인스턴스를 통해 응용 프로그램에 반환할 특성을 지정 합니다. Orchestrator는 Azure AD B2C에서 반환 된 클레임의 특성을 사용 하며 LDAP 디렉터리 및 데이터베이스와 같은 연결 된 다른 id 시스템에서 추가 특성을 검색할 수 있습니다. 이러한 특성은 HTTP 헤더에 설정 되며 업스트림 온-프레미스 응용 프로그램으로 전송 됩니다.

## <a name="configure-maverics-identity-orchestrator"></a>Maverics Id Orchestrator 구성

다음 섹션에서는 Orchestrator 인스턴스를 구성 하는 데 필요한 단계를 안내 합니다. 추가 지원 및 설명서는 [층](https://www.strata.io/contact/)에 문의 하세요.

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics Id Orchestrator 서버 요구 사항

온-프레미스 또는 Azure, AWS 또는 GCP와 같은 공급자의 공용 클라우드 인프라에 있든 관계 없이 모든 서버에서 Orchestrator 인스턴스를 실행할 수 있습니다.

- OS: REHL 7.7 이상, CentOS 7 이상

- 디스크: 10gb (소형)

- 메모리: 16gb

- 포트: 22 (SSH/SCP), 443, 80

- 설치/관리 태스크에 대 한 루트 액세스

- Maverics Id Orchestrator는 아래에서 사용자로 실행 됩니다. `maverics``systemd`

- Azure AD 테 넌 트에 연결할 수 있는 기능을 사용 하 여 Maverics Id Orchestrator를 호스트 하는 서버에서 네트워크를 송신 합니다.

### <a name="install-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator 설치

1. 최신 Maverics RPM 패키지를 받습니다. Maverics를 설치할 시스템에 패키지를 저장 합니다. 원격 호스트에 파일을 복사 하는 경우 [SCP](https://www.ssh.com/ssh/scp/) 는 유용한 도구입니다.

2. Maverics 패키지를 설치 하려면 대신 파일 이름을 바꿔서 다음 명령을 실행 합니다 `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   기본적으로 Maverics는 `/usr/local/bin` 디렉터리에 설치됩니다.

3. 설치가 완료되면 Maverics는 `systemd` 아래에서 서비스로 실행됩니다.  Maverics 서비스가 실행 중인지 확인 하려면 다음 명령을 실행 합니다.

   `sudo service maverics status`

  Orchestrator가 성공적으로 설치 되 면 다음과 유사한 메시지가 표시 됩니다.

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Maverics 서비스가 시작 되지 않으면 다음 명령을 실행 하 여 문제를 조사 합니다.

   `journalctl --unit=maverics.service --reverse`

   가장 최근의 로그 항목이 출력의 시작 부분에 표시 됩니다.

Maverics를 설치한 후에는 기본 `maverics.yaml` 파일이 `/etc/maverics` 디렉터리에 만들어집니다.

응용 프로그램을 보호 하도록 Orchestrator를 구성 합니다. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9)에서 Azure AD B2C와 통합 하 고, 저장 하 고, 암호를 검색 합니다. Orchestrator에서 구성을 읽어올 위치를 정의 합니다.

### <a name="supply-configuration-using-environment-variables"></a>환경 변수를 사용 하 여 구성 제공

환경 변수를 통해 Orchestrator 인스턴스에 구성을 제공 합니다.

`MAVERICS_CONFIG`

이 환경 변수는 사용할 YAML 구성 파일 및 시작 하거나 다시 시작 하는 동안 찾을 수 있는 위치를 Orchestrator 인스턴스에 지시 합니다. 에서 환경 변수를 설정 `/etc/maverics/maverics.env` 합니다.

### <a name="create-the-orchestrators-tls-configuration"></a>Orchestrator의 TLS 구성 만들기

`tls`의 필드는 `maverics.yaml` Orchestrator 인스턴스가 사용 하는 전송 계층 보안 구성을 선언 합니다. 커넥터는 TLS 개체와 Orchestrator 서버를 사용할 수 있습니다.

`maverics` 키는 Orchestrator 서버용으로 예약되어 있습니다. 그 외의 모든 키는 사용 가능하며 지정된 커넥터에 TLS 개체를 삽입하는 데 사용할 수 있습니다.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Azure AD B2C 커넥터 구성

Orchestrator는 커넥터를 사용 하 여 인증 및 특성 공급자와 통합 합니다. 이 경우이 Orchestrator App Gateway는 Azure AD B2C 커넥터를 인증 및 특성 공급자로 사용 합니다. Azure AD B2C는 인증에 소셜 IdP을 사용 하 고, Orchestrator에 대 한 특성 공급자 역할을 하며, HTTP 헤더에 설정 된 클레임의 특성을 전달 합니다.  

이 커넥터의 구성은 Azure AD B2C 테 넌 트에 등록 된 앱에 해당 합니다.

1. 테 넌 트의 앱 구성에서 클라이언트 ID, 비밀 및 리디렉션 URI를 복사 합니다.

2. 커넥터의 이름을로 지정 하 `azureADB2C` 고 커넥터를로 설정 `type` `azure` 합니다. 이 값은 아래의 다른 구성 매개 변수에 사용 되므로 커넥터 이름을 적어 둡니다.

3. 이 통합의 경우를 `authType` 로 설정 해야 합니다 `oidc` .

4. 1 단계에서 복사한 클라이언트 ID를 매개 변수의 값으로 설정 합니다 `oauthClientID` .

5. 1 단계에서 복사한 클라이언트 암호를 매개 변수의 값으로 설정 합니다 `oauthClientSecret` .

6. 1 단계에서 복사한 리디렉션 URI를 매개 변수의 값으로 설정 합니다 `oauthRedirectURL` .

7. Azure AD B2C OIDC 커넥터는 잘 알려진 OIDC 끝점을 사용 하 여 Url 및 서명 키를 비롯 한 메타 데이터를 검색 합니다. 의 값을 `oidcWellKnownURL` 테 넌 트의 끝점으로 설정 합니다.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>인증 공급자로 Azure AD B2C 정의

인증 공급자는 앱 리소스 요청의 일부로 유효한 세션을 표시 하지 않은 사용자에 대해 인증을 수행 하는 방법을 결정 합니다. Azure AD B2C 테 넌 트의 구성은 사용자에 게 자격 증명을 요구 하 고 추가 인증 정책을 적용 하는 방법을 결정 합니다. 예를 들어 인증 프로세스를 완료 하는 데 두 번째 요인이 필요 하며 인증에 성공한 후 Orchestrator 앱 게이트웨이로 반환 되어야 하는 클레임을 결정 합니다.

의 값은 `authProvider` 커넥터의 값과 일치 해야 합니다 `name` .

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Orchestrator 앱 Gateway를 사용 하 여 온-프레미스 앱 보호

Orchestrator의 앱 게이트웨이 구성은 Azure AD B2C 응용 프로그램을 보호 하는 방법과 사용자가 앱에 액세스 하는 방법을 선언 합니다.

1. 앱 게이트웨이의 이름을 만듭니다. 친숙 한 이름 또는 정규화 된 호스트 이름을 앱에 대 한 식별자로 사용할 수 있습니다.

2. `location`를 설정합니다. 응용 프로그램의 루트를 사용 하는 예제는 `/` 응용 프로그램의 모든 URL 경로일 수 있습니다.

3. `upstream`호스트: 포트 규칙:을 사용 하 여에서 보호 된 응용 프로그램을 정의 `https://example.com:8080` 합니다.

4. 오류 및 권한이 없는 페이지의 값을 설정 합니다.

5. 응용 프로그램에 제공 해야 하는 HTTP 헤더 이름 및 특성 값을 정의 하 여 인증을 설정 하 고 앱에 대 한 액세스를 제어 합니다. 헤더 이름은 임의 이며 일반적으로 앱의 구성에 해당 합니다. 특성 값은 해당 값을 제공 하는 커넥터에 의해 namespaced 됩니다. 아래 예제에서 Azure AD B2C 반환 된 값에는 커넥터 이름이 접두사로 붙습니다 `azureADB2C` . 여기서 접미사는 필요한 값을 포함 하는 특성의 이름입니다 (예:) `given_name` .

6. 정책을 평가 하 고 적용 하도록 설정 합니다. `allowUnauthenticated`, 및의 세 가지 작업이 정의 됩니다. `allowAnyAuthenticated` `allowIfAny` 각 작업은에 연결 되며이 `resource` 에 대 한 정책이 평가 됩니다 `resource` .

>[!NOTE]
>및는 모두 `headers` `policies` JavaScript 또는 GoLang service 확장을 사용 하 여 기본 기능을 크게 향상 시키는 임의 논리를 구현 합니다.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault를 비밀 공급자로 사용

Orchestrator가 Azure AD B2C 및 기타 id 시스템에 연결 하는 데 사용 하는 암호를 보호 하는 것이 중요 합니다. Maverics는 기본적으로 암호를 일반 텍스트로 로드 하지만 `maverics.yaml` 이 자습서에서는 Azure Key Vault를 암호 공급자로 사용 합니다.

지침에 따라 Orchestrator 인스턴스에서 암호 공급자로 사용할 [새 Key Vault를 만듭니다](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) . 자격 증명 모음에 비밀을 추가 하 고 지정 된를 `SECRET NAME` 각 비밀에 기록해 둡니다. `AzureADB2CClientSecret`)을 입력합니다.

`maverics.yaml` 구성 파일에서 값을 비밀로 선언하려면 비밀을 꺾쇠 괄호로 묶습니다.

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

꺾쇠 괄호 안에 지정 된 값은 Azure Key Vault의 지정 된에 해당 하는 값과 일치 해야 합니다 `SECRET NAME` .

Azure Key Vault에서 비밀을 로드 하려면 `MAVERICS_SECRET_PROVIDER` `/etc/maverics/maverics.env` 다음 패턴을 사용 하 여 파일의 azure-credentials.js에 있는 자격 증명을 사용 하 여 파일의 환경 변수를 설정 합니다.

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>모든 항목을 함께 넣기

위에서 설명한 구성을 완료할 때 Orchestrator의 구성이 표시 되는 방법은 다음과 같습니다.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>흐름 테스트

1. 온-프레미스 응용 프로그램 url로 이동 `https://example.com/sonar/dashboard` 합니다.

2. Orchestrator는 사용자 흐름에서 구성한 페이지로 리디렉션해야 합니다.

3. 페이지의 목록에서 IdP을 선택 합니다.

4. IdP으로 리디렉션된 후에는 해당 IdP에 필요한 경우 MFA 토큰을 포함 하 여 요청에 따라 자격 증명을 제공 합니다.

5. 성공적으로 인증 되 면 앱 요청을 Orchestrator 리디렉션 URI로 전달 하는 Azure AD B2C으로 리디렉션됩니다.

6. Orchestrator는 정책을 평가 하 고 헤더를 계산 하며 사용자를 업스트림 응용 프로그램으로 보냅니다.  

7. 요청 된 응용 프로그램이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
