---
title: '자습서: ITRP와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 ITRP 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 343439bbf47914404c4598369926f2f02cf5c9af
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65898127"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>자습서: ITRP와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ITRP를 통합하는 방법에 대해 알아봅니다.
ITRP를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* ITRP에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 ITRP에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

ITRP와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* ITRP Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* ITRP는 **SP** 시작 SSO를 지원합니다.

## <a name="adding-itrp-from-the-gallery"></a>갤러리에서 ITRP 추가

ITRP의 Azure AD 통합을 구성하려면 갤러리의 ITRP를 관리형 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ITRP를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **ITRP**를 입력하고 결과 패널에서 **ITRP**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 ITRP](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 ITRP에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 ITRP의 관련 사용자 간에 연결 관계가 설정되어 있어야 합니다.

ITRP에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[ITRP Single Sign-On 구성](#configure-itrp-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[ITRP 테스트 사용자 만들기](#create-itrp-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 ITRP에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

ITRP에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **ITRP** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![ITRP 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<tenant-name>.itrp.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<tenant-name>.itrp.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [ITRP 클라이언트 지원 팀](https://www.itrp.com/support)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML 서명 인증서** 섹션에서 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![SAML 서명 인증서 편집](common/edit-certificate.png)

6. **SAML 서명 인증서** 섹션에서 **지문**을 복사하여 컴퓨터에 저장합니다.

    ![지문 값 복사](common/copy-thumbprint.png)

7. **ITRP 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-itrp-single-sign-on"></a>ITRP Single Sign-On 구성

1. 다른 웹 브라우저 창에서 ITRP 회사 사이트에 관리자로 로그인합니다.

1. 위쪽에 도구 모음에서 **설정**을 클릭합니다.

    ![ITRP](./media/itrp-tutorial/ic775570.png "ITRP")

1. 왼쪽 탐색 창에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On](./media/itrp-tutorial/ic775571.png "Single Sign-On")

1. Single Sign-On 구성 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On](./media/itrp-tutorial/ic775572.png "Single Sign-On")

    ![Single Sign-On](./media/itrp-tutorial/ic775573.png "Single Sign-On")

    a. **사용**을 클릭합니다.

    b. Azure Portal에서 복사한 **로그아웃 URL** 값을  **원격 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을  **SAML SSO URL** 텍스트 상자에 붙여넣습니다.

    Azure Portal에서 복사한 인증서의 **지문** 값을 **인증서 지문** 텍스트 상자에 붙여넣습니다.

    e. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon@yourcompanydomain.extension`을 입력합니다. 예: BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ITRP에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**과 **ITRP**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **ITRP**를 선택합니다.

    ![애플리케이션 목록의 ITRP 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-itrp-test-user"></a>ITRP 테스트 사용자 만들기

Azure AD 사용자가 ITRP에 로그인할 수 있도록 하려면 Azure AD 사용자를 ITRP로 프로비저닝해야 합니다. ITRP의 경우 프로비저닝 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **ITRP** 테넌트에 로그인합니다.

1. 위쪽의 도구 모음에서 **레코드**를 클릭합니다.

    ![관리자](./media/itrp-tutorial/ic775575.png "관리자")

1. 팝업 메뉴에서 **사람**을 선택합니다.

    ![사람](./media/itrp-tutorial/ic775587.png "사람")

1. **새 사람 추가** (“+”)를 클릭합니다.

    ![관리자](./media/itrp-tutorial/ic775576.png "관리자")

1. 새 사람 추가 대화 상자에서 다음 단계를 수행합니다.

    ![사용자](./media/itrp-tutorial/ic775577.png "사용자")

    a. 프로비전할 유효한 AAD 계정의 **이름**과 **전자 메일**을 입력합니다.

    b. **저장**을 클릭합니다.

> [!NOTE]
> 다른 ITRP 사용자 계정 생성 도구 또는 ITRP가 제공한 API를 사용하여 AAD 사용자 계정을 프로비저닝할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ITRP 타일을 클릭하면 SSO가 설정된 ITRP에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
