---
title: '자습서: Palo Alto Networks - Captive Portal과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Palo Alto Networks - Captive Portal 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: eff08cc17f475e2b6ad6406e463de27371bbe5b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064732"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>자습서: Palo Alto Networks - Captive Portal과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Palo Alto Networks - Captive Portal을 통합하는 방법에 대해 알아봅니다.
Azure AD에 Palo Alto Networks - Captive Portal을 통합하면 다음과 같은 이점을 얻을 수 있습니다.

* Palo Alto Networks - Captive Portal에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Palo Alto Networks - Captive Portal에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Palo Alto Networks - Captive Portal과의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Palo Alto Networks - Captive Portal Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Palo Alto Networks - Captive Portal에서 **IDP** 시작 SSO를 지원합니다.

* Palo Alto Networks - Captive Portal에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>갤러리에서 Palo Alto Networks - Captive Portal 추가

Palo Alto Networks - Captive Portal의 Azure AD 통합을 구성하려면 갤러리의 Palo Alto Networks - Captive Portal을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Palo Alto Networks - Captive Portal을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Palo Alto Networks - Captive Portal**을 입력하고, 결과 패널에서 **Palo Alto Networks - Captive Portal**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Palo Alto Networks - Captive Portal](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Palo Alto Networks - Captive Portal에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Palo Alto Networks - Captive Portal의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Palo Alto Networks - Captive Portal에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Palo Alto Networks - Captive Portal Single Sign-On 구성](#configure-palo-alto-networks---captive-portal-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Palo Alto Networks - Captive Portal 테스트 사용자 만들기](#create-palo-alto-networks---captive-portal-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 Palo Alto Networks - Captive Portal에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Palo Alto Networks - Captive Portal에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Palo Alto Networks - Captive Portal** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 대화 상자에서 다음 단계를 수행합니다.

    ![Palo Alto Networks - Captive Portal 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `https://<Customer Firewall Hostname>/SAML20/SP` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<Customer Firewall Hostname>/SAML20/SP/ACS` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Palo Alto Networks - Captive Portal 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="configure-palo-alto-networks---captive-portal-single-sign-on"></a>Palo Alto Networks - Captive Portal Single Sign-On 구성

1. 다른 브라우저 창에서 관리자 권한으로 Palo Alto 사이트를 엽니다.

2. **디바이스**를 클릭합니다.

    ![Palo Alto Single Sign-On 구성](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 왼쪽 탐색 모음에서 **SAML ID 공급자**를 선택하고 "가져오기"를 클릭하여 메타데이터 파일을 가져옵니다.

    ![Palo Alto Single Sign-On 구성](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. 가져오기 창에서 다음 작업 수행

    ![Palo Alto Single Sign-On 구성](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. **프로필 이름** 텍스트 상자에 이름(예: Azure AD Admin UI)을 제공합니다.
    
    b. **ID 공급자 메타데이터**에서 **찾아보기**를 클릭하고 Azure Portal에서 다운로드한 metadata.xml 파일을 선택합니다.
    
    다. **확인**

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Palo Alto Networks - Captive Portal에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Palo Alto Networks - Captive Portal**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Palo Alto Networks - Captive Portal**을 입력하고 선택합니다.

    ![애플리케이션 목록의 Palo Alto Networks - Captive Portal 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-palo-alto-networks---captive-portal-test-user"></a>Palo Alto Networks - Captive Portal 테스트 사용자 만들기

이 섹션에서는 Palo Alto Networks - Captive Portal에서 Britta Simon이라는 사용자를 만듭니다. Palo Alto Networks - Captive Portal은 기본적으로 사용하도록 설정되는 **Just-In-Time 프로비전**을 지원합니다. 이 섹션에 작업 항목이 없습니다. Palo Alto Networks - Captive Portal에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!NOTE]
> 사용자를 수동으로 만들어야 하는 경우 [Palo Alto Networks - Captive Portal 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

Captive Portal은 Windows VM의 방화벽 뒤에서 구성됩니다. Captive Portal에서 Single Sign-On을 테스트하려면 RDP를 사용하여 Windows VM에 로그온합니다. RDP 세션 내에서 웹 사이트에 브라우저를 열면 SSO url을 자동으로 열고 인증을 위한 메시지를 표시합니다. 인증이 완료되면 웹 사이트로 이동할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

