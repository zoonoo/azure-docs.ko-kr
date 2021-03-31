---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 Harness 구성 | Microsoft Docs'
description: 사용자 계정을 Harness로 자동으로 프로비저닝 및 프로비저닝을 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 13ae960f5d259314f00f8f09b2999a36c0919bc5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94353733"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Harness 구성

이 문서에서는 Harness에 대한 사용자 및/또는 그룹을 자동으로 프로비저닝하거나 프로비저닝 해제하도록 Azure AD(Azure Active Directory)를 구성하는 방법에 대해 알아봅니다.

> [!NOTE]
> 이 문서에서는 Azure AD 사용자 프로비저닝 서비스를 기반으로 하여 구축된 커넥터에 대해 설명합니다. 이 서비스에 대한 중요 정보 및 질문과 대답은 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 대한 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Harness 테넌트](https://harness.io/pricing/)
* *관리자* 권한이 있는 Harness 사용자 계정

## <a name="assign-users-to-harness"></a>Harness에 사용자 할당

Azure Active Directory는 *할당* 이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 애플리케이션에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Harness에 액세스해야 하는 Azure AD의 사용자 또는 그룹을 결정합니다. 그런 다음, [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)의 지침에 따라 이러한 사용자 또는 그룹을 Harness에 할당할 수 있습니다.

## <a name="important-tips-for-assigning-users-to-harness"></a>사용자를 Harness에 할당하기 위한 주요 팁

* 단일 Azure AD 사용자를 Harness에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 또는 그룹은 나중에 할당할 수 있습니다.

* 사용자를 Harness에 할당할 때 **할당** 대화 상자에서 유효한 애플리케이션 특정 역할(사용 가능한 경우)을 선택해야 합니다. *기본 액세스* 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="set-up-harness-for-provisioning"></a>프로비저닝을 수행하도록 Harness 설정

1. [Harness 관리 콘솔](https://app.harness.io/#/login)에 로그인한 다음, **지속적인 보안** > **액세스 관리** 로 이동합니다.

    ![Harness 관리 콘솔](media/harness-provisioning-tutorial/admin.png)

1. **API 키** 를 선택합니다.

    ![Harness API 키 링크](media/harness-provisioning-tutorial/apikeys.png)

1. **API 키 추가** 를 선택합니다. 

    ![Harness API 키 추가 링크](media/harness-provisioning-tutorial/addkey.png)

1. **Api 키 추가** 창에서 다음을 수행합니다.

    ![Harness Api 키 추가 창](media/harness-provisioning-tutorial/title.png)
   
   a. **이름** 상자에 키 이름을 입력합니다.  
   b. **다음에서 상속된 권한** 드롭다운 목록에서 옵션을 선택합니다. 
   
1. **제출** 을 선택합니다.

1. 이 자습서의 뒷부분에서 사용할 수 있도록 **키** 를 복사합니다.

    ![Harness 토큰 만들기](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>갤러리에서 Harness 추가

Azure AD를 사용하여 사용자를 자동으로 프로비저닝하도록 Harness를 구성하려면 먼저 Harness를 Azure AD 애플리케이션 갤러리에서 관리형 SaaS 애플리케이션 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.

    !["Azure Active Directory" 단추](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택합니다.

    !["모든 애플리케이션" 링크](common/enterprise-applications.png)

1. 새 애플리케이션을 추가하려면 창 위쪽에서 **새 애플리케이션** 단추를 선택합니다.

    !["새 애플리케이션" 단추](common/add-new-app.png)

1. 검색 상자에 **Harness** 를 입력하고 결과 목록에서 **Harness** 를 선택한 다음, **추가** 단추를 선택하여 애플리케이션을 추가합니다.

    ![결과 목록의 Harness](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Harness에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당에 따라 Harness에서 사용자 또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> [Harness Single Sign-On 자습서](./harness-tutorial.md)에서 제공하는 지침에 따라 SAML 기반 Single Sign-On을 Harness에 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 사용자 프로비저닝과 별개로 구성할 수 있지만, 이러한 두 기능은 서로 보완적입니다.

> [!NOTE]
> SCIM 엔드포인트에 대한 자세한 내용은 Harness [API 키](https://docs.harness.io/article/smloyragsm-api-keys) 문서를 참조하세요.

다음을 수행하여 Azure AD에서 Harness에 대한 자동 사용자 프로비저닝을 구성합니다.

1. [Azure Portal](https://portal.azure.com)에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Harness** 를 선택합니다.

    ![애플리케이션 목록의 Harness 링크](common/all-applications.png)

1. **프로비저닝** 을 선택합니다.

    ![프로비저닝 단추](common/provisioning.png)

1. **프로비저닝 모드** 드롭다운 목록 **자동** 을 선택합니다.

    !["프로비저닝 모드" 드롭다운 목록](common/provisioning-automatic.png)

1. **관리자 자격 증명** 아래에서 다음을 수행합니다.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)
 
   a. **테넌트 URL** 상자에 **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** 를 입력합니다. Harness에 로그인할 때 브라우저의 URL에서 Harness 계정 ID를 얻을 수 있습니다.
   b. **비밀 토큰** 상자에 "프로비저닝을 수행하도록 Harness 설정" 섹션의 6단계에서 저장한 SCIM 인증 토큰 값을 입력합니다.  
   다. **연결 테스트** 를 선택하여 Azure AD에서 Harness에 연결할 수 있는지 확인합니다. 연결에 실패하면 Harness 계정에 *관리자* 권한이 있는지 확인하고 다시 시도합니다.

1. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 이메일 주소를 입력한 다음, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    !["알림 이메일" 상자](common/provisioning-notification-email.png)

1. **저장** 을 선택합니다.

1. **매핑** 아래에서 **Azure Active Directory 사용자를 Harness에 동기화** 를 선택합니다.

    ![Harness "Azure Active Directory 사용자를 Harness에 동기화" 링크](media/harness-provisioning-tutorial/usermappings.png)

1. **특성 매핑** 섹션 아래에서, Azure AD에서 Harness로 동기화되는 사용자 특성을 검토합니다. *일치* 로 선택한 특성은 업데이트 작업을 위해 Harness의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

    ![Harness 사용자 "특성 매핑" 창](media/harness-provisioning-tutorial/userattributes.png)

1. **매핑** 아래에서 **Azure Active Directory 그룹을 Harness에 동기화** 를 선택합니다.

    ![Harness "Azure Active Directory 그룹을 Harness에 동기화" 링크](media/harness-provisioning-tutorial/groupmappings.png)

1. **특성 매핑** 아래에서, Azure AD에서 Harness로 동기화되는 그룹 특성을 검토합니다. *일치* 속성으로 선택된 특성은 업데이트 작업 시 Harness의 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

    ![Harness 그룹 "특성 매핑" 창](media/harness-provisioning-tutorial/groupattributes.png)

1. 범위 지정 필터를 구성하려면 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비저닝](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

1. Harness에 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 에서 **프로비전 상태** 스위치를 **켜기** 로 전환합니다.

    ![프로비전 상태 스위치를 "켜기"로 전환](common/provisioning-toggle-on.png)

1. **설정** 의 **범위** 드롭다운 목록에서, Harness에 프로비저닝하는 사용자 또는 그룹을 동기화하는 방법을 선택합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로비저닝 저장 단추](common/provisioning-configuration-save.png)

이 작업을 수행하면 프로비저닝하는 사용자 또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 이후 동기화보다 오래 걸립니다. 동기화는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분 간격으로 발생합니다. 진행률을 모니터링하려면 **동기화 세부 정보** 섹션으로 이동합니다. Harness의 Azure AD 프로비저닝 서비스에서 수행되는 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따라 이동해도 됩니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).