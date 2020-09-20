---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 Apple School Manager 구성 Microsoft Docs'
description: Azure AD에서 Apple School Manager로 사용자 계정을 자동으로 프로 비전 및 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: a6543c56f7170f674a227421575ba7f842fb29a8
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761529"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Apple School Manager 구성



이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 Apple School Manager와 Azure Active Directory (Azure AD)에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure AD는 Azure AD 프로 비전 서비스를 사용 하 여 사용자를 자동으로 [Apple School Manager](https://school.apple.com/) 에 프로 비전 하 고 프로 비전 해제 합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * Apple School Manager에서 사용자 만들기
> * 더 이상 액세스할 필요가 없는 경우 Apple School Manager에서 사용자 제거
> * Azure AD와 Apple School Manager 간에 특정 사용자 특성을 동기화 상태로 유지

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로 비전을 구성할 수 있는 [권한이](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 있는 Azure AD의 사용자 계정 (예: 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자). 
* 관리자, Site Manager 또는 사람 관리자 역할을 하는 Apple School Manager 계정

> [!NOTE]
> Azure AD로 토큰을 전송 하 고 성공적인 연결을 설정 하는 작업은 4 일 동안 완료 되어야 합니다. 그렇지 않으면 프로세스를 다시 시작 해야 합니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
2. [프로비저닝 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정합니다.
3. [AZURE AD와 Apple School Manager 간에 매핑할](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)데이터를 결정 합니다.

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>2단계. Apple School Manager를 구성 하 여 Azure AD로 프로 비전 지원

1. Apple School Manager에서 관리자, Site Manager 또는 사용자 관리자 역할을 가진 계정으로 로그인 합니다.
2. 사이드바 아래쪽의 설정을 클릭 하 고 조직 설정 아래에서 데이터 원본을 클릭 한 다음 데이터 원본에 연결을 클릭 합니다.
3. SCIM 옆에 있는 연결을 클릭 하 고 경고를 자세히 읽고 복사를 클릭 한 다음 닫기를 클릭 합니다.
[SCIM에 연결 창에서 토큰 및 복사 단추를 제공 합니다.] Apple Business Manager에서 Azure AD로 테 넌 트 URL을 복사 하려면이 창을 열어 두세요. https://federation.apple.com/feeds/school/scim

    ![Apple School Manager](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> 비밀 토큰은 Azure AD 관리자가 아닌 다른 사용자와 공유 해서는 안 됩니다.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 Apple School Manager 추가

Apple school manager에 프로 비전 관리를 시작 하려면 Azure AD 응용 프로그램 갤러리에서 Apple School Manager를 추가 합니다. 이전에 SSO 용 Apple School Manager를 설치한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* Apple School Manager에 사용자를 할당할 때 **기본 액세스**외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다. 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>5단계. Apple School Manager에 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **Apple School Manager**를 선택 합니다.

    ![응용 프로그램 목록의 Apple School Manager](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![자동 프로 비전 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서, **테 넌 트 URL** 및 **암호 토큰** 의 Apple School Manager에서 검색 된 **scim 2.0 기준 url 및 액세스 토큰** 값을 입력 합니다. **연결 테스트** 를 클릭 하 여 Azure AD가 Apple School Manager에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 Apple School Manager 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>연결에 성공 하면 Apple School Manager는 SCIM 연결을 활성으로 표시 합니다. 이 프로세스는 Apple School Manager에서 최신 연결 상태를 반영 하는 데 최대 60 초까지 걸릴 수 있습니다.

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 클릭합니다.

8. **매핑** 섹션 아래에서 **Apple School Manager에 Azure Active Directory 사용자 동기화를**선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 Apple School Manager로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Apple School Manager의 사용자 계정을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |활성|부울|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Apple School Manager에 대해 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 설정 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 Apple School Manager에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링

프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)를 참조하세요.  

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Apple School Manager의 SCIM 요구 사항 검토](URL=https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Apple School Manager에서 사용자 ID를 사용 하는 방법](URL=https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [SCIM을 사용 하 여 Apple School Manager로 사용자 가져오기](URL=https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Apple School Manager에서 SCIM 사용자 계정 충돌 해결](URL=https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Apple School Manager에 표시 되는 Azure AD 계정 삭제](URL=https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Apple School Manager에서 SCIM 활동 보기](URL=https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Apple School Manager에서 기존 SCIM 토큰 및 연결 관리](URL=https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* Apple [School manager에서 scim 연결 끊기](URL=https://support.apple.com/guide/apple-school-manager/apd609be3a61)[apple school manager에서 기존 scim 토큰 및 연결 관리](URL=https://support.apple.com/guide/apple-school-manager/apdc9a8236e9)
* [Apple School Manager에서 SCIM 연결 문제 해결](URL=https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
