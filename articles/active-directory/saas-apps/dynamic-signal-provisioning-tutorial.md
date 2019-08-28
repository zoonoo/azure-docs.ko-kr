---
title: '자습서: Azure Active Directory로 자동 사용자 프로 비전에 대 한 Dynamic Signal 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 Dynamic Signal에 사용자 계정을 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: f9bfa0eaea67919bad775af5cf9071cd3d6973ca
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672661"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Dynamic Signal 구성

이 자습서의 목적은 자동으로 프로 비전 하 고 사용자 및/또는 그룹 Dynamic Signal을 프로 비전 해제 하도록 Azure AD를 구성 하려면 Dynamic Signal 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여 주기 위해입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 일반적인 Microsoft Azure 사용 약관 미리 보기 기능에 대 한 자세한 내용은 참조 하세요. [사용 특약 조건의 Microsoft Azure 미리 보기에 대 한](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)합니다.

## <a name="prerequisites"></a>전제 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Dynamic Signal 테 넌 트](https://dynamicsignal.com/)
* 관리자 권한으로 Dynamic Signal에서 사용자 계정.

## <a name="add-dynamic-signal-from-the-gallery"></a>갤러리에서 Dynamic Signal을 추가 합니다.

Dynamic Signal 자동 사용자를 Azure AD를 사용 하 여 프로 비전을 구성 하기 전에 Azure AD 응용 프로그램 갤러리에서 Dynamic Signal을 관리 되는 SaaS 응용 프로그램의 목록에 추가 해야 합니다.

**Azure AD 응용 프로그램 갤러리에서 Dynamic Signal을 추가 하려면 다음 단계를 수행 합니다.**

1. 에  **[Azure portal](https://portal.azure.com)** , 왼쪽된 탐색 패널에서 선택 **Azure Active Directory**합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 선택 합니다 **새 응용 프로그램** 창의 맨 위에 있는 단추입니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 입력 **Dynamic Signal**를 선택 **Dynamic Signal** 클릭 한 다음 확인 하 고 결과 패널에서 합니다 **추가** 단추는 응용 프로그램을 추가 합니다.

    ![결과 목록의 Dynamic Signal](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Dynamic Signal에 사용자 할당

이라는 개념을 사용 하 여 azure Active Directory *할당* 어떤 사용자가 선택한 앱에 대 한 액세스를 받아야 하는지를 판단 합니다. 자동 사용자 프로 비전의 컨텍스트에서의 사용자 및/또는 Azure AD에서 응용 프로그램에 할당 된 그룹 동기화 됩니다.

를 구성 하 고 자동 사용자 프로 비전을 사용 하도록 설정 하기 전에 Dynamic Signal에 대 한 액세스를 해야 하는 사용자 및/또는 Azure AD의 그룹 결정 해야 합니다. 결정 했으면 할당할 수 있습니다 이러한 사용자 및/또는 그룹을 Dynamic Signal 여기의 지침에 따라.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Dynamic Signal에 사용자를 할당 하기 위한 주요 팁

* 것이 좋습니다는 단일 Azure AD 사용자가 자동 사용자 프로 비전 구성을 테스트 하려면 Dynamic Signal에 할당 합니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* Dynamic Signal에 사용자를 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)를 선택 해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Dynamic Signal에 자동 사용자 프로비저닝 구성 

이 섹션에서는 생성, 업데이트 및 사용자 및/또는 Azure AD의 사용자 및/또는 그룹 할당에 따라 Dynamic Signal에서 그룹을 사용 하지 않도록 설정 하려면 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

> [!TIP]
> 제공한 지침에 따라 SAML 기반 single sign-on 사용 Dynamic Signal에 대 한 수도 있습니다, 합니다 [Dynamic Signal single sign-on 자습서](dynamicsignal-tutorial.md)합니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Azure AD에서 Dynamic Signal에 대 한 자동 사용자 프로 비전을 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 선택 **엔터프라이즈 응용 프로그램**을 선택한 후 **모든 응용 프로그램**합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Dynamic Signal**을 선택합니다.

    ![애플리케이션 목록의 Dynamic Signal 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로 비전 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 탭](common/provisioning-automatic.png)

5. 아래는 **관리자 자격 증명** 섹션에서 입력 합니다 **테 넌 트 URL** 및 **비밀 토큰** 6 단계에에서 설명 된 대로 Dynamic Signal의 계정.

6. Dynamic Signal 관리 콘솔에서로 이동 **관리 > 고급 > API**합니다.

    ![Dynamic Signal 프로 비전](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    복사 합니다 **SCIM API URL** 하 **테 넌 트 URL**합니다. 클릭할 **새 토큰 생성** 생성 하는 **전달자 토큰** 값을 복사한 **비밀 토큰**합니다.

    ![Dynamic Signal 프로 비전](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. 5 단계에서에서 표시 된 필드를 채우면 클릭 **연결 테스트** 를 azure AD는 Dynamic Signal에 연결할 수 있습니다. 연결에 실패 하면 Dynamic Signal 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![테 넌 트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

8. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 전자 메일](common/provisioning-notification-email.png)

9. **Save**을 클릭합니다.

10. 아래는 **매핑을** 섹션에서 **동기화 할 Azure Active Directory 사용자를 Dynamic Signal**합니다.

    ![Dynamic Signal 사용자 매핑](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Dynamic signal에서 Azure AD에서 동기화 되는 사용자 특성을 검토 합니다 **특성 매핑** 섹션입니다. 으로 선택한 특성 **일치** 속성 업데이트 작업에 대 한 Dynamic Signal에서 사용자 계정을 일치 시키는 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Dynamic Signal 사용자 특성](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Dynamic Signal에 대 한 서비스를 프로 비전 하는 Azure AD를 사용 하도록 설정 하려면 변경 합니다 **프로 비전 상태** 에 **에** 에 **설정** 섹션.

    ![프로 비전 상태에서 전환](common/provisioning-toggle-on.png)

14. Dynamic Signal에 프로 비전에서 원하는 값을 선택 하 여 사용자 및/또는 하려는 그룹을 정의 **범위** 에 **설정** 섹션입니다.

    ![프로 비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로 비전 구성을 저장 합니다.](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. 사용할 수는 **동기화 세부 정보** 진행률을 모니터링 하 고 링크를 프로 비전 활동 보고서를 프로 비전 서비스 Dynamic Signal에서 Azure AD에서 수행한 모든 작업을 설명 하는 섹션입니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Dynamic Signal에서 Azure AD에서 영구 사용자 삭제를 지원 하지 않습니다. Dynamic Signal에 영구적으로 사용자를 삭제 하려면 작업 Dynamic Signal 관리 콘솔 UI 통해 수행 해야 합니다. 
* Dynamic Signal 현재 그룹을 지원 하지 않습니다.

## <a name="additional-resources"></a>추가 자료

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).

