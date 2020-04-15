---
title: '자습서: Azure Active Directory를 사용 하 고 자동 사용자 프로비저닝을 위한 Oracle 클라우드 인프라 콘솔 구성 | 마이크로 소프트 문서'
description: Azure AD에서 Oracle 클라우드 인프라 콘솔로 사용자 계정을 자동으로 프로비전및 프로비저닝 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378952"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위해 Oracle 클라우드 인프라 콘솔 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Oracle 클라우드 인프라 콘솔 및 Azure Active Directory(Azure AD)에서 수행해야 하는 단계를 설명합니다. 구성되면 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [Oracle 클라우드 인프라 콘솔로](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) 자동으로 프로비전하고 제거합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * 오라클 클라우드 인프라 콘솔에서 사용자 만들기
> * 더 이상 액세스가 필요하지 않은 경우 Oracle 클라우드 인프라 콘솔의 사용자 제거
> * Azure AD와 Oracle 클라우드 인프라 콘솔 간에 사용자 속성 동기화 유지
> * 오라클 클라우드 인프라 콘솔에서 그룹 및 그룹 멤버십 프로비저닝
> * 오라클 클라우드 인프라 [콘솔에 대한 단일 사인온(권장)](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로비저닝을 구성할 수 [있는 권한이](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 있는 Azure AD의 사용자 계정(예: 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자). 
* 오라클 클라우드 인프라 제어 [테넌트](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* 관리자 권한이 있는 Oracle 클라우드 인프라 제어의 사용자 계정입니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식에](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)대해 알아봅니다.
2. [프로비저닝 범위에](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)속할 사람을 결정합니다.
3. [Azure AD와 Oracle 클라우드 인프라 콘솔 간에 매핑할](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)데이터를 결정합니다. 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD로 프로비전을 지원하도록 Oracle 클라우드 인프라 콘솔 구성

1. 오라클 클라우드 인프라 콘솔의 관리 포털에 로그인합니다. 화면 왼쪽 상단 모서리에서 **ID > 페더레이션으로**이동합니다.

    ![오라클 관리자](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Oracle ID 클라우드 서비스 콘솔 옆에 있는 페이지에 표시되는 URL을 클릭합니다.

    ![오라클 URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. ID **공급자 추가를** 클릭하여 새 ID 공급자를 만듭니다. 테넌트 URL의 일부로 사용할 IdP ID를 저장합니다. 응용 프로그램 탭 옆에 있는 더하기 **아이콘을** 클릭하여 OAuth 클라이언트를 만들고 IDCS ID 도메인 관리자 AppRole을 부여합니다.

    ![오라클 클라우드 아이콘](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. 아래 스크린샷을 따라 응용 프로그램을 구성합니다. 구성이 완료되면 **저장을**클릭합니다.

    ![오라클 구성](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![오라클 토큰 발급 정책](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. 응용 프로그램의 구성 탭에서 **일반 정보** 옵션을 확장하여 클라이언트 ID 및 클라이언트 보안 정보를 검색합니다.

    ![오라클 토큰 생성](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. 비밀 토큰 Base64를 생성하려면 클라이언트 **ID:Client Secret**형식의 클라이언트 ID 및 클라이언트 암호를 인코딩합니다. 비밀 토큰을 저장합니다. 이 값은 Azure 포털의 Oracle 클라우드 인프라 콘솔 응용 프로그램의 프로비저닝 탭의 **비밀 토큰** 필드에 입력됩니다.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 Oracle 클라우드 인프라 콘솔 추가

Azure AD 응용 프로그램 갤러리에서 Oracle 클라우드 인프라 콘솔을 추가하여 Oracle 클라우드 인프라 콘솔에 대한 프로비저닝 관리를 시작합니다. 이전에 SSO용 Oracle 클라우드 인프라 콘솔을 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트할 때 별도의 앱을 만드는 것이 좋습니다. 갤러리에서 응용 프로그램을 추가하는 방법에 대한 자세한 내용은 [여기를](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)참조하십시오. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 속할 사람 정의 

Azure AD 프로비전 서비스를 사용하면 응용 프로그램에 할당하거나 사용자/그룹의 특성을 기반으로 프로비전할 사용자를 범위를 지정할 수 있습니다. 할당에 따라 앱에 프로비전할 사용자를 지정하는 경우 다음 [단계를](../manage-apps/assign-user-or-group-access-portal.md) 사용하여 응용 프로그램에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기반으로 프로비전할 범위를 지정하는 경우 [여기에](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* Oracle 클라우드 인프라 콘솔에 사용자 및 그룹을 할당할 **때기본 액세스**가 아닌 다른 역할을 선택해야 합니다. 기본 액세스 역할을 가진 사용자는 프로비저닝에서 제외되며 프로비저닝 로그에서 효과적으로 사용할 수 없는 것으로 표시됩니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [응용 프로그램 매니페스트를 업데이트하여](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 추가 역할을 추가할 수 있습니다. 

* 작게 시작하십시오. 모든 사용자에게 배포하기 전에 작은 사용자 및 그룹으로 테스트합니다. 프로비저닝 범위가 할당된 사용자 및 그룹으로 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 이를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정된 경우 [특성 기반 범위 지정 필터를](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>5단계. Oracle 클라우드 인프라 콘솔에 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당을 기반으로 TestApp에서 사용자 및/또는 그룹을 생성, 업데이트 및 비활성화하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Azure AD에서 Oracle 클라우드 인프라 콘솔에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행하십시오.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 응용 프로그램을**선택한 다음 모든 응용 프로그램을 **선택합니다.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Oracle Cloud Infrastructure Console**을 선택합니다.

    ![애플리케이션 목록의 Oracle 클라우드 인프라 콘솔 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. 관리자 **자격 증명** 섹션에서 **테넌트 URL을** 형식에 `https://<IdP ID>.identity.oraclecloud.com/admin/v1` 입력합니다. 예: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. 비밀 토큰에서 앞에서 검색한 **비밀 토큰**값을 입력합니다. Azure AD가 Oracle 클라우드 인프라 콘솔에 연결할 수 있는지 확인하려면 **테스트 연결을** 클릭합니다. 연결이 실패하면 Oracle Cloud Infrastructure Console 계정에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![프로비전](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. 알림 **전자 메일** 필드에 프로비저닝 오류 알림을 받아야 하는 개인 또는 그룹의 전자 메일 주소를 입력하고 오류가 발생할 때 전자 메일 알림 보내기 확인란을 선택합니다. **Send an email notification when a failure occurs**

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Oracle 클라우드 인프라 콘솔에 동기화선택합니다.**

9. **특성 매핑** 섹션에서 Azure AD에서 Oracle 클라우드 인프라 콘솔로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Oracle 클라우드 인프라 콘솔의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성을](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)변경하도록 선택한 경우 Oracle Cloud Infrastructure Console API가 해당 특성을 기반으로 사용자를 필터링하는 것을 지원해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |특성|Type|
      |---|---|
      |displayName|String|
      |userName|String|
      |활성|부울|
      |title|String|
      |emails[type eq "work"].value|String|
      |preferredLanguage|String|
      |name.givenName|String|
      |name.familyName|String|
      |주소[eq "작업"을 입력하십시오.형식|String|
      |주소[eq "작업"을 입력하십시오.지역성|String|
      |주소[eq "작업"을 입력합니다.영역|String|
      |addresses[type eq "work"].postalCode|String|
      |주소[eq "작업"을 입력하십시오.국가|String|
      |addresses[type eq "work"].streetAddress|String|
      |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:직원번호|String|
      |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:부서|String|
      |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:costCenter|String|
      |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:분할|String|
      |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:관리자|참조|
      |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:조직|String|
      |urn:ietf:params:scim:schemas:oracle:idcs:확장:사용자:사용자:바이패스 알림|부울|
      |urn:ietf:params:scim:schemas:oracle:idcs:확장:사용자:사용자:isFederatedUser|부울|

10. **매핑** 섹션에서 **Azure Active Directory 그룹 동기화를 Oracle 클라우드 인프라 콘솔에 연결합니다.**

11. **특성 매핑** 섹션에서 Azure AD에서 Oracle 클라우드 인프라 콘솔로 동기화된 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업에 대한 Oracle 클라우드 인프라 콘솔의 그룹과 일치하는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |특성|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|참조|

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Oracle 클라우드 인프라 콘솔에 대한 Azure AD 프로비저닝 서비스를 사용하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **Scope에서** 원하는 값을 선택하여 Oracle 클라우드 인프라 콘솔에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 및 그룹의 초기 동기화 주기를 시작합니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 초기 주기를 수행하는 데 시간이 더 오래 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

* [프로비저닝 로그를](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) 사용하여 프로비저닝이 성공적으로 또는 실패한 사용자를 확인합니다.
* [진행률 표시줄을](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) 확인하여 프로비저닝 주기의 상태와 완료에 얼마나 가까운지 확인합니다.
* 프로비저닝 구성이 비정상 상태인 것 같으면 응용 프로그램이 격리됩니다. 검역 상태에 대한 자세한 내용은 [여기를 참조하십시오.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>추가 자료

* [엔터프라이즈 앱용 사용자 계정 프로비저닝 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
