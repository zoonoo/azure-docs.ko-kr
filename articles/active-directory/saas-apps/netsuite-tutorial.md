---
title: '자습서: NetSuite와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 NetSuite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438505"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>자습서: NetSuite와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 NetSuite를 연결하는 방법에 대해 알아봅니다. Azure AD와 NetSuite를 연결하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 NetSuite에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 NetSuite에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* NetSuite SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* NetSuite에서 **IDP** 시작 SSO를 지원합니다.

* NetSuite에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

* NetSuite에서 [자동 사용자 프로비전](NetSuite-provisioning-tutorial.md)을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-netsuite-from-the-gallery"></a>갤러리에서 NetSuite 추가

NetSuite를 Azure AD에 통합하도록 구성하려면 갤러리에서 NetSuite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **NetSuite**를 입력합니다.
1. 결과 패널에서 **NetSuite**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>NetSuite에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 NetSuite에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 NetSuite의 관련 사용자 간에 연결 관계를 설정해야 합니다.

NetSuite에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[NetSuite SSO 구성](#configure-netsuite-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[NetSuite 테스트 사용자 만들기](#create-netsuite-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 NetSuite에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **NetSuite** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    **회신 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트합니다. 해당 값을 얻으려면 [NetSuite 클라이언트 지원 팀](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. NetSuite 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 사용자 특성 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도, NetSuite 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. [사용자 특성] 대화 상자의 [사용자 클레임] 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

    | Name | 원본 특성 | 
    | ---------------| --------------- |
    | 계정  | `account id` |

    1. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    1. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    1. **네임스페이스**를 비워 둡니다.

    1. 원본을 **특성**으로 선택합니다.

    1. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    1. **확인**을 클릭합니다.

    1. **저장**을 클릭합니다.

    >[!NOTE]
    >계정 특성의 값은 실제 값이 아닙니다. 이 값은 업데이트되며, 자습서의 뒷부분에서 설명합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **NetSuite 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 NetSuite에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **NetSuite**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-netsuite-sso"></a>NetSuite SSO 구성

1. 브라우저에서 새 탭을 열고 관리자 권한으로 NetSuite 회사 사이트에 로그인합니다.

2. 페이지의 맨 위에 있는 도구 모음에서 **설정**을 클릭한 다음, **회사**로 이동하고, **기능 사용**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setupsaml.png)

3. 페이지의 가운데에 있는 도구 모음에서 **SuiteCloud**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-suitecloud.png)

4. **인증 관리** 섹션 아래에서 **SAML Single Sign-On**을 선택하여 NetSuite에서 SAML Single Sign-On 옵션을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-ticksaml.png)

5. 페이지의 위쪽에 있는 도구 모음에서 **설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

6. **설정 작업** 목록에서 **통합**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-integration.png)

7. **인증 관리** 섹션에서 **SAML Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml.png)

8. **SAML 설정** 페이지의 **NetSuite 구성** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **기본 인증 방법**을 선택합니다.

    b. **SAMLV2 ID 공급자 메타데이터**라는 레이블이 지정된 필드에서 **IDP 메타데이터 파일 업로드**를 선택합니다. 그런 다음 **찾아보기**를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    다. **제출**을 클릭합니다.

9. NetSuite에서 **설정**을 클릭한 다음, **회사**로 이동하고, 위쪽 탐색 메뉴에서 **회사 정보**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-com.png)

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-account-id.png)

    b. 오른쪽 열의 **회사 정보** 페이지에서 **계정 ID**를 복사합니다.

    다. NetSuite 계정에서 복사한 **계정 ID**를 Azure AD의 **특성 값** 필드에 붙여넣습니다. 

10. 사용자가 NetSuite에 Single Sign-On을 수행하려면 먼저 NetSuite에 적절한 권한을 할당해야 합니다. 이러한 사용 권한을 할당하려면 아래 지침을 따릅니다.

    a. 위쪽 탐색 메뉴에서 **설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    b. 왼쪽 탐색 메뉴에서 **사용자/역할**을 선택한 다음 **역할 관리**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-roles.png)

    다. **새 역할**을 클릭합니다.

    d. 새 역할에 **이름**을 입력합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-new-role.png)

    e. **저장**을 클릭합니다.

    f. 위쪽에 있는 메뉴에서 **사용 권한**을 클릭합니다. **설치**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-sso.png)

    g. **SAML Single Sign-On**을 선택한 다음, **추가**를 클릭합니다.

    h. **저장**을 클릭합니다.

    i. 위쪽 탐색 메뉴에서 **설치**를 클릭한 다음 **설치 관리자**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    j. 왼쪽 탐색 메뉴에서 **사용자/역할**을 선택한 다음 **사용자 관리**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-users.png)

    k. 테스트 사용자를 선택합니다. **편집**을 클릭한 다음, **액세스** 탭으로 이동합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-edit-user.png)

    l. 역할 대화 상자에서 만든 적합한 역할을 할당합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-add-role.png)

    m. **저장**을 클릭합니다.

### <a name="create-netsuite-test-user"></a>NetSuite 테스트 사용자 만들기

이 섹션에서는 NetSuite에서 Britta Simon이라는 사용자를 만듭니다. NetSuite는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. NetSuite에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 NetSuite 타일을 클릭하면 SSO를 설정한 NetSuite에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 NetSuite 사용해보기](https://aad.portal.azure.com/)

