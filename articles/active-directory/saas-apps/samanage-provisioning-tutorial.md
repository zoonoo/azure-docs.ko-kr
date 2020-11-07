---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 SolarWinds 서비스 데스크 (이전 Samanage) 구성 Microsoft Docs'
description: 사용자 계정을 Azure AD에서 SolarWinds Service Desk (이전에 Samanage)로 자동으로 프로 비전 및 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 5cdc36c20cbba148bb68bda700f5fdccbc593caf
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94353002"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 SolarWinds 서비스 데스크 (이전 Samanage) 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 SolarWinds 서비스 데스크 (이전 Samanage) 및 Azure Active Directory (Azure AD)에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure ad는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 [SolarWinds 서비스 데스크](https://www.samanage.com/pricing/) 에 자동으로 프로 비전 및 프로 비전 해제 합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>새 SolarWinds Service Desk 응용 프로그램으로 마이그레이션

SolarWinds Service Desk와 기존 통합이 있는 경우 예정 된 변경 내용에 대 한 다음 섹션을 참조 하세요. SolarWinds Service Desk를 처음으로 설정 하는 경우이 섹션을 건너뛰고 **지원 되는 기능** 으로 이동할 수 있습니다.

#### <a name="whats-changing"></a>변경 내용은?

* Azure AD 쪽 변경: Samange에서 사용자를 프로 비전 하는 권한 부여 방법이 **기본 인증** 입니다. 곧 권한 부여 방법이 **Long 존속 비밀 토큰** 으로 변경 된 것을 볼 수 있습니다.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>기존 사용자 지정 통합을 새 응용 프로그램으로 마이그레이션하려면 어떻게 해야 하나요?

유효한 관리자 자격 증명과 기존 SolarWinds Service Desk를 통합 하는 경우에 **는 아무 조치도 필요 하지** 않습니다. 새 응용 프로그램으로 고객을 자동으로 마이그레이션합니다. 이 프로세스는 백그라운드에서 완전히 수행 됩니다. 기존 자격 증명이 만료 되거나 응용 프로그램에 대 한 액세스 권한을 다시 부여 해야 하는 경우에는 수명이 긴 비밀 토큰을 생성 해야 합니다. 새 토큰을 생성 하려면이 문서의 2 단계를 참조 하세요.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>응용 프로그램이 마이그레이션 되었는지 어떻게 알 수 있나요? 

응용 프로그램이 마이그레이션되면 관리자 **자격 증명** 섹션에서 **관리자 사용자 이름** 및 **관리자 암호** 필드는 단일 **비밀 토큰** 필드로 대체 됩니다.

## <a name="capabilities-supported"></a>지원되는 기능

> [!div class="checklist"]
> * SolarWinds Service Desk에서 사용자 만들기
> * 더 이상 액세스 하지 않아도 되는 SolarWinds 서비스 데스크 사용자 제거
> * Azure AD와 SolarWinds Service Desk 간에 사용자 특성을 동기화 상태로 유지
> * SolarWinds Service Desk에서 그룹 및 그룹 멤버 자격 프로 비전
> * SolarWinds Service Desk에 대 [한 Single sign-on](./samanage-tutorial.md) (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../users-groups-roles/directory-assign-admin-roles.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* 전문 패키지를 사용 하는 [SolarWinds Service Desk 테 넌 트](https://www.samanage.com/pricing/)
* 관리자 권한이 있는 SolarWinds Service Desk의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [AZURE AD와 SolarWinds Service Desk 간에 매핑할](../app-provisioning/customize-application-attributes.md)데이터를 결정 합니다. 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>2단계. SolarWinds Service Desk를 구성 하 여 Azure AD로 프로 비전 지원

인증용 비밀 토큰을 생성 하려면 [자습서 토큰 인증 API 통합](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)을 참조 하세요.

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 응용 프로그램 갤러리에서 SolarWinds Service Desk 추가

Azure AD 응용 프로그램 갤러리에서 SolarWinds Service Desk를 추가 하 여 SolarWinds Service Desk로 프로 비전 관리를 시작 합니다. 이전에 SSO에 대해 SolarWinds Service Desk를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* SolarWinds Service Desk에 사용자 및 그룹을 할당할 때 **기본 액세스** 외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>5단계. SolarWinds Service Desk에 자동 사용자 프로 비전 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Azure AD에서 SolarWinds 서비스 데스크에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** , **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **SolarWinds Service Desk** 를 선택 합니다.

3. **프로비전** 탭을 선택합니다.

    ![선택 된 프로 비전 탭을 보여 주는 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동으로 설정 된 프로 비전 모드를 보여 주는 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에 `https://api.samanage.com` 있는 **테 넌 트 URL** 에 입력 합니다.  **비밀 토큰** 에 이전에 검색된 비밀 토큰 값을 입력합니다. **연결 테스트** 를 선택 하 여 Azure AD가 SolarWinds Service Desk에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 SolarWinds Service Desk 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![선택 된 연결 테스트 단추를 보여 주는 스크린샷](./media/samanage-provisioning-tutorial/provisioning.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션 아래에서 **SolarWinds Service Desk에 Azure Active Directory 사용자 동기화를** 선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 SolarWinds Service Desk로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 SolarWinds Service Desk의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](../app-provisioning/customize-application-attributes.md)을 변경 하도록 선택 하는 경우 SolarWinds SERVICE Desk API가 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      ![Samange 사용자 매핑](./media/samanage-provisioning-tutorial/user-attributes.png)

10. **매핑** 섹션 아래에서 **SolarWinds Service Desk에 Azure Active Directory 그룹 동기화를** 선택 합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 SolarWinds Service Desk로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 SolarWinds Service Desk의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      ![Samange 그룹 매핑](./media/samanage-provisioning-tutorial/group-attributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. SolarWinds Service Desk에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 SolarWinds Service Desk에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로 비전 할 준비가 되 면 **저장** 을 선택 합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](../app-provisioning/application-provisioning-quarantine-status.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

**모든 사용자 및 그룹 동기화** 옵션을 선택 하 고 SolarWinds 서비스 데스크 **역할** 특성에 대 한 값을 구성 하는 경우 **null (옵션)** 상자의 값은 다음 형식으로 표시 되어야 합니다.

- {"displayName": "role"}. 여기서 role은 원하는 기본값입니다.

## <a name="change-log"></a>로그 변경

* 09/14/2020-Samanage에서 SolarWinds Service Desk (이전에 Samanage)의 두 SaaS 자습서로 회사 이름을 변경 했습니다 https://github.com/ravitmorales .
* 04/22/2020-기본 인증에서 수명이 긴 비밀 토큰으로 권한 부여 방법이 업데이트 되었습니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).