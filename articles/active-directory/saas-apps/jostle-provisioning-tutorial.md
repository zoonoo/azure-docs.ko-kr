---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 Jostle 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 Jostle로 자동으로 프로비저닝 및 프로비저닝을 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: c302f7731c111c0718ac7c4d5889208020cc09a8
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110067594"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Jostle 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Jostle 및 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명합니다. 구성된 경우 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [Jostle](https://www.jostle.me/)로 자동으로 프로비저닝 및 프로비저닝을 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Jostle에서 사용자 만들기
> * Jostle에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 Jostle 간에 사용자 특성을 동기화된 상태로 유지
> * Jostle에 [Single Sign-On](jostle-tutorial.md) 사용(권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* [Jostle 테넌트](https://www.jostle.me/).
* 관리자 권한이 있는 Jostle의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
1. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
1. [Azure AD와 Jostle 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다.

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD에서 프로비저닝을 지원하도록 Jostle 구성

### <a name="automation-account"></a>Automation 계정

시작하기 전에 Jostle 인트라넷에서 **Automation 사용자** 를 만들어야 합니다. Azure를 사용하여 구성하는 데 사용할 계정입니다. Automation 사용자는 관리자 **설정 > 사용자 계정 및 데이터 > Automation 사용자 관리** 에서 만들 수 있습니다.

Automation 사용자 및 Automation 사용자를 만드는 방법에 대한 자세한 내용은 [이 문서](https://forum.jostle.us/hc/en-us/articles/360057364073)를 참조하세요.

Automation 사용자 계정을 만든 후에는 Azure를 구성하는 데 사용할 수 있도록 먼저 **활성화되어야 합니다**(즉, 한 번 이상 인트라넷에 로그인해야 함).

### <a name="manage-user-provisioning"></a>사용자 프로비저닝 관리

시작하기 전에 계정 구독에 **SSO/사용자 프로비저닝 기능이 포함되어** 있는지 확인합니다. 그렇지 않으면 고객 성공 관리자 <success@jostle.me>에게 연락하여 계정에 추가하는 데 도움을 받을 수 있습니다.

다음 단계는 Jostle에서 **API URL** 및 **API 키** 를 가져오는 것입니다.

1. 기본 탐색으로 이동하여 **관리 설정** 을 클릭합니다.
1. **다른 시스템에 대한 사용자 데이터** 에서 **사용자 프로비저닝 관리** 를 클릭합니다. 여기에서 "사용자 프로비저닝 관리"가 표시되지 않고 계정에 SSO/사용자 프로비저닝이 포함되어 있는지 확인한 경우 지원 담당자 <support@jostle.me>에게 문의하여 이 페이지를 관리 설정에서 사용하도록 설정합니다.
1. **사용자 프로비저닝 API 세부 정보** 섹션에서 **기준 URL** 필드로 이동하고 복사 단추를 클릭한 다음 나중에 쉽게 액세스할 수 있는 위치에 URL을 저장합니다.                                                           

   ![프로비전](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. 다음으로 **새 키 추가**... 단추를 클릭합니다.
1. 다음 화면에서 **Automation 사용자** 필드로 이동하고 드롭다운 메뉴를 사용하여 Automation 사용자 계정을 선택합니다. 

   ![통합 계정](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. **프로비저닝 API 키 설명** 필드에서 키에 이름(예: "Azure")을 제공하고 **추가** 단추를 클릭합니다.

1. 키가 생성되면 **바로 복사하여** URL을 저장한 위치에 저장해야 합니다. 이 때가 키가 표시되는 유일한 시간이기 때문입니다.                                                               
1. 다음으로 **API URL** 및 **API 키** 를 사용하여 Azure에서 통합을 구성합니다.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 애플리케이션 갤러리에서 Jostle 추가

Azure AD 애플리케이션 갤러리에서 Jostle을 추가하여 Jostle에 대한 프로비저닝 관리를 시작합니다. 이전에 SSO용 Jostle을 설정했다면 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](../manage-apps/add-application-portal.md)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* 사용자 및 그룹을 Jostle에 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 더 많은 역할을 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>5단계. Jostle에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및 그룹 할당에 따라 Jostle 앱에서 사용자 및 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!NOTE]
> Jostle에 대한 자동 사용자 프로비저닝에 대한 자세한 내용은 [User-Provisioning-Azure-Integration](https://forum.jostle.us/hc/en-us/articles/360056368534-User-Provisioning-Azure-Integration)을 참조하세요.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Azure AD에서 Jostle에 대한 자동 사용자 프로비저닝을 구성하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Jostle** 을 선택합니다.

    ![애플리케이션 목록의 Jostle 링크](common/all-applications.png)

1. **프로비저닝** 탭을 선택하고 **시작하기** 를 클릭합니다.

    ![프로비저닝 탭](common/provisioning.png)

1.  **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로비저닝 탭 자동](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션에 Jostle **테넌트 URL** 및 **비밀 토큰** 정보를 입력합니다. **연결 테스트** 를 선택하여 Azure AD에서 Jostle에 연결할 수 있는지 확인합니다. 연결에 실패하면 Jostle 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 이메일** 필드에서 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 이메일 주소를 입력합니다. **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다. 하지만 Jostle은 프로비저닝 실패 알림도 보내므로 선택 사항입니다.

    ![알림 이메일](common/provisioning-notification-email.png)

1. **저장** 을 선택합니다.

1. **매핑** 섹션에서 **Azure Active Directory 사용자를 Jostle에 프로비저닝** 을 선택합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Jostle로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Jostle의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하는 경우 Jostle API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |활성|부울|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |emails[type eq "personal"].value|String|
   |emails[type eq "alternate1"].value|String|
   |emails[type eq "alternate2"].value|String|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|String|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |String|  

1. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공된 지침을 참조하세요.

1. Jostle에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 에서 원하는 값을 선택하여 Jostle에 프로비저닝할 사용자 또는 그룹을 정의합니다. Jostle의 경우 **범위** 를 "할당된 사용자 및 그룹만 동기화"로 설정해야 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 다음 주기보다 더 오래 수행됩니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링

프로비저닝이 구성되면 다음 리소스를 사용하여 배포를 모니터링합니다.

* [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
* [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
* 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [격리 상태의 애플리케이션 프로비저닝](../app-provisioning/application-provisioning-quarantine-status.md)을 참조하세요.

## <a name="more-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).