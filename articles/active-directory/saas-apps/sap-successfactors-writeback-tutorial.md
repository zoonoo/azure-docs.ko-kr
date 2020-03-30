---
title: '자습서: Azure 활성 디렉터리에서 successFactors 쓰기 복구 구성 | 마이크로 소프트 문서'
description: Azure AD에서 SuccessFactors로 특성 쓰기를 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060051"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>자습서: Azure AD에서 SAP 성공 요소로 특성 쓰기 를 구성(미리 보기)
이 자습서의 목적은 Azure AD에서 SuccessFactors 직원 중앙에 대한 쓰기 복구 특성을 작성하기 위해 수행해야 하는 단계를 보여 드리는 것입니다. 현재 쓰기 회수에 대해 지원되는 유일한 특성은 전자 메일 특성입니다. 

## <a name="overview"></a>개요

[SuccessFactors를 온-프레미스 AD](sap-successfactors-inbound-provisioning-tutorial.md) 프로비저닝 앱으로 인바운드 프로비저닝 통합을 설정한 후 Azure AD 프로비저닝 [앱에 대한 SuccessFactors](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 프로비저닝 앱에 대한 SuccessFactors 쓰기 백 앱을 선택적으로 구성하여 이메일 주소를 SuccessFactors로 다시 쓸 수 있습니다. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 SuccessFactors 쓰기 백 사용자 프로비저닝 솔루션은 다음과 같은 경우에 이상적입니다.

* IT에서 관리하는 신뢰할 수 있는 특성(예: 전자 메일 주소)을 SuccessFactors로 다시 다시 백업하려는 Office 365를 사용하는 조직

## <a name="configuring-successfactors-for-the-integration"></a>통합을 위한 성공 요소 구성

모든 SuccessFactors 프로비저닝 커넥터의 공통 요구 사항은 SuccessFactors OData API를 호출할 수 있는 올바른 권한이 있는 SuccessFactors 계정의 자격 증명이 필요하다는 것입니다. 이 섹션에서는 SuccessFactors에서 서비스 계정을 만들고 적절한 권한을 부여하는 단계를 설명합니다. 

* [SuccessFactors에서 API 사용자 계정 생성/식별](#createidentify-api-user-account-in-successfactors)
* [API 사용 권한 역할 만들기](#create-an-api-permissions-role)
* [API 사용자에 대한 권한 그룹 만들기](#create-a-permission-group-for-the-api-user)
* [권한 그룹에 권한 부여 역할 부여](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>SuccessFactors에서 API 사용자 계정 생성/식별
SuccessFactors 관리자 팀 또는 구현 파트너와 협력하여 OData API를 호출하는 데 사용할 SuccessFactors에서 사용자 계정을 만들거나 식별합니다. Azure AD에서 프로비저닝 앱을 구성할 때 이 계정의 사용자 이름 및 암호 자격 증명이 필요합니다. 

### <a name="create-an-api-permissions-role"></a>API 사용 권한 역할 만들기

* 관리자 센터에 액세스할 수 있는 사용자 계정으로 SAP SuccessFactors에 로그인합니다.
* 사용 *권한 역할 관리를*검색한 다음 검색 결과에서 **권한 역할 관리를** 선택합니다.
  ![권한 역할 관리](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 권한 역할 목록에서 **새 만들기를 클릭합니다.**
  > [!div class="mx-imgBorder"]
  > ![새 권한 역할 만들기](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 새 사용 권한 역할에 대한 **역할 이름** 및 **설명을** 추가합니다. 이름과 설명은 역할이 API 사용 권한에 대한 것임을 나타내야 합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 역할 세부 정보](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 권한 설정에서 **권한...** **Manage Integration Tools** **관리자가 기본 인증을 통해 OData API에 액세스할 수 있도록 허용하는**확인란을 선택합니다.
  > [!div class="mx-imgBorder"]
  > ![통합 도구 관리](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 동일한 상자에서 아래로 스크롤하여 **직원 중앙 API를**선택합니다. ODATA API를 사용하여 읽고 ODATA API를 사용하여 편집하려면 아래와 같이 권한을 추가합니다. 성공 요소에 쓰기 시나리오에 대해 동일한 계정을 사용하려는 경우 편집 옵션을 선택합니다. 
  > [!div class="mx-imgBorder"]
  > ![쓰기 권한 읽기](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* **완료**를 클릭합니다. **변경 내용 저장**을 클릭합니다.

### <a name="create-a-permission-group-for-the-api-user"></a>API 사용자에 대한 권한 그룹 만들기

* SuccessFactors 관리 센터에서 권한 *그룹 관리를*검색한 다음 검색 결과에서 **권한 그룹 관리를** 선택합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 관리](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 권한 그룹 관리 창에서 **새 .**
  > [!div class="mx-imgBorder"]
  > ![새 그룹 추가](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 새 그룹에 대한 그룹 이름을 추가합니다. 그룹 이름은 그룹이 API 사용자를 위한 것임을 나타내야 합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 이름](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* 그룹에 구성원을 추가합니다. 예를 들어 피플 풀 드롭다운 메뉴에서 **사용자 이름을** 선택한 다음 통합에 사용할 API 계정의 사용자 이름을 입력할 수 있습니다. 
  > [!div class="mx-imgBorder"]
  > ![그룹 멤버 추가](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* **완료를** 클릭하여 권한 그룹 만들기를 완료합니다.

### <a name="grant-permission-role-to-the-permission-group"></a>권한 그룹에 권한 부여 역할 부여

* SuccessFactors 관리 센터에서 *권한 관리 역할을*검색한 다음 검색 결과에서 권한 역할 **관리를** 선택합니다.
* 권한 **역할 목록에서**API 사용 권한에 대해 만든 역할을 선택합니다.
* **이 역할 부여에서...** **Add...**
* 드롭다운 메뉴에서 **권한 그룹을** 선택한 다음 **선택...을** 클릭하여 그룹 창을 열어 위에서 만든 그룹을 검색하고 선택합니다. 
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 추가](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 권한 그룹에 대한 권한 역할 부여를 검토합니다. 
  > [!div class="mx-imgBorder"]
  > ![권한 역할 및 그룹 세부 정보](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* **변경 내용 저장**을 클릭합니다.

## <a name="configuring-successfactors-writeback"></a>성공 요소 쓰기 회수 구성

이 섹션에서는 

* [프로비저닝 커넥터 앱을 추가하고 SuccessFactors에 연결을 구성합니다.](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [특성 매핑 구성](#part-2-configure-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1부: 프로비저닝 커넥터 앱을 추가하고 SuccessFactors에 대한 연결을 구성합니다.

**성공 요소 쓰기 를 구성하려면 다음을 수행합니다.**

1. [https://editor.swagger.io](<https://portal.azure.com>) 으로 이동합니다.

2. 왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

4. **애플리케이션 추가**를 선택하고 **모두** 범주를 선택합니다.

5. **SuccessFactors 쓰기 백을**검색하고 갤러리에서 해당 앱을 추가합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7. **프로비전** **모드**를 **자동**으로 변경합니다.

8. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리자 사용자 이름** - 회사 ID가 추가된 SuccessFactors API 사용자 계정의 사용자 이름을 입력합니다. 그것은 형식을 가지고 : **사용자\@이름 companyID**

   * **관리자 암호 -** SuccessFactors API 사용자 계정의 암호를 입력합니다. 

   * **테넌트 URL –** SuccessFactors OData API 서비스 끝점의 이름을 입력합니다. http 또는 https 없이 서버의 호스트 이름만 입력합니다. 이 값은 다음과 같아야 합니다 **api-server-name.successfactors.com.**

   * **알림 메일 –** 메일 주소를 입력하고 “오류가 발생하면 메일 보내기” 확인란을 선택합니다.
    > [!NOTE]
    > Azure AD 프로비전 서비스는 프로비전 작업이 [격리](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 실패하면 SuccessFactors 자격 증명 및 URL이 유효한지 다시 한 번 확인합니다.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 자격 증명이 성공적으로 저장되면 **매핑** 섹션에 Azure **Active Directory 사용자를 SuccessFactors로 동기화하는** 기본 매핑이 표시됩니다.

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성

이 섹션에서는 사용자 데이터가 SuccessFactors에서 Active Directory로 흐르는 방식을 구성합니다.

1. 매핑 아래의 프로비저닝 탭에서 **Azure Active Directory 사용자를 SuccessFactors로 동기화를** **클릭합니다.**

1. 소스 **개체 범위** 필드에서 특성 기반 필터 집합을 정의하여 쓰기 복구에 대해 고려할 Azure AD의 사용자 집합을 선택할 수 있습니다. 기본 범위는 "Azure AD의 모든 사용자"입니다. 
   > [!TIP]
   > 프로비전 앱을 처음 구성하는 경우 특성 매핑 및 식을 테스트하고 확인하여 원하는 결과를 제공하는지 확인해야 합니다. **소스 개체 범위** 아래의 범위 지정 필터를 사용하여 Azure AD의 몇 명의 테스트 사용자와 함께 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **대상 개체 작업** 필드는 **업데이트** 작업만 지원합니다.

1. 특성 **매핑** 섹션에서는 SuccessFactors 사용자 프로필을 Azure AD 사용자와 연결하는 데 사용되는 일치 ID만 변경할 수 있으며 Azure AD의 속성은 전자 메일의 원본역할을 합니다. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >SuccessFactors 쓰기 철회는 전자 메일 특성만 지원합니다. 새 특성을 추가하려면 **새 매핑 추가를** 사용하지 마십시오. 

1. 매핑을 저장하려면 속성 매핑 섹션 상단의 **저장을** 클릭합니다.

특성 매핑 구성이 완료되면 이제 [사용자 프로비저닝 서비스를 사용하도록 설정하고 시작](#enable-and-launch-user-provisioning)할 수 있습니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

SuccessFactors 프로비저닝 앱 구성이 완료되면 Azure 포털에서 프로비저닝 서비스를 켤 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 오류 또는 Workday 데이터 문제가 있는 경우 프로비전 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 이 작업은 초기 동기화를 시작하며 SuccessFactors 테넌트에 있는 사용자 수에 따라 가변적인 시간이 걸릴 수 있습니다. 진행률 표시줄을 확인하여 동기화 주기의 진행률을 추적할 수 있습니다. 

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그는 Workday에서 어떤 사용자를 읽은 다음, Active Directory에 추가 또는 업데이트되는지와 같은 프로비저닝 서비스에서 수행한 모든 개별 동기화 이벤트를 나열합니다. 

5. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

   > [!div class="mx-imgBorder"]
   > ![프로비저닝 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [SuccessFactors와 Azure Active Directory 간에 단일 사인온을 구성하는 방법에 대해 알아봅니다.](successfactors-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로비저닝 구성내보내기 및 가져오기 방법 알아보기](../app-provisioning/export-import-provisioning-configuration.md)

