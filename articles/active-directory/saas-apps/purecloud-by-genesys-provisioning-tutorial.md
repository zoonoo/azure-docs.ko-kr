---
title: '자습서: Azure Active Directory를 사용 하 여 자동 사용자 프로비저닝에 대 한 Genesys별 PureCloud 구성 | 마이크로 소프트 문서'
description: Azure AD에서 Genesys의 퓨어클라우드로 사용자 계정을 자동으로 프로비전하고 프로비저닝 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370674"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>튜토리얼: 자동 사용자 프로비저닝을 위해 제네시스별로 퓨어클라우드 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 Genesys 및 Azure Active Directory(Azure AD)에 의한 PureCloud에서 수행해야 하는 단계를 설명합니다. 구성되면 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 [Genesys에서 퓨어클라우드로](https://www.genesys.com) 사용자 및 그룹을 자동으로 프로비전하고 프로비전 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * 제네시스의 퓨어클라우드에서 사용자 만들기
> * 더 이상 액세스가 필요하지 않을 때 제네시스의 퓨어클라우드 사용자 제거
> * Azure AD와 제네시스의 퓨어클라우드 간에 사용자 속성 동기화 유지
> * 제네시스의 퓨어클라우드에서 그룹 및 그룹 멤버십 제공
> * 제네시스의 퓨어클라우드에 [대한 단일 사인온(권장)](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로비저닝을 구성할 수 [있는 권한이](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 있는 Azure AD의 사용자 계정(예: 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자). 
* 퓨어클라우드 [조직.](https://help.mypurecloud.com/?p=81984)
* Oauth [클라이언트를](https://help.mypurecloud.com/?p=24360) 만들 수 있는 권한이 있는 사용자입니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식에](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)대해 알아봅니다.
2. [프로비저닝 범위에](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)속할 사람을 결정합니다.
3. [Azure AD와 PureCloud by Genesys 간에 매핑할 데이터를 결정합니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD로 프로비전을 지원하도록 Genesys별 PureCloud 구성

1. 퓨어클라우드 조직에서 구성된 [Oauth 클라이언트를](https://help.mypurecloud.com/?p=188023) 만듭니다.
2. [oauth 클라이언트를 통해](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)토큰을 생성합니다.
3. PureCloud 내에서 그룹 구성원 자격을 자동으로 프로비전하려는 경우 Azure AD의 그룹과 동일한 이름을 가진 PureCloud에서 [그룹을 만들어야](https://help.mypurecloud.com/?p=52397) 합니다.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 제네시스별 퓨어클라우드 추가

Azure AD 응용 프로그램 갤러리에서 제네시스별 PureCloud를 추가하여 Genesys에 의한 퓨어클라우드프로 프로비저닝 관리를 시작합니다. 이전에 SSO에 대한 제네시스에 의해 PureCloud를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트할 때 별도의 앱을 만드는 것이 좋습니다. 갤러리에서 응용 프로그램을 추가하는 방법에 대한 자세한 내용은 [여기를](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)참조하십시오. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 속할 사람 정의 

Azure AD 프로비전 서비스를 사용하면 응용 프로그램에 할당하거나 사용자/그룹의 특성을 기반으로 프로비전할 사용자를 범위를 지정할 수 있습니다. 할당에 따라 앱에 프로비전할 사용자를 지정하는 경우 다음 [단계를](../manage-apps/assign-user-or-group-access-portal.md) 사용하여 응용 프로그램에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기반으로 프로비전할 범위를 지정하는 경우 [여기에](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* Genesys의 퓨어클라우드에 사용자 및 그룹을 할당할 때 **기본 액세스**가 아닌 다른 역할을 선택해야 합니다. 기본 액세스 역할을 가진 사용자는 프로비저닝에서 제외되며 프로비저닝 로그에서 효과적으로 사용할 수 없는 것으로 표시됩니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [응용 프로그램 매니페스트를 업데이트하여](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 추가 역할을 추가할 수 있습니다. 

* 작게 시작하십시오. 모든 사용자에게 배포하기 전에 작은 사용자 및 그룹으로 테스트합니다. 프로비저닝 범위가 할당된 사용자 및 그룹으로 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 이를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정된 경우 [특성 기반 범위 지정 필터를](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>5단계. 제네시스에 의해 퓨어 클라우드에 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당을 기반으로 TestApp에서 사용자 및/또는 그룹을 생성, 업데이트 및 비활성화하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Azure AD에서 Genesys별 PureCloud에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다. **엔터프라이즈 응용 프로그램을**선택한 다음 모든 응용 프로그램을 **선택합니다.**

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **PureCloud by Genesys**를 선택합니다.

    ![애플리케이션 목록의 PureCloud by Genesys 링크](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비저닝 모드를** **자동으로**설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. 관리자 **자격 증명** 섹션에서 Genesys API URL및 Oauth 토큰에 대한 PureCloud를 **테넌트 URL** 및 비밀 **토큰** 필드에 각각 입력합니다. API URL은 [PureCloud](https://developer.mypurecloud.com/api/rest/index.html)개발자 `{{API Url}}/api/v2/scim/v2`센터에서 PureCloud 리전의 API URL을 사용하여 로 구성됩니다. Azure AD가 Genesys의 퓨어클라우드에 연결할 수 있는지 확인하려면 **테스트 연결을** 클릭합니다. 연결이 실패하면 Genesys 계정의 PureCloud에 관리자 권한이 있는지 확인하고 다시 시도하십시오.

    ![프로비전](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. 알림 **전자 메일** 필드에 프로비저닝 오류 알림을 받아야 하는 개인 또는 그룹의 전자 메일 주소를 입력하고 오류가 발생할 때 전자 메일 알림 보내기 확인란을 선택합니다. **Send an email notification when a failure occurs**

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Genesys순클라우드로 동기화합니다.**

9. **속성 매핑** 섹션에서 Azure AD에서 PureCloud로 동기화된 사용자 특성을 제네시스별로 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 퓨어클라우드 by Genesys의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성을](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)변경하도록 선택한 경우 Genesys API의 PureCloud가 해당 특성을 기반으로 필터링 사용자를 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

     |특성|Type|
     |---|---|
     |userName|String|
     |활성|부울|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |title|String|
     |phoneNumbers[type eq "mobile"].value|String|
     |phoneNumbers[type eq "work"].value|String|
     |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:부서|String|
     |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:관리자|참고|

10. **매핑** 섹션에서 **Azure Active Directory 그룹 동기화를 제네시스별 퓨어클라우드로**동기화합니다.

11. **속성 매핑** 섹션에서 Azure AD에서 PureCloud로 동기화된 그룹 특성을 제네시스별로 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 퓨어클라우드 by Genesys의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다. PureCloud by Genesys는 그룹 생성 또는 삭제를 지원하지 않으며 그룹 업데이트만 지원합니다.

      |특성|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|참고|

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Genesys의 퓨어클라우드에 대한 Azure AD 프로비저닝 서비스를 사용하려면 **설정** 섹션에서 **프로비저닝 상태를** **켜기로** 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **범위에서** 원하는 값을 선택하여 Genesys에 의해 PureCloud에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **Scope에** 정의된 모든 사용자 및 그룹의 초기 동기화 주기를 시작합니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 초기 주기를 수행하는 데 시간이 더 오래 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

* [프로비저닝 로그를](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) 사용하여 프로비저닝이 성공적으로 또는 실패한 사용자를 확인합니다.
* [진행률 표시줄을](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) 확인하여 프로비저닝 주기의 상태와 완료에 얼마나 가까운지 확인합니다.
* 프로비저닝 구성이 비정상 상태인 것 같으면 응용 프로그램이 격리됩니다. 검역 상태에 대한 자세한 내용은 [여기를 참조하십시오.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱용 사용자 계정 프로비저닝 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
