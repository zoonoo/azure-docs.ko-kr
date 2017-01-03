---
title: "자습서: Adaptive Suite와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 Adaptive Suite를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0e21378d3ce568917296665a134c8d376d488091


---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>자습서: Adaptive Suite와 Azure Active Directory 통합
이 자습서는 Azure 및 Adaptive Suite의 통합을 보여 주기 위한 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* Adaptive Suite 테넌트

이 자습서를 완료한 후 Adaptive Suite에 할당한 Azure AD 사용자가 Adaptive Suite 회사 사이트 (서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. Adaptive Suite에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "시나리오")

## <a name="enabling-the-application-integration-for-adaptive-suite"></a>Adaptive Suite에 응용 프로그램 통합 사용
이 섹션은 Adaptive Suite에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>Adaptive Suite에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "응용 프로그램 추가")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에 **Adaptive Suite**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "응용 프로그램 갤러리")
7. 결과 창에서 **Adaptive Suite**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
   
   ## <a name="configuring-single-sign-on"></a>Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Adaptive Suite에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  
Adaptive Suite에 대한 Single Sign-On을 구성하려면 인증서의 손도장(thumbprint) 값을 검색해야 합니다.  
이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **Adaptive Suite** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Single Sign-On 구성")
2. **Adaptive Suite에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Single Sign-On 구성")
3. **앱 설정 구성** 페이지의 **회신 URL** 텍스트 상자에 "*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" 패턴을 사용하여 URL을 입력하고 **다음**을 클릭합니다.
   
   > [!NOTE]
   > Adaptive Suite의 **SAML SSO 설정** 페이지에서 이 값을 가져올 수 있습니다.
   > 
   > 
   
   ![앱 설정 구성](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "앱 설정 구성")
4. **Adaptive Suite에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Single Sign-On 구성")
5. 다른 웹 브라우저 창에서 Adaptive Suite 회사 사이트에 관리자로 로그인합니다.
6. **관리자**로 이동합니다.
   
   ![관리자](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "관리자")
7. **사용자 및 역할** 섹션에서 **SAML SSO 설정 관리**를 클릭합니다.
   
   ![SAML SSO 설정 관리](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "SAML SSO 설정 관리")
8. **SAML SSO 설정** 페이지에서 다음 단계를 수행합니다.
   
   ![SAML SSO 설정](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO 설정")
   
   1. **ID 공급자 이름** 텍스트 상자에 구성할 이름을 입력합니다.
   2. Azure 클래식 포털의 **Adaptive Suite에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **엔터티 ID** 값을 복사한 다음 **ID 공급자 엔터티 ID** 텍스트 상자에 붙여넣습니다.
   3. Azure 클래식 포털의 **Adaptive Suite에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **SAML SSO URL** 값을 복사한 다음 **ID 공급자 SSO URL** 텍스트 상자에 붙여넣습니다.
   4. Azure 클래식 포털의 **Adaptive Suite에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **SAML SSO URL** 값을 복사한 다음 **사용자 지정 로그아웃 URL** 텍스트 상자에 붙여넣습니다.
   5. 다운로드한 인증서를 업로드하려면 **파일 선택**을 클릭합니다.
   6. **SAML 사용자 id**로 **사용자의 Adaptive Insights 사용자 이름**을 선택합니다.
   7. **SAML 사용자 id 위치**로 **제목의 NameID에서 사용자 id**를 선택합니다.
   8. **SAML NameID 형식**으로 **전자 메일 주소**를 선택합니다.
   9. **SAML 사용**으로 **SAML SSO 및 Adaptive Insights 직접 로그인 허용**을 선택합니다.
   10. **Save**를 클릭합니다.
9. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Single Sign-On 구성")
   
   ## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성

Azure AD 사용자가 Adaptive Suite에 로그인할 수 있도록 하려면 Adaptive Suite로 프로비전되어야 합니다.  
Adaptive Suite의 경우 프로비전은 수동 작업입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면
1. **Adaptive Suite** 회사 사이트에 관리자 권한으로 로그인합니다.
2. **관리자**로 이동합니다.
   
   ![관리자](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "관리자")
3. **사용자 및 역할** 섹션에서 **사용자 추가**를 클릭합니다.
   
   ![사용자 추가](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "사용자 추가")
4. **새 사용자** 섹션에서 다음 단계를 수행합니다.
   
   ![제출](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "제출")
   
   1. 관련된 텍스트 상자에 프로비전할 유효한 Azure Active Directory 사용자의 **이름**, **로그인**, **전자 메일**, **암호**를 입력합니다.
   2. **역할**을 선택합니다.
   3. **Submit**를 클릭합니다.

> [!NOTE]
> 다른 Adaptive Suite 사용자 계정 생성 도구 또는 Adaptive Suite가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
> 
> 

## <a name="assigning-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### <a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Adaptive Suite에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **Adaptive Suite** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "사용자 할당")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.




<!--HONumber=Dec16_HO5-->


