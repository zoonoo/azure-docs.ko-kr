---
title: "자습서: Intacct와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 Intacct를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 20db13c5ba67fcc402db5c692842719dd9a34eb1
ms.openlocfilehash: 0ea77d5f9c5b139ce6f87bc0cdd817166b03c22e
ms.lasthandoff: 03/01/2017

---

# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>자습서: Intacct와 Azure Active Directory 통합
이 자습서는 Azure와 Intacct의 통합을 보여주기 위한 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* Intacct 테넌트

이 자습서를 완료하면 Intacct에 할당한 Azure AD(Azure Active Directory) 사용자가 Intacct 회사 사이트(서비스 공급자가 제공한 로그온)에서 응용 프로그램에 로그인하거나 [액세스 패널](active-directory-saas-access-panel-introduction.md)을 사용할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

* Intacct에 응용 프로그램 통합 사용
* Single Sign-On 구성
* 사용자 프로비전 구성
* 사용자 할당

![시나리오](./media/active-directory-saas-intacct-tutorial/IC790030.png "시나리오")

## <a name="enable-the-application-integration-for-intacct"></a>Intacct에 응용 프로그램 통합 사용
이 섹션에서는 Intacct에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명합니다.

**Intacct에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 창에서 Active Directory 아이콘을 클릭합니다.

   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

   ![응용 프로그램](./media/active-directory-saas-intacct-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.

   ![응용 프로그램 추가](./media/active-directory-saas-intacct-tutorial/IC749321.png "응용 프로그램 추가")
5. **수행할 작업** 페이지에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-intacct-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. 검색 상자에 **Intacct**를 입력합니다.

   ![응용 프로그램 갤러리](./media/active-directory-saas-intacct-tutorial/IC790031.png "응용 프로그램 갤러리")
7. 결과 창에서 **Intacct**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")

## <a name="configure-single-sign-on"></a>Single Sign-On 구성

이 섹션에서는 사용자가 Azure AD의 계정으로 Intacct에 인증할 수 있게 하는 방법을 간략하게 설명합니다. SAML 프로토콜을 기반으로 하는 페더레이션을 이 인증에 사용합니다.  

이 절차의 일부로 base&64;로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

**Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **Intacct** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 페이지를 엽니다.

   ![Single Sign-On 구성](./media/active-directory-saas-intacct-tutorial/IC790033.png "Single Sign-On 구성")
2. **Intacct에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

   ![Single Sign-On 구성](./media/active-directory-saas-intacct-tutorial/IC790034.png "Single Sign-On 구성")
3. **앱 URL 구성** 페이지의 **로그인 URL** 상자에서 *https://Intacct.com/company* 패턴을 사용하는 URL을 입력하고 **다음**을 클릭합니다.

   ![앱 URL 구성](./media/active-directory-saas-intacct-tutorial/IC790035.png "앱 URL 구성")
4. **Intacct에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

   ![Single Sign-On 구성](./media/active-directory-saas-intacct-tutorial/IC790036.png "Single Sign-On 구성")
5. 다른 웹 브라우저 창에서 Intacct 회사 사이트에 관리자로 로그인합니다.
6. **회사** 탭을 클릭하고 **회사 정보**를 클릭합니다.

   ![회사](./media/active-directory-saas-intacct-tutorial/IC790037.png "회사")
7. **보안** 탭을 클릭한 다음 **편집**을 클릭합니다.

   ![보안](./media/active-directory-saas-intacct-tutorial/IC790038.png "보안")
8. **Single Sign-On** 섹션에서 다음 단계를 수행합니다.

   ![Single Sign On](./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign On")

   1. **Single Sign-On 사용**을 선택합니다.
   2. **ID 공급자 유형**으로 **SAML 2.0**을 선택합니다.
   3. Azure 클래식 포털의 **Intacct에 Single Sign-On 구성** 페이지에서 **발급자 URL** 값을 복사한 다음 **발급자 URL** 상자에 붙여넣습니다.
   4. Azure 클래식 포털의 **Intacct에 Single Sign-On 구성** 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그인 URL** 상자에 붙여넣습니다.
   5. 다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다. 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하세요.      
   6. Base&64;로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **인증서** 상자에 붙여넣습니다.
   7. **Save**를 클릭합니다.
9. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 페이지를 닫습니다.

   ![Single Sign-On 구성](./media/active-directory-saas-intacct-tutorial/IC790040.png "Single Sign-On 구성")

## <a name="configure-user-provisioning"></a>사용자 프로비전 구성

Azure AD 사용자가 Intacct에 로그인할 수 있도록 설정하려면 Intacct로 프로비전되어야 합니다. Intacct의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Intacct** 테넌트에 로그인합니다.
2. **회사** 탭을 클릭하고 **사용자**를 클릭합니다.

   ![사용자](./media/active-directory-saas-intacct-tutorial/IC790041.png "사용자")
3. **추가** 탭을 클릭합니다.

   ![추가](./media/active-directory-saas-intacct-tutorial/IC790042.png "추가")
4. **사용자 정보** 섹션에서 다음 단계를 수행합니다.

   ![사용자 정보](./media/active-directory-saas-intacct-tutorial/IC790043.png "사용자 정보")

   1. **사용자 정보** 섹션으로 프로비전하려는 Azure AD 계정의 **사용자 ID**, **성**, **이름**, **전자 메일 주소**, **직함** 및 **전화 번호**를 입력합니다.
   2. 프로비전하려는 Azure AD 계정의 **관리자 권한**을 선택합니다.
   3. **Save**를 클릭합니다. Azure AD 계정 보유자에게 전자 메일이 발송되며 여기에 포함된 링크를 클릭하여 계정을 확인하면 계정이 활성화됩니다.

>[!NOTE]
>Azure AD 사용자 계정을 프로비전하려면 다른 Intacct 사용자 계정 생성 도구 또는 Intacct에서 제공한 API를 사용할 수 있습니다.
>

## <a name="assign-users"></a>사용자 할당
구성을 테스트하려면 Azure AD 사용자를 Intacct에 할당해야 합니다. 사용자를 할당한 후에 응용 프로그램에 액세스할 수 있습니다.

**Intacct에 사용자를 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **Intacct** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

   ![사용자 할당](./media/active-directory-saas-intacct-tutorial/IC790044.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

   ![예](./media/active-directory-saas-intacct-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.


### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon에게 Intacct에 대한 액세스를 허용하여 Azure Single Sign-On을 사용하도록 설정합니다.

![사용자 할당][200]

**Britta Simon을 Intacct에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동한 후 **엔터프라이즈 응용 프로그램**으로 이동하고 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201]

2. 응용 프로그램 목록에서 **Intacct**를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png)

3. **관리** 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭한 다음 **할당 추가**에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹**의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹**에서 **선택** 단추를 클릭합니다.

7. **할당 추가**에서 **할당** 단추를 클릭합니다.



### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Intacct 타일을 클릭하면 Intacct 응용 프로그램에 자동으로 로그인됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

