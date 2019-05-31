---
title: 애플리케이션을 추가할 때 사용할 애플리케이션 형식을 선택하는 방법 | Microsoft Docs
description: Azure AD와 통합할 수 있는 지원되는 애플리케이션의 형식 및 해당 관련 구성 옵션 이해
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30a4bfa2cf7a39a28db0575a6851f5b7b481524c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388181"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션을 추가할 때 애플리케이션 유형 선택
Azure AD(Azure Active Directory)에 추가할 수 있는 네 가지 유형의 애플리케이션에 대해 알아봅니다. Azure Active Directory에서 애플리케이션을 추가할 때 4개의 애플리케이션 유형 중 하나를 선택하라는 메시지가 표시됩니다. 

## <a name="what-are-the-types-of-applications"></a>애플리케이션의 유형은 무엇인가요?

Azure AD는 **엔터프라이즈 애플리케이션** 아래에 있는 **추가** 기능을 사용하여 추가할 수 있는 네 가지 기본 애플리케이션 형식을 지원합니다. 내용은 다음과 같습니다.

-   **Azure AD 갤러리 애플리케이션** – Single Sign-On에 대해 Azure AD와 사전 통합된 애플리케이션입니다.

-   **애플리케이션 프록시 애플리케이션** – 외부로 보안 Single Sign-On을 제공하려는 온-프레미스 환경에서 실행 중인 애플리케이션입니다.

-   **사용자 지정 개발된 애플리케이션** – 조직에서 Azure AD 애플리케이션 개발 플랫폼에서 개발하고자 하지만 아직 존재하지 않을 수 있는 애플리케이션입니다.

-   **비 갤러리 애플리케이션** – 사용자의 애플리케이션을 가져옵니다. 원하는 모든 웹 링크 또는 사용자 이름 및 암호 필드를 렌더링하는 모든 애플리케이션은 SAML 또는 OpenID Connect 프로토콜을 지원하거나 Single Sign-On에 대해 Azure AD와 통합하려는 SCIM을 지원합니다.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>애플리케이션 유형에서 지원되는 기능 및 특징

다음 기능은 Azure AD에서 이전 4개의 애플리케이션 형식 중 하나에서 지원됩니다.

-   **빠른 시작** – [간단한 배포 단계](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)를 수행하여 신속하게 애플리케이션 시작

-   **일반 속성 관리** – 애플리케이션에 [직접 딥 링크](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) 가져오기, 애플리케이션의 [브랜딩 사용자 지정](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) 또는 모든 사용자에 대한 [애플리케이션을 사용하지 않도록 설정](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

-   **사용자 및 그룹 관리** – 사용자 및 그룹을 애플리케이션에 [할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) 또는 [제거](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) 및 경우에 따라 액세스를 가진 이러한 사용자 및 그룹에 특정 애플리케이션 역할 할당

-   **셀프 서비스 애플리케이션 액세스** - 사용자가 애플리케이션을 직접 추가하거나 [셀프 서비스가 활성화된 그룹에 가입](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)하거나 경우에 따라 비즈니스 승인을 요구하여 애플리케이션 액세스 패널에서 애플리케이션에 대한 [셀프 서비스 애플리케이션 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)를 요청하도록 함

-   **로그인 로그** – [애플리케이션에 대한 모든 로그인](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) 또는 모든 애플리케이션 보기

-   **감사 로그** – [애플리케이션 수정에 대한 자세한 감사 로그](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) 또는 모든 애플리케이션 보기

-   **조건부 및 리스크 기반 액세스** - 사용자가 특정 애플리케이션에 대한 로그인을 시도할 때 적용되는 강력한 [조건 기반 액세스 규칙](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) 설정

-   **사용 권한 보기** – 애플리케이션이 단일 위치의 디렉터리에서 액세스를 갖는 [OAuth2 권한](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) 중 하나 보기

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>특정 애플리케이션 형식에서 지원되는 Single Sign-On 및 프로비전 모드

다음 표는 이전 각 애플리케이션 형식에서 지원하는 서로 다른 Single Sign-On 및 프로비전 모드를 설명합니다. 이 테이블을 사용하여 특정 목표를 지원하기 위해 추가해야 하는 애플리케이션을 이해할 수 있습니다.

  ![앱 형식 테이블](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Single Sign-On 모드를 선택하는 방법

다음은 Azure AD 애플리케이션에 대해 지원되는 **Single Sign-On** 모드입니다.

- **Azure AD Single Sign-On 사용 안 함** – 이 애플리케이션을 Single Sign-On을 사용하여 Azure AD와 통합할 준비가 되지 않았거나 단순히 테스트하는 경우 Azure AD Single Sign-On 사용 안 함 **Single Sign-On 모드** 선택

- **연결된 로그온** - 기존 Single Sign-On 솔루션에 연결된 애플리케이션이 있거나 [애플리케이션 액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 또는 [Office 365 애플리케이션 시작 관리자](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)에서 사용자에게 간단한 링크를 배포하려는 경우 [연결된 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)**Single Sign-On 모드** 선택

- **암호 기반 로그온** - 애플리케이션이 HTML 사용자 이름 및 암호 필드를 렌더링하고 해당 사용자 이름 및 암호가 나중에 애플리케이션에 재생되도록 안전하게 저장하려는 경우 [암호 기반 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)**Single Sign-On 모드** 선택

- **SAML 기반 로그온** - 애플리케이션이 SAML 또는 OpenID Connect 프로토콜을 지원하거나 사용자를 SAML 클레임에서 정의하는 규칙에 따라 특정 애플리케이션 역할에 매핑할 수 있게 되기를 원하는 경우 [SAML 기반 로그온](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) Single Sign-On 모드 선택 *

  >[!NOTE]
  >애플리케이션 프록시가 애플리케이션에 대해 구성된 경우 이 옵션은 사용할 수 없습니다.
  >
  >

- **헤더 기반 로그온** – Single Sign-On을 수행하려는 HTTP 헤더 기반 인증을 지원하는 PingAccess를 사용하는 애플리케이션이 있는 경우 이 [헤더 기반 로그온](application-proxy-configure-single-sign-on-with-ping-access.md) Single Sign-On 모드 선택 

  >[!NOTE]
  >애플리케이션 프록시 및 PingAccess가 애플리케이션에 대해 구성된 경우에만 이 옵션을 사용할 수 있습니다.
  >
  >

- **Windows 통합 인증** - Single Sign-On을 수행하려는 온-프레미스 WIA 애플리케이션을 노출하는 경우 [Windows 통합 인증](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) Single Sign-On 모드 선택 

  >[!NOTE]
  >애플리케이션 프록시가 애플리케이션에 대해 구성된 경우에만 이 옵션을 사용할 수 있습니다.
  >
  >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>사용자 지정 개발된 애플리케이션에 대한 Single Sign-On 모드

사용자 지정 개발된 애플리케이션 환경을 통해 사용자 지정 개발한 애플리케이션은 이전에 나열되지 않은 추가 Single Sign-On 모드도 지원하며, 다음을 포함합니다.

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) 기반 로그온

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 기반 로그온

-   [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) 기반 로그온

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) 기반 로그온

이러한 Single Sign-On 모드를 지원하는 사용자 지정 개발된 애플리케이션을 만드는 방법에 대해 자세히 알아보려면 [Azure Active Directory 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)를 참조하세요.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>애플리케이션의 Single Sign-On 모드를 설정하는 방법

애플리케이션의 **Single Sign-On** 모드를 설정하려면 아래 지침을 따릅니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5. **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

   * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

## <a name="how-to-choose-a-provisioning-mode"></a>프로비전 모드를 선택하는 방법

- **수동 프로비전** - 기존 계정이 있거나 Azure AD 외부에서 이 애플리케이션에 대한 계정을 관리하려는 경우 [수동](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) 프로비전 모드를 선택합니다.

- **자동 프로비전** - 자동 API 기반 프로비전 및/또는 이 애플리케이션에 사용자 계정의 프로비전 해제를 활성화하려면 [자동](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning)**프로비전 모드** 선택 

  >[!NOTE]
  >이 옵션은 [Azure AD 애플리케이션 갤러리](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal)의 **주요** 범주 내의 애플리케이션에만 사용할 수 있습니다.
  >
  >

- **SCIM 기반 자동 프로비전** - 애플리케이션이 Azure AD와 통합된 모든 애플리케이션에 변경 내용을 자동으로 내보내는 사용자 및 그룹에 대한 변경 내용 감지에 SCIM 프로토콜을 지원하는 경우 [SCIM 기반 자동 프로비전](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) 사용 

  >[!NOTE]
  >이 옵션은 특정 프로비전 모드로 나열되어 있지 않지만 Azure AD와 통합된 모든 애플리케이션에 기본적으로 활성화됩니다.
  >
  >

## <a name="how-to-set-an-applications-provisioning-mode"></a>애플리케이션의 프로비전 모드를 설정하는 방법

애플리케이션의 **프로비전** 모드를 설정하려면 아래 지침을 따릅니다.

애플리케이션의 **Single Sign-On** 모드를 설정하려면 아래 지침을 따릅니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5. **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

   * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6. 프로비전을 구성하려는 애플리케이션을 선택합니다.

7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **프로비전**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 애플리케이션 관리](what-is-application-management.md)
