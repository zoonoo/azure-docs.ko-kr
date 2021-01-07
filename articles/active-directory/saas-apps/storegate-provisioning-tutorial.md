---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 Storegate 구성 | Microsoft Docs'
description: 사용자 계정을 Storegate로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357945"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Storegate 구성

이 자습서에서는 사용자 및/또는 그룹을 Storegate로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Storegate 및 Azure AD에서 수행하는 단계를 보여줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Storegate 테넌트](https://www.storegate.com)
* 관리자 권한이 있는 Storegate의 사용자 계정

## <a name="assign-users-to-storegate"></a>Storegate에 사용자 할당

Azure Active Directory는 할당이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 할당된 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에 Storegate에 액세스해야 하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 일단 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Storegate에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Storegate에 사용자를 할당하기 위한 주요 팁

* 단일 Azure AD 사용자를 Storegate에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Storegate에 할당할 때 할당 대화 상자에서 유효한 애플리케이션별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비저닝에서 제외됩니다.

## <a name="set-up-storegate-for-provisioning"></a>프로비저닝을 위한 Storegate 설정

Azure AD를 사용하여 자동 사용자 프로비저닝을 수행하도록 Storegate를 구성하려면 먼저 Storegate에서 일부 프로비저닝 정보를 검색해야 합니다.

1. [Storegate 관리 콘솔](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)에 로그인하고 오른쪽 위 모서리에 있는 사용자 아이콘을 클릭하여 설정으로 이동하여 **계정 설정** 을 선택합니다.

    ![Storegate 추가 SCIM](media/storegate-provisioning-tutorial/admin.png)

2. 설정 내에서 **팀> 설정** 으로 이동하고 **Single Sign-On** 섹션에서 토글 스위치가 켜져 있는지 확인합니다.

    ![Storegate 설정](media/storegate-provisioning-tutorial/team.png)

    ![Storegate 토글 단추](media/storegate-provisioning-tutorial/sso.png)

3. **테넌트 URL** 및 **토큰** 을 복사합니다. 이러한 값은 Azure Portal의 Storegate 애플리케이션 프로비저닝 탭에서 각각 **테넌트 URL** 및 **비밀 토큰** 필드에 입력됩니다. 

    ![Storegate 토큰 만들기](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>갤러리에서 Storegate 추가

Azure AD를 사용하여 자동 사용자 프로비저닝을 위해 Storegate을 구성하려면 Storegate을 Azure AD 애플리케이션 갤러리에서 관리형 SaaS 애플리케이션 목록으로 추가해야 합니다.

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 를 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 창 위쪽에서 **새 애플리케이션** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Storegate** 를 입력하고 결과 패널에서 **Storegate** 를 선택합니다. 

    ![결과 목록의 Storegate](common/search-new-app.png)

5. **Storegate에 가입** 단추를 선택하여 Storegate 로그인 페이지로 리디렉션합니다. 

    ![Storegate OIDC 추가](media/storegate-provisioning-tutorial/signup.png)

6.  [Storegate 관리 콘솔](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367)에 로그인하고 오른쪽 위 모서리에 있는 사용자 아이콘을 클릭하여 설정으로 이동하여 **계정 설정** 을 선택합니다.

    ![Storegate 로그인](media/storegate-provisioning-tutorial/admin.png)

7. 설정 내에서 **팀> 설정** 으로 이동하고 Single Sign-On 섹션에서 토글 스위치를 클릭하면 동의 과정이 시작됩니다. **활성화** 를 클릭합니다.

    ![Storegate 팀](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate 활성화](media/storegate-provisioning-tutorial/activate.png)

8. Storegate는 OpenIDConnect 앱이므로 Microsoft 회사 계정을 사용하여 Storegate에 로그인하도록 선택합니다.

    ![Storegate OIDC 로그인](media/storegate-provisioning-tutorial/login.png)

9. 인증에 성공하면 동의 페이지에 대한 동의 확인 프롬프트를 수락합니다. 그러면 애플리케이션이 테넌트에 자동으로 추가되고 Storegate 계정으로 리디렉션됩니다.

    ![Storegate OIDC 동의](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Storegate에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 Storegate에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!NOTE]
> Storegate의 SCIM 엔드포인트에 대한 자세한 내용은 [이](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)를 참조하세요.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Azure AD에서 Storegate를 위한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Storegate** 를 선택합니다.

    ![애플리케이션 목록의 Storegate 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비저닝 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 호출된 프로비저닝 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래의 **테넌트 URL** 에 `https://dialpad.com/scim`를 입력합니다. 이전에 Storegate에서 검색하고 저장한 값을 **비밀 토큰** 에 입력합니다. **연결 테스트** 를 클릭하여 Azure AD가 Storegate에 연결할 수 있는지 확인합니다. 연결에 실패하면 Storegate 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 클릭합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Storegate에 동기화** 를 선택합니다.

    ![Storegate 사용자 매핑](media/storegate-provisioning-tutorial/usermappings.png)

9. **특성 매핑** 섹션에서 Azure AD에서 Storegate로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Storegate의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Storegate 사용자 특성](media/storegate-provisioning-tutorial/userattributes.png)

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Storegate에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비저닝 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 의 **범위** 섹션에서 원하는 값을 선택하여 Storegate에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비저닝 범위](common/provisioning-scope.png)

13. 프로비저닝할 준비가 되면 **저장** 을 클릭합니다.

    ![프로비저닝 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고, Storegate의 Azure AD 프로비저닝 서비스에서 수행한 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비저닝 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
