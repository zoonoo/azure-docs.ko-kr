---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Workplace by Facebook 구성 | Microsoft Docs'
description: Azure Active Directory와 Workplace by Facebook 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603216"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>자습서: 사용자 프로비전을 위한 Workplace by Facebook 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Facebook 및 Azure Active Directory(Azure AD)의 Workplace에서 수행해야 하는 단계를 설명합니다. 구성되면 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 Facebook에서 사용자 및 그룹을 [Workplace로](https://work.workplace.com/) 자동으로 프로비전하고 프로비전 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>페이스 북 응용 프로그램에 의해 새로운 직장으로 마이그레이션
Facebook의 Workplace와 기존 통합이 있는 경우 향후 변경 사항에 대한 아래 섹션을 참조하세요. Facebook에서 Workplace를 처음 설정하는 경우 이 섹션을 건너뛰고 지원되는 기능으로 이동할 수 있습니다. 

#### <a name="whats-changing"></a>변경 내용은?
* Azure AD 측의 변경 사항: Workplace에서 사용자를 프로비전하는 권한 부여 방법은 역사적으로 오래 지속되는 비밀 토큰이었습니다. 곧 승인 방법이 OAuth 권한 부여로 변경된 것을 볼 수 있습니다. 
* 직장 측면의 변경 사항: 이전에는 Azure AD 앱이 Facebook의 Workplace에서 사용자 지정 통합이었습니다. 이제 Workplace 통합 디렉터리에서 Azure AD를 타사 응용 프로그램으로 볼 수 있습니다. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>기존 사용자 지정 통합을 새 응용 프로그램으로 마이그레이션하려면 어떻게 해야 합니까?
유효한 토큰과 기존 Workplace 통합이 있는 경우 **작업이 필요하지 않습니다.** 매주 고객을 새 응용 프로그램으로 자동으로 마이그레이션합니다. 이것은 무대 뒤에서 완전히 이루어집니다. 기다릴 수 없고 새 응용 프로그램으로 수동으로 이동하려는 경우 갤러리에서 Workplace의 새 인스턴스를 추가하고 프로비저닝을 다시 구성할 수 있습니다. Workplace의 모든 새 인스턴스는 자동으로 새 응용 프로그램 버전을 사용합니다. 

 
Workplace 통합이 격리된 경우 마이그레이션하려면 유효한 토큰을 다시 제공해야 합니다. 관리자 자격 증명 섹션은 회색으로 표시됩니다.하지만 다음 **(? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true)를**URL에 사용하여 자격 증명을 다시 저장합니다. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>응용 프로그램이 마이그레이션되었는지 어떻게 알 수 있습니까? 
응용 프로그램이 마이그레이션되면 변경 내용 수정에 대한 권한 부여 섹션의 배너가 제거되고 비밀 토큰 필드가 파란색 권한 부여 단추로 바뀝니다. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>관리자 자격 증명 섹션이 내 응용 프로그램에서 회색으로 표시됩니다. 그 이유는
기존 Workplace 고객을 위한 관리자 자격 증명 섹션을 잠급했습니다. 테넌트가 새 Workplace 응용 프로그램으로 마이그레이션되면 관리자 자격 증명 섹션을 다시 업데이트할 수 있습니다. 기다릴 수 없는 경우 위의 URL을 사용하여 응용 프로그램을 편집할 수 있습니다. 

 
#### <a name="when-will-these-changes-happen"></a>이러한 변경 사항은 언제 발생합니까?
Workplace의 모든 새 인스턴스는 이미 새 통합/권한 부여 방법을 사용하고 있습니다. 기존 통합은 5월까지 점진적으로 마이그레이션될 예정입니다. 작업장 팀은 2월 28일부터 5월 1일까지 기한을 연장했습니다. 

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * 페이스 북에 의해 직장에서 사용자 만들기
> * 더 이상 액세스가 필요하지 않은 경우 Facebook에서 Workplace에서 사용자를 제거합니다.
> * Azure AD와 Workplace 간에 Facebook사용자 속성 동기화 유지
> * 페이스 북에 의해 직장에 [단일 로그인](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로비저닝을 구성할 [수 있는 권한이](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 있는 Azure AD의 사용자 계정(예: 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 글로벌 관리자)
* Workplace by Facebook Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없는 경우 여기에서 1개월 평가판을 얻을 수 [있습니다.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식에](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)대해 알아봅니다.
2. [프로비저닝 범위에](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)속할 사람을 결정합니다.
3. [Facebook에서 Azure AD와 Workplace 간에 매핑할 데이터를 결정합니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD로 프로비전을 지원하도록 Facebook에서 Workplace 구성

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Workplace by Facebook 앱에 액세스해야 하는 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Workplace by Facebook 앱에 할당할 수 있습니다.

*   단일 Azure AD 사용자를 Workplace by Facebook에 할당하여 프로비전 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   Workplace by Facebook에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 Facebook별 작업 공간 추가

Azure AD 응용 프로그램 갤러리에서 Facebook별 Workplace를 추가하여 Facebook에서 Workplace로 프로비저닝관리를 시작합니다. 이전에 SSO에 대한 페이스 북에 의해 직장을 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트할 때 별도의 앱을 만드는 것이 좋습니다. 갤러리에서 응용 프로그램을 추가하는 방법에 대한 자세한 내용은 [여기를](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)참조하십시오.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 속할 사람 정의 

Azure AD 프로비전 서비스를 사용하면 응용 프로그램에 할당하거나 사용자/그룹의 특성을 기반으로 프로비전할 사용자를 범위를 지정할 수 있습니다. 할당에 따라 앱에 프로비전할 사용자를 지정하는 경우 다음 [단계를](../manage-apps/assign-user-or-group-access-portal.md) 사용하여 응용 프로그램에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기반으로 프로비전할 범위를 지정하는 경우 [여기에](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* Facebook에서 Workplace에 사용자 및 그룹을 할당할 때 **기본 액세스**가 아닌 다른 역할을 선택해야 합니다. 기본 액세스 역할을 가진 사용자는 프로비저닝에서 제외되며 프로비저닝 로그에서 효과적으로 사용할 수 없는 것으로 표시됩니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [응용 프로그램 매니페스트를 업데이트하여](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 추가 역할을 추가할 수 있습니다. 

* 작게 시작하십시오. 모든 사용자에게 배포하기 전에 작은 사용자 및 그룹으로 테스트합니다. 프로비저닝 범위가 할당된 사용자 및 그룹으로 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 이를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정된 경우 [특성 기반 범위 지정 필터를](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)지정할 수 있습니다. 

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램을**선택한 다음 모든 응용 프로그램을 **선택합니다.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Workplace by Facebook**을 선택합니다.

    ![애플리케이션 목록의 Workplace by Facebook 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. 관리자 자격 증명 섹션에서 **권한 부여를** **클릭합니다.** 당신은 페이스 북의 승인 페이지에 의해 직장으로 리디렉션됩니다. Facebook 사용자 이름으로 Workplace를 입력하고 **계속** 버튼을 클릭합니다. Azure AD가 Facebook에서 Workplace에 연결할 수 있는지 확인하려면 **테스트 연결을** 클릭합니다. 연결이 실패하면 Facebook 계정의 Workplace에 관리자 권한이 있는지 확인하고 다시 시도하세요.

    ![프로비전](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![권한 부여](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. 알림 **전자 메일** 필드에 프로비저닝 오류 알림을 받아야 하는 개인 또는 그룹의 전자 메일 주소를 입력하고 오류가 발생할 때 전자 메일 알림 보내기 확인란을 선택합니다. **Send an email notification when a failure occurs**

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Facebook에서 Workplace로 동기화를**선택합니다.

9. **속성 매핑** 섹션에서 Facebook에서 Azure AD에서 Workplace로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Workplace by Facebook의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성을](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)변경하도록 선택한 경우 Facebook API의 Workplace에서 해당 특성을 기반으로 사용자를 필터링하는 것을 지원해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |특성|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |활성|부울|
   |title|부울|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.형식|String|
   |주소[eq "작업"을 입력하십시오.형식|String|
   |addresses[type eq "work"].streetAddress|String|
   |주소[eq "작업"을 입력하십시오.지역성|String|
   |주소[eq "작업"을 입력합니다.영역|String|
   |주소[eq "작업"을 입력하십시오.국가|String|
   |addresses[type eq "work"].postalCode|String|
   |주소[eq "기타"]를 입력합니다.형식|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:관리자|String|
   |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:부서|String|

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Facebook에서 Workplace에 대한 Azure AD 프로비저닝 서비스를 활성화하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위에서** 원하는 값을 선택하여 Facebook에서 Workplace에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 및 그룹의 초기 동기화 주기를 시작합니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 초기 주기를 수행하는 데 시간이 더 오래 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그를](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) 사용하여 프로비저닝이 성공적으로 또는 실패한 사용자를 확인합니다.
2. [진행률 표시줄을](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) 확인하여 프로비저닝 주기의 상태와 완료에 얼마나 가까운지 확인합니다.
3. 프로비저닝 구성이 비정상 상태인 것 같으면 응용 프로그램이 격리됩니다. 검역 상태에 대한 자세한 내용은 [여기를 참조하십시오.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="troubleshooting-tips"></a>문제 해결 팁
*  사용자가 생성되지 않았고 코드 "1789003"이 있는 감사 로그 이벤트가 있는 경우 사용자가 확인되지 않은 도메인에 있음을 의미합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱용 사용자 계정 프로비저닝 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
