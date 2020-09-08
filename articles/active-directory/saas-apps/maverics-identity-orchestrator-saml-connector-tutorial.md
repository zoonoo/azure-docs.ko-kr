---
title: '자습서: Maverics Identity Orchestrator SAML Connector와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Maverics Identity Orchestrator SAML Connector 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: 116ee7c8db3070a667c21a052bec739fd397a2dd
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88825565"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>자습서: Maverics Identity Orchestrator SAML Connector와 Azure AD Single Sign-On 통합

Strata는 인증 및 액세스 제어를 위해 온-프레미스 애플리케이션을 Azure AD(Azure Active Directory)와 통합하는 간단한 방법을 제공합니다.

이 문서에서는 다음과 같은 작업을 수행하도록 Maverics Identity Orchestrator를 구성하는 방법을 안내합니다.
* 레거시 온-프레미스 애플리케이션에 로그인한 동안 사용자를 온-프레미스 ID 시스템에서 Azure AD로 점진적으로 마이그레이션합니다.
* 레거시 웹 액세스 관리 제품(예: CA SiteMinder 또는 Oracle Access Manager)의 로그인 요청을 Azure AD로 라우팅합니다.
* 사용자를 Azure AD에 대해 인증한 후 HTTP 헤더 또는 독점적 세션 쿠키를 사용하여 보호되는 온-프레미스 애플리케이션에 사용자를 인증합니다.

Strata는 온-프레미스 또는 클라우드에 배포할 수 있는 소프트웨어를 제공합니다. 여러 ID 공급자를 검색, 연결 및 오케스트레이션하여 하이브리드 및 다중 클라우드 엔터프라이즈용 분산 ID 관리를 만드는 데 유용합니다.

이 자습서에서는 인증 및 액세스 제어에 Azure AD를 사용하도록 기존 웹 액세스 관리 제품(CA SiteMinder)으로 보호되는 온-프레미스 웹 애플리케이션을 마이그레이션하는 방법을 보여줍니다. 기본 단계는 다음과 같습니다.
1. Maverics Identity Orchestrator를 설치합니다.
2. 엔터프라이즈 애플리케이션을 Azure AD에 등록하고 Maverics Azure AD SAML Zero Code Connector를 SAML 기반 SSO(Single Sign-On)에 사용하도록 구성합니다.
3. Maverics를 SiteMinder 및 LDAP(Lightweight Directory Access Protocol) 사용자 저장소와 통합합니다.
4. Azure Key Vault를 설정하고, Azure Key Vault를 비밀 관리 공급자로 사용하도록 Maverics를 구성합니다.
5. 온-프레미스 Java 웹 애플리케이션에 대한 액세스를 제공하도록 Maverics를 사용하여 사용자 마이그레이션 및 세션 추상화를 수행하는 방법을 보여줍니다.

설치 및 구성과 관련된 추가 지침은 [Strata 웹 사이트](https://strata.io/docs)에서 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
- Maverics Identity Orchestrator SAML Connector SSO가 설정된 구독. Maverics 소프트웨어를 얻으려면 [Strata sales](mailto:sales@strata.io)에 문의하세요.

## <a name="install-maverics-identity-orchestrator"></a>Maverics Identity Orchestrator 설치

Maverics Identity Orchestrator 설치를 시작하려면 [설치 지침](https://strata.io/docs)을 참조하세요.

### <a name="system-requirements"></a>시스템 요구 사항
* 지원되는 운영 체제
  * RHEL 7+
  * CentOS 7+

* 종속성
  * systemd

### <a name="installation"></a>설치

1. Maverics RPM(Redhat Package Manager) 패키지를 가져옵니다. Maverics 소프트웨어를 설치할 시스템에 패키지를 복사합니다.

2. `maverics.rpm` 대신 파일 이름을 대체하여 Mavericks 패키지를 설치합니다.

    `sudo rpm -Uvf maverics.rpm`

3. Maverics 설치가 완료되면 `systemd`에서 서비스로 실행됩니다. 서비스가 실행 중인지 확인하려면 다음 명령을 실행합니다.

    `sudo systemctl status maverics`

기본적으로 Maverics는 */usr/local/bin* 디렉터리에 설치됩니다.

Maverics를 설치하면 기본 *maverics.yaml* 파일이 */etc/maverics* 디렉터리에 만들어집니다. `workflows` 및 `connectors`를 포함하도록 구성을 편집하기 전의 구성 파일은 다음과 같습니다.

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="configuration-options"></a>구성 옵션
### <a name="version"></a>버전
`version` 필드는 사용 중인 구성 파일의 버전을 선언합니다. 버전을 지정하지 않으면 최신 구성 버전이 사용됩니다.

```yaml
version: 0.1
```
### <a name="listenaddress"></a>listenAddress
`listenAddress`는 Orchestrator가 수신 대기할 주소를 선언합니다. 주소의 호스트 섹션이 비어 있으면 Orchestrator는 로컬 시스템의 사용 가능한 모든 유니캐스트 및 애니캐스트 IP 주소를 수신 대기합니다. 주소의 포트 섹션이 비어 있으면 포트 번호가 자동으로 선택됩니다.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls` 필드는 TLS(전송 계층 보안) 개체의 맵을 선언합니다. TLS 개체는 커넥터 및 Orchestrator 서버에서 사용할 수 있습니다. 사용 가능한 모든 TLS 옵션은 `transport`의 패키지 설명서를 참조하세요.

SAML 기반 SSO를 사용하는 경우 Microsoft Azure에는 TLS를 통한 통신이 필요합니다. 인증서 생성에 대한 자세한 내용은 [Let's Encrypt 웹 사이트](https://letsencrypt.org/getting-started/)를 참조하세요.

`maverics` 키는 Orchestrator 서버용으로 예약되어 있습니다. 그 외의 모든 키는 사용 가능하며 지정된 커넥터에 TLS 개체를 삽입하는 데 사용할 수 있습니다.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>포함 파일

별도의 구성 파일에서 `connectors` 및 `workflows`를 정의하고, 다음 예제와 같이 `includeFiles`를 사용하여 *maverics.yaml* 파일에서 참조합니다.

```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

이 자습서에서는 단일 *maverics.yaml* 구성 파일을 사용합니다.

## <a name="use-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault를 비밀 공급자로 사용

### <a name="manage-secrets"></a>비밀 관리

비밀을 로드하기 위해, Maverics는 다양한 비밀 관리 솔루션과 통합할 수 있습니다. 현재 통합에는 파일, Hashicorp Vault 및 Azure Key Vault가 포함됩니다. 비밀 관리 솔루션이 지정되지 않으면 Maverics는 기본적으로 *maverics.yaml* 파일에서 일반 텍스트로 비밀을 로드합니다.

*maverics.yaml* 구성 파일에서 값을 비밀로 선언하려면 비밀을 꺾쇠 괄호로 묶습니다.

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="load-secrets-from-a-file"></a>파일에서 비밀 로드

1. 파일에서 비밀을 로드하려면 다음을 사용하여 */etc/maverics/maverics.env* 파일에 환경 변수 `MAVERICS_SECRET_PROVIDER`를 추가합니다.

   `MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

2. 다음을 실행하여 Mavericks 서비스를 다시 시작합니다.

   `sudo systemctl restart maverics`

*secrets.yaml* 파일 콘텐츠에 `secrets`를 원하는 만큼 채울 수 있습니다.

```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="set-up-an-azure-key-vault"></a>Azure Key Vault 설정

Azure Key Vault는 Azure Portal 또는 Azure CLI를 사용하여 설정할 수 있습니다.

**Azure Portal 사용**
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [새 키 자격 증명 모음을 만듭니다.](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault)
1. [키 자격 증명 모음에 비밀을 추가합니다.](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)
1. [Azure AD에 애플리케이션을 등록합니다](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).
1. [비밀을 사용하도록 애플리케이션에 권한을 부여합니다](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault).

**Azure CLI 사용**

1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 열고 다음 명령을 입력합니다.

    ```shell
    az login
    ```

1. 다음 명령을 실행하여 새 키 자격 증명 모음을 만듭니다.
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

1. 다음 명령을 실행하여 키 자격 증명 모음에 비밀을 추가합니다.
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

1. 다음 명령을 실행하여 Azure AD에 애플리케이션을 등록합니다.
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

1. 다음 명령을 실행하여 비밀을 사용하도록 애플리케이션에 권한을 부여합니다.
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

1. Azure Key Vault에서 비밀을 로드하려면 *azure-credentials.json* 파일에 있는 자격 증명을 사용하여 */etc/maverics/maverics.env* 파일의 환경 변수 `MAVERICS_SECRET_PROVIDER`를 다음과 같은 형식으로 설정합니다.
 
   `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

1. Maverics 서비스를 다시 시작합니다. `sudo systemctl restart maverics`

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Azure AD에서 SAML 기반 SSO를 사용하도록 애플리케이션 구성

1. Azure AD 테넌트에서 **엔터프라이즈 애플리케이션**으로 이동하여 **Maverics Identity Orchestrator SAML Connector**를 검색한 다음, 선택합니다.

1. Maverics Identity Orchestrator SAML Connector **속성** 창에서 **사용자 할당이 필요합니까?** 를 **아니요**로 설정하여 애플리케이션이 새로 마이그레이션된 사용자에 대해 작동하도록 설정합니다.

1. Maverics Identity Orchestrator SAML Connector **개요** 창에서 **Single Sign-On 설정**을 선택하고 **SAML**을 선택합니다.

1. Maverics Identity Orchestrator SAML Connector **SAML 기반 로그온** 창에서 **편집**(연필 아이콘) 단추를 선택하여 **기본 SAML 구성**을 편집합니다.

   !["기본 SAML 구성" 편집 단추 스크린샷](common/edit-urls.png)

1. 다음 형식으로 URL을 입력하여 **엔터티 ID**를 입력합니다. `https://<SUBDOMAIN>.maverics.org` 엔터티 ID는 테넌트의 앱에서 고유해야 합니다. Maverics의 구성에 포함되도록 여기에 입력된 값을 저장합니다.

1. **회신 URL**을 다음과 같은 형식으로 입력합니다. `https://<AZURECOMPANY.COM>/<MY_APP>/` 

1. **로그온 URL**을 다음과 같은 형식으로 입력합니다. `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` 

1. **저장**을 선택합니다.

1. **SAML 서명 인증서** 섹션에서, **복사** 단추를 선택하여 **앱 페더레이션 메타데이터 URL**을 복사한 다음, 컴퓨터에 저장합니다.

    !["SAML 서명 인증서" 복사 단추 스크린샷](common/copy-metadataurl.png)

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector"></a>Maverics Identity Orchestrator Azure AD SAML Connector 구성

Maverics Identity Orchestrator Azure AD Connector는 OpenID Connect 및 SAML Connect를 지원합니다. 커넥터를 구성하려면 다음을 수행합니다. 

1. SAML 기반 SSO를 사용하려면 `authType: saml`을 설정합니다.

1. `samlMetadataURL`의 값을 다음 형식으로 만듭니다. `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`

1. 사용자가 Azure 자격 증명을 사용하여 로그인한 후 Azure가 앱에서 다시 리디렉션될 URL을 정의합니다. 다음 형식을 사용합니다. `samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. 이전에 구성된 EntityID `samlEntityID: https://<SUBDOMAIN>.maverics.org`에서 값을 복사합니다.

1. Azure AD가 SAML 응답을 게시하는 데 사용할 회신 URL의 값을 복사합니다. `samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. [OpenSSL 도구](https://www.openssl.org/source/)를 사용하여 Mavericks Identity Orchestrator 세션 정보를 보호하는 데 사용되는 JWT(JSON Web Token) 서명 키를 생성합니다.

    ```shell 
    openssl rand 64 | base64
    ```
1. 응답을 `jwtSigningKey` config 속성 `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`에 복사합니다.

## <a name="attributes-and-attribute-mapping"></a>특성 및 특성 매핑
특성 매핑은 사용자가 설정된 후 원본 온-프레미스 사용자 디렉터리에서 Azure AD 테넌트로 사용자 특성 매핑을 정의하는 데 사용됩니다.

특성은 클레임의 애플리케이션에 반환되거나, 세션 쿠키에 전달되거나, HTTP 헤더 변수의 애플리케이션에 전달될 수 있는 사용자 데이터를 결정합니다.

## <a name="configure-the-maverics-identity-orchestrator-azure-ad-saml-connector-yaml-file"></a>Maverics Identity Orchestrator Azure AD SAML Connector YAML 파일 구성

Maverics Identity Orchestrator Azure AD Connector 구성은 다음과 같습니다.

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-an-azure-ad-tenant"></a>사용자를 Azure AD 테넌트로 마이그레이션

이 구성에 따라 CA SiteMinder, Oracle Access Manager, IBM Tivoli 등의 웹 액세스 관리 제품의 사용자를 점진적으로 마이그레이션할 수 있습니다. LDAP(Lightweight Directory Access Protocol) 디렉터리 또는 SQL 데이터베이스에서 마이그레이션할 수도 있습니다.

### <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>사용자를 만들기 위해 Azure AD에서 애플리케이션 권한 구성

1. Azure AD 테넌트에서 `App registrations`로 이동하여 **Maverics Identity Orchestrator SAML Connector** 애플리케이션을 선택합니다.

1. **Maverics Identity Orchestrator SAML Connector | 인증서 및 비밀** 창에서 `New client secret`을 선택한 다음, 만료 옵션을 선택합니다. **복사** 단추를 선택하고 비밀을 복사하여 컴퓨터에 저장합니다.

1. **Maverics Identity Orchestrator SAML Connector | 사용 권한** 창에서 **권한 추가**를 선택한 다음, **API 사용 권한 요청** 창에서 **Microsoft Graph** 및 **애플리케이션 권한**을 선택합니다. 

1. 다음 화면에서 **User.ReadWrite.All**을 선택하고 **권한 추가**를 선택합니다. 

1. **API 권한** 창으로 돌아가서 **관리자 동의 허용**을 선택합니다.

### <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-file-for-user-migration"></a>사용자 마이그레이션을 위해 Maverics Identity Orchestrator SAML Connector YAML 구성

사용자 마이그레이션 워크플로를 사용하도록 설정하려면 구성 파일에 다음 속성을 추가합니다.
1. **Azure Graph URL**을 다음과 같은 형식으로 입력합니다. `graphURL: https://graph.microsoft.com`
1. **OAuth 토큰 URL**을 다음과 같은 형식으로 입력합니다. `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`
1. 이전에 생성된 클라이언트 암호를 다음과 같은 형식으로 입력합니다. `oauthClientSecret: <CLIENT SECRET>`


최종 Maverics Identity Orchestrator Azure AD Connector 구성 파일은 다음과 같습니다.

```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

### <a name="configure-maverics-zero-code-connector-for-siteminder"></a>SiteMinder에 대한 Maverics Zero Code Connector 구성

SiteMinder 커넥터를 사용하여 사용자를 Azure AD 테넌트로 마이그레이션합니다. 새로 만든 Azure AD ID 및 자격 증명을 사용하여 SiteMinder로 보호되는 레거시 온-프레미스 애플리케이션에 사용자를 로그인합니다.

이 자습서에서는 양식 기반 인증과 `SMSESSION` 쿠키를 사용하여 레거시 애플리케이션을 보호하도록 SiteMinder가 구성되었습니다. HTTP 헤더를 통해 인증 및 세션 정보를 사용하는 앱과 통합하려면 헤더 에뮬레이션 구성을 커넥터에 추가해야 합니다.

이 예제에서는 다음과 같이 `username` 특성을 `SM_USER` HTTP 헤더에 매핑합니다.

```yaml
  headers:
    SM_USER: username
```

`proxyPass`를 요청이 프록시 설정되는 위치로 설정합니다. 일반적으로 이 위치는 보호된 애플리케이션의 호스트입니다.

`loginPage`는 인증을 위해 사용자를 리디렉션할 때 SiteMinder에서 현재 사용하는 로그인 양식의 URL과 일치해야 합니다.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

### <a name="configure-maverics-zero-code-connector-for-ldap"></a>LDAP에 대한 Maverics Zero Code Connector 구성

애플리케이션이 SiteMinder 같은 WAM(웹 액세스 관리) 제품으로 보호되는 경우 사용자 ID 및 특성은 일반적으로 LDAP 디렉터리에 저장됩니다.

이 커넥터 구성은 LDAP 디렉터리에 연결하는 방법을 보여줍니다. 커넥터는 마이그레이션 워크플로 중에 올바른 사용자 프로필 정보를 수집하고 해당 사용자를 Azure AD에서 만들 수 있도록 SiteMinder의 사용자 저장소로 구성됩니다.

* `baseDN`은 LDAP 검색을 수행할 디렉터리의 위치를 지정합니다.

* `url`은 연결할 LDAP 서버의 주소 및 포트입니다.

* `serviceAccountUsername`은 LDAP 서버에 연결하는 데 사용되는 사용자 이름으로, 일반적으로 바인딩 DN으로 표현됩니다(예: `CN=Directory Manager`).

* `serviceAccountPassword`는 LDAP 서버 연결에 사용되는 암호입니다. 이 값은 이전에 구성한 Azure Key Vault 인스턴스에 저장됩니다.  

* `userAttributes`는 쿼리할 사용자 관련 특성 목록을 정의합니다. 이러한 특성은 나중에 해당하는 Azure AD 특성에 매핑됩니다.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

### <a name="configure-the-migration-workflow"></a>마이그레이션 워크플로 구성

마이그레이션 워크플로 구성은 Maverics가 SiteMinder 또는 LDAP에서 Azure AD로 사용자를 마이그레이션하는 방법을 결정합니다.

이 워크플로는 다음과 같은 작업을 수행합니다.
- SiteMinder 커넥터를 사용하여 SiteMinder 로그인을 프록시합니다. 사용자 자격 증명은 SiteMinder 인증을 통해 유효성 검사 후 워크플로의 후속 단계에 전달됩니다.
- SiteMinder 사용자 저장소에서 사용자 프로필 특성을 검색합니다.
- Azure AD 테넌트에 사용자를 만들어 달라고 Microsoft Graph API에 요청합니다.

마이그레이션 워크플로를 구성하려면 다음을 수행합니다.

1. 워크플로의 이름을 지정합니다(예: **SiteMinder에서 Azure AD로 마이그레이션**).
1. 요청에 대한 응답으로 해당 워크플로의 `actions`를 트리거하는 워크플로가 노출되는 HTTP 경로인 `endpoint`를 지정합니다. `endpoint`는 일반적으로 프록시 설정된 앱(예: `/my_app`)에 해당합니다. 이 값은 선행 슬래시와 후행 슬래시를 모두 포함해야 합니다.
1. 워크플로에 적절한 `actions`를 추가합니다.

   a. SiteMinder 커넥터에 대한 `login` 메서드를 정의합니다. 커넥터 값은 커넥터 구성의 이름 값과 일치해야 합니다.

   b. LDAP 커넥터에 대한 `getprofile` 메서드를 정의합니다.

   c.  AzureAD 커넥터에 대한 `createuser` 메서드를 정의합니다.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>마이그레이션 워크플로 확인

1. Maverics 서비스가 아직 실행되고 있지 않은 경우  명령을 실행하여 시작합니다. 

   `sudo systemctl start maverics`

1. 프록시 설정된 로그인 URL `http://host.company.com/my_app`으로 이동합니다.
1. SiteMinder로 보호되는 동안 애플리케이션에 로그인하는 데 사용되는 사용자 자격 증명을 입력합니다.
4. **홈** > **사용자 | 모든 사용자**로 이동하여 Azure AD 테넌트에 사용자가 만들어졌는지 확인합니다.  

### <a name="configure-the-session-abstraction-workflow"></a>세션 추상화 워크플로 구성

세션 추상화 워크플로는 레거시 온-프레미스 웹 애플리케이션에 대한 인증 및 액세스 제어를 Azure AD 테넌트로 이동합니다.

Azure 커넥터는 `login` 메서드를 사용하여 사용자를 로그인 URL로 리디렉션하며, 세션이 없다고 가정합니다.

인증된 후 결과로 생성된 세션 토큰이 Maverics에 전달됩니다. SiteMinder 커넥터의 `emulate` 메서드는 쿠키 기반 세션 또는 헤더 기반 세션을 에뮬레이션한 다음, 애플리케이션에 필요한 추가 특성으로 요청을 데코레이트하는 데 사용됩니다.

1. 워크플로에 이름을 지정합니다(예: **SiteMinder 세션 추상화**).
1. 프록시 설정되는 앱에 해당하는 `endpoint`를 지정합니다. 이 값은 선행 슬래시와 후행 슬래시를 모두 포함해야 합니다(예: `/my_app/`).
1. 워크플로에 적절한 `actions`를 추가합니다.

   a. Azure 커넥터에 대한 `login` 메서드를 정의합니다. `connector` 값은 커넥터 구성의 `name` 값과 일치해야 합니다.

   b. SiteMinder 커넥터에 대한 `emulate` 메서드를 정의합니다.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>세션 추상화 워크플로 확인

1. 프록시 설정된 애플리케이션 URL `https://<AZURECOMPANY.COM>/<MY_APP>`으로 이동합니다. 
    
    프록시 설정된 로그인 페이지로 리디렉션됩니다.

1. Azure AD 사용자 자격 증명을 입력합니다.

   SiteMinder에서 직접 인증한 것처럼 애플리케이션으로 리디렉션됩니다.
