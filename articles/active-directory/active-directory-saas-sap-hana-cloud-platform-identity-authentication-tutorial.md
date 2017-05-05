---
title: "자습서: SAP HANA Cloud Platform Identity Authentication과 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory 및 SAP HANA Cloud Platform Identity Authentication 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9b3f305e71502119e5dee6592347cf30bafef157
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform-identity-authentication"></a>자습서: SAP HANA Cloud Platform Identity Authentication과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP HANA Cloud Platform Identity Authentication을 통합하는 방법에 대해 알아봅니다. 기본 IdP로 Azure AD를 사용하여 SAP 응용 프로그램에 액세스하는 프록시 IdP로 SAP HANA Cloud Platform Identity Authentication이 사용됩니다.

SAP HANA Cloud Platform Identity Authentication을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- SAP 응용 프로그램에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SAP 응용 프로그램 SSO(Single Sign-on)에 자동으로 로그온되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

SAP HANA Cloud Platform Identity Authentication과 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- **SAP HANA Cloud Platform Identity Authentication** SSO가 설정된 구독


>[!NOTE] 
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
>

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SAP HANA Cloud Platform Identity Authentication 추가
2. Azure AD SSO 구성 및 테스트

기술 세부 정보를 분석하기 앞서 확인하려는 개념을 이해해야 합니다. SAP HANA Cloud Platform Identity Authentication 및 Azure Active Directory 페더레이션을 통해 SAP HANA Cloud Platform Identity Authentication으로 보호되는 SAP 응용 프로그램 및 서비스와 함께 AAD(IdP로)로 보호되는 응용 프로그램 또는 서비스 간에 SSO를 구현할 수 있습니다.

현재는 SAP HANA Cloud Platform Identity Authentication이 SAP 응용 프로그램에 대한 프록시 ID 공급자로 작동합니다. 그리고 Azure Active Directory는 이 설정에서 주요 ID 공급자로 작동합니다. 

아래 다이어그램은 다음 사항을 보여 줍니다.    

![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sap-hana-cloud-tutorial/architecture-01.png)

이 설정을 통해 SAP HANA Cloud Platform Identity Authentication 테넌트가 Azure Active Directory에서 신뢰할 수 있는 응용 프로그램으로 구성됩니다. 

이 방법을 통해 보호하려는 모든 SAP 응용 프로그램 및 서비스는 SAP HANA Cloud Platform Identity Authentication 관리 콘솔에서 나중에 구성됩니다.

따라서 이러한 설정을 위해 SAP HANA Cloud Platform Identity Authentication에서 SAP 응용 프로그램 및 서비스에 대한 액세스 권한 부여를 수행해야 합니다(Azure Active Directory에서 권한 부여 구성이 아님).

Azure Active Directory Marketplace를 통해 응용 프로그램으로 SAP HANA Cloud Platform Identity Authentication을 구성하면, 필요한 개별 클레임/SAML 어설션 및 SAP 응용 프로그램에 유효한 인증 토큰을 생성하는 데 필요한 변환을 구성할 필요가 없습니다.

>[!NOTE] 
>현재 웹 SSO는 두 파티에서만 테스트되었습니다. 앱-API 또는 API-API 통신에 필요한 흐름은 작동하지만 아직 테스트되지 않았습니다. 후속 작업의 일부로 테스트될 예정입니다.
>

## <a name="add-sap-hana-cloud-platform-identity-authentication-from-the-gallery"></a>갤러리에서 SAP HANA Cloud Platform Identity Authentication 추가
SAP HANA Cloud Platform Identity Authentication의 Azure AD 통합을 구성하려면 갤러리의 SAP HANA Cloud Platform Identity Authentication을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SAP HANA Cloud Platform Identity Authentication을 추가하려면 다음 단계를 수행합니다.**

1. [**Azure 관리 포털**](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **SAP HANA Cloud Platform Identity Authentication**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_01.png)

5. 결과 창에서 **SAP HANA Cloud Platform Identity Authentication**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_02.png)


##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SAP HANA Cloud Platform Identity Authentication에서 Azure AD SSO를 구성하고 테스트합니다.

SSO가 작동하려면 Azure AD에서 사용자에 해당하는 SAP HANA Cloud Platform Identity Authentication 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SAP HANA Cloud Platform Identity Authentication의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 SAP HANA Cloud Platform Identity Authentication의 **Username** 값으로 할당하여 설정합니다.

SAP HANA Cloud Platform Identity Authentication에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SAP HANA Cloud Platform Identity Authentication 테스트 사용자 만들기](#creating-a-sap-hana-cloud-platform-identity-authentication-test-user)** - Azure AD를 대표하여 SAP HANA Cloud Platform Identity Authentication에서 Britta Simon에 해당하는 사용자가 있어야 합니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD SSO를 사용하도록 설정하고 SAP HANA Cloud Platform Identity Authentication 응용 프로그램에서 Single Sign-On을 구성합니다.

SAP HANA Cloud Platform Identity Authentication 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![Single Sign-on 구성](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_03.png)

**SAP HANA Cloud Platform Identity Authentication에서 Azure AD SSO를 구성하려면 다음 단계가 필요합니다.**

1. Azure 관리 포털의 **SAP HANA Cloud Platform Identity Authentication** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 구성][5]

3. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 SAP 응용 프로그램에 특성이 필요한 경우(예: "firstName") SAML 토큰 특성 대화 상자에서 "firstName" 특성을 추가합니다.
 1. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.
 
    ![Single Sign-On 구성][6]

    ![Single Sign-on 구성](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_05.png)
 2. **특성 이름** 텍스트 상자에서 특성 이름 "firstName"을 입력합니다.
 3. **특성 값** 목록에서 특성 값으로 "user.givenname"을 선택합니다.
 4. **확인**을 클릭합니다.

4. **SAP HANA Cloud Platform Identity Authentication 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_06.png)
 1. **로그인 URL** 텍스트 상자에 SAP 응용 프로그램에 대한 로그인 URL을 입력합니다.
 2. **식별자** 텍스트 상자에 다음 패턴으로 값을 입력합니다. `<entity-id>.accounts.ondemand.com` 
    * 이 값을 모르는 경우 [Tenant SAML 2.0 구성](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)에서 SAP HANA Cloud Platform Identity Authentication 설명서를 따르세요.

5. **SAP HANA Cloud Platform Identity Authentication 구성** 섹션에서 **SAP HANA Cloud Platform Identity Authentication 구성**을 클릭하여 **로그온 구성** 대화 상자를 엽니다. **SAML XML 메타데이터**를 클릭한 후, 컴퓨터에 파일을 저장합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_07.png) 

    ![Single Sign-on 구성](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_08.png)

6. 응용 프로그램에 대해 SSO를 구성하려면 SAP HANA Cloud Platform Identity Authentication 관리 콘솔로 이동합니다. URL 패턴은 다음과 같습니다. `https://<tenant-id>.accounts.ondemand.com/admin`
 * 그런 다음 SAP HANA Cloud Platform Identity Authentication 설명서에 따라 [SAP HANA Cloud Platform Identity Authentication에서 기업 ID 공급자로 Microsoft Azure AD를 구성](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)합니다. 

7. Azure 관리 포털에서 **저장** 단추를 클릭합니다.
8. 다른 SAP 응용 프로그램에 대해 SSO를 추가하고 사용하도록 설정하려는 경우에만 다음 단계를 계속합니다. SAP HANA Cloud Platform Identity Authentication의 다른 인스턴스를 추가하려면 "갤러리에서 SAP HANA Cloud Platform Identity Authentication 추가" 섹션의 단계를 반복합니다.
9. Azure 관리 포털의 **SAP HANA Cloud Platform Identity Authentication** 응용 프로그램 통합 페이지에서 **연결된 로그온**을 클릭합니다.

     ![연결된 로그온 구성](./media/active-directory-saas-sap-hana-cloud-tutorial/linked_sign_on.png)
10. 그런 다음 구성을 저장합니다.

>[!NOTE] 
>새 응용 프로그램은 이전 SAP 응용 프로그램의 SSO 구성을 활용합니다. SAP HANA Cloud Platform Identity Authentication 관리 콘솔에서 동일한 회사 ID 공급자를 사용하는지 확인합니다.
>

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 새로운 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_04.png) 
  1. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.
  2. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.
  3. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.
  4. **만들기**를 클릭합니다. 

### <a name="create-a-sap-hana-cloud-platform-identity-authentication-test-user"></a>SAP HANA Cloud Platform Identity Authentication 테스트 사용자 만들기

SAP HANA Cloud Platform Identity Authentication에서 사용자를 만들 필요가 없습니다. Azure AD 사용자 저장소에 있는 사용자는 SSO 기능을 사용할 수 있습니다.

SAP HANA Cloud Platform Identity Authentication에서는 ID 페더레이션 옵션을 지원합니다. 이 옵션을 통해 응용 프로그램에서 기업 ID 공급자가 인증한 사용자가 SAP HANA Cloud Platform Identity Authentication의 사용자 저장소에 있는지 확인할 수 있습니다. 

기본 설정에서는 ID 페더레이션 옵션을 사용할 수 없습니다. ID 페더레이션이 사용된 경우 SAP HANA Cloud Platform Identity Authentication에서 가져온 사용자만 응용 프로그램에 액세스할 수 있습니다. 

SAP HANA Cloud Platform Identity Authentication에서 ID 페더레이션을 사용 또는 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 [SAP HANA Cloud Platform Identity Authentication의 사용자 저장소에서 ID 페더레이션 구성](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)의 SAP HANA Cloud Platform Identity Authentication으로 ID 페더레이션 설정을 참조하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP HANA Cloud Platform Identity Authentication에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**SAP HANA Cloud Platform Identity Authentication에 Britta Simon을 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SAP HANA Cloud Platform Identity Authentication**을 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_09.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 SAP HANA Cloud Platform Identity Authentication 타일을 클릭하면 SAP HANA Cloud Platform Identity Authentication 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_06.png

[100]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_203.png
