---
title: "자습서: XMatters OnDemand와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 xMatters OnDemand를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 143e4a856a31a44a9ec909d07d5f24710a6e2803


---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>자습서: xMatters OnDemand와 Azure Active Directory 통합
이 자습서는 Azure 및 xMatters OnDemand의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* xMatters OnDemand 테넌트

이 자습서를 완료한 후 xMatters OnDemand에 할당한 Azure AD 사용자가 xMatters OnDemand 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. xMatters OnDemand에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario")

## <a name="enabling-the-application-integration-for-xmatters-ondemand"></a>xMatters OnDemand에 응용 프로그램 통합 사용
이 섹션은 xMatters OnDemand에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>xMatters OnDemand에 응용 프로그램 통합을 사용하도록 설정하려면
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램 추가](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Add application")

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Add an application from gallerry")

6. **검색 상자**에 **xMatters OnDemand**를 입력합니다.
   
    ![응용 프로그램 갤러리](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Application gallery")

7. 결과 창에서 **xMatters OnDemand**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

## <a name="configuring-single-sign-on"></a>Single Sign-On 구성
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 xMatters OnDemand에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **XMatters OnDemand** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configure single sign-on")

2. **xMatters OnDemand에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configure single sign-on")

3. **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configure app URL")
   
    a. **XMatters OnDemand 로그인 URL** 텍스트 상자에서 `https://<tenant-name>.XMattersOnDemandapp.com` 패턴을 사용하여 URL을 입력합니다.
   
    b. **다음**을 클릭합니다.

4. **xMatters OnDemand에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 **c:\\XMatters OnDemand.cer**로 저장합니다.
   
    > [!IMPORTANT]
    > XMatters 지원팀에 인증서를 전달해야 합니다. Single Sign-On 구성을 완료하기 전에 xMatters 지원팀에서 인증서를 업로드해야 합니다.
    > 
    > 
   
    ![Single Sign-On 구성](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configure single sign on")

5. 다른 웹 브라우저 창에서 xMatters OnDemand 회사 사이트에 관리자 권한으로 로그인합니다.

6. 위쪽에 도구 모음에서 **관리자**를 클릭한 후 왼쪽 탐색 모음에서 **회사 세부 정보**를 클릭합니다.
   
    ![관리자](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7. **SAML 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![SAML 구성](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML configuration")
   
    a. **SAML 사용**을 선택합니다.
   
    b. Azure 클래식 포털의 **XMatters OnDemand에서 Single Sign-On 구성** 대화 상자 페이지에서 **ID 공급자 ID** 값을 복사한 다음 **ID 공급자 URL** 텍스트 상자에 붙여 넣습니다.
   
    c. Azure 클래식 포털의 **XMatters OnDemand에서 Single Sign-On 구성** 대화 상자 페이지에서 **Single Sign-On 서비스 URL** 값을 복사한 다음 **Single 로그온 URL** 텍스트 상자에 붙여 넣습니다.
   
    ㄹ. Azure 클래식 포털의 **XMatters OnDemand에서 Single Sign-On 구성** 대화 상자 페이지에서 **Single Sign-Out 서비스 URL** 값을 복사한 다음 **단일 로그아웃 URL** 텍스트 상자에 붙여 넣습니다.
   
    e. 회사 상세 정보 페이지 위쪽에서 **변경 내용 저장**을 클릭합니다.
    
    ![회사 세부 정보](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Company details")

8. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
    ![Single Sign On 구성](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configure single sign on")

## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성
Azure AD 사용자가 XMatters OnDemand에 로그인할 수 있도록 하려면 XMatters OnDemand로 프로비전되어야 합니다.  
XMatters OnDemand의 경우 프로비전은 수동 작업입니다.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.
1. **XMatters OnDemand** 테넌트에 로그인합니다.

2. **사용자** 탭을 클릭합니다.

3. **사용자 추가**를 클릭합니다.
  
    ![사용자](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Users")

4. **활성**을 선택합니다.

5. **사용자 추가** 섹션에서 다음 단계를 수행합니다.
   
    ![사용자 추가](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Add a User")
   
    a. 프로비전하려는 유효한 AAD 계정의 **사용자 ID**, **이름**, **성** 및 **사이트**를 입력합니다.
    
    b. **저장**을 클릭합니다.


## <a name="assigning-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### <a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>XMatters OnDemand에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.

2. **XMatters OnDemand** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
    ![사용자 할당](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assign users")

3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
    ![예](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Yes")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.




<!--HONumber=Dec16_HO1-->


