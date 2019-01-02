---
title: '자습서: N2F - Expense Reports와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 N2F - Expense Reports 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 27fb299bc3bbbbf75bdf40ae02eac627763ce6d4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007597"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>자습서: N2F - Expense Reports와 Azure Active Directory 통합

이 자습서에서는 N2F - Expense Reports를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

N2F - Expense Reports를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- N2F - Expense Reports에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 N2F - Expense Reports에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

N2F - Expense Reports와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- N2F - Expense Reports Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 N2F - Expense Reports 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>갤러리에서 N2F - Expense Reports 추가

N2F - Expense Reports를 Azure AD애 통합하도록 구성하려면 갤러리에서 N2F - Expense Reports를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 N2F - Expense Reports를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **N2F - Expense Reports**를 입력하고 결과 패널에서 **N2F - Expense Reports**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 N2F - Expense Reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 N2F - Expense Reports에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 N2F - Expense Reports 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 N2F - Expense Reports의 관련 사용자 간에 연결 관계를 설정해야 합니다.

N2F - Expense Reports에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[N2F - Expense Reports 테스트 사용자 만들기](#create-a-n2f---expense-reports-test-use)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 N2F - Expense Reports에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 N2F - Expense Reports 애플리케이션에서 Single Sign-On을 구성합니다.

**N2F - Expense Reports에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **N2F - Expense Reports** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 대화 상자](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. **N2F - Expense Reports 도메인 및 URL** 섹션에서 **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우에는 앱이 이미 Azure와 사전 통합되어 있으므로 사용자가 수행해야 하는 단계가 없습니다.

    ![N2F - Expense Reports 도메인 및 URL Single Sign-On 정보](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![N2F - Expense Reports 도메인 및 URL Single Sign-On 정보](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    **로그온 URL** 텍스트 상자에서 URL `https://www.n2f.com/app/`을 입력합니다.

5. **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 붙여넣습니다.

    ![인증서 다운로드 링크](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. **N2F - Expense Reports 구성** 섹션에서 **N2F - Expense Reports 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML 엔터티 ID**를 복사합니다.

    ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. 다른 웹 브라우저 창에서 N2F - Expense Reports 회사 사이트에 관리자로 로그인합니다.

9. **설정**을 클릭한 다음, 드롭다운에서 **고급 설정**을 선택합니다.

    ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/configure1.png)

10. **계정 설정**을 선택합니다.

    ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/configure2.png)

11. **인증**을 선택한 다음, **+ Add an authentication method**(인증 방법 추가) 탭을 선택합니다.

    ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/configure3.png)

12. **SAML Microsoft Office 365**를 인증 방법으로 선택합니다.

    ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/configure4.png)

13. **인증 방법** 섹션에서 다음 단계를 수행합니다.

    ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/configure5.png)

    a. Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 **엔터티 ID** 텍스트 상자에 붙여넣습니다.

    b. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **메타데이터 URL** 텍스트 상자에 붙여넣습니다.

    다. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="create-a-n2f---expense-reports-test-user"></a>N2F - Expense Reports 테스트 사용자 만들기

Azure AD 사용자가 N2F - Expense Reports에 로그인할 수 있도록 하려면 사용자가 N2F - Expense Reports에 프로비전되어야 합니다. N2F - Expense Reports의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. N2F - Expense Reports 회사 사이트에 관리자로 로그인합니다.

2. **설정**을 클릭한 다음, 드롭다운에서 **고급 설정**을 선택합니다.

   ![N2F - Expense 사용자 추가](./media/n2f-expensereports-tutorial/configure1.png)

3. 왼쪽 탐색 창에서 **사용자** 탭을 선택합니다.

    ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/user1.png)

4. **+ 새 사용자** 탭을 선택합니다.

   ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/user2.png)

5. **사용자** 섹션에서 다음 단계를 수행합니다.

    ![N2F - Expense Reports 구성](./media/n2f-expensereports-tutorial/user3.png)

    a. **이메일 주소** 텍스트 상자에서 사용자의 이메일 주소(예: **brittasimon@contoso.com**)를 입력합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    다. **이름** 텍스트 상자에 **BrittaSimon**과 같은 사용자의 이름을 입력합니다.

    d. 조직 요구 사항에 따라 **역할, 직속 관리자(N+1)** 및 **부서**를 선택합니다.

    e. **Validate and send invitation**(유효성 검사 및 초대장 보내기)을 클릭합니다.

    > [!NOTE]
    > 사용자를 추가하는 동안 문제가 발생하면 [N2F - Expense Reports 지원 팀](mailto:support@n2f.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 N2F - Expense Reports에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200]

**Britta Simon을 N2F - Expense Reports에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **N2F - Expense Reports**를 선택합니다.

    ![애플리케이션 목록의 N2F - Expense Reports 링크](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 N2F - Expense Reports 타일을 클릭하면 N2F - Expense Reports 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

