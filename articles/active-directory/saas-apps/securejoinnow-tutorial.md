---
title: '자습서: SecureW2 JoinNow Connector와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SecureW2 JoinNow Connector 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b367befb90ec28ece963d67b479749e1c8ad363
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60340006"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>자습서: SecureW2 JoinNow Connector와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SecureW2 JoinNow Connector를 통합하는 방법에 대해 알아봅니다.

SecureW2 JoinNow Connector를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- SecureW2 JoinNow Connector에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SecureW2 JoinNow Connector에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SecureW2 JoinNow Connector와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SecureW2 JoinNow Connector Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SecureW2 JoinNow Connector 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>갤러리에서 SecureW2 JoinNow Connector 추가
SecureW2 JoinNow Connector가 Azure AD에 통합되도록 구성하려면 갤러리의 SecureW2 JoinNow Connector를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SecureW2 JoinNow Connector를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]
    
3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **SecureW2 JoinNow Connector**를 입력하고 결과 패널에서 **SecureW2 JoinNow Connector**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 SecureW2 JoinNow Connector](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 SecureW2 JoinNow Connector에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SecureW2 JoinNow Connector 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SecureW2 JoinNow Connector의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

SecureW2 JoinNow Connector에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SecureW2 JoinNow Connector 테스트 사용자 만들기](#create-a-securew2-joinnow-connector-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SecureW2 JoinNow Connector에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SecureW2 JoinNow Connector 응용 프로그램에서 Single Sign-On을 구성합니다.

**SecureW2 JoinNow Connector에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SecureW2 JoinNow Connector** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. **SecureW2 JoinNow Connector 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![SecureW2 JoinNow Connector 도메인 및 URL Single Sign-On 정보](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. **식별자** 텍스트 상자에서 `https://<organization-identifier>-auth.securew2.com/auth/saml` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [SecureW2 JoinNow Connector 클라이언트 지원 팀](mailto:support@securew2.com)에 문의하세요. 

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. **SecureW2 JoinNow Connector** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **메타데이터 XML**을 [SecureW2 JoinNow Connector 지원 팀](mailto:support@securew2.com)에 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>SecureW2 JoinNow Connector 테스트 사용자 만들기

이 섹션에서는 SecureW2 JoinNow Connector에서 Britta Simon이라는 사용자를 만듭니다.  [SecureW2 JoinNow Connector 클라이언트 지원 팀](mailto:support@securew2.com)과 협력하여 SecureW2 JoinNow Connector 플랫폼에 사용자를 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 SecureW2 JoinNow Connector에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 SecureW2 JoinNow Connector에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SecureW2 JoinNow Connector**를 선택합니다.

    ![응용 프로그램 목록의 SecureW2 JoinNow Connector 링크](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

**응용 프로그램을 테스트하려면 다음 단계를 수행합니다.** 

a. SecureW2 JoinNow Connector 클라이언트를 열고 목록에서 해당 디바이스를 선택한 후 **로그인** 단추를 클릭합니다.

b. 기본 브라우저가 열리고 인증을 위해 Azure Portal로 리디렉션되어야 합니다.

다. 성공적으로 인증하려면 SecureW2 JoinNow Connector의 초기 방문 페이지로 돌아가야 합니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png

