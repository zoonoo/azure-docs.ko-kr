---
title: "자습서: DocuSign과 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory 및 DocuSign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ac53d0a2c1c0a257e86fe74e10c8781aa6305975
ms.openlocfilehash: 662e853ca31d426a71121d22733256556ca56fc6
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>자습서: DocuSign와 Azure Active Directory 통합
이 자습서는 Azure 및 DocuSign의 통합을 보여주기 위한 것입니다.
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* DocuSign의 테넌트

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. [DocuSign에 응용 프로그램 통합 사용](#enabling-the-application-integration-for-docusign) 
2. [Single Sign-On 구성](#configuring-single-sign-on) 
3. [계정 프로비전 구성](#configuring-account-provisioning) 
4. [사용자 할당](#assigning-users) 
   
![Single Sign-On 구성][0]

## <a name="enabling-the-application-integration-for-docusign"></a>DocuSign에 응용 프로그램 통합 사용
이 섹션은 DocuSign에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>DocuSign에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Single Sign-On 구성][1]
2. 디렉터리 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![Single Sign-On 구성][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. 수행할 작업 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![Single Sign-On 구성][4]
6. 검색 상자에 **DocuSign**을 입력합니다.
   
    ![Single Sign-On 구성][5]
7. 결과 창에서 **DocuSign**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Single Sign-On 구성][6]

## <a name="configuring-single-sign-on"></a>Single Sign-On 구성
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 DocuSign에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **DocuSign 응용 프로그램 통합** 페이지에서 **Single Sign-On 구성**을 클릭하여 Single Sign-On 구성 대화 상자를 엽니다.
   
    ![Single Sign-On 구성][7]
2. **DocuSign에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 다음을 클릭합니다.
   
    ![Single Sign-On 구성][8]
3. **앱 설정 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성][61]
   
    a. **로그온 URL** 텍스트 상자에 `https://account.docusign.com/*`를 입력합니다.  
   
    b. **식별자** 텍스트 상자에 `https://account.docusign.com/*`를 입력합니다.  
   
    c. **Next**를 클릭합니다. 

    > [!TIP] 
    > 로그온 URL 및 식별자 값은 자리 표시자입니다. 사용자 환경에 대한 실제 값을 검색하는 방법에 대한 지침은 이 항목의 뒷부분에서 설명합니다.


1. **DocuSign에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.
   
    ![Single Sign-On 구성][10]
2. 다른 웹 브라우저 창에서 **DocuSign 관리 포털** 에 관리자로 로그인합니다.
3. 왼쪽 탐색 메뉴에서 **도메인**을 클릭합니다.
   
    ![Single Sign-On 구성][51]
4. 오른쪽 창에서 **도메인 클레임**을 클릭합니다.
   
    ![Single Sign-On 구성][52]
5. **도메인 클레임** 대화 상자의 **도메인 이름** 텍스트 상자에 회사 도메인을 입력한 다음 **클레임**을 클릭합니다. 도메인을 확인하고 상태가 활성인지 확인합니다.
   
    ![Single Sign-On 구성][53]
6. 왼쪽 메뉴에서 **ID 공급자**  
   
    ![Single Sign-On 구성][54]
7. 오른쪽 창에서 **ID 공급자 추가**를 클릭합니다. 
   
    ![Single Sign-On 구성][55]
8. **ID 공급자 설정** 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성][56]

    a. **이름** 텍스트 상자에 구성할 고유한 이름을 입력합니다. 공백을 사용하지 마십시오.

    b. Azure 클래식 포털에서 발급자 URL을 복사한 다음 **ID 공급자 발급자** 텍스트 상자에 붙여 넣습니다.

    c. Azure 클래식 포털에서 **원격 로그인 URL**을 복사한 다음 **ID 공급자 로그인 URL** 텍스트 상자에 붙여 넣습니다.

    d. Azure 클래식 포털에서 **원격 로그아웃 URL**을 복사한 다음 **ID 공급자 로그아웃 URL** 텍스트 상자에 붙여 넣습니다.

    e. **Sign AuthN 요청**을 선택합니다.

    f. **AuthN 요청 보내기**로 **POST**를 선택합니다.

    g. **로그아웃 요청 보내기**로 **POST**를 선택합니다. 


1. **사용자 지정 특성 매핑** 섹션에서 Azure AD 클레임을 사용하여 매핑하려는 필드를 선택합니다. 이 예제에서는 **emailaddress** 클레임이 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 값으로 매핑됩니다. 이는 Azure Ad의 전자 메일 클레임에 대한 기본 클레임 이름입니다. 
   
    > [!NOTE]
    > 적절한 **사용자 식별자** 를 사용하여 Azure AD에서 Docusign 사용자 매핑으로 사용자를 매핑합니다. 적절한 필드를 선택하고 조직 설정에 따라 적절한 값을 입력합니다.
    > 
    > 
   
    ![Single Sign-On 구성][57]
2. **ID 공급자 인증서** 섹션에서 **인증서 추가**를 클릭하고 Azure AD 클래식 포털에서 다운로드한 인증서를 업로드합니다.   
   
    ![Single Sign-On 구성][58]
3. **Save**를 클릭합니다.
4. **ID 공급자** 섹션에서 **작업**을 클릭한 다음 **끝점**을 클릭합니다.   
   
    ![Single Sign-On 구성][59]
5. Azure 클래식 포털에서 **앱 설정 구성** 페이지로 이동합니다. 
6. **DocuSign 관리자 포털**의 **SAML 2.0 끝점 보기** 섹션에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성][60]
   
    a. **서비스 공급자 발급자 URL**을 복사한 다음 Azure 클래식 포털의 **식별자** 텍스트 상자에 붙여 넣습니다.
   
    b. **서비스 공급자 로그인 URL**을 복사한 다음 Azure 클래식 포털의 **로그온 URL** 텍스트 상자에 붙여 넣습니다.
   
    c.  페이지 맨 아래에 있는 **닫기**  
7. Azure 클래식 포털에서 **다음**을 클릭합니다. 
8. Azure 클래식 포털에서** Single Sign-On 구성 확인**을 선택하고 **다음**을 클릭합니다.
   
    ![응용 프로그램][14]
9. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
   
    ![응용 프로그램][15]

## <a name="configuring-account-provisioning"></a>계정 프로비전 구성
이 섹션에서는 DocuSign에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명합니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비저닝을 구성하려면
1. **Azure 클래식 포털**의 **DocuSign 응용 프로그램 통합** 페이지에서 **계정 프로비전 구성**을 클릭하여 사용자 프로비전 구성 대화 상자를 엽니다.
   
    ![계정 프로비전 구성][30]
2. **설정 및 관리자 자격 증명** 페이지에서 자동 사용자 프로비전을 사용하려면 충분한 권한이 있는 DocuSign 계정의 자격 증명을 제공한 후 **다음**을 클릭합니다. 
   
    ![계정 프로비전 구성][31]
3. **테스트 연결** 대화 상자에서 **테스트 시작**을 클릭하고 테스트가 성공하면 **다음**을 클릭합니다.
   
    ![계정 프로비전 구성][32]
4. **확인** 페이지에서 **완료**를 클릭합니다.
   
    ![계정 프로비전 구성][33]

## <a name="assigning-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>DocuSign에 사용자를 할당하려면 다음 단계를 수행합니다.
1. **Azure 클래식 포털**에서 테스트 계정을 만듭니다.
2. **DocuSign 응용 프로그램 통합** 페이지에서 **사용자 할당**을 클릭합니다.
   
    ![사용자 할당][40]
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
    ![사용자 할당][41]

이제 10분 동안 기다린 후 계정이 DocuSign에 동기화되었는지 확인해야 합니다.

첫 번째 확인 단계로 Azure 클래식 포털의 DocuSign 응용 프로그램 통합 페이지에서 D의 대시보드를 클릭하여 프로비전 상태를 확인할 수 있습니다.

![사용자 할당][42]

주기를 프로비전하는 성공적으로 완료된 사용자는 관련된 상태에서 표시됩니다.

![사용자 할당][43]

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다.

액세스 패널에 대한 자세한 내용은 액세스 패널 소개를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png

