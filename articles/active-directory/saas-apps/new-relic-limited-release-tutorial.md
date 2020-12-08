---
title: '자습서: New Relic과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 New Relic 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327069"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>자습서: New Relic과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 New Relic을 통합하는 방법에 대해 알아봅니다. Azure AD와 New Relic을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 New Relic에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 New Relic에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* New Relic SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* New Relic에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* New Relic이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>갤러리에서 New Relic 애플리케이션 추가

New Relic이 Azure AD에 통합되도록 구성하려면 갤러리의 **New Relic (By Organization)** 을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. **Azure Active Directory** 서비스를 선택합니다.
1. **Enterprise 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **Azure AD 갤러리 찾아보기** 페이지의 검색 상자에서 **New Relic (By Organization)** 을 입력합니다.
1. 결과 패널에서 **New Relic (By Organization)** 을 선택한 다음, **만들기** 를 선택합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>New Relic에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 New Relic에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 New Relic의 관련 사용자 간에 연결 관계를 설정해야 합니다.

New Relic에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
   1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
   1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[New Relic SSO 구성](#configure-new-relic-sso)** - New Relic 쪽에서 Single Sign-On 설정을 구성합니다.
   1. **[New Relic 테스트 사용자 만들기](#create-a-new-relic-test-user)** - B.Simon의 Azure AD 사용자와 연결된 해당 사용자를 New Relic에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **New Relic by Organization** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.

1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.

1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 **식별자** 및 **회신 URL** 에 대한 값을 입력합니다.

   * 이러한 값은 New Relic **My Organization** 애플리케이션을 사용하여 검색할 수 있습니다. 이 애플리케이션을 사용하려면 다음 단계를 수행합니다.
      1. [New Relic](https://login.newrelic.com/)에 로그인합니다.
      1. 위쪽 메뉴에서 **Apps(앱)** 를 선택합니다.
      1. **Your apps(앱)** 섹션에서 **My Organization(내 조직)** 을 선택합니다.
      1. **Authentication domains(인증 도메인)** 를 클릭합니다.
      1. Azure AD SSO에서 연결하도록 하려는 인증 도메인을 선택합니다(둘 이상의 인증 도메인이 있는 경우). 대부분의 회사에는 **Default(기본)** 라는 하나의 인증 도메인만 있습니다. 하나의 인증 도메인만 있으면 선택할 필요가 없습니다.
      1. **Authentication(인증)** 섹션의 **Assertion consumer URL(인증 소비자 URL)** 에는 **회신 URL** 에 사용할 값이 포함되어 있습니다.
      1. **Authentication** 섹션의 **Our entity ID(엔터티 ID)** 에는 **식별자** 에 사용할 값이 포함되어 있습니다.

1. **사용자 특성 및 클레임** 섹션에서 **고유한 사용자 ID** 가 New Relic에서 사용되는 이메일 주소가 포함된 필드에 매핑되었는지 확인합니다.

   * **user.userprincipalname** 기본 필드가 New Relic 이메일 주소와 동일한 값이면 사용할 수 있습니다.
   * **user.userprincipalname** 필드가 New Relic 이메일 주소가 아닌 경우 **user.mail** 필드가 더 효율적으로 작동할 수 있습니다.

1. **SAML 서명 인증서** 섹션에서 **앱 페더레이션 메타데이터 URL** 을 복사하고, 나중에 사용할 수 있도록 해당 값을 저장합니다.

1. **New Relic by Organization 설정** 섹션에서 **로그인 URL** 을 복사하고, 나중에 사용할 수 있도록 해당 값을 저장합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal에서 **Azure Active Directory** 서비스를 선택합니다.
1. **사용자** 를 선택합니다.
1. 새 사용자를 추가하려면 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **새 사용자** 페이지에서 다음 단계를 수행합니다.
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `b.simon@contoso.com`입니다. 이는 New Relic 쪽에서 사용할 이메일 주소와 일치해야 합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **암호 표시** 확인란을 선택한 다음, **초기 암호** 필드에 표시된 값을 저장합니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 B.Simon에게 **New Relic by Organization** 애플리케이션에 대한 액세스 권한을 부여하여 해당 사용자가 Azure AD Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **Azure Active Directory** 서비스를 선택합니다.
1. **Enterprise 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **New Relic by Organization** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**(또는 요금제 수준에 따라 **사용자**)을 선택합니다.

   ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹**(또는 **사용자**) 대화 상자의 [사용자] 목록에서 **B.Simon** 을 선택한 다음, 화면 위쪽에서 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-new-relic-sso"></a>New Relic SSO 구성

New Relic에서 다음 단계에 따라 SSO를 구성합니다.

1. [New Relic](https://login.newrelic.com/)에 로그인합니다.

1. 위쪽 메뉴에서 **Apps(앱)** 를 선택합니다.

1. **Your apps(앱)** 섹션에서 **My Organization(내 조직)** 을 선택합니다.

1. **Authentication domains(인증 도메인)** 를 클릭합니다.

1. Azure AD SSO에서 연결하도록 하려는 인증 도메인을 선택합니다(둘 이상의 인증 도메인이 있는 경우). 대부분의 회사에는 **Default(기본)** 라는 하나의 인증 도메인만 있습니다. 하나의 인증 도메인만 있으면 선택할 필요가 없습니다.

1. **Authentication(인증)** 섹션에서 **Configure(구성)** 를 클릭합니다.

   1. **Source of SAML metadata(SAML 메타데이터 원본)** 필드에서 이전에 저장한 Azure AD 쪽 **앱 페더레이션 메타데이터 URL** 필드의 값을 입력합니다.

   1. **SSO target URL(SSO 대상 URL)** 필드에서 이전에 저장한 Azure AD 쪽 **로그인 URL** 필드의 값을 입력합니다.

   1. Azure AD 및 New Relic 쪽 모두에서 설정이 올바른지 확인한 다음, **Save(저장)** 를 클릭합니다. 양쪽 모두가 제대로 구성되지 않으면 사용자가 New Relic에 로그인할 수 없습니다.

### <a name="create-a-new-relic-test-user"></a>New Relic 테스트 사용자 만들기

이 섹션에서는 New Relic에서 B.Simon이라는 사용자를 만듭니다. 다음 단계에 따라 사용자를 만듭니다.

1. [New Relic](https://login.newrelic.com/)에 로그인합니다.

1. 위쪽 메뉴에서 **Apps(앱)** 를 선택합니다.

1. **Your apps(앱)** 섹션에서 **User Management(사용자 관리)** 를 선택합니다.

1. **Add user(사용자 추가)** 단추를 클릭합니다.

   1. **이름** 필드에서 **B.Simon** 을 입력합니다.
   
   1. **Email(이메일)** 필드에서 Azure AD SSO에서 보낼 값을 입력합니다.
   
   1. 사용자에 대한 사용자 **Type(유형)** 및 사용자 **Group(그룹)** 을 선택합니다. 테스트 사용자의 경우 Type에 대해 **Basic User(기본 사용자)** 를 선택하고 Group에 대해 **User(사용자)** 를 선택하는 것이 좋습니다.
   
   1. **Add User(사용자 추가)** 를 클릭하여 해당 사용자를 저장합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 **New Relic by Organization** 타일을 클릭하면 New Relic에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](./tutorial-list.md)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)

- [Azure AD에서 New Relic 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](/cloud-app-security/proxy-intro-aad)
