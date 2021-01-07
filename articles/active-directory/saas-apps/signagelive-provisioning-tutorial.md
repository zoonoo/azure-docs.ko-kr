---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 Signagelive 구성 | Microsoft Docs'
description: 사용자 계정을 Signagelive로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 10ad06041e8136b5661b1b1ff487cd4d3b0f5153
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358408"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Signagelive 구성

이 자습서에서는 사용자 및/또는 그룹을 Signagelive로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Signagelive 및 Azure AD에서 수행하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트.
* [Signagelive 테넌트](https://signagelive.com/pricing/)
* 관리자 권한이 있는 Signagelive의 사용자 계정

## <a name="assigning-users-to-signagelive"></a>Signagelive에 사용자 할당   

Azure Active Directory는 *할당* 이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 할당된 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Signagelive에 액세스해야 하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 일단 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Signagelive에 할당할 수 있습니다.
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Signagelive에 사용자를 할당하기 위한 주요 팁   

* 단일 Azure AD 사용자를 Signagelive에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Signagelive에 할당할 때 할당 대화 상자에서 유효한 애플리케이션별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="setup-signagelive--for-provisioning"></a>프로비저닝을 위한 Signagelive 설정

Azure AD를 사용하여 자동 사용자 프로비저닝을 위해 Signagelive를 구성하기 전에 Signagelive에서 SCIM 프로비저닝을 사용하도록 설정해야 합니다.

  SCIM 프로비저닝을 구성하는 데 필요한 비밀 토큰을 얻기 위해 [Signagelive](mailto:development@signagelive.com)에 연결합니다.

## <a name="add-signagelive-from-the-gallery"></a>갤러리에서 Signagelive 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 위해 Signagelive를 구성하려면 Signagelive를 Azure AD 애플리케이션 갤러리에서 관리형 SaaS 애플리케이션 목록으로 추가해야 합니다.

**Azure AD 애플리케이션 갤러리에서 Signagelive를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 창 위쪽에서 **새 애플리케이션** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Signagelive** 를 입력하고, 결과 패널에서 **Signagelive** 를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Signagelive](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Signagelive에 자동 사용자 프로비저닝 구성    

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 Signagelive에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
>  [Signagelive Single Sign-On 자습서](Signagelive-tutorial.md)에서 제공한 지침에 따라 Signagelive에 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Azure AD에서 Signagelive에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Signagelive** 를 선택합니다.

    ![애플리케이션 목록의 Signagelive 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. ` https://samlapi.signagelive.com/scim/v2`관리자 자격 증명 **섹션 아래의 테넌트 URL에** 를 입력합니다. **비밀 토큰** 필드에 엔지니어링 개발 팀에서 제공하는 **전달자 토큰** 값을 입력합니다. **연결 테스트** 를 클릭하여 Azure AD가 Signagelive에 연결할 수 있는지 확인합니다. 연결에 실패하면 Signagelive 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.
    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 클릭합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Signagelive에 동기화** 를 선택합니다.

    ![Azure Active Directory 사용자를 Signagelive로 동기화 옵션이 호출된 매핑 섹션의 스크린샷](media/signagelive-provisioning-tutorial/usermapping.png)

9. **특성 매핑** 섹션에서 Azure AD에서 Signagelive로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Signagelive의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![7개의 매핑이 표시된 특성 매핑 섹션의 스크린샷.](media/signagelive-provisioning-tutorial/userattribute.png)

10. **매핑** 섹션에서 **Azure Active Directory 그룹을 Signagelive에 동기화** 를 선택합니다.

    ![Azure Active Directory 그룹을 Signagelive로 동기화 옵션이 호출된 매핑 섹션의 스크린샷](media/signagelive-provisioning-tutorial/groupmapping.png)

11. **특성 매핑** 섹션에서 Azure AD에서 Signagelive로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Signagelive의 그룹 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![3개의 매핑이 표시된 특성 매핑 섹션의 스크린샷.](media/signagelive-provisioning-tutorial/groupattribute.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Signagelive에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 Signagelive에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화를 수행하는 경우 후속 동기화보다 더 오랜 시간이 걸립니다. 사용자 및/또는 그룹을 프로비저닝하는 데 걸리는 시간에 대한 자세한 내용은 [사용자를 프로비저닝하는 데 걸리는 시간](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)을 참조하세요. 

**현재 상태** 섹션을 사용하여 진행 상황을 모니터링하고, Signagelive의 Azure AD 프로비저닝 서비스에서 수행한 모든 작업을 설명하는 프로비저닝 작업 보고서에 대한 링크를 따를 수 있습니다. 자세한 내용은 [사용자 프로비저닝 상태 확인](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 참조하세요. Azure AD 프로비저닝 로그를 읽으려면 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
