---
title: '자습서: Procore SSO와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Procore SSO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 1cd82188ef9f5a4e0078a8ea21882f99bb8b8f44
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171289"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>자습서: Procore SSO와 Azure Active Directory 통합

이 자습서에서는 Procore SSO를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

Procore SSO를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Procore SSO에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자의 Azure AD 계정으로 Procore SSO(Single Sign-on)에 자동으로 로그온되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Procore SSO와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Procore SSO Single Sign-On을 사용하도록 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Procore SSO 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-procore-sso-from-the-gallery"></a>갤러리에서 Procore SSO 추가

Procore SSO가 Azure AD로 통합되도록 구성하려면 Procore SSO를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Procore SSO를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에서 **Procore SSO**를 입력하고 결과 패널에서 **Procore SSO**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Procore SSO](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Procore SSO에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Procore SSO 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Procore SSO의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

Procore SSO에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Procore SSO 테스트 사용자 만들기](#creating-a-procore-sso-test-user)** - Britta Simon의 Azure AD 표현과 연결된 대응 사용자를 Procore SSO에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Procore SSO 애플리케이션에서 Single Sign-On을 구성합니다.

**Procore SSO에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Procore SSO** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. 앱이 Azure와 이미 사전 통합되었으므로 사용자는 **기본 SAML 구성** 섹션에서 아무 단계도 수행할 필요가 없습니다.

    ![Procore SSO 도메인 및 URL Single Sign-On 정보](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **페더레이션 메타데이터 XML**을 다운로드한 다음, 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. **Procore SSO 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![Procore SSO 구성](common/configuresection.png)

7. **Procore SSO** 측에서 Single Sign-On을 구성하려면 Procore 회사 사이트에 관리자로 로그인합니다.

8. 도구 상자 드롭다운에서 **관리**를 클릭하여 SSO 설정 페이지를 엽니다.

    ![Configure Single Sign-On](./media/procoresso-tutorial/procore_tool_admin.png)

9. 아래 설명에 따라 값을 상자에 붙여 넣습니다.

    ![Configure Single Sign-On](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. **Single Sign On 발급자 URL** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    b. **SAML 로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 식별자** 값을 붙여넣습니다.

    다. 이제 Azure Portal에 위에서 다운로드한 **페더레이션 메타데이터 XML**을 열고 **X509Certificate**라는 태그에 인증서를 복사합니다. 복사한 값을 **Single Sign-On x509 인증서** 상자에 붙여 넣습니다.

10. **변경 내용 저장**을 클릭합니다.

11. 이러한 설정 후에 Procore에 로그인할 때 사용한 **도메인 이름**(예: **contoso.com**)을 [Procore 지원 팀](https://support.procore.com/)에 보내서 해당 도메인에 대해 페더레이션된 SSO를 활성화하도록 해야 합니다.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

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

### <a name="creating-a-procore-sso-test-user"></a>Procore SSO 테스트 사용자 만들기

Procore SSOc 측에서 Procore 테스트 사용자를 만들려면 다음 단계를 수행하세요.

1. Procore 회사 사이트에 관리자 권한으로 로그인합니다.  

2. 도구 상자 드롭다운에서 **디렉터리**를 클릭하여 회사 디렉터리 페이지를 엽니다.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_sso_directory.png)

3. **Add a Person**(사람 추가) 옵션을 클릭하여 양식을 열고 다음 옵션을 입력합니다.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_add.png)

    a. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    다. **전자 메일 주소** 텍스트 상자에 사용자의 전자 메일 주소(예: **BrittaSimon@contoso.com**)를 입력합니다.

    d. **Permission Template**(권한 템플릿)에 **Apply Permission Template Later**(권한 템플릿 나중에 적용)를 선택합니다.

    e. **만들기**를 클릭합니다.

4. 새로 추가된 연락처에 대한 세부 정보를 확인하고 업데이트합니다.

    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_check.png)

5. **Save and Send Invitiation**(저장 및 초대 보내기)(메일을 통한 초대가 필요한 경우) 또는 **저장**(바로 저장)을 클릭하여 사용자 등록을 완료합니다.
    
    ![Configure Single Sign-On](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Procore SSO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **Procore SSO**를 선택합니다.

    ![Configure Single Sign-On](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Procore SSO 타일을 클릭하면 Procore SSO 애플리케이션에 자동으로 로그온됩니다.
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
