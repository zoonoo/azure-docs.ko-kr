---
title: "자습서: NetDocuments와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 NetDocuments를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e35d086cdb59f52db95d16429c7266934813fe24


---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>자습서: NetDocuments와 Azure Active Directory 통합
이 자습서는 Azure 및 NetDocuments의 통합을 보여주기 위한 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* 유효한 Azure 구독
* NetDocuments 테넌트

이 자습서를 완료한 후 NetDocuments에 할당한 Azure AD 사용자가 NetDocuments 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. NetDocuments에 응용 프로그램 통합 사용
2. Single Sign-On 구성
3. 사용자 프로비전 구성
4. 사용자 할당

![시나리오](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scenario")

## <a name="enabling-the-application-integration-for-netdocuments"></a>NetDocuments에 응용 프로그램 통합 사용
이 섹션은 NetDocuments에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-enable-the-application-integration-for-netdocuments-perform-the-following-steps"></a>NetDocuments에 응용 프로그램 통합을 사용하도록 설정하려면
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Applications")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Add application")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Add an application from gallerry")
6. **검색 상자**에 **NetDocuments**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Application Gallery")
7. 결과 창에서 **NetDocuments**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
   
   ## <a name="configuring-single-sign-on"></a>Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 NetDocuments에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  
NetDocuments에 대한 Single Sign-On을 구성하려면 인증서의 지문(thumbprint) 값을 검색해야 합니다.  
이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 **NetDocuments** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-on 구성](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configure Single Sign-On")
2. **NetDocuments에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-on 구성](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configure Single Sign-On")
3. **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.
   
   ![앱 URL 구성](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configure App URL")
   
   1. **로그온 URL** 텍스트 상자에 사용자가 NetDocuments 응용 프로그램에 로그인하는 데 사용하는 URL(예: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*")을 입력합니다.
   2. **NetDocuments 회신 URL** 텍스트 상자에 **로그온 URL** 텍스트 상자에 입력한 것과 동일한 값을 입력합니다.  
      
      > [!NOTE]
      > **페더레이션 ID** 대화 상자의 끝에서 올바른 값을 찾을 수 있습니다(9단계의 스크린샷 참조).
      > 
      > 
   3. 페이지 맨 아래에 있는 **다음**
4. **NetDocuments에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configure Single Sign-On")
5. 다른 웹 브라우저 창에서 NetDocuments 회사 사이트에 관리자로 로그인합니다.
6. **관리자**로 이동합니다.
7. **사용자와 그룹 추가 및 제거**를 클릭합니다.
   
   ![리포지토리](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repository")
8. **고급 인증 옵션 구성**을 클릭합니다.
   
   ![고급 인증 옵션 구성](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configure advanced authentication options")
9. **페더레이션 ID** 대화 상자에서 다음 단계를 수행합니다.
   
   ![페더레이션된 ID](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Federated Identitty")
   
   1. **페더레이션 ID 서버 유형**으로 **Active Directory Federation Services**를 선택합니다.
   2. **파일 선택**을 클릭하여 다운로드한 메타데이터 파일을 업로드합니다.
   3. **확인**을 클릭합니다.
10. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성

Azure AD 사용자가 NetDocuments에 로그인할 수 있도록 하려면 NetDocuments로 프로비전되어야 합니다.  
NetDocuments의 경우 프로비전은 수동 작업입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면
1. **NetDocuments** 회사 사이트에 관리자 권한으로 로그온합니다.
2. 위쪽의 메뉴에서 **관리자**를 클릭합니다.
   
   ![관리자](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Admin")
3. **사용자와 그룹 추가 및 제거**를 클릭합니다.
   
   ![리포지토리](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repository")
4. **메일 주소** 텍스트 상자에 프로비전하려는 유효한 Azure Active Directory 계정의 이메일 주소를 입력한 다음 **사용자 추가**를 클릭합니다.
   
   ![메일 주소](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Email Address")
   
   > [!NOTE]
   > Azure Active Directory 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 이메일을 받습니다.
   > 
   > 

> [!NOTE]
> 다른 NetDocuments 사용자 계정 생성 도구 또는 NetDocuments가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
> 
> 

## <a name="assigning-users"></a>사용자 할당
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### <a name="to-assign-users-to-netdocuments-perform-the-following-steps"></a>NetDocuments에 사용자를 할당하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털에서 테스트 계정을 만듭니다.
2. **NetDocuments** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.
   
   ![사용자 할당](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Assign Users")
3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.
   
   ![예](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Yes")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.




<!--HONumber=Nov16_HO3-->


