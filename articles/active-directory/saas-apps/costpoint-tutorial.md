---
title: '자습서: Costpoint와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Costpoint 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879580"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>자습서: Costpoint와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Costpoint를 통합하는 방법에 대해 알아봅니다. Azure AD와 Costpoint를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Costpoint에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Costpoint에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Costpoint SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. Costpoint는 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-costpoint-from-the-gallery"></a>갤러리에서 Costpoint 추가

Costpoint의 Azure AD 통합을 구성하려면 갤러리의 Costpoint를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Costpoint**를 입력합니다.
1. 결과 패널에서 **Costpoint**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Costpoint에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Costpoint의 관련 사용자 간에 연결이 형성되어야 합니다.

Costpoint에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
2. **[Costpoint 구성](#configure-costpoint)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. **[Costpoint 테스트 사용자 만들기](#create-costpoint-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Costpoint에 만듭니다.
6. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Costpoint** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **서비스 공급자 메타데이터 파일**이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    > [!NOTE]
    > 서비스 공급자 메타데이터 파일은 자습서의 뒷부분에서 설명하는 **Costpoint 메타데이터 생성** 섹션에서 가져옵니다.
 
    1. **메타데이터 파일 업로드**를 클릭합니다.
    
    1. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.
    
    1. 메타데이터 파일이 성공적으로 업로드되면 Costpoint 섹션 텍스트 상자에서 **식별자** 및 **회신 URL** 값이 자동으로 채워집니다.

        > [!Note]
        > **식별자** 및 **회신 URL** 값이 자동으로 입력되지 않으면 요구 사항에 따라 수동으로 값을 입력합니다. **식별자(엔터티 ID)** 및 **회신 URL(Assertion Consumer Service URL)** 이 올바르게 설정되어 있고 **ACS URL**이 **/LoginServlet.cps**로 끝나는 유효한 Costpoint URL인지 확인합니다.

    1. **추가 URL 설정**을 클릭합니다.

    1. **릴레이 상태** 텍스트 상자에서 다음 패턴을 사용하여 값을 입력합니다. `system=[your system], (for example, **system=DELTEKCP**)`

1. **SP** 시작 모드로 애플리케이션을 구성하려는 경우 다음 단계를 수행합니다.
    
    **로그온 URL** 텍스트 상자에 `https://costpointteea.deltek.com/cpweb/cploginform.htm` URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 릴레이 상태로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Costpoint 클라이언트 지원 팀](https://www.deltek.com/about/contact-us)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 아이콘을 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 저장합니다.

   ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Costpoint 메타데이터 생성

Costpoint SAML SSO 구성은 **DeltekCostpoint711Security.pdf** 가이드에 설명되어 있습니다. 여기서 **SAML Single Sign-on Setup -> Configure SAML Single Sign-on between Costpoint and Azure AD**(SAML Single Sign-On 설정 -> Costpoint 및 Azure AD 간 SAML Single Sign-On 구성) 섹션을 참조합니다. 지침에 따라 **Costpoint SP 페더레이션 메타데이터 XML** 파일을 생성합니다. 이 파일은 Azure Portal의 **기본 SAML 구성**에서 사용합니다.

![Costpoint 구성 유틸리티](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> **DeltekCostpoint711Security.pdf** 가이드는 [Costpoint 클라이언트 지원 팀](https://www.deltek.com/about/contact-us)에서 제공합니다. 없는 경우 이 파일을 얻으려면 이 지원 팀에 문의하세요.

### <a name="configure-costpoint"></a>Costpoint 구성

**Costpoint 구성 유틸리티**로 돌아가서 **앱 페더레이션 메타데이터 URL**을 **IdP 페더레이션 메타데이터 XML** 텍스트 상자에 붙여넣고, **DeltekCostpoint711Security.pdf** 가이드의 지침을 계속 수행하여 Costpoint SAML 설정을 완료합니다. 

![Costpoint 구성 유틸리티](./media/costpoint-tutorial/config01.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예: `B.Simon@contoso.com`
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Costpoint에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Costpoint**를 선택합니다.
1. 앱의 개요 페이지에 있는 **관리** 섹션에서 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택하고, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-costpoint-test-user"></a>Costpoint 테스트 사용자 만들기

이 섹션에서는 Costpoint에서 사용자를 만듭니다. **사용자 ID**가 **B.SIMON**이고 이름이 **B.Simon**이라고 가정합니다. [Costpoint 클라이언트 지원 팀](https://www.deltek.com/about/contact-us)과 협력하여 사용자를 Costpoint 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.
 
사용자가 만들어지면 사용자의 **Authentication Method**(인증 방법) 선택 항목이 **Active Directory**이고, **SAML Single Sign-On** 확인란을 선택하며, Azure Active Directory의 사용자 이름은 **Active Directory 또는 인증서 ID**여야 합니다(아래 참조).

![Costpoint 사용자](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Costpoint 타일을 선택하면 SSO를 설정한 Costpoint에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)