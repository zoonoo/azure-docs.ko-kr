---
title: '자습서: Azure Active Directory에서 사용자를 자동으로 프로비저닝하도록 디지털 프로세스 자동화를 위한 Bizagi Studio 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 디지털 프로세스 자동화를 위한 Bizagi Studio로 자동으로 프로비저닝 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 72e021f47bb8db4dedf0e434d0d94bb2118a4c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728165"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>자습서: 사용자를 자동으로 프로비저닝하도록 디지털 프로세스 자동화를 위한 Bizagi Studio 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 디지털 프로세스 자동화를 위한 Bizagi Studio와 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명합니다. 구성이 완료되면 Azure AD가 Azure AD 프로비저닝 서비스를 사용하여 사용자와 그룹을 [디지털 프로세스 자동화를 위한 Bizagi Studio](https://www.bizagi.com/)로 자동으로 프로비저닝 및 프로비전 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * 디지털 프로세스 자동화를 위한 Bizagi Studio에서 사용자 만들기
> * 디지털 프로세스 자동화를 위한 Bizagi Studio에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 디지털 프로세스 자동화를 위한 Bizagi Studio 간에 사용자 특성을 동기화된 상태로 유지
> * 디지털 프로세스 자동화를 위한 Bizagi Studio에 [Single Sign-On](./bizagi-studio-for-digital-process-automation-tutorial.md)(권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명한 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로비저닝을 구성할 수 있는 [권한](../roles/permissions-reference.md)이 있는 Azure AD의 사용자 계정. 예를 들어 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자가 있습니다. 
* 디지털 프로세스 자동화를 위한 Bizagi Studio 버전 11.2.4.2X 이상

## <a name="plan-your-provisioning-deployment"></a>프로비저닝 배포 계획
계획 수립을 위해 다음 단계를 수행합니다.

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 포함될 사용자를 결정합니다.
3. [Azure AD와 디지털 프로세스 자동화를 위한 Bizagi Studio 간에 매핑](../app-provisioning/customize-application-attributes.md)할 데이터를 결정합니다. 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Azure AD에서 프로비저닝을 지원하도록 구성
Azure AD에서 프로비저닝을 지원하도록 디지털 프로세스 자동화를 위한 Bizagi Studio를 구성하려면 다음 단계를 수행합니다.

1. **관리자 권한** 이 있는 사용자로 작업 포털에 로그인합니다.

2. **관리** > **보안** > **OAuth 2 애플리케이션** 으로 이동합니다.

   ![OAuth 2 애플리케이션이 강조 표시된 Bizagi의 스크린샷](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. **추가** 를 선택합니다.
4. **권한 부여 유형** 에서 **전달자 토큰** 을 선택합니다. **허용 범위** 에서 **API** 및 **사용자 동기화** 를 선택합니다. 그런 다음 **저장** 을 선택합니다.

   ![권한 부여 유형 및 허용 범위가 강조 표시된 애플리케이션 등록의 스크린샷](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. **클라이언트 암호** 를 복사하여 저장합니다. Azure Portal에서 디지털 프로세스 자동화를 위한 Bizagi Studio 애플리케이션은 클라이언트 암호 값이 **프로비저닝** 탭의 **비밀 토큰** 필드에 입력됩니다.

   ![클라이언트 암호가 강조 표시된 Oauth의 스크린샷](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 애플리케이션을 추가합니다.

디지털 프로세스 자동화를 위한 Bizagi Studio로의 프로비저닝 관리를 시작하려면 Azure AD 애플리케이션 갤러리에서 앱을 추가합니다. 이전에 디지털 프로세스 자동화를 위한 Bizagi Studio에 대해 Single Sign-On을 설정한 경우 동일한 애플리케이션을 사용할 수 있습니다. 그러나 통합을 처음으로 테스트할 때에는 별도의 앱을 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure AD(Azure Active Directory) 테넌트에 애플리케이션 추가](../manage-apps/add-application-portal.md)를 참조하세요. 

## <a name="define-who-is-in-scope-for-provisioning"></a>프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 프로비저닝되는 사용자의 범위를 애플리케이션에 대한 할당, 사용자와 그룹의 특성 또는 둘 다를 기준으로 지정할 수 있습니다. 할당을 기준으로 범위를 지정하는 경우 [Graph API를 사용하여 앱에 대해 사용자 및 그룹을 할당 또는 할당 취소](../manage-apps/assign-user-or-group-access-portal.md)의 단계를 참조하여 사용자와 그룹을 애플리케이션에 할당하세요. 사용자 또는 그룹의 특성만을 기준으로 범위를 지정하는 경우 범위 지정 필터를 사용하면 됩니다. 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비저닝](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요. 

범위 지정에 대해 유의할 사항은 다음과 같습니다.

* 사용자 및 그룹을 디지털 프로세스 자동화를 위한 Bizagi Studio에 할당하는 경우 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에서 실질적으로 권한이 없는 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우 [애플리케이션 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md)하여 더 많은 역할을 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정되면 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 


## <a name="configure-automatic-user-provisioning"></a>자동 사용자 프로비저닝 구성 

이 섹션에서는 사용자와 그룹을 만들고, 업데이트하고, 비활성화하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다. 이 작업은 Azure AD에서의 사용자 및 그룹 할당을 기준으로 테스트 앱에서 수행합니다.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Azure AD에서 디지털 프로세스 자동화를 위한 Bizagi Studio에 대해 자동 사용자 프로비저닝 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 으로 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 및 모든 애플리케이션이 강조 표시된 Azure Portal의 스크린샷](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **디지털 프로세스 자동화를 위한 Bizagi Studio** 를 선택합니다.

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝이 강조 표시된 관리 옵션의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

    ![자동이 강조 표시된 프로비저닝 모드의 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 디지털 프로세스 자동화를 위한 Bizagi Studio의 테넌트 URL 및 비밀 토큰을 입력합니다. 

      * **테넌트 URL:** Bizagi SCIM 엔드포인트를 다음 구조로 입력합니다. `<Your_Bizagi_Project>/scim/v2/`
         예: `https://my-company.bizagi.com/scim/v2/`

      * **비밀 토큰:** 이 값은 이 문서의 앞부분에 설명된 단계에서 검색됩니다.

      Azure AD가 디지털 프로세스 자동화를 위한 Bizagi Studio에 연결할 수 있는지 확인하려면 **연결 테스트** 를 선택합니다. 연결에 실패하는 경우 디지털 프로세스 자동화를 위한 Bizagi Studio 계정에 관리자 권한이 있는지 확인하고 다시 시도하세요.

   ![연결 테스트가 강조 표시된 관리자 자격 증명의 스크린샷](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 로는 프로비저닝 오류 알림을 받을 사용자 또는 그룹의 메일 주소를 입력합니다. **오류가 발생할 경우 메일 알림 보내기** 옵션을 선택합니다.

    ![알림 메일 옵션의 스크린샷](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 디지털 프로세스 자동화를 위한 Bizagi Studio에 동기화** 를 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 디지털 프로세스 자동화를 위한 Bizagi Studio로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 디지털 프로세스 자동화를 위한 Bizagi Studio의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](../app-provisioning/customize-application-attributes.md)을 변경하는 경우 디지털 프로세스 자동화 API를 위한 Bizagi Studio에서 해당 특성에 따라 사용자를 필터링하도록 지원하는지 확인해야 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |활성|부울|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|

   사용자 지정 확장 특성은 **고급 옵션 표시 > Bizagi의 특성 목록 편집** 으로 이동하여 추가할 수 있습니다. 사용자 지정 확장 특성은 접두사 **urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:** 로 시작해야 합니다. 예를 들어 사용자 지정 확장 특성이 **IdentificationNumber** 인 경우 특성에는 **urn:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:IdentificationNumber** 가 추가되어야 합니다. 변경 내용을 커밋하려면 **저장** 을 선택합니다.
   
    ![특성 목록 편집](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   사용자 지정 특성을 추가하는 방법에 대한 자세한 내용은 [애플리케이션 특성 사용자 지정](../app-provisioning/customize-application-attributes.md)에서 확인할 수 있습니다.

> [!NOTE]
> 기본 형식 속성만 지원 됩니다(예: 문자열, 정수, 부울, 날짜/시간 등). 매개 변수 테이블 또는 여러 형식에 연결된 속성은 아직 지원되지 않습니다.

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 참조하세요.

11. Azure AD 프로비저닝 서비스를 디지털 프로세스 자동화를 위한 Bizagi Studio에 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태** 를 **켜기** 로 변경합니다.

    ![프로비저닝 상태 전환 스크린샷](common/provisioning-toggle-on.png)

12. 디지털 프로세스 자동화를 위한 Bizagi Studio에 프로비저닝할 사용자 및 그룹을 정의합니다. **설정** 섹션에서 **범위** 에 원하는 값을 선택합니다.

    ![범위 옵션의 스크린샷](common/provisioning-scope.png)

13. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![저장 컨트롤의 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="monitor-your-deployment"></a>배포 모니터링
프로비저닝이 구성되면 다음 리소스를 사용하여 배포를 모니터링합니다.

- [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
- [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
- 프로비저닝 구성이 비정상 상태에 있는 경우 애플리케이션이 격리됩니다. 자세한 내용은 [격리 상태에서의 애플리케이션 프로비저닝](../app-provisioning/application-provisioning-quarantine-status.md)을 참조하세요.  

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).