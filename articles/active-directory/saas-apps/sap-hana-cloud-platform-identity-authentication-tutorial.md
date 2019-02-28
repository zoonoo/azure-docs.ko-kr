---
title: '자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP Cloud Platform Identity Authentication 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69ca6edb9dd125350e1e557633cdfd6f07217c06
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867646"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>자습서: SAP Cloud Platform Identity Authentication과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Cloud Platform Identity Authentication을 통합하는 방법에 대해 알아봅니다.
SAP Cloud Platform Identity Authentication을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* SAP Cloud Platform Identity Authentication에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 SAP Cloud Platform Identity Authentication에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

SAP Cloud Platform Identity Authentication과 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* SAP Cloud Platform Identity Authentication Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAP Cloud Platform Identity Authentication에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.

본격적으로 세부적인 기술 정보를 살펴보기에 앞서 먼저 여기서 다루는 개념을 이해해야 합니다. SAP 클라우드 플랫폼 ID 인증 및 Active Directory Federation Services를 이용하면 SAP 클라우드 플랫폼 ID 인증으로 보호되는 SAP 애플리케이션과 서비스를 이용하여 (IdP로 사용되는) Azure AD로 보호되는 애플리케이션과 서비스에서 SSO를 구현할 수 있습니다.

현재는 SAP Cloud Platform Identity Authentication이 SAP 애플리케이션에 대한 프록시 ID 공급자로 기능합니다. 그리고 Azure Active Directory는 이 설정에서 주요 ID 공급자로 작동합니다. 

아래 다이어그램에서 이 관계를 확인할 수 있습니다.

![Azure AD 테스트 사용자 만들기](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

시이 설정을 적용하면 SAP Cloud Platform Identity Authentication 테넌트가 Azure Active Directory에서 신뢰할 수 있는 애플리케이션으로 구성됩니다.

이 방법으로 보호하려는 모든 SAP 애플리케이션 및 서비스는 이후 SAP 클라우드 플랫폼 ID 인증 관리 콘솔에서 구성됩니다.

따라서 (Azure Active Directory가 아닌) SAP 클라우드 플랫폼 ID 인증에서 SAP 애플리케이션 및 서비스에 대한 액세스 권한 부여를 수행해야 합니다.

Azure Active Directory Marketplace를 통해 SAP 클라우드 플랫폼 ID 인증을 애플리케이션으로서 구성하면 개별 클레임 또는 SAML 어설션을 구성할 필요가 없습니다.

> [!NOTE]
> 현재로서는 웹 SSO만 양측에 의해 테스트되었습니다. 앱-API 또는 API-API 통신에 필요한 흐름은 작동하긴 하나 테스트는 거치지 않았습니다. 이는 후속 작업의 일부로 테스트될 예정입니다.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>갤러리에서 SAP Cloud Platform Identity Authentication 추가

SAP Cloud Platform Identity Authentication의 Azure AD 통합을 구성하려면 갤러리의 SAP Cloud Platform Identity Authentication을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SAP Cloud Platform Identity Authentication을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **SAP Cloud Platform Identity Authentication**을 입력하고 결과 패널에서 **SAP Cloud Platform Identity Authentication**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록에서 SAP Cloud Platform Identity Authentication](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 [애플리케이션 이름]에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 [애플리케이션 이름]의 관련 사용자 간에 연결 관계를 설정해야 합니다.

[애플리케이션 이름]에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[SAP Cloud Platform Identity Authentication Single Sign-On 구성](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[SAP Cloud Platform Identity Authentication 테스트 사용자 만들기](#create-sap-cloud-platform-identity-authentication-test-user)** - Azure AD의 Britta Simon 사용자와 연결된 SAP Cloud Platform Identity Authentication 사용자를 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

[애플리케이션 이름]에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SAP Cloud Platform Identity Authentication** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![SAP Cloud Platform Identity Authentication 도메인 및 URL Single Sign-On이 설정된 구독](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `<IAS-tenant-id>.accounts.ondemand.com` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이 값을 얻으려면 [SAP Cloud Platform Identity Authentication 클라이언트 지원 팀](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)에 문의하세요. 식별자 값을 모르는 경우 SAP Cloud Platform Identity Authentication 설명서의 [Tenant SAML 2.0 구성](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html) 관련 내용을 참조하세요.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![SAP Cloud Platform Identity Authentication 도메인 및 URL Single Sign-On이 설정된 구독](common/metadata-upload-additional-signon.png)

    **로그인 URL** 텍스트 상자에서 `{YOUR BUSINESS APPLICATION URL}` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트하세요. 해당 비즈니스 응용 프로그램 로그온 URL을 사용하세요. 궁금한 점은 [SAP Cloud Platform Identity Authentication 클라이언트 지원 팀](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)에 문의하세요.

6. SAP Cloud Platform Identity Authentication 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

7. SAP 애플리케이션에 **firstName**과 같은 특성이 필요한 경우에는 **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에 **firstName** 특성을 추가하고 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 특성 이름, firstName을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 특성 값, user.givenname을 선택합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **메타데이터 XML**를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

9. **Set up SAP Cloud Platform Identity Authentication** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>SAP Cloud Platform Identity Authentication Single Sign-On 구성

1. 애플리케이션에 대해 SSO를 구성하려면 SAP Cloud Platform Identity Authentication 관리 콘솔로 이동합니다. URL 패턴은 다음과 같습니다. `https://<tenant-id>.accounts.ondemand.com/admin` [Microsoft Azure AD와 통합](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)에서 SAP Cloud Platform Identity Authentication 설명서를 참조하세요.

2. Azure Portal에서 **저장** 단추를 선택합니다.

3. 다른 SAP 애플리케이션에 SSO를 추가하고 활성화하려는 경우에만 다음 단계를 계속합니다. **갤러리에서 SAP Cloud Platform Identity Authentication 추가** 섹션의 단계를 반복합니다.

4. Azure Portal의 **SAP Cloud Platform Identity Authentication** 애플리케이션 통합 페이지에서 **연결된 로그온**을 선택합니다.

    ![연결된 로그온 구성](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. 구성을 저장합니다.

> [!NOTE]
> 새 애플리케이션은 이전 SAP 애플리케이션의 Single Sign-On 구성을 사용합니다. SAP Cloud Platform Identity Authentication 관리 콘솔에서 동일한 회사 ID 공급자를 사용했는지 확인합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP Cloud Platform Identity Authentication에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **SAP Cloud Platform Identity Authentication**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SAP Cloud Platform Identity Authentication**을 선택합니다.

    ![애플리케이션 목록에서 SAP Cloud Platform Identity Authentication 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>SAP Cloud Platform Identity Authentication 테스트 사용자 만들기

SAP Cloud Platform Identity Authentication에서 사용자를 만들 필요가 없습니다. Azure AD 사용자 저장소에 있는 사용자는 SSO 기능을 사용할 수 있습니다.

SAP Cloud Platform Identity Authentication에서는 ID 페더레이션 옵션을 지원합니다. 이 옵션을 통해 애플리케이션에서 기업 ID 공급자가 인증한 사용자가 SAP Cloud Platform Identity Authentication의 사용자 저장소에 있는지 확인할 수 있습니다.

ID 페더레이션 옵션은 기본적으로 사용 안 함으로 설정되어 있습니다. ID 페더레이션을 사용하도록 설정하면 SAP 클라우드 플랫폼 ID 인증으로 가져온 사용자만 애플리케이션에 액세스할 수 있습니다.

SAP Cloud Platform Identity Authentication에서 ID 페더레이션을 사용 또는 사용하지 않도록 설정하는 방법은 [SAP Cloud Platform Identity Authentication의 사용자 저장소에서 ID 페더레이션 구성](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)의 “SAP Cloud Platform Identity Authentication으로 ID 페더레이션 설정”을 참조하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP Cloud Platform Identity Authentication 타일을 클릭하면 SSO를 설정한 SAP Cloud Platform Identity Authentication에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
