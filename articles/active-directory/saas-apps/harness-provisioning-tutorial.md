---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝에 대 한 하 네스 티 를 구성 | 마이크로 소프트 문서'
description: Azure Active Directory를 구성하여 사용자 계정을 Harness에 자동으로 프로비전 및 프로비저닝 해제하도록 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057841"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 하네스 구성

이 문서에서는 사용자 또는 그룹을 Harness에 자동으로 프로비전및 프로비저닝해제하도록 Azure Active Directory(Azure AD)를 구성하는 방법을 알아봅니다.

> [!NOTE]
> 이 문서에서는 Azure AD 사용자 프로비전 서비스 위에 구축된 커넥터에 대해 설명합니다. 이 서비스에 대한 중요한 정보와 자주 묻는 질문에 대한 답변은 [Azure Active Directory를 사용하여 SaaS 응용 프로그램에 대한 사용자 프로비전 자동화 및 프로비저닝 해제를](../app-provisioning/user-provisioning.md)참조하십시오.
>
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서 설명하는 시나리오에서는 이미 다음과 같은 필수 구성 조건이 있다고 가정합니다.

* Azure AD 테넌트
* [하네스 테넌트](https://harness.io/pricing/)
* *관리자* 권한이 있는 하네스의 사용자 계정

## <a name="assign-users-to-harness"></a>하네스에 사용자 할당

Azure Active Directory는 *할당이라는* 개념을 사용하여 선택한 앱에 대한 액세스 권한을 받아야 하는 사용자를 결정합니다. 자동 사용자 프로비저닝의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Azure AD에서 Harness에 액세스해야 하는 사용자 또는 그룹을 결정합니다. 그런 다음 엔터프라이즈 앱에 사용자 또는 그룹 할당의 지침에 따라 이러한 사용자 [또는 그룹을 Harness에 할당할 수](../manage-apps/assign-user-or-group-access-portal.md)있습니다.

## <a name="important-tips-for-assigning-users-to-harness"></a>사용자를 하네스에 할당하기 위한 중요한 팁

* 단일 Azure AD 사용자를 Harness에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 나중에 추가 사용자 또는 그룹을 할당할 수 있습니다.

* 사용자를 Harness에 할당할 때 **할당** 대화 상자에서 유효한 응용 프로그램 별 역할(사용 가능한 경우)을 선택해야 합니다. *기본 액세스* 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-harness-for-provisioning"></a>프로비저닝을 위한 하네스 설정

1. [하네스 관리 콘솔에](https://app.harness.io/#/login)로그인한 다음 **지속적인 보안** > **액세스 관리로**이동합니다.

    ![하네스 관리 콘솔](media/harness-provisioning-tutorial/admin.png)

1. **API 키를 선택합니다.**

    ![API 키 링크 하네스](media/harness-provisioning-tutorial/apikeys.png)

1. **API 키 추가를 선택합니다.** 

    ![API 키 링크 추가 하기](media/harness-provisioning-tutorial/addkey.png)

1. API **키 추가** 창에서 다음을 수행합니다.

    ![API 키 창 추가 하기](media/harness-provisioning-tutorial/title.png)
   
   a. **이름** 상자에 키이름을 지정합니다.  
   b. 드롭다운 목록에서 **상속된 사용 권한에서** 옵션을 선택합니다. 
   
1. **제출**을 선택합니다.

1. 이 자습서에서 나중에 사용할 수 있는 **키를** 복사합니다.

    ![하네스 토큰 만들기](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>갤러리에서 하네스 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 위해 Harness를 구성하기 전에 Azure AD 응용 프로그램 갤러리에서 관리되는 SaaS 응용 프로그램 목록에 Harness를 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    !["Azure Active Directory" 단추](common/select-azuread.png)

1. **엔터프라이즈 응용 프로그램** > 선택**모든 응용 프로그램**.

    !["모든 애플리케이션" 링크](common/enterprise-applications.png)

1. 새 응용 프로그램을 추가하려면 창 상단의 **새 응용 프로그램** 단추를 선택합니다.

    !["새 애플리케이션" 단추](common/add-new-app.png)

1. 검색 상자에서 **하네스를**입력하고 결과 목록에서 **하네스를** 선택한 다음 **추가** 단추를 선택하여 응용 프로그램을 추가합니다.

    ![결과 목록에서 활용](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>하네스에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 또는 그룹 할당을 기반으로 Harness에서 사용자 또는 그룹을 생성, 업데이트 및 사용하지 않도록 설정하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> 하네스 단일 [사인온 자습서의](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)지침에 따라 하네스에 대한 SAML 기반 단일 사인온을 사용하도록 선택할 수도 있습니다. 이 두 기능이 서로 보완되지만 자동 사용자 프로비저닝과 는 별개로 단일 사인온을 구성할 수 있습니다.

> [!NOTE]
> SCIM 하네스 끝점에 대한 자세한 내용은 [하네스 API 키](https://docs.harness.io/article/smloyragsm-api-keys) 문서를 참조하십시오.

Azure AD에서 Harness에 대한 자동 사용자 프로비전을 구성하려면 다음을 수행합니다.

1. Azure [포털에서](https://portal.azure.com) **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램을 선택합니다.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Harness**를 선택합니다.

    ![응용 프로그램 목록의 하네스 링크](common/all-applications.png)

1. **프로비저닝 을 선택합니다.**

    ![프로비저닝 단추](common/provisioning.png)

1. **프로비저닝 모드** 드롭다운 목록에서 **자동**을 선택합니다.

    !["프로비저닝 모드" 드롭다운 목록](common/provisioning-automatic.png)

1. 관리자 자격 증명에서 다음을 **수행합니다.**

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)
 
   a. **테넌트 URL** 상자에 **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** 를 입력합니다.  
   b. 비밀 **토큰** 상자에 "프로비저닝을 위한 하네스 설정" 섹션의 6단계에서 저장한 SCIM 인증 토큰 값을 입력합니다.  
   다. Azure AD가 하네스에 연결할 수 있는지 확인하려면 **테스트 연결을** 선택합니다. 연결이 실패하면 하네스 계정에 *관리자* 권한이 있는지 확인하고 다시 시도하십시오.

1. 알림 **전자 메일** 상자에 프로비저닝 오류 알림을 받아야 하는 개인 또는 그룹의 전자 메일 주소를 입력한 다음 오류가 발생할 때 전자 메일 알림 보내기 확인란을 선택합니다. **Send an email notification when a failure occurs**

    !["알림 이메일" 상자](common/provisioning-notification-email.png)

1. **저장**을 선택합니다.

1. **매핑에서**Azure **Active Directory 사용자를 사용하여 사용할 동기화를**선택합니다.

    !["Azure Active Directory 사용자를 Harness로 동기화" 링크 사용](media/harness-provisioning-tutorial/usermappings.png)

1. **특성 매핑에서**Azure AD에서 Harness로 동기화된 사용자 특성을 검토합니다. *일치로* 선택된 특성은 업데이트 작업에 대한 Harness의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![사용자 "특성 매핑" 창 활용](media/harness-provisioning-tutorial/userattributes.png)

1. **매핑에서**사용할 **Azure Active Directory 그룹 동기화를 선택합니다.**

    !["Azure Active Directory 그룹을 Harness로 동기화" 링크 사용](media/harness-provisioning-tutorial/groupmappings.png)

1. **특성 매핑에서**Azure AD에서 Harness로 동기화된 그룹 특성을 검토합니다. *일치* 속성으로 선택한 특성은 업데이트 작업에 대한 Harness의 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![하네스 그룹 "특성 매핑" 창](media/harness-provisioning-tutorial/groupattributes.png)

1. 범위 지정 필터를 구성하려면 [범위 지정 필터를 사용하여 특성 기반 응용 프로그램 프로비저닝을](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)참조하십시오.

1. **설정에서**Harness에 대한 Azure AD 프로비저닝 서비스를 활성화하려면 **프로비저닝 상태** 스위치를 **켜기로**전환합니다.

    ![프로비저닝 상태 스위치가 "켜기"로 전환됨](common/provisioning-toggle-on.png)

1. **설정**에서 **범위** 드롭다운 목록에서 Harness에 프로비전할 사용자 또는 그룹을 동기화하는 방법을 선택합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비전할 준비가 되면 **저장을**선택합니다.

    ![프로비저닝 저장 버튼](common/provisioning-configuration-save.png)

이 작업은 프로비저닝하려는 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 이후 동기화보다 수행하는 데 시간이 오래 걸립니다. 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생합니다. 진행률을 모니터링하려면 **동기화 세부 정보** 섹션으로 이동하십시오. Harness에서 Azure AD 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따를 수도 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고서를](../app-provisioning/check-status-user-account-provisioning.md)참조하십시오.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
