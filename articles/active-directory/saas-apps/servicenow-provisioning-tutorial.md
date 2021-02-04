---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 ServiceNow 구성 | Microsoft Docs'
description: Azure AD에서 ServiceNow로 사용자 계정을 자동으로 프로 비전 하 고 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539046"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 ServiceNow 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 ServiceNow 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 설명 합니다. Azure AD가 구성 되 면 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 [ServiceNow](https://www.servicenow.com/) 에 자동으로 프로 비전 하 고 프로 비전 합니다. 

이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * ServiceNow에서 사용자 만들기
> * 더 이상 액세스할 필요가 없는 경우 ServiceNow에서 사용자 제거
> * Azure AD와 ServiceNow 간에 사용자 특성을 동기화된 상태로 유지
> * ServiceNow에서 그룹 및 그룹 구성원 프로비저닝
> * ServiceNow에 [Single Sign-On](servicenow-tutorial.md) 허용 (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로 비전을 구성할 수 있는 [권한이](../roles/permissions-reference.md) 있는 Azure AD의 사용자 계정 (응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자)
* Calgary 이상의 [ServiceNow 인스턴스](https://www.servicenow.com/)
* Helsinki 이상의 [ServiceNow Express 인스턴스](https://www.servicenow.com/)
* 관리자 역할이 있는 ServiceNow의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1 단계: 프로 비전 배포 계획
1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [Azure AD와 ServiceNow 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>2 단계: Azure AD로 프로 비전을 지원 하도록 ServiceNow 구성

1. ServiceNow 인스턴스 이름을 식별합니다. ServiceNow에 액세스하는 데 사용하는 URL에서 인스턴스 이름을 찾을 수 있습니다. 다음 예에서는 인스턴스 이름이 **dev35214** 입니다.

   ![ServiceNow 인스턴스를 보여 주는 스크린샷](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. ServiceNow에서 관리자에 대한 자격 증명을 가져옵니다. ServiceNow의 사용자 프로필로 이동 하 여 사용자에 게 관리자 역할이 있는지 확인 합니다. 

   ![ServiceNow 관리자 역할을 보여 주는 스크린샷](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>3 단계: Azure AD 응용 프로그램 갤러리에서 ServiceNow 추가

Azure AD 애플리케이션 갤러리에서 ServiceNow를 추가하여 ServiceNow로 프로비저닝 관리를 시작합니다. 이전에 Single Sign-On (SSO) 용 ServiceNow를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 통합을 테스트 하는 경우 별도의 앱을 만드는 것이 좋습니다. [갤러리에서 응용 프로그램을 추가 하는 방법에 대해 자세히 알아보세요](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4 단계: 프로 비전 범위에 있는 사용자 정의 

Azure AD 프로 비전 서비스를 사용 하 여 응용 프로그램에 대 한 할당 또는 사용자 또는 그룹의 특성을 기반으로 프로 비전 되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로 비전 되는 사용자의 범위를 선택 하는 경우 [응용 프로그램에 사용자 및 그룹을 할당 하는 단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로 비전 할 사용자의 범위를 선택 하는 경우 범위 지정 [필터를 사용할](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)수 있습니다. 

다음 팁을 염두에 두어야 합니다.

* ServiceNow에 사용자 및 그룹을 할당 하는 경우 기본 액세스 외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [응용 프로그램 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md) 하 여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 프로 비전 범위가 할당 된 사용자 및 그룹으로 설정 된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여이를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정 된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>5 단계: ServiceNow에 자동 사용자 프로 비전 구성 

이 섹션에서는 TestApp에서 사용자 및 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다. Azure AD에서 사용자 및 그룹 할당에 대 한 구성을 기반으로 할 수 있습니다.

Azure AD에서 ServiceNow에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 애플리케이션 창을 보여 주는 스크린샷](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **ServiceNow** 를 선택 합니다.

    ![응용 프로그램 목록을 보여 주는 스크린샷](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 옵션이 호출된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동 옵션이 out 인 프로 비전 모드 드롭다운 목록의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 ServiceNow 관리자 자격 증명 및 사용자 이름을 입력 합니다. **연결 테스트** 를 선택 하 여 Azure AD가 ServiceNow에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 ServiceNow 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![관리자 자격 증명을 입력할 수 있는 서비스 프로 비전 페이지를 보여 주는 스크린샷](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사람 또는 그룹의 이메일 주소를 입력합니다. 그런 다음 **오류가 발생 하면 전자 메일 알림 보내기** 확인란을 선택 합니다.

    ![알림 전자 메일에 대 한 상자를 표시 하는 스크린샷](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **ServiceNow에 사용자 Azure Active Directory 동기화를** 선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 ServiceNow로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 ServiceNow의 사용자 계정을 일치시키는 데 사용됩니다. 

   일치 하는 [대상 특성](../app-provisioning/customize-application-attributes.md)을 변경 하도록 선택 하는 경우 ServiceNow API가 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. 
   
   **저장** 단추를 선택하여 변경 내용을 커밋합니다.

10. **매핑** 섹션에서 **ServiceNow에 Azure Active Directory 그룹 동기화를** 선택 합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 ServiceNow로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 ServiceNow의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 참조하세요.

13. ServiceNow에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로 비전 상태를 전환 하는 것을 보여 주는 스크린샷](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 ServiceNow에 프로 비전 할 사용자 및 그룹을 정의 합니다.

    ![프로 비전 범위에 대 한 선택 항목을 보여 주는 스크린샷](common/provisioning-scope.png)

15. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로 비전 구성을 저장 하는 단추의 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 이후 주기 보다 수행 하는 데 더 많은 시간이 걸립니다. Azure AD 프로 비전 서비스가 실행 되는 동안에는 40 분 마다 이후 주기가 발생 합니다. 

## <a name="step-6-monitor-your-deployment"></a>6 단계: 배포 모니터링
프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

- [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
- [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
- 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. [격리 상태에 대해 자세히 알아보세요](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>문제 해결 팁
* ServiceNow에서 특정 특성 (예: **부서** 및 **위치**)을 프로 비전 하는 경우 servicenow의 참조 테이블에 값이 이미 있어야 합니다. 그렇지 않으면 **Invalidlookupreference** 오류가 발생 합니다. 

   예를 들어 ServiceNow의 특정 테이블에 두 개의 위치 (시애틀, 로스앤젤레스) 및 3 개의 부서 (판매, 재무, 마케팅)가 있을 수 있습니다. 부서가 "Sales"이 고 해당 위치가 "시애틀" 인 사용자를 프로 비전 하려는 경우 해당 사용자는 성공적으로 프로 비전 됩니다. 부서가 "Sales"이 고 위치가 "LA" 인 사용자를 프로 비전 하려는 경우 사용자가 프로 비전 되지 않습니다. "LA" 위치는 ServiceNow의 참조 테이블에 추가 해야 합니다. 그렇지 않으면 ServiceNow의 형식과 일치 하도록 Azure AD의 사용자 특성을 업데이트 해야 합니다. 
* **EntryJoiningPropertyValueIsMissing** 오류가 발생 하는 경우 [특성 매핑을](../app-provisioning/customize-application-attributes.md) 검토 하 여 일치 하는 특성을 식별 합니다. 이 값은 프로 비전 하려는 사용자 또는 그룹에 있어야 합니다. 
* 요구 사항 또는 제한 사항 (예: 사용자에 대 한 국가 코드를 지정 하는 형식)을 이해 하려면 [SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html)를 검토 합니다.
* 프로비저닝 요청은 기본적으로 https://{your-instance-name}.service-now.com/{table-name}으로 보내집니다. 사용자 지정 테 넌 트 URL이 필요한 경우 인스턴스 이름으로 전체 URL을 제공할 수 있습니다.
* **ServiceNowInstanceInvalid** 오류는 ServiceNow 인스턴스와 통신 하는 데 문제가 있음을 나타냅니다. 오류의 텍스트는 다음과 같습니다.
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  연결 테스트에 문제가 있는 경우 ServiceNow에서 다음 설정에 대해 **아니요** 를 선택 해 보세요.
   
  - **시스템 보안**  >  **높은 수준의 보안 설정**  >  **들어오는 스키마 요청에 대 한 기본 인증 필요**
  - **시스템 속성**  >  **웹 서비스**  >  **들어오는 SOAP 요청에 대 한 기본 권한 부여 필요**

     ![SOAP 요청에 대 한 권한을 부여 하는 옵션을 보여 주는 스크린샷](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  그래도 문제를 해결할 수 없는 경우에는 ServiceNow 지원에 문의 하 여 문제 해결에 도움이 되는 SOAP 디버깅을 설정 하도록 요청 하세요. 

* Azure AD 프로 비전 서비스는 현재 특정 [IP 범위](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)에서 작동 합니다. 필요한 경우 다른 IP 범위를 제한 하 고 이러한 특정 IP 범위를 응용 프로그램의 허용 목록에 추가할 수 있습니다. 이러한 기술은 Azure AD 프로 비전 서비스에서 응용 프로그램으로의 트래픽 흐름을 허용 합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
