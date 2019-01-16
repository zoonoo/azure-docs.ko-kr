---
title: Single Sign-On - 비 갤러리 애플리케이션 - Azure Active Directory | Microsoft Docs
description: Azure AD(Azure Active Directory)에서 비 갤러리 애플리케이션에 대한 SSO(Single Sign-On 구성)
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 01/08/2019
ms.author: barbkess
ms.reviewer: asmalser,luleon
ms.openlocfilehash: bb2fcbad3d415a8ac7a8e0c3237fcb97042defd7
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121348"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-azure-active-directory"></a>Azure Active Directory에서 비 갤러리 애플리케이션에 대한 Single Sign-On 구성

이 문서에서는 관리자가 Azure Active Directory 앱 갤러리에 없는 애플리케이션에 Single Sign-On을 *코드 작성 없이*구성할 수 있도록 설정하는 기능에 대해 설명합니다. 이 문서 대신 코드를 통해 사용자 지정 앱을 Azure AD와 통합하는 방법에 대한 개발자 참고 자료를 찾는다면 [Azure AD의 인증 시나리오](../develop/authentication-scenarios.md)를 참조하세요.

Azure Active Directory 애플리케이션 갤러리는 [이 문서](what-is-single-sign-on.md)에 설명한 대로 Azure Active Directory와의 Single Sign-On 형식을 지원하는 것으로 알려진 애플리케이션 목록을 제공합니다. (조직의 IT 전문가 또는 시스템 통합자인 경우)연결할 애플리케이션을 찾으면 Azure Portal에 나와 있는 단계별 지침에 따라 Single Sign-On을 사용하도록 설정할 수 있습니다.

사용권 계약에 따라 이러한 기능을 사용할 수도 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요. 

- SAML 2.0 ID 공급자를 지원하는 애플리케이션의 셀프 서비스 통합(SP에서 시작 또는 IdP에서 시작)
- [암호 기반 SSO](what-is-single-sign-on.md#password-based-sso)를 사용하는 HTML 기반 로그인 페이지가 있는 모든 웹 애플리케이션의 셀프 서비스 통합
* 사용자 프로비전에 SCIM 프로토콜을 사용하는 애플리케이션의 셀프 서비스 연결([여기에 설명됨](use-scim-to-provision-users-and-groups.md))
* [Office 365 앱 시작 관리자](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](what-is-single-sign-on.md#linked-sso)에서 애플리케이션에 대한 링크를 추가하는 기능

사용하지만 아직 Azure AD 애플리케이션 갤러리에 등록되지 않은 SaaS 애플리케이션 뿐만 아니라 조직이 클라우드 또는 온-프레미스에서 제어하는 서버에 배포된 타사 웹 애플리케이션을 포함할 수 있습니다.

*앱 통합 템플릿*이라고도 하는 이러한 기능은 SAML, SCIM 또는 폼 기반 인증을 지원하는 앱에 대한 표준 기반 연결점을 제공하며, 다양한 애플리케이션과의 호환성을 위해 유연한 옵션과 설정을 포함하고 있습니다. 

## <a name="adding-an-unlisted-application"></a>목록에 없는 애플리케이션 추가
앱 통합 템플릿을 사용하여 애플리케이션을 연결하려면 Azure Active Directory 관리자 계정을 사용하여 Azure Portal에 로그인합니다. **Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 새 애플리케이션 &gt; 비갤러리 애플리케이션** 섹션으로 이동하여 **추가**, **갤러리에서 애플리케이션 추가**를 차례로 선택합니다.

  ![애플리케이션 추가](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

앱 갤러리에서 원하는 앱을 찾을 수 없는 경우 검색 결과에 표시된 **비갤러리 애플리케이션** 타일을 선택하여 목록에 없는 앱을 추가할 수 있습니다. 애플리케이션에 이름을 입력한 후에 Single Sign On 옵션 및 동작을 구성할 수 있습니다. 

**빠른 팁**:  모범 사례로 애플리케이션 갤러리에 애플리케이션이 이미 존재하는지 확인하려면 검색 기능을 사용합니다. 앱이 검색되었고 설명에 "Single Sign-On"이 포함되어 있는 경우에는 페더레이션된 Single Sign-On이 애플리케이션에 이미 지원됩니다.

  ![검색](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

이런 방식으로 애플리케이션을 추가하면 사전 통합된 애플리케이션에 제공되는 환경과 비슷한 환경을 구성할 수 있습니다. 시작하려면 **Single Sign-On 구성**을 선택하거나 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다. 다음 화면에서 Single Sign-On 구성을 위한 옵션이 표시됩니다. 옵션은 이 문서의 다음 섹션에 설명되어 있습니다.
  
![구성 옵션](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>SAML 기반 Single Sign-On
이 옵션을 선택하여 애플리케이션에 대한 SAML 기반 인증을 구성합니다. 애플리케이션이 SAML 2.0을 지원해야 합니다. 계속하기 전에 애플리케이션의 SAML 기능을 사용하는 방법에 대한 정보를 수집해야 합니다. 다음 섹션을 완료하여 애플리케이션과 Azure AD 간 Single Sign-On을 구성합니다.

### <a name="enter-basic-saml-configuration"></a>기본 SAML 구성 입력

Azure AD를 설정하려면 기본 SAML 구성을 입력합니다. 필드 값을 추출하려면 수동으로 값을 입력하거나 메타데이터 파일을 업로드할 수 있습니다.

  ![Litware 도메인 및 URL](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **로그온 URL(SP에서 시작된 경우에만 해당)** – 사용자가 이 애플리케이션에 로그인하는 위치입니다. 서비스 공급자 시작 Single Sign-On을 수행하도록 애플리케이션이 구성된 경우 사용자가 이 URL로 이동하면 서비스 공급자는 사용자를 인증하고 로그온하기 위해 Azure AD로 필요한 리디렉션을 수행합니다. 이 필드가 채워지면 Azure AD는 이 URL을 사용하여 Office 365 및 Azure AD 액세스 패널에서 애플리케이션을 시작합니다. 이 필드가 생략되면 해당 앱이 Office 365, Azure AD 액세스 패널 또는 Azure AD Single Sign-On URL(대시보드 탭에서 복사 가능)에서 시작할 경우 Azure AD는 ID 공급자에서 시작된 로그인을 대신 수행합니다.
- **식별자** - 구성될 Single Sign-On에 대해 애플리케이션을 고유하게 식별해야 합니다. 애플리케이션에서 보낸 AuthRequest(SAML 요청)에서 발급자 요소로 이 값을 찾을 수 있습니다. 또한 이 값은 애플리케이션에서 제공하는 모든 SAML 메타데이터 내에서 **엔터티 ID** 로 표시됩니다. 엔터티 ID 또는 대상 값에 대한 자세한 내용은 애플리케이션의 SAML 설명서를 확인합니다. 

    다음은 애플리케이션에서 Azure AD에 보낸 SAML 요청에 식별자 또는 발급자가 표시되는 방법의 예제입니다.

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **회신 URL** - 회신 URL은 애플리케이션이 SAML 토큰을 수신해야 하는 위치입니다. 이 URL은 ACS(Assertion Consumer Service) URL이라고도 합니다. SAML 토큰 회신 URL 또는 ACS URL에 대한 자세한 내용은 애플리케이션의 SAML 설명서를 확인합니다. 

    여러 replyURL을 구성하려면 다음 PowerShell 스크립트를 사용할 수 있습니다.

    ```PowerShell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

자세한 내용은 [Azure AD(Azure Active Directory)에서 지원하는 SAML 2.0 인증 요청 및 응답](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)을 참조


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>SAML 토큰에 발급된 클레임 검토 또는 사용자 지정

사용자가 애플리케이션에 인증할 때 Azure AD는 고유하게 식별하는 사용자에 대한 정보(또는 클레임)를 포함하는 앱에 SAML 토큰을 발급합니다. 기본적으로 사용자의 사용자 이름, 전자 메일 주소, 이름 및 성을 포함합니다. 

**특성** 탭에서 애플리케이션의 SAML 토큰에 전송된 클레임을 보거나 편집할 수 있습니다.

  ![특성](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

SAML 토큰에 발급된 클레임을 편집해야 할만한 두 가지 이유는 다음과 같습니다.

- 애플리케이션이 다른 클레임 URI 또는 클레임 값 집합을 요구하도록 작성되었습니다.
- Azure Active Directory에 저장된 사용자 이름(AKA 사용자 계정 이름) 이외의 NameIdentifier 클레임을 요구하는 방식으로 애플리케이션이 배포되었습니다. 

자세한 내용은 [엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발급된 클레임 사용자 지정](./../develop/../develop/active-directory-saml-claims-customization.md)을 참조하세요. 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>인증서 만료 데이터, 상태 및 이메일 알림 검토

갤러리 또는 비갤러리 애플리케이션을 만들 경우 Azure AD에서 생성 날짜로부터 3년의 만료일이 있는 애플리케이션별 인증서를 만듭니다. Azure AD 및 애플리케이션 간 신뢰를 설정하려면 이 인증서가 필요합니다. 인증서 형식에 대한 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다. 

Azure AD에서 Base64 또는 원시 형식의 인증서를 다운로드할 수 있습니다. 또한 애플리케이션 메타데이터 XML 파일을 다운로드하거나 앱의 페더레이션 메타데이터 URL을 사용하여 인증서를 가져올 수 있습니다.

  ![인증서](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

인증서에 다음이 있는지 확인합니다.

- 원하는 만료 날짜입니다. 만료 날짜를 최대 3년으로 구성할 수 있습니다.
- 활성 상태입니다. 상태가 비활성이면 상태를 활성으로 변경합니다. 상태를 변경하려면 **활성**을 확인한 다음, 구성을 저장합니다. 
- 올바른 알림 이메일입니다. 활성 인증서의 만료 날짜가 가까운 경우 Azure AD는 이 필드에서 구성된 이메일 주소에 알림을 전송합니다.  

자세한 내용은 [Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](manage-certificates-for-federated-single-sign-on.md)를 참조합니다.

### <a name="set-up-target-application"></a>대상 애플리케이션 설정

Single Sign-On에 대해 애플리케이션을 구성하려면 애플리케이션의 설명서를 찾습니다. 설명서를 찾으려면 SAML 기반 로그온 구성 페이지의 끝으로 스크롤한 다음, **구성<application name>** 을 클릭합니다. 

필요한 값은 애플리케이션에 따라 달라집니다. 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다. 로그온 및 로그아웃 서비스 URL은 모두 동일한 엔드포인트로 해결하는데, 이는 Azure AD의 인스턴스에 대한 SAML 요청 처리 엔드포인트입니다. SAML 엔터티 ID는 애플리케이션에 발급된 SAML 토큰 내에서 발급자로 표시되는 값입니다.


### <a name="assign-users-and-groups-to-your-saml-application"></a>SAML 애플리케이션에 사용자 및 그룹 할당

애플리케이션이 Azure AD를 SAML 기반 ID 공급자로 사용하도록 구성되면 테스트할 준비가 다 됩니다. 보안 컨트롤인 Azure AD는 Azure AD가 사용자에게 액세스를 허용한 경우가 아니면 해당 사용자가 애플리케이션에 로그인하도록 허용하는 토큰을 발급하지 않습니다. 사용자는 직접 또는 그룹 멤버 자격을 통해 액세스를 부여받을 수 있습니다. 

애플리케이션에 사용자 또는 그룹을 할당하려면 **사용자 할당** 단추를 클릭합니다. 할당하려는 사용자 또는 그룹을 선택한 다음 **할당** 단추를 선택합니다.

  ![사용자 할당](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

사용자 할당은 Azure AD가 해당 사용자에게 토큰을 발급하도록 허용합니다. 또한 이 애플리케이션의 타일이 사용자의 액세스 패널에 표시되도록 합니다. 또한 사용자가 Office 365를 사용하는 경우 애플리케이션 타일은 Office 365 애플리케이션 시작 관리자에 표시됩니다. 

> [!NOTE] 
> 애플리케이션에 **구성** 탭의 **로고 업로드** 단추를 사용하여 애플리케이션에 대한 타일 로고를 업로드할 수 있습니다. 


### <a name="test-the-saml-application"></a>SAML 애플리케이션 테스트

SAML 애플리케이션을 테스트하기 전에 Azure AD로 애플리케이션을 설정하고 애플리케이션에 사용자 또는 그룹을 할당해야 합니다. SAML 애플리케이션을 테스트하려면 [Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그하는 방법](../develop/howto-v1-debug-saml-sso-issues.md)을 참조합니다.

## <a name="password-single-sign-on"></a>암호 SSO(Single sign-on)

이 옵션을 선택하여 HTML 로그인 페이지를 포함하는 웹 애플리케이션에 [암호 기반 Single Sign-On](what-is-single-sign-on.md)을 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에 유용합니다. 

**다음**을 선택한 후에 애플리케이션의 웹 기반 로그인 페이지의 URL을 입력하라는 메시지가 표시됩니다. 사용자 이름 및 암호 입력 필드를 포함하는 페이지여야 합니다. 입력하면 Azure AD는 사용자 이름 및 암호 입력에 대한 로그인 페이지를 구문 분석하는 프로세스를 시작합니다. 프로세스가 실패한 경우 필드를 수동으로 캡처할 수 있도록 브라우저 확장을 설치하는 다른 과정을 안내합니다.(Internet Explorer, Chrome 또는 Firefox 필요)

로그인 페이지가 캡처되면 사용자 및 그룹은 할당될 수 있고 일반 [암호 SSO 앱](what-is-single-sign-on.md)과 마찬가지로 자격 증명 정책을 설정할 수 있습니다 .

> [!NOTE] 
> 애플리케이션에 **구성** 탭의 **로고 업로드** 단추를 사용하여 애플리케이션에 대한 타일 로고를 업로드할 수 있습니다. 
>

## <a name="existing-single-sign-on"></a>기존 Single Sign-On
이 옵션을 선택하여 조직의 Azure AD 액세스 패널 또는 Office 365 포털에 애플리케이션에 대한 링크를 추가합니다. 인증에 대한 Azure AD 대신 현재 Active Directory Federation Services(또는 다른 페더레이션 서비스)를 사용하는 사용자 지정 웹앱에 대한 링크를 추가하는 데 사용할 수 있습니다. 혹은 사용자의 액세스 패널에 표시하려는 특정 SharePoint 페이지 또는 다른 웹 페이지에 딥 링크를 추가할 수 있습니다. 

**다음**을 선택한 후에 링크할 애플리케이션의 URL을 입력하라는 메시지가 표시됩니다. 완료되면 사용자 및 그룹은 애플리케이션에 할당될 수 있으며 이는 애플리케이션이 해당 사용자에 대한 [Office 365 앱 시작 관리자](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](end-user-experiences.md)에 표시되도록 합니다.

> [!NOTE] 
> 애플리케이션에 **구성** 탭의 **로고 업로드** 단추를 사용하여 애플리케이션에 대한 타일 로고를 업로드할 수 있습니다. 
>

## <a name="related-articles"></a>관련 문서
- [사전 통합된 앱에 대해 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법](../develop/active-directory-saml-claims-customization.md)
- [SAML 기반 Single Sign-On 문제 해결](../develop/howto-v1-debug-saml-sso-issues.md)

