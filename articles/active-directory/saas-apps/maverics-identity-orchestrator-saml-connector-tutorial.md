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
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599029"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Maverics Identity Orchestrator SAML Connector와 Azure AD Single Sign-On 통합

Strata의 Maverics Identity Orchestrator는 인증 및 액세스 제어를 위해 온-프레미스 애플리케이션을 Azure AD(Azure Active Directory)와 통합하는 간단한 방법을 제공합니다. Maverics Orchestrator는 현재 헤더, 쿠키 및 기타 독점 인증 방법에 의존하는 앱의 인증 및 권한 부여를 현대화할 수 있습니다. Maverics Orchestrator 인스턴스는 온-프레미스 또는 클라우드에 배포할 수 있습니다. 

이 하이브리드 액세스 자습서에서는 인증 및 액세스 제어에 Azure AD를 사용하도록 기존 웹 액세스 관리 제품으로 보호되는 온-프레미스 웹 애플리케이션을 마이그레이션하는 방법을 보여줍니다. 기본 단계는 다음과 같습니다.

1. Maverics Orchestrator 설정
1. 애플리케이션 프록시 구성
1. Azure AD에서 엔터프라이즈 애플리케이션 등록
1. Azure를 통해 인증하고 애플리케이션에 대한 액세스 권한 부여
1. 원활한 애플리케이션 액세스를 위한 헤더 추가
1. 여러 애플리케이션과 연동

## <a name="prerequisites"></a>사전 요구 사항

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Maverics Identity Orchestrator SAML Connector SSO가 설정된 구독. Maverics 소프트웨어를 받으려면 [Strata sales](mailto:sales@strata.io)에 문의하세요.
* 헤더 기반 인증을 사용하는 하나 이상의 애플리케이션. 이 예제는 `https://app.connectulum.com`에서 호스팅되는 Connectulum이라는 애플리케이션에 대해 작동합니다.
* Maverics Orchestrator를 호스트하는 Linux 머신
  * OS: RHEL 7.7 이상, CentOS 7 이상
  * 디스크: 10GB 이상
  * 메모리: 4GB 이상
  * 포트: 22(SSH/SCP), 443, 7474
  * 설치/관리 작업에 필요한 루트 액세스 권한
  * Maverics Identity Orchestrator를 호스트하는 서버에서 보호된 애플리케이션으로의 네트워크 송신

## <a name="step-1-set-up-the-maverics-orchestrator"></a>1단계: Maverics Orchestrator 설정

### <a name="install-maverics"></a>Maverics 설치

1. 최신 Maverics RPM을 받습니다. Maverics 소프트웨어를 설치할 시스템에 패키지를 복사합니다.

1. `maverics.rpm` 대신 파일 이름을 대체하여 Mavericks 패키지를 설치합니다.

   `sudo rpm -Uvf maverics.rpm`

   Maverics 설치가 완료되면 `systemd`에서 서비스로 실행됩니다. 서비스가 실행 중인지 확인하려면 다음 명령을 실행합니다.

   `sudo systemctl status maverics`

1. Orchestrator를 다시 시작하고 로그를 따르려면 다음 명령을 실행합니다.

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Maverics를 설치하면 기본 `maverics.yaml` 파일이 `/etc/maverics` 디렉터리에 만들어집니다. `appgateways` 및 `connectors`를 포함하도록 구성을 편집하기 전의 구성 파일은 다음과 같습니다.

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>DNS 구성

DNS를 구성하면 Orchestrator 서버의 IP를 기억할 필요가 없으므로 도움이 됩니다.

가상 Orchestrator IP 12.34.56.78을 사용하여 브라우저 머신(노트북)의 호스트 파일을 편집합니다. Linux 기반 운영 체제에서 이 파일은 `/etc/hosts`에 있습니다. Windows에서 이 파일은 `C:\windows\system32\drivers\etc`에 있습니다.

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

DNS가 예상대로 구성되었는지 확인하려면 Orchestrator의 상태 엔드포인트로 요청을 보내면 됩니다. 브라우저에서 http://sonar.maverics.com:7474/status 를 요청합니다.

### <a name="configure-tls"></a>TLS 구성

보안 유지를 위해 보안 채널을 통해 Orchestrator와 통신하는 것이 중요합니다. `tls` 섹션에서 인증서/키 쌍을 추가하여 보안을 강화할 수 있습니다.

Orchestrator 서버에 대한 자체 서명된 인증서와 키를 생성하려면 `/etc/maverics` 디렉터리 내에서 다음 명령을 실행합니다.

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> 프로덕션 환경의 경우 브라우저에서 경고가 발생하지 않도록 알려진 CA에서 서명한 인증서를 사용하는 것이 좋습니다. 신뢰할 수 있는 CA를 찾고 있다면 [Let's Encrypt](https://letsencrypt.org/)는 무료로 사용할 수 있는 훌륭한 옵션입니다.

이제 Orchestrator에 대해 새로 생성한 인증서 및 키를 사용합니다. 이제 구성 파일에는 다음 코드가 포함되어 있습니다.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

TLS가 예상대로 구성되었는지 확인하려면 Maverics 서비스를 다시 시작하고 상태 엔드포인트에 대한 요청을 만듭니다.

## <a name="step-2-proxy-an-application"></a>2단계: 애플리케이션 프록시 구성

다음으로 `appgateways`를 사용하여 Orchestrator에서 기본 프록시를 구성합니다. 이 단계를 통해 Orchestrator와 보호된 애플리케이션이 연결되어 있는지 확인할 수 있습니다.

이제 구성 파일에는 다음 코드가 포함되어 있습니다.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

프록시가 예상대로 작동하는지 확인하려면 Maverics 서비스를 다시 시작하고 Maverics 프록시를 통해 애플리케이션에 대한 요청을 만듭니다. 필요한 경우 특정 애플리케이션 리소스에 요청할 수 있습니다.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>3단계: Azure AD에서 엔터프라이즈 애플리케이션 등록

이제 Azure AD에서 최종 사용자를 인증하는 데 사용할 새 엔터프라이즈 애플리케이션을 만듭니다.

> [!NOTE]
> 조건부 액세스와 같은 Azure AD 기능을 사용하는 경우 온-프레미스 애플리케이션마다 엔터프라이즈 애플리케이션을 만드는 것이 중요합니다. 이렇게 하면 앱별 조건부 액세스, 앱별 위험 평가, 앱별 할당된 권한 등이 허용됩니다. 일반적으로 Azure AD의 엔터프라이즈 애플리케이션은 Maverics의 Azure 커넥터에 매핑됩니다.

Azure AD에서 엔터프라이즈 애플리케이션을 등록하려면 다음을 수행합니다.

1. Azure AD 테넌트에서 **엔터프라이즈 애플리케이션** 으로 이동하여 **새 애플리케이션** 을 선택합니다. Azure AD 갤러리에서 **Maverics Identity Orchestrator SAML Connector** 를 검색하여 선택합니다.

1. Maverics Identity Orchestrator SAML Connector **속성** 창에서 **사용자 할당이 필요합니까?** 를 **아니요** 로 설정하여 애플리케이션이 디렉터리의 모든 사용자에게 작동하도록 설정합니다.

1. Maverics Identity Orchestrator SAML Connector **개요** 창에서 **Single Sign-On 설정** 을 선택하고 **SAML** 을 선택합니다.

1. Maverics Identity Orchestrator SAML Connector **SAML 기반 로그온** 창에서 **편집**(연필 아이콘) 단추를 선택하여 **기본 SAML 구성** 을 편집합니다.

   !["기본 SAML 구성" 편집 단추 스크린샷](common/edit-urls.png)

1. **엔터티 ID** `https://sonar.maverics.com`을 입력합니다. 엔터티 ID는 테넌트의 앱에서 고유해야 하며 임의의 값을 사용할 수 있습니다. 다음 섹션에서 Azure 커넥터에 대한 `samlEntityID` 필드를 정의할 때 이 값을 사용합니다.

1. **회신 URL** `https://sonar.maverics.com/acs`를 입력합니다. 다음 섹션에서 Azure 커넥터에 대한 `samlConsumerServiceURL` 필드를 정의할 때 이 값을 사용합니다.

1. **로그인 URL** `https://sonar.maverics.com/`을 입력합니다. 이 필드는 Maverics에서 사용되지 않지만, Azure AD에서 사용자가 Azure AD 내 앱 포털을 통해 애플리케이션에 액세스할 수 있도록 설정하는 데 필요합니다.

1. **저장** 을 선택합니다.

1. **SAML 서명 인증서** 섹션에서 **복사** 단추를 선택하여 **앱 페더레이션 메타데이터 URL** 값을 복사한 다음, 컴퓨터에 저장합니다.

   !["SAML 서명 인증서" 복사 단추 스크린샷](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>4단계: Azure를 통해 인증하고 애플리케이션에 대한 액세스 권한 부여

다음으로 방금 만든 엔터프라이즈 애플리케이션을 사용하여 Maverics에서 Azure 커넥터를 구성합니다. 이 `connectors` 구성을 `idps` 블록과 쌍으로 사용하면 Orchestrator에서 사용자를 인증할 수 있습니다.

이제 구성 파일에는 다음 코드가 포함되어 있습니다. `METADATA_URL`을 이전 단계의 앱 페더레이션 메타데이터 URL 값으로 바꿔야 합니다.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

인증이 예상대로 작동하는지 확인하려면 Maverics 서비스를 다시 시작하고 Maverics 프록시를 통해 애플리케이션 리소스에 대한 요청을 만듭니다. 리소스에 액세스하려면 인증이 필요하도록 Azure로 리디렉션됩니다.

## <a name="step-5-add-headers-for-seamless-application-access"></a>5단계: 원활한 애플리케이션 액세스를 위한 헤더 추가

아직 헤더를 업스트림 애플리케이션으로 보내지 않습니다. 업스트림 애플리케이션이 사용자를 식별할 수 있도록 Maverics 프록시를 통과하는 `headers`를 요청에 추가하겠습니다.

이제 구성 파일에는 다음 코드가 포함되어 있습니다.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

인증이 예상대로 작동하는지 확인하려면 Maverics 프록시를 통해 애플리케이션 리소스에 대한 요청을 만듭니다. 이제 보호된 애플리케이션이 요청에서 헤더를 받습니다. 

애플리케이션에 다른 헤더가 필요한 경우 헤더 키를 자유롭게 편집할 수 있습니다. Azure AD에서 SAML 흐름의 일부로 다시 들어오는 모든 클레임을 헤더에 사용할 수 있습니다. 예를 들어 또 다른 헤더 `secondary_email: azureSonarApp.email`을 포함할 수 있습니다. 여기서 `azureSonarApp`은 커넥터 이름이고 `email`은 Azure AD에서 반환된 클레임입니다. 

## <a name="step-6-work-with-multiple-applications"></a>6단계: 여러 애플리케이션과 연동

이번에는 서로 다른 호스트에 있는 여러 애플리케이션으로 프록시를 구성하는 데 필요한 작업을 살펴보겠습니다. 이 단계를 수행하기 위해 또 다른 App Gateway, Azure AD의 또 다른 엔터프라이즈 애플리케이션 그리고 또 다른 커넥터를 구성합니다.

이제 구성 파일에는 다음 코드가 포함되어 있습니다.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

이 코드는 App Gateway 정의에 `host` 필드를 추가합니다. `host` 필드를 사용하면 Maverics Orchestrator에서 트래픽을 프록시할 업스트림 호스트를 구분할 수 있습니다.

새로 추가된 App Gateway가 예상대로 작동하는지 확인하려면 `https://connectulum.maverics.com` 에 대한 요청을 만듭니다.

## <a name="advanced-scenarios"></a>고급 시나리오

### <a name="identity-migration"></a>ID 마이그레이션

수명이 종료된 웹 액세스 관리 도구에 불만이 많지만, 대량 암호 재설정 없이 사용자를 마이그레이션할 방법이 없나요? Maverics Orchestrator는 `migrationgateways`를 사용하여 ID 마이그레이션을 지원합니다.

### <a name="web-server-gateways"></a>웹 서버 게이트웨이

Maverics Orchestrator를 통해 네트워크 및 프록시 트래픽을 재작업하고 싶지 않은지요? 문제 없습니다. Maverics Orchestrator를 웹 서버 게이트웨이(모듈)와 페어링하면 프록시 없이 동일한 솔루션을 제공할 수 있습니다.

## <a name="wrap-up"></a>마무리

Maverics Orchestrator를 설치하고, Azure AD에서 엔터프라이즈 애플리케이션을 만들고 구성하고, 인증을 요구하고 정책을 적용하는 한편 보호된 애플리케이션에 프록시하도록 Orchestrator를 구성했습니다. Maverics Orchestrator를 분산 ID 관리 사용 사례에 사용할 수 있는 방법에 대한 자세한 내용은 [Strata에 문의](mailto:sales@strata.io)하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)
