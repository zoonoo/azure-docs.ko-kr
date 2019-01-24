---
title: '자습서: FreshDesk와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 FreshDesk 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.openlocfilehash: f8f47201149ce2384eb0adcef86936dc9d017c85
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813596"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>자습서: FreshDesk와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 FreshDesk를 통합하는 방법에 대해 알아봅니다.
FreshDesk를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* FreshDesk에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 FreshDesk에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

FreshDesk와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* FreshDesk Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* FreshDesk에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-freshdesk-from-the-gallery"></a>갤러리에서 FreshDesk 추가

FreshDesk의 Azure AD 통합을 구성하려면 갤러리의 FreshDesk를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 FreshDesk를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **FreshDesk**를 입력하고 결과 패널에서 **FreshDesk**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 FreshDesk](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 FreshDesk에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 FreshDesk의 관련 사용자 간에 연결 관계를 설정해야 합니다.

FreshDesk에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[FreshDesk Single Sign-On 구성](#configure-freshdesk-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[FreshDesk 테스트 사용자 만들기](#create-freshdesk-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 FreshDesk에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

FreshDesk에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **FreshDesk** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![FreshDesk 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<tenant-name>.freshdesk.com` 패턴 또는 Freshdesk에서 제안한 다른 값을 사용하는 URL을 입력합니다.

    b. **ID(엔터티 ID)** 텍스트 상자에서 `https://<tenant-name>.freshdesk.com` 패턴 또는 Freshdesk에서 제안한 다른 값을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이 값을 얻으려면 [FreshDesk Client 지원 팀](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. FreshDesk 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. **고유 사용자 식별자**의 기본값은 **user.userprincipalname**이지만 FreshDesk에서 이 값이 사용자의 이메일 주소와 매핑되어야 합니다. 목록에서 **user.mail** 특성을 사용하거나 조직 구성을 기반으로 적절한 특성 값을 사용할 수 있기 위해서입니다. 

    ![이미지](common/edit-attribute.png)

6. 위의 이미지와 같이 SAML 토큰 특성을 구성하기 위해 **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 **편집 아이콘**을 사용하여 클레임을 편집하거나 **새 클레임 추가**를 사용하여 클레임을 추가하고, 다음 단계를 수행합니다.
    
    | 이름 | 원본 특성 |
    | ---------------| --------------- |
    | 고유한 사용자 ID | user.mail |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

8. **명령 프롬프트**를 열고 다음 명령을 실행합니다.

    a. 명령 프롬프트에서 `certutil.exe -dump FreshDesk.cer` 값을 입력합니다.

    > [!NOTE]
    > 여기에서 **FreshDesk.cer**은 Azure Portal에서 다운로드한 인증서입니다.

    b. **Cert Hash(sha256)** 값을 복사하여 메모장에 붙여 넣습니다. 

9. **FreshDesk 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-freshdesk-single-sign-on"></a>FreshDesk Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Freshdesk 회사 사이트에 관리자로 로그인합니다.

2. **설정 아이콘**을 선택하고 **보안** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign On](./media/freshdesk-tutorial/IC776770.png "Single Sign On")
  
    a. **SSO(Single Sign On)** 의 경우 **On**을 선택합니다.

    b. **SAML SSO**를 선택합니다.

    다. **SAML 로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    d. **로그아웃 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    e. **보안 인증서 지문** 텍스트 상자에 앞서 얻은 **Cert Hash(sha256)** 값을 붙여넣습니다.
  
    f. **저장**을 클릭합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 FreshDesk에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **FreshDesk**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **FreshDesk**를 입력하고 선택합니다.

    ![애플리케이션 목록의 FreshDesk 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-freshdesk-test-user"></a>FreshDesk 테스트 사용자 만들기

Azure AD 사용자가 FreshDesk에 로그인할 수 있도록 하려면 FreshDesk로 프로비전되어야 합니다.  
FreshDesk의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Freshdesk** 테넌트에 로그인합니다.

2. 위쪽의 메뉴에서 **관리자**를 클릭합니다.

    ![관리자](./media/freshdesk-tutorial/IC776772.png "관리자")

3. **일반 설정** 탭에서 **에이전트**를 클릭합니다.
  
    ![에이전트](./media/freshdesk-tutorial/IC776773.png "에이전트")

4. **새 에이전트**를 클릭합니다.

    ![새 에이전트](./media/freshdesk-tutorial/IC776774.png "새 에이전트")

5. 에이전트 정보 대화 상자에서 다음 단계를 수행합니다.

    ![에이전트 정보](./media/freshdesk-tutorial/IC776775.png "에이전트 정보")

    a. **전자 메일** 텍스트 상자에 프로비전하려는 Azure AD 계정의 Azure AD 전자 메일 주소를 입력합니다.

    b. **전체 이름** 텍스트 상자에 프로비전하려는 Azure AD 계정의 이름을 입력합니다.

    다. **제목** 텍스트 상자에 프로비전하려는 Azure AD 계정의 제목을 입력합니다.

    d. **저장**을 클릭합니다.

    >[!NOTE]
    >Azure AD 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다.
    >
    >[!NOTE]
    >다른 Freshdesk 사용자 계정 생성 도구 또는 Freshdesk가 제공한 API를 사용하여AAD 사용자 계정을 Freshdesk에 프로비전할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 FreshDesk 타일을 클릭하면 SSO를 설정한 FreshDesk에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

