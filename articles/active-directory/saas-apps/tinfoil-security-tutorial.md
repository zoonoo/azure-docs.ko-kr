---
title: '자습서: TINFOIL SECURITY와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 TINFOIL SECURITY 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a4e64bf6899d3384e0dadc9c2b4ce2fd5d092385
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849417"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>자습서: TINFOIL SECURITY와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TINFOIL SECURITY를 통합하는 방법에 대해 알아봅니다.
TINFOIL SECURITY를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* TINFOIL SECURITY에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정을 사용하여 TINFOIL SECURITY(Single Sign-On)에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

TINFOIL SECURITY와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* TINFOIL SECURITY Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* TINFOIL SECURITY는 **IDP**에서 시작된 SSO를 지원합니다.

## <a name="adding-tinfoil-security-from-the-gallery"></a>갤러리에서 TINFOIL SECURITY 추가

TINFOIL SECURITY가 Azure AD로 통합되도록 구성하려면 갤러리에서 TINFOIL SECURITY를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 TINFOIL SECURITY를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **TINFOIL SECURITY**를 입력하고 결과 창에서 **TINFOIL SECURITY**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 TINFOIL SECURITY](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 TINFOIL SECURITY에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 TINFOIL SECURITY의 관련 사용자 간에 연결 관계를 설정해야 합니다.

TINFOIL SECURITY에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[TINFOIL SECURITY Single Sign-On 구성](#configure-tinfoil-security-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[TINFOIL SECURITY 테스트 사용자 만들기](#create-tinfoil-security-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 TINFOIL SECURITY에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

TINFOIL SECURITY에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **TINFOIL SECURITY** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. 앱이 Azure와 이미 사전 통합되었으므로 사용자는 **기본 SAML 구성** 섹션에서 아무 단계도 수행할 필요가 없습니다.

    ![TINFOIL SECURITY 도메인 및 URL Single Sign-On 정보](common/preintegrated.png)

5. TINFOIL SECURITY 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.  **편집**  아이콘을 클릭하여  **사용자 특성**  대화 상자를 엽니다.

        ![이미지](common/edit-attribute.png)

6. TINFOIL SECURITY 애플리케이션의 경우 위에서 언급한 특성 외에 몇 가지 추가 특성을 SAML 응답에 다시 전달해야 합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

    | Name | 원본 특성 |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 텍스트 상자에 나중에 이 자습서에서 얻을 계정 ID 값을 붙여넣습니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

7. **SAML 서명 인증서** 섹션에서 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![SAML 서명 인증서 편집](common/edit-certificate.png)

8. **SAML 서명 인증서** 섹션에서 **지문**을 복사하여 컴퓨터에 저장합니다.

    ![지문 값 복사](common/copy-thumbprint.png)

9. **TINFOIL SECURITY 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-tinfoil-security-single-sign-on"></a>TINFOIL SECURITY Single Sign-On 구성

1. 다른 웹 브라우저 창에서 TINFOIL SECURITY 회사 사이트에 관리자로 로그인합니다.

2. 위쪽에 도구 모음에서 **내 계정**을 클릭합니다.
   
    ![대시보드](./media/tinfoil-security-tutorial/ic798971.png "대시보드")

3. **보안**을 클릭합니다.
   
    ![보안](./media/tinfoil-security-tutorial/ic798972.png "보안")

4. **Single Sign-On** 구성 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On](./media/tinfoil-security-tutorial/ic798973.png "Single Sign-On")
   
    a. **SAML 사용**을 선택합니다.
   
    b. **수동 구성**을 클릭합니다.
   
    다. **SAML 게시물 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.
   
    d. **SAML Certificate Fingerprint**(SAML 인증서 지문) 텍스트 상자에 **SAML 서명 인증서** 섹션에서 복사한 **지문** 값을 붙여넣습니다.
  
    e. **Your Account ID**(사용자의 계정 ID) 값을 복사하여 Azure Portal에서 **특성 추가** 섹션의 **특성 값** 텍스트 상자에 붙여넣습니다.
   
    f. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 brittasimon@yourcompanydomain.extension을 입력합니다. 예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 TINFOIL SECURITY에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **TINFOIL SECURITY**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **TINFOIL SECURITY**를 선택합니다.

    ![애플리케이션 목록의 TINFOIL SECURITY 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-tinfoil-security-test-user"></a>TINFOIL SECURITY 테스트 사용자 만들기

Azure AD 사용자가 TINFOIL SECURITY에 로그인하려면 TINFOIL SECURITY로 프로비저닝되어야 합니다. TINFOIL SECURITY의 경우 프로비전이 수동 작업입니다.

**사용자를 프로비전하려면 다음 단계를 따르십시오.**

1. 사용자가 엔터프라이즈 계정의 일부라면 [TINFOIL SECURITY 지원 팀에 문의](https://www.tinfoilsecurity.com/contact)하여 사용자 계정을 만들어야 합니다.

1. 사용자가 일반적인 TINFOIL SECURITY SaaS 사용자라면 사용자는 원하는 사용자 사이트에 협력자를 추가할 수 있습니다. 그러면 지정된 이메일로 초대장을 보내는 프로세스가 트리거되고 새로운 TINFOIL SECURITY 사용자 계정이 만들어집니다.

> [!NOTE]
> 다른 TINFOIL SECURITY 사용자 계정 생성 도구 또는 TINFOIL SECURITY가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.
> 

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 TINFOIL SECURITY 타일을 클릭하면 SSO를 설정한 TINFOIL SECURITY에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

