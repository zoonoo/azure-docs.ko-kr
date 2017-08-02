---
title: "자습서: ServiceNow와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory와 ServiceNow 및 ServiceNow Express 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: a91fab90a94b655b93c8ae9064ea4836b80d7678
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>자습서: ServiceNow와 Azure Active Directory 통합
이 자습서에서는 Azure AD(Azure Active Directory)와 ServiceNow 및 ServiceNow Express를 통합하는 방법에 대해 알아봅니다.

ServiceNow 및 ServiceNow Express를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* ServiceNow 및 ServiceNow Express에 대한 액세스 권한이 있는 사용자는 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 ServiceNow 및 ServiceNow Express에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
ServiceNow 및 ServiceNow Express와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* ServiceNow의 경우 ServiceNow의 인스턴스 또는 테넌트, Calgary 버전 이상
* ServiceNow Express의 경우 ServiceNow Express의 인스턴스, Helsinki 버전 이상
* ServiceNow 테넌트에서 [여러 공급자 Single Sign-On 플러그 인](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)을 사용하도록 설정해야 합니다. [서비스 요청을 제출](https://hi.service-now.com)하면 이렇게 설정할 수 있습니다. 

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
> 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ServiceNow 추가
2. ServiceNow 또는 ServiceNow Express에 대한 Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-servicenow-from-the-gallery"></a>갤러리에서 ServiceNow 추가
ServiceNow 또는 ServiceNow Express의 Azure AD 통합을 구성하려면 갤러리의 ServiceNow를 관리되는 SaaS 앱 목록에 추가해야 합니다. 

**갤러리에서 ServiceNow를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에 **ServiceNow**를 입력합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. 결과 창에서 **ServiceNow**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 ServiceNow 또는 ServiceNow Express에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ServiceNow 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 ServiceNow의 관련 사용자 간에 연결이 형성되어야 합니다.
이 연결 관계는 Azure AD의 **사용자 이름** 값을 ServiceNow의 **Username** 값으로 할당하여 설정합니다. ServiceNow에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[ServiceNow에 대한 Azure AD Single Sign-on 구성](#configuring-azure-ad-single-sign-on-for-servicenow)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[ServiceNow Express에 대한 Azure AD Single Sign-on 구성](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
3. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[ServiceNow 테스트 사용자 만들기](#creating-a-servicenow-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ServiceNow에 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
6. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

> [!NOTE]
> ServiceNow를 구성하려는 경우 2단계를 생략합니다. 마찬가지로 ServiceNow Express를 구성하려는 경우 1단계를 생략합니다.
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>ServiceNow에 대한 Azure AD Single Sign-On 구성
1. Azure AD 클래식 포털의 **ServiceNow** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Single Sign-On 구성")

2. **ServiceNow에 대한 사용자 로그온 방법 선택** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Single Sign-On 구성")

3. **앱 설정 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC769497.png "앱 URL 구성")
   
    a. **ServiceNow 로그인 URL** 텍스트 상자에 다음 패턴 `https://<instance-name>.service-now.com`을 따라 사용자가 ServiceNow 응용 프로그램에 로그인하는 데 사용한 URL을 입력합니다.
   
    b. **식별자** 텍스트 상자에 다음 패턴 `https://<instance-name>.service-now.com`을 따라 사용자가 ServiceNow 응용 프로그램에 로그인하는 데 사용한 URL을 입력합니다.
   
    c. **다음**을 누릅니다

4. Azure AD에서 SAML 기반 인증용으로 ServiceNow를 자동으로 구성하도록 하려면 ServiceNow 인스턴스 이름, 관리자 사용자 이름 및 관리자 암호를 **Single Sign-On 자동 구성** 양식에 입력하고 *구성*을 클릭합니다. 입력하는 관리자 사용자 이름에 ServiceNow의 **security_admin** 역할이 할당되어 있어야 이 절차를 수행할 수 있습니다. 이 방법을 사용하지 않고 ServiceNow가 SAML ID 공급자로 Azure AD를 사용하도록 수동으로 구성하려면 **응용 프로그램을 Single Sign-On에 대해 수동 구성**을 클릭하고 **다음**을 클릭하여 다음 단계를 완료합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "앱 URL 구성")

5. **ServiceNow에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭하고 컴퓨터에 로컬로 인증서 파일을 저장합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Single Sign-On 구성")

6. ServiceNow 응용 프로그램에 관리자 권한으로 로그온합니다.

7. 다음 단계를 따라 *통합 - 여러 공급자 Single Sign-On 설치 관리자* 플러그 인을 활성화합니다.
   
    a. 왼쪽 탐색 창에서 **시스템 정의** 섹션으로 이동한 다음 **플러그 인**을 클릭합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "플러그 인 활성화")
   
    b. *통합 - 여러 공급자 Single Sign-On 설치 관리자*를 검색합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "플러그 인 활성화")
   
    c. 플러그 인을 선택합니다. 마우스 오른쪽을 클릭하고 **활성화/업그레이드**를 선택합니다.
   
    d. **활성화** 단추를 클릭합니다.

8. 왼쪽의 탐색 창에서 **속성**을 클릭합니다.  
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "앱 URL 구성")

9. **여러 공급자 SSO 속성** 대화 상자에서 다음 단계를 수행합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "앱 URL 구성")
   
    a. **여러 공급자 SSO 사용**을 **예**로 선택합니다.
   
    b. **디버그 로깅에 여러 공급자 SSO 통합을 사용**하도록 설정을 **예**로 선택합니다.
   
    c. **...하는 사용자 테이블의 필드** 텍스트 상자에서 **user_name**을 입력합니다.
   
    d. **Save**를 클릭합니다.

10. 왼쪽의 탐색 창에서 **x509 Certificates**을 클릭합니다.
    
     ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Single Sign-On 구성")

11. **X.509 인증서** 대화 상자에서 **새로 만들기**를 클릭합니다.
    
     ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Single Sign-On 구성")

12. **X.509 Certificates** 대화 상자에서 다음 단계를 수행합니다.
    
     ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Single Sign-On 구성")
    
     a. **새로 만들기**를 클릭합니다.
    
     b. **이름** 텍스트 상자에서 구성할 이름을 입력합니다(예: **TestSAML2.0**).
    
     c. **활성**을 선택합니다.
    
     d. **형식**으로 **PEM**을 선택합니다.
    
     e. **형식**으로 **저장소 인증서 신뢰**를 선택합니다.
    
     f. Base64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **PEM 인증서** 텍스트 상자에 붙여 넣습니다.
    
     g. **업데이트**를 클릭합니다.

13. 왼쪽의 탐색 창에서 **ID 공급자**를 클릭합니다.
    
     ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Single Sign-On 구성")

14. **ID 공급자** 대화 상자에서 **새로 만들기**를 클릭합니다.
    
     ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Single Sign-On 구성")

15. **ID 공급자** 대화 상자에서 **SAML2 Update1?**을 클릭합니다.
    
     ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Single Sign-On 구성")

16. SAML2 Update1 속성 대화 상자에서 다음 단계를 수행합니다.
    
     ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Single Sign-On 구성")

    a. **이름** 텍스트 상자에서 구성할 이름을 입력합니다(예: **SAML 2.0**).

    b. ServiceNow 배포에서 사용자를 고유하게 식별하는 데 사용되는 필드에 따라 **사용자 필드** 텍스트 상자에 **email** 또는 **user_name**을 입력합니다. 

    > [!NOTE] 
    > Azure 클래식 포털의 **ServiceNow > 특성 > Single Sign-On** 섹션으로 이동한 다음 원하는 필드를 **nameidentifier** 특성에 매핑하면 Azure AD 사용자 ID(사용자 계정 이름) 또는 전자 메일 주소를 SAML 토큰의 고유 식별자로 내보내도록 Azure AD를 구성할 수 있습니다. Azure AD에서 선택한 특성에 대해 저장되는 값(예: 사용자 계정 이름)은 입력하는 필드에 대해 ServiceNow에 저장된 값(예: user_name)과 일치해야 합니다.

    c. Azure AD 클래식 포털에서 **ID 공급자 ID** 값을 복사한 다음 **ID 공급자 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure AD 클래식 포털에서 **인증 요청 URL** 값을 복사한 다음 **ID 공급자의 AuthnRequest** 텍스트 상자에 붙여넣습니다.

    e. Azure AD 클래식 포털에서 **Single Sign-Out 서비스 URL** 값을 복사한 다음 **ID 공급자의 SingleLogoutRequest 텍스트 상자**에 붙여넣습니다.

    f. **ServiceNow 홈페이지** 텍스트 상자에 ServiceNow 인스턴스 홈페이지의 URL을 입력합니다.

    > [!NOTE] 
    > ServiceNow 인스턴스 홈페이지의 URL은 **ServieNow 테넌트 URL** 및 **/navpage.do**의 연결입니다(예: `https://fabrikam.service-now.com/navpage.do`).

    g. **엔터티 ID/발급자** 텍스트 상자에 ServiceNow 테넌트의 URL을 입력합니다.

    h. **대상 URL** 텍스트 상자에 ServiceNow 테넌트의 URL을 입력합니다. 

    i. **IDP의 SingleLogoutRequest에 대한 프로토콜 바인딩** 텍스트 상자에 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**를 입력합니다.

    j. NameID 정책 텍스트 상자에 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**를 입력합니다.

    k. **AuthnContextClass 만들기**의 선택을 취소합니다.

    l. **AuthnContextClassRef 메서드**에 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`를 입력합니다. 클라우드 전용 조직인 경우에만 필요합니다. 인증으로 온-프레미스 ADFS 또는 MFA를 사용하는 경우 이 값을 구성하면 안 됩니다. 

    m. **시계 기울이기** 텍스트 상자에 **60**을 입력합니다.

    n. **Single Sign On 스크립트**로 **MultiSSO_SAML2_Update1**를 선택합니다.

    o. **x509 인증서**로 이전 단계에서 만든 인증서를 선택합니다.

    p. **제출**을 클릭합니다. 

1. Azure AD 클래식 포털에서 Single Sign-On을 구성했음을 확인한다는 확인란을 선택하고 **다음**을 클릭합니다. 
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Single Sign-On 구성")

2. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Single Sign-On 구성")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>ServiceNow Express에 대한 Azure AD Single Sign-On 구성
1. Azure AD 클래식 포털의 **ServiceNow** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Single Sign-On 구성")

2. **ServiceNow에 대한 사용자 로그온 방법 선택** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Single Sign-On 구성")

3. **앱 설정 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC769497.png "앱 URL 구성")
   
    a. **ServiceNow 로그인 URL** 텍스트 상자에 다음 패턴 `https://<instance-name>.service-now.com`을 따라 사용자가 ServiceNow 응용 프로그램에 로그인하는 데 사용한 URL을 입력합니다.
   
    b. **발급자 URL** 텍스트 상자에 다음 패턴 `https://<instance-name>.service-now.com`을 따라 사용자가 ServiceNow 응용 프로그램에 로그인하는 데 사용한 URL을 입력합니다.
   
    c. **다음**을 누릅니다

4. **Single Sign-On에 대한 응용 프로그램을 수동으로 구성**을 클릭한 후 **다음**을 클릭하고 다음 단계를 완료합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "앱 URL 구성")

5. **ServiceNow에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭하고 컴퓨터에 로컬로 인증서 파일을 저장한 후 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Single Sign-On 구성")

6. ServiceNow Express 응용 프로그램에 관리자 권한으로 로그온합니다.

7. 왼쪽 탐색 창에서 **Single Sign-On**을 클릭합니다.  
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "앱 URL 구성")

8. **Single Sign-on** 대화 상자에서 오른쪽 위의 구성 아이콘을 클릭하고 다음 속성을 설정합니다.
   
    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "앱 URL 구성")
   
    a. **여러 공급자 SSO 사용**을 오른쪽으로 설정/해제합니다.
   
    b. **여러 공급자 SSO 통합에 대한 디버그 로깅 활성화**를 오른쪽으로 설정/해제합니다.
   
    c. **...하는 사용자 테이블의 필드** 텍스트 상자에서 **user_name**을 입력합니다.
9. **Single Sign-On** 대화 상자에서 **새 인증서 추가**를 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Single Sign-On 구성")
10. **X.509 Certificates** 대화 상자에서 다음 단계를 수행합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Single Sign-On 구성")
    
    a. **이름** 텍스트 상자에서 구성할 이름을 입력합니다(예: **TestSAML2.0**).
    
    b. **활성**을 선택합니다.
    
    c. **형식**으로 **PEM**을 선택합니다.
    
    d. **형식**으로 **저장소 인증서 신뢰**를 선택합니다.
    
    e. 다운로드한 인증서에서 Base64로 인코딩된 파일을 만듭니다.
    
    > [!NOTE]
    > 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하세요.
    > 
    > 
    
    f. Base64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **PEM 인증서** 텍스트 상자에 붙여 넣습니다.
    
    g. **업데이트**를 클릭합니다.
11. **Single Sign-On** 대화 상자에서 **새 IdP 추가**를 클릭합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Single Sign-On 구성")
12. **새 ID 공급자 추가** 대화 상자의 **ID 공급자 구성** 아래에서 다음 단계를 수행합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Single Sign-On 구성")

    a. **이름** 텍스트 상자에서 구성할 이름을 입력합니다(예: **SAML 2.0**).

    b. Azure AD 클래식 포털에서 **ID 공급자 ID** 값을 복사한 다음 **ID 공급자 URL** 텍스트 상자에 붙여넣습니다.

    c. Azure AD 클래식 포털에서 **인증 요청 URL** 값을 복사한 다음 **ID 공급자의 AuthnRequest** 텍스트 상자에 붙여넣습니다.

    d. Azure AD 클래식 포털에서 **Single Sign-Out 서비스 URL** 값을 복사한 다음 **ID 공급자의 SingleLogoutRequest 텍스트 상자**에 붙여넣습니다.

    e. **ID 공급자 인증서**로 이전 단계에서 만든 인증서를 선택합니다.


1. **고급 설정**을 클릭하고 **추가 ID 공급자 속성** 아래에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Single Sign-On 구성")
   
    a. **IDP의 SingleLogoutRequest에 대한 프로토콜 바인딩** 텍스트 상자에 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**를 입력합니다.
   
    b. **NameID 정책** 텍스트 상자에 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**를 입력합니다.    
   
    c. **AuthnContextClassRef 메서드**에, **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**를 입력합니다.
   
    d. **AuthnContextClass 만들기**의 선택을 취소합니다.

2. **추가 서비스 공급자 속성** 아래에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Single Sign-On 구성")
   
    a. **ServiceNow 홈페이지** 텍스트 상자에 ServiceNow 인스턴스 홈페이지의 URL을 입력합니다.
   
    > [!NOTE]
    > ServiceNow 인스턴스 홈페이지의 URL은 **ServieNow 테넌트 URL** 및 **/navpage.do**의 연결입니다(예: `https://fabrikam.service-now.com/navpage.do`).
    > 
    > 
   
    b. **엔터티 ID/발급자** 텍스트 상자에 ServiceNow 테넌트의 URL을 입력합니다.
   
    c. **대상 URI** 텍스트 상자에 ServiceNow 테넌트의 URL을 입력합니다. 
   
    d. **시계 기울이기** 텍스트 상자에 **60**을 입력합니다.
   
    e. ServiceNow 배포에서 사용자를 고유하게 식별하는 데 사용되는 필드에 따라 **사용자 필드** 텍스트 상자에 **email** 또는 **user_name**을 입력합니다.
   
    > [!NOTE]
    > Azure 클래식 포털의 **ServiceNow > 특성 > Single Sign-On** 섹션으로 이동한 다음 원하는 필드를 **nameidentifier** 특성에 매핑하면 Azure AD 사용자 ID(사용자 계정 이름) 또는 전자 메일 주소를 SAML 토큰의 고유 식별자로 내보내도록 Azure AD를 구성할 수 있습니다. Azure AD에서 선택한 특성에 대해 저장되는 값(예: 사용자 계정 이름)은 입력하는 필드에 대해 ServiceNow에 저장된 값(예: user_name)과 일치해야 합니다.
    > 
    > 
   
    f. **Save**를 클릭합니다. 

3. Azure AD 클래식 포털에서 Single Sign-On을 구성했음을 확인한다는 확인란을 선택하고 **다음**을 클릭합니다. 
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Single Sign-On 구성")

4. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Single Sign-On 구성")

## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성
이 섹션에서는 ServiceNow에 Active Directory 사용자 계정을 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명합니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비저닝을 구성하려면
1. Azure 관리 클래식 포털의 **ServiceNow** 응용 프로그램 통합 페이지에서 **사용자 프로비전 구성**을 클릭합니다. 
   
    ![사용자 프로비전](./media/active-directory-saas-servicenow-tutorial/IC769498.png "사용자 프로비전")

2. **ServiceNow 자격 증명을 입력하여 자동 사용자 프로비전 사용** 페이지에서 다음 구성 설정을 제공합니다.
   
     a. **ServiceNow 인스턴스 이름** 텍스트 상자에 ServiceNow 인스턴스 이름을 입력합니다.
   
     b. **ServiceNow 관리자 사용자 이름** 텍스트 상자에 ServiceNow 관리자 계정의 이름을 입력합니다.
   
     c. **ServiceNow 관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.
   
     d. **유효성 검사** 를 클릭하여 구성을 확인합니다.
   
     e. **다음** 단추를 클릭하여 **다음 단계** 페이지를 엽니다.
   
     f. 이 응용 프로그램에 모든 사용자를 프로비전하려는 경우 "**이 응용 프로그램에 대한 디렉터리의 모든 사용자 계정을 자동으로 프로비전**"을 선택합니다. 
   
    ![다음 단계](./media/active-directory-saas-servicenow-tutorial/IC698804.png "다음 단계")
   
     g. **다음 단계** 페이지에서 **완료**를 클릭하여 구성을 저장합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.
   
    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
   
    c. **다음**을 누릅니다.

6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
   
   b. **성** 텍스트 상자에 **Simon**을 입력합니다.
   
   c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   
   d. **역할** 목록에서 **사용자**를 선택합니다.
   
   e. **다음**을 누릅니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a. **새 암호**값을 적어둡니다.
   
    b. **완료**를 클릭합니다.   

### <a name="creating-a-servicenow-test-user"></a>ServiceNow 테스트 사용자 만들기
이 섹션에서는 ServiceNow에서 Britta Simon이라는 사용자를 만듭니다. 이 섹션에서는 ServiceNow에서 Britta Simon이라는 사용자를 만듭니다. ServiceNow 또는 ServiceNow Express 계정에 사용자를 추가하는 방법을 모르는 경우 ServiceNow 지원 팀에 문의합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ServiceNow에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 ServiceNow에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **ServiceNow**를 선택합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203] 

4. 모든 사용자 목록에서 **Britta Simon**을 선택합니다.

5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 ServiceNow 타일을 클릭하면 ServiceNow 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png

