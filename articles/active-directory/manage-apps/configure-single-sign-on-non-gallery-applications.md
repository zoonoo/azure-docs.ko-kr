---
title: Single sign-on-비 갤러리 응용 프로그램-Microsoft id 플랫폼 | Microsoft Docs
description: Microsoft id 플랫폼 (Azure AD)의 비 갤러리 응용 프로그램에서 single sign-on (SSO) 구성
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95b7cbcf4e485dc93d49b9559dcb7d0d4f597ebe
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550345"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Microsoft id 플랫폼에서 비 갤러리 응용 프로그램에서 single sign-on 구성

이 문서는 Microsoft identity 플랫폼 앱 갤러리에 없는 응용 프로그램에서 single sign-on을 구성 하는 작업을 할당할 수 있는 기능에 대 한 *코드를 작성 하지 않고도*합니다.

이 문서 대신 코드를 통해 사용자 지정 앱을 Azure AD와 통합하는 방법에 대한 개발자 참고 자료를 찾는다면 [Azure AD의 인증 시나리오](../develop/authentication-scenarios.md)를 참조하세요.

Microsoft identity 플랫폼 응용 프로그램 갤러리에 설명 된 대로 Microsoft id 플랫폼을 사용 하 여에서 single sign-on의 형식을 지 원하는 것으로 알려진 응용 프로그램의 목록을 제공 [이 문서에서는](what-is-single-sign-on.md)합니다. (조직의 IT 전문가 또는 시스템 통합자인 경우)연결할 애플리케이션을 찾으면 Azure Portal에 나와 있는 단계별 지침에 따라 Single Sign-On을 사용하도록 설정할 수 있습니다.

사용권 계약에 따라 다음과 같은 기능을 사용할 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory/)를 참조하세요.

- 와 같은 최신 프로토콜을 사용 하는 응용 프로그램의 셀프 서비스 통합 [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 에서 사용자를 인증을 위한 토큰을 가져올 [Microsoft Graph](https://graph.microsoft.com)합니다.
- 지 원하는 응용 프로그램의 셀프 서비스 통합 [Assertion Markup Language (SAML (Security) 2.0](https://wikipedia.org/wiki/SAML_2.0) id 공급자 (SP 시작 또는 IdP에서 시작한)
- [암호 기반 SSO](what-is-single-sign-on.md#password-based-sso)를 사용하는 HTML 기반 로그인 페이지가 있는 모든 웹 애플리케이션의 셀프 서비스 통합
- 연결을 사용 하는 응용 프로그램의 셀프 서비스는 [Cross-domain Identity Management (SCIM) 프로토콜에 대 한 사용자 프로 비전 시스템](use-scim-to-provision-users-and-groups.md)
- [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](what-is-single-sign-on.md#linked-sign-on)에서 애플리케이션에 대한 링크를 추가하는 기능

온 보 딩 응용 프로그램을 Azure AD 응용 프로그램 갤러리에 아직를 가진 경우에 이러한 기능을 사용 하는 서비스 (SaaS) 응용 프로그램으로 소프트웨어의 셀프 서비스 통합을 포함할 수 있습니다. 다른 기능은 조직의 클라우드 또는 온-프레미스에서 제어 하는 서버에 배포 된 타사 웹 응용 프로그램을 셀프 서비스로 통합 합니다.

라고도 *앱 통합 템플릿*, 이러한 기능은 SAML, SCIM 또는 폼 기반 인증을 지 원하는 응용 프로그램에 대 한 표준 기반 연결점을 제공 합니다. 유연한 옵션 및 다양 한 응용 프로그램을 사용 하 여 호환성에 대 한 설정을 포함 합니다.

## <a name="adding-an-unlisted-application"></a>목록에 없는 애플리케이션 추가

Microsoft id 플랫폼 응용 프로그램을 등록 하는 두 가지 메커니즘을 제공 합니다.

와 같은 최신 프로토콜을 사용 하는 응용 프로그램 [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) 사용 하 여 등록은 해당 사용자를 인증 하는 [응용 프로그램 등록 포털](../develop/quickstart-register-app.md)합니다.

응용 프로그램의 다른 모든 형식을 사용 하 여 등록 [인증 메커니즘을 지원](what-is-single-sign-on.md)같은 [SAML](../develop/single-sign-on-saml-protocol.md) 프로토콜을 사용 합니다 **엔터프라이즈 응용 프로그램** 연결 블레이드 Microsoft id 플랫폼을 사용 하 여 합니다.

앱 통합 템플릿을 사용 하 여 목록에 없는 응용 프로그램에 연결 하려면 다음이 단계를 수행 합니다.

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com/) 에 Microsoft id 플랫폼 관리자 계정을 사용 하 여 합니다.
2. 선택 **엔터프라이즈 응용 프로그램** > **새 응용 프로그램**합니다.
3. (선택 사항 이지만 권장 됨) 에 **갤러리에서 추가** 검색 상자에서 응용 프로그램의 표시 이름을 입력 합니다. 응용 프로그램 검색 결과에 나타나면 선택 하 고이 절차의 나머지를 건너뜁니다.
4. 선택 **비 갤러리 응용 프로그램**합니다. 합니다 **사용자 고유의 응용 프로그램을 추가** 페이지가 나타납니다.

   ![애플리케이션 추가](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. 새 응용 프로그램에 대 한 표시 이름을 입력 합니다.
6. **추가**를 선택합니다.

이러한 방식으로 응용 프로그램을 추가 하 여 미리 통합 된 응용 프로그램에 대 한를 사용할 수 있는 유사한 환경을 제공 합니다. 먼저 선택 **Single sign on** 응용 프로그램의 사이드바에서. 다음 페이지 (**single sign-on 방법을 선택**) SSO를 구성 하는 것에 대 한 옵션을 제공 합니다.

- **SAML**
- **암호 기반**
- **연결됨**

![Single Sign-On 방법 선택](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

이러한 옵션에 대 한 자세한 내용은이 문서의 다음 섹션을 참조 하세요.

## <a name="saml-based-single-sign-on"></a>SAML 기반 Single Sign-On

선택 된 **SAML** 응용 프로그램에 SAML 기반 인증을 구성 하는 옵션입니다. (이 옵션이 필요는 응용 프로그램이 SAML 2.0을 지원 합니다.) 합니다 **구성에서 Single Sign-on SAML을 사용 하 여 설정** 페이지가 나타납니다.

![Saml에서 single sign-on을 설정](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

이 페이지에는 5 개의 다른 머리글에 있습니다.

| 번호 머리글 | 제목 이름 | 이 머리글의 요약을 참조 하세요. |
| --- | --- | --- |
| 1 | **기본 SAML 구성** | [기본 SAML 구성을 입력 합니다.](#enter-basic-saml-configuration) |
| 2 | **사용자 특성 및 클레임** | [검토 또는 SAML 토큰에서 발급 된 클레임 사용자 지정](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **SAML 서명 인증서** | [검토 인증서 만료 데이터, 상태 및 전자 메일 알림](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **설정 \<응용 프로그램 이름 >** | [대상 응용 프로그램 설정](#set-up-target-application) |
| 5 | **Single sign-on을 테스트 \<응용 프로그램 이름 >** | [SAML 응용 프로그램 테스트](#test-the-saml-application) |

이제 계속 하기 전에 응용 프로그램의 SAML 기능을 사용 하는 방법에 정보를 수집 합니다. 응용 프로그램과 Azure AD 간에 SSO를 구성 하려면 다음 섹션을 완료 합니다.

### <a name="enter-basic-saml-configuration"></a>기본 SAML 구성 입력

Azure AD를 설정 하려면로 이동 합니다 **기본 SAML 구성** 제목과 선택 해당 **편집** 아이콘 (연필). 필드 값을 추출하려면 수동으로 값을 입력하거나 메타데이터 파일을 업로드할 수 있습니다.

![기본 SAML 구성](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

다음 두 필드를이 필요 합니다.

- **식별자**합니다. 이 값에서 single sign-on이 되 고 구성 된 응용 프로그램을 고유 하 게 식별 해야 합니다. 이 값을 찾을 수 있습니다를 **발급자** 요소에는 **AuthnRequest** 응용 프로그램에서 보낸 (SAML 요청). 또한 이 값은 애플리케이션에서 제공하는 모든 SAML 메타데이터 내에서 **엔터티 ID** 로 표시됩니다. 항목에 대 한 자세한 내용은 응용 프로그램의 SAML 설명서를 확인 합니다. 해당 **엔터티 ID** 또는 **대상** 값이 있습니다.

  다음 코드에서는 하는 방법을 **식별자** 하거나 **발급자** 응용 프로그램이 Azure AD로 전송 하는 SAML 요청에 표시 됩니다.

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **회신 URL**합니다. 회신 URL은 응용 프로그램이 SAML 토큰을 수신 해야 하는 위치입니다. 이 URL은 어설션 소비자 서비스 (ACS) URL 라고도 합니다. SAML 토큰 회신 URL 또는 ACS URL에 대한 자세한 내용은 애플리케이션의 SAML 설명서를 확인합니다.

  회신 Url을 여러 개를 구성 하려면 다음 PowerShell 스크립트를 사용할 수 있습니다.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

다음 세 가지 필드는 선택 사항:

- **로그온 URL (SP에서 시작한만)** 합니다. 이 값이 응용이 프로그램에 로그인 할 사용자의 이동 위치를 나타냅니다. 응용 프로그램을 SP에서 시작한 SSO를 수행 하는 경우 다음 사용자가이 URL로 이동할 때 SP가 필요한 리디렉션을 Azure AD를 인증 하 고 사용자를 로그인 합니다. 이 필드를 지정 하는 경우 Azure AD는 Office 365 및 Azure AD 액세스 패널에서 응용 프로그램을 시작 하려면이 URL을 사용 합니다. 이 필드를 생략 하면 Azure AD 대신 수행 IdP 시작 로그온 Office 365, Azure AD 액세스 패널 또는 Azure AD SSO URL에서 응용 프로그램 시작 중 (에서 복사할 수 있습니다 합니다 **대시보드** 페이지).

- **릴레이 상태**합니다. 응용 프로그램이 인증 후 사용자를 리디렉션할 위치를 지시 하는 saml에서 릴레이 상태를 지정할 수 있습니다. 값은 URL 또는 URL을 응용 프로그램 내의 특정 위치로 사용자를 사용 하는 경로입니다.

- **로그아웃 URL** 이 값은 응용 프로그램에 SAML 로그 아웃 응답을 보내는 데 있습니다.

자세한 내용은 [Single sign-on SAML 프로토콜](../develop/single-sign-on-saml-protocol.md)합니다.

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>SAML 토큰에 발급된 클레임 검토 또는 사용자 지정

사용자가 응용 프로그램을 인증 하면 Azure AD 응용 프로그램을 SAML 토큰을 발급 정보 (또는 클레임)를 사용 하 여 고유 하 게 식별 하는 사용자에 대 한 합니다. 기본적으로이 정보에는 사용자의 사용자 이름, 전자 메일 주소, 이름 및 성을 포함 됩니다.

확인 하거나 응용 프로그램에 SAML 토큰에 전송 된 클레임을 편집 합니다.

- 로 이동 합니다 **사용자 특성 및 클레임** 제목과 선택 합니다 **편집** 아이콘. 합니다 **사용자 특성 및 클레임** 페이지가 나타납니다.

![사용자 특성 및 클레임](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

두 가지 이유로 SAML 토큰에서 발급 된 클레임을 편집 해야 합니다.

- 애플리케이션에 다른 클레임 URI 또는 클레임 값 집합이 필요합니다.
- 응용 프로그램에 필요 합니다 **이름 식별자 값** Microsoft id 플랫폼에 저장 된 사용자 이름 (라고도 사용자 주체 이름) 이외의 것으로 주장할 합니다.

자세한 내용은 [방법: 엔터프라이즈 응용 프로그램에 SAML 토큰에서 발급 된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)합니다.

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>인증서 만료 데이터, 상태 및 이메일 알림 검토

갤러리 또는 비 갤러리 응용 프로그램을 만들면 Azure AD는 생성 날짜 로부터 3 년이 지나면 만료 되는 응용 프로그램별 인증서를 만듭니다. Azure AD 및 애플리케이션 간 신뢰를 설정하려면 이 인증서가 필요합니다. 인증서 형식에 대한 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다.

Azure AD에서 활성 주에서 직접 Base64 또는 원시 형식으로 인증서를 다운로드할 수 있습니다 **구성에서 Single Sign-on SAML을 사용 하 여 설정** 페이지입니다. 또는 응용 프로그램 메타 데이터 XML 파일을 다운로드 하거나 앱 페더레이션 메타 데이터 URL을 사용 하 여 활성 인증서를 가져올 수 있습니다.

보거나, 만들거나, (활성 또는 비활성) 인증서를 다운로드로 이동 합니다 **SAML 서명 인증서** 머리글을 선택 합니다 **편집** 아이콘입니다. 합니다 **SAML 서명 인증서** 나타납니다.

![SAML 서명 인증서](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

인증서에 다음이 있는지 확인합니다.

- *원하는 만료 날짜입니다.* 미래의 최대 3 년에 대 한 만료 날짜를 구성할 수 있습니다.
- *원하는 인증서에 대 한 활성 상태가 됩니다.* 상태가 **비활성**, 상태를 변경할 **활성**합니다. 상태를 변경 하려면 원하는 인증서의 행을 마우스 오른쪽 단추로 클릭 하 고 선택 **인증서를 활성화**합니다.
- *올바른 서명 옵션 및 알고리즘입니다.*
- *올바른 알림 전자 메일 주소입니다.* 활성 인증서 만료 날짜에 가까운 경우 Azure AD는이 필드에 구성 된 전자 메일 주소로 알림의 보냅니다.  

자세한 내용은 [페더레이션된 single sign-on에 대 한 인증서 관리](manage-certificates-for-federated-single-sign-on.md) 하 고 [고급 인증서 서명 옵션 SAML 토큰에서](certificate-signing-options.md)합니다.

### <a name="set-up-target-application"></a>대상 애플리케이션 설정

SSO에 대 한 응용 프로그램을 구성 하려면 응용 프로그램의 설명서를 찾습니다. 설명서를 찾으려면에서로 이동 합니다 **설정 \<응용 프로그램 이름 >** 제목과 선택 **단계별 지침을 보려면**합니다. 설명서에 표시 된 **sign on 구성** 페이지입니다. 해당 페이지에 작성 방법을 안내 합니다 **로그인 URL**를 **Azure AD 식별자**, 및 **로그 아웃 URL** 값을 **설정 \<응용 프로그램 이름 >** 제목입니다.

필요한 값은 애플리케이션에 따라 달라집니다. 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다. 합니다 **로그인 URL** 하 고 **로그 아웃 URL** 둘 다 Azure AD의 인스턴스에 대 한 SAML 요청 처리 끝점 인 동일한 끝점으로 해결 하는 값입니다. 합니다 **Azure AD 식별자** 의 값인 합니다 **발급자** 응용 프로그램에 발급 된 SAML 토큰에서.

### <a name="assign-users-and-groups-to-your-saml-application"></a>SAML 애플리케이션에 사용자 및 그룹 할당

SAML 기반 id 공급자로 Azure AD를 사용 하도록 응용 프로그램을 구성한 후에 거의 테스트할 준비가 되었습니다. 보안 컨트롤로 Azure AD만 Azure AD 사용자에 게 액세스 권한을 부여한 경우에 응용 프로그램에 로그인 할 사용자를 허용 하는 토큰을 발급 합니다. 사용자가 직접 또는 그룹 멤버 자격을 통해 액세스할 수 있습니다.

새 사용자 또는 그룹에 할당할 응용 프로그램:

1. 응용 프로그램 보충 기사에서 선택 **사용자 및 그룹**합니다. 합니다  **\<응용 프로그램 이름 >-사용자 및 그룹** 현재 할당 된 사용자 및 그룹 목록을 보여 주는 페이지가 나타납니다.
2. 선택 **사용자 추가**합니다. 합니다 **할당 추가** 페이지가 나타납니다.
3. 선택 **사용자 및 그룹 (\<번호 > 선택한)** 합니다. 합니다 **사용자 및 그룹** 사용 가능한 사용자 및 그룹 목록을 보여 주는 페이지가 나타납니다.
4. 형식 또는 스크롤 사용자 또는 목록에서 할당 하려는 그룹을 찾을 수 있습니다.
5. 각 사용자 또는 그룹을 추가 하 고 다음을 선택 합니다 **선택** 단추입니다. 합니다 **사용자 및 그룹** 페이지 사라집니다.
6. 에 **할당 추가** 페이지에서 **할당**합니다. 합니다  **\<응용 프로그램 이름 >-사용자 및 그룹** 페이지가 목록에 표시 된 추가 사용자에 게 표시 됩니다.

   ![응용 프로그램 사용자 및 그룹](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

이 목록에서 다음을 수행할 수 있습니다.

- 사용자를 제거 합니다.
- 해당 역할을 편집 합니다.
- 사용자가 사용자의 액세스 패널 내에서 응용 프로그램에 인증할 수 있도록 자격 증명 (사용자 이름 및 암호)을 업데이트 합니다.

편집 하거나 한 번에 여러 사용자 또는 그룹을 제거할 수 있습니다.

사용자 토큰을 발급 하도록 Azure AD를 허용 사용자를 할당 합니다. 또한 이 애플리케이션의 타일이 사용자의 액세스 패널에 표시되도록 합니다. 응용 프로그램 타일을 사용자가 Office 365를 사용 하는 경우 Office 365 응용 프로그램 실행에도 나타납니다.

> [!NOTE]
> 애플리케이션에 **구성** 탭의 **로고 업로드** 단추를 사용하여 애플리케이션에 대한 타일 로고를 업로드할 수 있습니다.

### <a name="test-the-saml-application"></a>SAML 애플리케이션 테스트

SAML 응용 프로그램을 테스트 하기 전에 있어야 이미 Azure AD 사용 하 여 응용 프로그램을 설정 하 고 응용 프로그램에 사용자 또는 그룹을 할당 합니다. SAML 응용 프로그램을 테스트 하려면 다음을 선택 **Single sign on**, 수를 반환 하는 **SAML 기반 로그온** 페이지입니다. (다른 SSO 메서드를 적용 하는 경우 선택할 **single sign-on 모드를 변경할** > **SAML** 너무.) 그런 다음 합니다 **single sign-on 테스트 \<응용 프로그램 이름 >** 을 선택 **테스트**합니다. 자세한 내용은 [디버그 SAML 기반 single sign에서 Azure Active Directory 응용 프로그램에](../develop/howto-v1-debug-saml-sso-issues.md)입니다.

## <a name="password-single-sign-on"></a>암호 SSO(Single sign-on)

구성 하려면이 옵션을 선택 [암호 기반 single sign on](what-is-single-sign-on.md) HTML 로그인 페이지를 사용 하 여 웹 응용 프로그램에 대 한 합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유 해야 하는 시나리오에도 유용 합니다.

선택한 후 **암호 기반**, 응용 프로그램의 웹 기반 로그인 페이지의 URL을 입력 하 라는 메시지가 나타납니다.

![암호 기반 Single Sign-On](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

다음이 단계를 수행 합니다.

1. URL을 입력 합니다. 이 문자열에는 사용자 이름 입력된 필드를 포함 하는 페이지 여야 합니다.
2. **저장**을 선택합니다. Azure AD는 사용자 이름 및 암호 입력에 대 한 로그인 페이지를 구문 분석 하려고 시도 합니다.
3. Azure AD의 시도가 실패 하면 구문 분석 하는 경우 선택 **구성 \<응용 프로그램 이름 > 암호 Single sign-on 설정** 표시할 합니다 **sign on 구성** 페이지입니다. (시도가 성공 하면 무시할 수 있습니다이 절차의 나머지.)
4. 선택 **로그인 필드 수동 검색**합니다. 로그인 필드 수동 검색 설명 하는 추가 지침이 표시 됩니다.

   ![암호 기반 single sign-on의 수동 구성](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. 선택 **로그인 필드 캡처**합니다. 메시지를 보여 주는 새 탭에는 캡처 상태 페이지가 열립니다 **메타 데이터 캡처가 현재 진행 중인**합니다.
6. 경우는 **액세스 패널 확장 필요** 선택 상자는 새 탭에 표시 됩니다 **지금 설치** 설치 하는 **내 앱 보안 로그인 확장** 브라우저 확장 합니다. (브라우저 확장을 Microsoft Edge, Chrome 또는 Firefox 필요합니다.) 설치, 시작 및 확장을 사용 하도록 설정 및 캡처 상태 페이지를 새로 고칩니다.

   그런 다음 브라우저 확장을 입력 한 URL을 표시 하는 다른 탭을 엽니다.
7. 입력 한 URL 사용 하 여 탭에서 로그인 프로세스를 진행 합니다. 사용자 이름 및 암호 필드를 입력 하 고 로그인을 시도 합니다. (필요가 올바른 암호를 제공 합니다.)

   캡처된 로그인 필드를 저장 하 라는 메시지가 됩니다.
8. **확인**을 선택합니다. 탭을 닫습니다, 메시지를 사용 하 여 캡처 상태 페이지를 업데이트 하는 브라우저 확장을 **응용 프로그램에 대 한 메타 데이터가 업데이트 된**, 및 해당 브라우저 탭도 닫습니다.
9. Azure ad에서 **sign on 구성** 페이지에서 **앱에 성공적으로 로그인 적용할 수 있었으며,** .
10. **확인**을 선택합니다.

로그인 페이지의 캡처를 후 사용자 및 그룹을 할당할 수 있습니다 및 일반 마찬가지로 자격 증명 정책을 설정할 수 있습니다 [암호 SSO 응용 프로그램](what-is-single-sign-on.md)합니다.

> [!NOTE]
> 애플리케이션에 **구성** 탭의 **로고 업로드** 단추를 사용하여 애플리케이션에 대한 타일 로고를 업로드할 수 있습니다.

## <a name="existing-single-sign-on"></a>기존 Single Sign-On

조직의 Azure AD 액세스 패널 또는 Office 365 포털에서 응용 프로그램에 대 한 링크를 추가 하려면이 옵션을 선택 합니다. 인증을 위해 Azure AD 대신 현재 Active Directory Federation Services (또는 다른 페더레이션 서비스)를 사용 하는 사용자 지정 웹 응용 프로그램에 대 한 링크를 추가 하려면이 메서드를 사용할 수 있습니다. 혹은 사용자의 액세스 패널에 표시하려는 특정 SharePoint 페이지 또는 다른 웹 페이지에 딥 링크를 추가할 수 있습니다.

선택한 후 **연결 된**, 연결할 응용 프로그램의 URL을 입력 하 라는 메시지가 나타납니다. URL을 입력 하 고 선택 **저장할**합니다. 응용 프로그램의 표시에 응용 프로그램에 사용자 및 그룹을 할당할 수 있습니다 합니다 [Office 365 앱 시작 관리자](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](end-user-experiences.md) 해당 사용자에 대 한 합니다.

> [!NOTE]
> 애플리케이션에 **구성** 탭의 **로고 업로드** 단추를 사용하여 애플리케이션에 대한 타일 로고를 업로드할 수 있습니다.

## <a name="related-articles"></a>관련 문서

- [방법: 엔터프라이즈 응용 프로그램에 SAML 토큰에서 발급 된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)
- [Azure Active Directory의 응용 프로그램에 SAML 기반 single sign on 디버그](../develop/howto-v1-debug-saml-sso-issues.md)
- [Microsoft id 플랫폼 (이전 Azure Active Directory 개발자 용)](../develop/index.yml)
