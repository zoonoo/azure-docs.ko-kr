---
title: '자습서: Egnyte와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Egnyte 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeedes
ms.openlocfilehash: e33fc71e0e43864d7d70495fc5056a8acaf4ad56
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159015"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>자습서: Egnyte와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Egnyte를 통합하는 방법에 대해 알아봅니다.

Egnyte를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Egnyte에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Egnyte에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Egnyte와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Egnyte Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Egnyte 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-egnyte-from-the-gallery"></a>갤러리에서 Egnyte 추가

Egnyte의 Azure AD 통합을 구성하려면 갤러리의 Egnyte를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Egnyte를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Egnyte**를 입력하고 결과 패널에서 **Egnyte**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Egnyte](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Egnyte에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Egnyte 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Egnyte의 관련 사용자 간에 연결이 형성되어야 합니다.

Egnyte에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Egnyte 테스트 사용자 만들기](#creating-an-egnyte-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Egnyte에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Egnyte 애플리케이션에서 Single Sign-On을 구성합니다.

**Egnyte에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Egnyte** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Egnyte 도메인 및 URL Single Sign-On 정보](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Egnyte 클라이언트 지원 팀](https://www.egnyte.com/corp/contact_egnyte.html)에 문의하세요. 

5. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **인증서(Base64)** 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

6. **Egnyte 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![Egnyte 구성](common/configuresection.png)

7. 다른 웹 브라우저 창에서 Egnyte 회사 사이트에 관리자로 로그인합니다.

8. **설정**을 클릭합니다.
   
    ![설정](./media/egnyte-tutorial/ic787819.png "설정")

9. 메뉴에서 **설정**을 클릭합니다.

    ![설정](./media/egnyte-tutorial/ic787820.png "설정")

10. **구성**탭을 클릭한 다음 **보안**을 클릭합니다.

    ![보안](./media/egnyte-tutorial/ic787821.png "보안")

11. **Single Sign-On 인증** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign On 인증](./media/egnyte-tutorial/ic787822.png "Single Sign On 인증")   
    
    a. **Single sign-on 인증**으로 **SAML 2.0**을 선택합니다.
   
    b. **ID 공급자**로 **AzureAD**를 선택합니다.
   
    다. Azure Portal에서 복사한 **로그인 URL**을 **ID 공급자 로그인 URL** 텍스트 상자에 붙여 넣습니다.
   
    d. Azure Portal에서 복사한 **Azure AD 식별자**를 **ID 공급자 엔터티 ID** 텍스트 상자에 붙여 넣습니다.
      
    e. Azure Portal에서 다운로드한 base-64로 인코딩된 인증서를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음, **ID 공급자 인증서** 텍스트 상자에 붙여넣습니다.
   
    f. **기본 사용자 매핑**으로 **전자 메일 주소**를 선택합니다.
   
    g. **도메인 특정 발급자 값 사용**을 **비활성화**로 선택합니다.
   
    h. **저장**을 클릭합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="creating-an-egnyte-test-user"></a>Egnyte 테스트 사용자 만들기

Azure AD 사용자가 Egnyte에 로그인할 수 있도록 하려면 Egnyte로 프로비전되어야 합니다. Egnyte의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Egnyte** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **설정 \> 사용자 및 그룹**으로 이동합니다.

3. **새 사용자 추가**를 클릭한 다음 추가하려는 사용자의 유형을 선택합니다.
   
    ![사용자](./media/egnyte-tutorial/ic787824.png "사용자")

4. **새 고급 사용자** 섹션에서 다음 단계를 수행합니다.
    
    ![새 표준 사용자](./media/egnyte-tutorial/ic787825.png "새 표준 사용자")   

    a. **이메일** 텍스트 상자에 사용자의 이메일(예: **Brittasimon@contoso.com**)을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 사용자 이름(예: **Brittasimon**)을 입력합니다.

    다. **인증 유형**으로 **Single Sign On**을 선택합니다.
   
    d. **저장**을 클릭합니다.
    
    >[!NOTE]
    >Azure Active Directory 계정 소유자는 알림 전자 메일을 받습니다.
    >

>[!NOTE]
>다른 Egnyte 사용자 계정 생성 도구 또는 Egnyte가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Egnyte에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **Egnyte**를 선택합니다.

    ![Configure Single Sign-On](./media/egnyte-tutorial/tutorial_egnyte_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Egnyte 타일을 클릭하면 Egnyte 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
