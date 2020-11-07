---
title: '자습서: Azure Active Directory를 사용 하 여 자동 사용자 프로 비전을 위해 Mobile Tic-Tac 구성 | Microsoft Docs'
description: Azure AD에서 Tic-Tac Mobile로 사용자 계정을 자동으로 프로 비전 및 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 99fe2f8356b53b5d8e459dbf62534ddb4f0019b2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357269"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위해 Mobile Tic-Tac 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 Tic-Tac Mobile 및 Azure Active Directory (Azure AD)에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure AD는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 [3 목 모바일](https://www.tictacmobile.com/) 에 자동으로 프로 비전 하 고 프로 비전 합니다. 이 서비스에서 수행 하는 작업, 작동 방법 및 질문과 대답에 대 한 자세한 내용은 [AZURE AD를 사용 하 여 SaaS (software as a service) 응용 프로그램에 사용자 프로비저닝 자동화 및 프로 비전](../manage-apps/user-provisioning.md)해제를 참조 하세요.


## <a name="capabilities-supported"></a>지원되는 기능

> [!div class="checklist"]
> * Tic-Tac Mobile에서 사용자를 만듭니다.
> * 더 이상 액세스할 필요가 없는 Tic-Tac Mobile의 사용자를 제거 합니다.
> * Azure AD와 Tic-Tac Mobile 간에 사용자 특성을 동기화 된 상태로 유지 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* 프로 비전을 구성할 수 있는 [권한이](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 있는 Azure AD의 사용자 계정. 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자의 예가 있습니다.
* 슈퍼 관리자 역할이 있는 [3 목-Tac Mobile](https://www.tictacmobile.com/) 계정


## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
1. [프로비저닝 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정합니다.
1. [AZURE AD와 Tic-Tac Mobile 간에 매핑할](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)데이터를 결정 합니다.

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용 하 여 프로 비전을 지원 하도록 모바일 Tic-Tac 구성

연락 support@tictacmobile.com 하 여 **테 넌 트 URL** 및 **암호 토큰** 을 가져옵니다. 토큰을 받으려면 Tic-Tac Mobile에서 슈퍼 관리자 역할이 있어야 합니다. 토큰은 Azure Portal Tic-Tac 모바일 응용 프로그램의 **프로 비전** 탭에 있는 **비밀 토큰** 상자에 입력 됩니다.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>3단계: Azure AD 응용 프로그램 갤러리에서 Tic-Tac Mobile 추가

Azure AD 응용 프로그램 갤러리에서 Tic-Tac Mobile을 추가 하 여 Tic-Tac Mobile에 대 한 프로 비전 관리를 시작 합니다. 이전에 Single Sign-On에 Tic-Tac Mobile을 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 처음에 통합을 테스트 하는 경우 별도의 앱을 만듭니다. 갤러리에서 응용 프로그램을 추가 하는 방법에 대해 자세히 알아보려면 [범위 지정 필터를 사용한 특성 기반 응용 프로그램 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)을 참조 하세요.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의

Azure AD 프로 비전 서비스를 사용 하 여 응용 프로그램에 대 한 할당 또는 사용자 또는 그룹의 특성을 기반으로 프로 비전 되는 사용자의 범위를 지정할 수 있습니다. 할당에 따라 앱에 프로 비전 할 사용자의 범위를 선택 하는 경우 응용 프로그램에 사용자 및 그룹을 할당 하려면 [Azure Active Directory에서 앱에 대 한 사용자 할당 관리](../manage-apps/assign-user-or-group-access-portal.md) 의 단계를 수행 합니다. 사용자 또는 그룹의 특성만을 기준으로 프로 비전 할 사용자의 범위를 선택 하는 경우 범위 지정 [필터를 사용한 특성 기반 응용 프로그램 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 설명 된 대로 범위 지정 필터를 사용 합니다.

* Tic-Tac Mobile에 사용자 및 그룹을 할당 하는 경우 **기본 액세스** 외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로 비전에서 제외 되 고 프로 비전 로그에 효과적으로 부여 되지 않은 것으로 표시 됩니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [응용 프로그램 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 하 여 역할을 더 추가할 수 있습니다.
* 소규모로 시작합니다. 모든 사용자에 게 롤아웃 전에 소수의 사용자 및 그룹 집합을 사용 하 여 테스트 합니다. 프로 비전 범위가 할당 된 사용자 및 그룹으로 설정 된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여 제어를 유지할 수 있습니다. Scope를 모든 사용자 및 그룹으로 설정 하면 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다.

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>5단계. Tic-Tac Mobile에 대 한 자동 사용자 프로 비전 구성

이 섹션에서는 azure ad의 사용자 또는 그룹 할당을 기반으로 TestApp의 사용자 또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Azure AD에서 Tic-Tac Mobile에 대 한 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 선택합니다.

    ![엔터프라이즈 응용 프로그램 창을 보여 주는 스크린샷](common/enterprise-applications.png)

1. 응용 프로그램 목록에서 **3 목-Tac Mobile** 을 선택 합니다.

    ![응용 프로그램 목록에서 Tic-Tac Mobile 링크를 보여 주는 스크린샷](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로 비전 탭을 보여 주는 스크린샷](common/provisioning.png)

1. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![프로 비전 탭 자동 옵션을 보여 주는 스크린샷](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션 아래에서 Tic-Tac 모바일 **테 넌 트 URL** 및 **암호 토큰** 을 입력 합니다. **연결 테스트** 를 선택 하 여 Azure AD가 Tic-Tac Mobile에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 Tic-Tac Mobile 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![비밀 토큰 상자를 보여 주는 스크린샷](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 전자 메일** 상자에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 합니다. **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 전자 메일 상자를 보여 주는 스크린샷](common/provisioning-notification-email.png)

1. **저장** 을 선택합니다.

1. **매핑** 섹션에서 **사용자 Azure Active Directory Tic-Tac 모바일에 동기화를** 선택 합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Tic-Tac Mobile로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Tic-Tac Mobile의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경 하는 경우 Tic-Tac Mobile API에서 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |title|String|
   |전자 메일 [type eq "work"]. value|String|
   |preferredLanguage|String|
   |externalId|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

1. 범위 지정 필터를 구성 하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 참조 하세요.

1. Tic-Tac Mobile에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로 비전 상태를 전환 하는 것을 보여 주는 스크린샷](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 모바일 Tic-Tac에 프로 비전 할 사용자 또는 그룹을 정의 합니다.

    ![프로 비전 범위를 보여 주는 스크린샷](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![프로 비전 구성 저장을 보여 주는 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링

프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

1. [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
1. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
1. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대해 자세히 알아보려면 [격리 상태에서 응용 프로그램 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)을 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
