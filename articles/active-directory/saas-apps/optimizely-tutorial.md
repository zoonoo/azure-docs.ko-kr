---
title: '자습서: Optimizely와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Optimizely 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: be56218e174e5d8b0e6bde394f2dfd40fc91e87d
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42146211"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>자습서: Optimizely와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Optimizely를 통합하는 방법에 대해 알아봅니다.

Optimizely를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Optimizely 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Optimizely에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Optimizely와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Optimizely Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Optimizely 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-optimizely-from-the-gallery"></a>갤러리에서 Optimizely 추가

Optimizely의 Azure AD 통합을 구성하려면 갤러리의 Optimizely를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Optimizely를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![애플리케이션][2]

3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![애플리케이션][3]

4. 검색 상자에 **Optimizely**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/tutorial_optimizely_search.png)

5. 결과 창에서 **Optimizely**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Optimizely에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Optimizely 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Optimizely의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Optimizely의 **Username** 값으로 할당하여 설정합니다.

Optimizely에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Optimizely 테스트 사용자 만들기](#creating-an-optimizely-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Optimizely에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Optimizely 응용 프로그램에서 Single Sign-On을 구성합니다.

**Optimizely에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Optimizely** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. **Optimizely 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://app.optimizely.net/<instance name>`

    b. **식별자** 텍스트 상자에 `urn:auth0:optimizely:contoso` 패턴으로 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 로그온 URL 및 식별자로 값을 업데이트하게 됩니다.

4. Optimizely 응용 프로그램에는 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대한 다음 클레임을 구성하세요. 응용 프로그램 통합 페이지의 **"사용자 특성"** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.
    
    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_attribute.png)
    
5. **사용자 특성** 섹션에서 **기타 모든 사용자 특성 보기 및 편집**을 클릭하고 특성을 확장합니다. 표시된 각 특성에 대해 다음 단계를 수행합니다.

    | 특성 이름 | 특성 값 |
    | ---------------| --------------- |
    | email | user.mail |

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_attribute_05.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 **특성 이름**을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.

    d. **Ok**를 클릭합니다.

6. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_certificate.png)

7. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_general_400.png)

8. **Optimizely 구성** 섹션에서 **Optimizely 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_configure.png)

9. **Optimizely** 쪽에서 Single Sign-On을 구성하려면 Optimizely 계정 관리자에게 문의하여 다운로드한 **인증서(Base64)** 및 **SAML Single Sign-On 서비스 URL**을 제공합니다.

10. 전자 메일에 대한 응답으로 Optimizely는 로그온 URL(SP에서 시작한 SSO) 및 식별자(서비스 공급자 엔터티 ID) 값을 제공합니다.

    a. Optimizely에서 제공한 **SP 시작 SSO URL**을 복사하여 Azure Portal **Optimizely 도메인 및 URL** 섹션의 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    b. Optimizely에서 제공한 **서비스 공급자 엔터티 ID**를 복사하여 Azure Portal **Optimizely 도메인 및 URL** 섹션의 **식별자** 텍스트 상자에 붙여넣습니다.

11. 다른 브라우저 창에서 Optimizely 애플리케이션에 로그온합니다.

12. 오른쪽 위 구석에 있는 계정 이름을 클릭하고 **계정 설정**을 클릭합니다.

    ![Azure AD Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_09.png)

13. 계정 탭에서 **개요** 섹션의 Single Sign-On 아래에 있는 **SSO 사용** 확인란을 선택합니다.
  
    ![Azure AD Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_10.png)

14. 페이지 맨 아래에 있는 **저장**

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 Britta Simon의 **메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="creating-an-optimizely-test-user"></a>Optimizely 테스트 사용자 만들기

이 섹션에서는 Optimizely에서 Britta Simon이라는 사용자를 만듭니다.

1. 홈페이지에서 **Collaborators** 탭을 선택합니다.

2. 프로젝트에 새 공동 작업자를 추가하려면 **New Collaborator** 를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/create_aaduser_10.png)

3. 전자 메일 주소를 입력하고 역할을 할당합니다. **초대**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/create_aaduser_11.png)

4. 테스트 사용자는 이메일 초대를 받게 됩니다. 테스트 사용자는 이메일 주소를 사용하여 Optimizely에 로그인해야 합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Optimizely에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Optimizely에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201]

2. 응용 프로그램 목록에서 **Optimizely**를 선택합니다.

    ![Configure Single Sign-On](./media/optimizely-tutorial/tutorial_optimizely_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Optimizely 타일을 클릭하면 Optimizely 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/optimizely-tutorial/tutorial_general_01.png
[2]: ./media/optimizely-tutorial/tutorial_general_02.png
[3]: ./media/optimizely-tutorial/tutorial_general_03.png
[4]: ./media/optimizely-tutorial/tutorial_general_04.png

[100]: ./media/optimizely-tutorial/tutorial_general_100.png

[200]: ./media/optimizely-tutorial/tutorial_general_200.png
[201]: ./media/optimizely-tutorial/tutorial_general_201.png
[202]: ./media/optimizely-tutorial/tutorial_general_202.png
[203]: ./media/optimizely-tutorial/tutorial_general_203.png