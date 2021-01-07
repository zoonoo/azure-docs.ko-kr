---
title: '자습서: Azure Active Directory에서 Workday 인바운드 프로비저닝 구성 | Microsoft Docs'
description: Workday에서 Azure AD로 인바운드 프로비저닝을 구성하는 방법을 알아봅니다.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 197b7ff0a6c613a019007ba507d678b619c9afd4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358595"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>자습서: Workday에서 Azure AD로 사용자 프로비저닝 구성
이 자습서에서는 작업자 데이터를 Workday에서 Azure Active Directory로 프로비저닝하기 위해 수행해야 하는 단계를 보여 줍니다. 

>[!NOTE]
>Workday에서 프로비저닝하도록 하려는 사용자가 온-프레미스 AD 계정이 필요하지 않은 클라우드 전용 사용자인 경우 이 자습서를 사용합니다. 사용자에게 온-프레미스 AD 계정만 필요하거나 AD 및 Azure AD 계정이 모두 필요한 경우 [Workday에서 Active Directory로 사용자 프로비저닝 구성](workday-inbound-tutorial.md) 자습서를 참조하세요. 

## <a name="overview"></a>개요

[Azure Active Directory 사용자 프로비전 서비스](../app-provisioning/user-provisioning.md)는 사용자 계정을 프로비전하기 위해 [Workday 인적 자원 API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)와 통합됩니다. Azure AD 사용자 프로비전 서비스에서 지원되는 Workday 사용자 프로비전 워크플로는 다음과 같은 인적 자원 및 ID 수명 주기 관리 시나리오의 자동화를 지원합니다.

* **새 직원 고용** - 새 직원이 Workday에 추가되면 사용자 계정이 Azure Active Directory 및 선택적으로 Microsoft 365 및 [Azure AD에서 지원하는 다른 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 자동으로 만들어지고, 이메일 주소가 Workday에 쓰기 저장됩니다.

* **직원 특성 및 프로필 업데이트** - 직원 레코드(예: 이름, 직위 및 관리자)가 Workday에서 업데이트되면 해당 사용자 계정이 Azure Active Directory 및 선택적으로 Microsoft 365 및 [Azure AD에서 지원하는 다른 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 자동으로 업데이트됩니다.

* **직원 해고** - 직원이 Workday에서 해고되면 해당 사용자 계정이 Azure Active Directory 및 선택적으로 Microsoft 365 및 [Azure AD에서 지원하는 다른 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 자동으로 사용하지 않도록 설정됩니다.

* **직원 재고용** - 직원이 Workday에서 다시 고용되면 해당 이전 계정이 Azure Active Directory 및 선택적으로 Microsoft 365 및 [Azure AD에서 지원하는 다른 SaaS 애플리케이션](../app-provisioning/user-provisioning.md)에서 기본 설정에 따라 자동으로 다시 활성화되거나 다시 프로비저닝될 수 있습니다.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 Workday에서 Azure Active Directory로 사용자 프로비저닝 솔루션이 적합한 조직은 다음과 같습니다.

* Workday 사용자 프로비전에 미리 작성된 클라우드 기반 솔루션을 원하는 조직

* 사용자를 Workday에서 Azure Active Directory로 직접 프로비저닝해야 하는 조직

* Workday에서 가져온 데이터를 사용하여 사용자를 프로비저닝해야 하는 조직

* Microsoft 365를 이메일에 사용하는 조직

## <a name="solution-architecture"></a>솔루션 아키텍처

이 섹션에서는 클라우드 전용 사용자를 위한 엔드투엔드 사용자 프로비저닝 솔루션 아키텍처에 대해 설명합니다. 두 가지의 관련된 흐름이 있습니다.

* **신뢰할 수 있는 HR 데이터 흐름 – Workday에서 Azure Active Directory로:** 이 흐름에서는 작업자 이벤트(예: 새 고용, 이동, 해고)가 먼저 Workday에서 발생한 다음, 이벤트 데이터가 Azure Active Directory로 이동합니다. 이벤트에 따라 Azure AD에서 만들기/업데이트/사용/사용 안 함 작업이 수행될 수 있습니다.
* **쓰기 저장 흐름 - 온-프레미스 Active Directory에서 Workday로:** Active Directory에서 계정 만들기가 완료되면 Azure AD Connect를 통해 Azure AD와 동기화되고 이메일, 사용자 이름 및 전화 번호와 같은 정보가 Workday에 쓰기 저장될 수 있습니다.

  ![개요](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>엔드투엔드 사용자 데이터 흐름

1. HR 팀이 Workday Employee Central에서 작업자 트랜잭션(참가자/이동자/탈퇴자 또는 새 고용/이동/해고)을 수행합니다.
2. Azure AD 프로비저닝 서비스에서 Workday EC에서 예약된 ID 동기화를 실행하고, 온-프레미스 Active Directory와 동기화하기 위해 처리해야 하는 변경 내용을 식별합니다.
3. Azure AD 프로비저닝 서비스에서 변경 내용을 확인하고, Azure AD의 사용자에 대한 만들기/업데이트/사용/사용 안 함 작업을 호출합니다.
4. [Workday 쓰기 저장](workday-writeback-tutorial.md) 앱이 구성되면 Azure AD에서 이메일, 사용자 이름 및 전화 번호와 같은 특성을 검색합니다. 
5. Azure AD 프로비저닝 서비스에서 이메일, 사용자 이름 및 전화 번호를 Workday에 설정합니다.

## <a name="planning-your-deployment"></a>배포 계획

클라우드 HR 구동 사용자 프로비저닝을 Workday에서 Azure AD로 수행하도록 구성하려면 다음과 같은 다양한 측면이 포함된 상당한 계획이 필요합니다.

* 일치하는 ID 확인 
* 특성 매핑
* 특성 변환 
* 범위 지정 필터

이러한 항목에 대한 포괄적인 지침은 [클라우드 HR 배포 계획](../app-provisioning/plan-cloud-hr-provision.md)을 참조하세요. 

## <a name="configure-integration-system-user-in-workday"></a>Workday에서 통합 시스템 사용자 구성

작업자 데이터를 검색할 수 있는 권한이 있는 Workday 통합 시스템 사용자 계정을 만들려면 [통합 시스템 사용자 구성](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) 섹션을 참조하세요. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Workday에서 Azure AD로 사용자 프로비저닝 구성

다음 섹션에서는 클라우드 전용 배포를 위해 Workday에서 Azure AD로 사용자 프로비저닝을 구성하는 단계를 설명합니다.

* [Azure AD 프로비전 커넥터 앱 추가 및 Workday에 대한 연결 만들기](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Workday 및 Azure AD 특성 매핑 구성](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1부: Azure AD 프로비전 커넥터 앱 추가 및 Workday에 대한 연결 만들기

**클라우드 전용 사용자에 대한 Workday-Azure Active Directory 프로비전을 구성하려면:**

1. [https://editor.swagger.io](<https://portal.azure.com>) 로 이동합니다.

2. Azure Portal에서 **Azure Active Directory** 를 검색하고 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.

4. **애플리케이션 추가** 를 선택한 후 **모두** 범주를 선택합니다.

5. **Workday에서 Azure AD로 사용자 프로비저닝** 을 검색하고, 갤러리에서 해당 앱을 추가합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비저닝** 을 선택합니다.

7. **프로비저닝** **모드** 를 **자동** 으로 변경합니다.

8. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **Workday 사용자 이름** – 테넌트 도메인 이름이 추가된 Workday 통합 시스템 계정의 사용자 이름을 입력합니다. 다음과 같은 형태여야 합니다. username@contoso4

   * **Workday 암호 –** Workday 통합 시스템 계정의 암호를 입력합니다.

   * **Workday Web Services API URL –** 테넌트의 Workday 웹 서비스 엔드포인트에 대한 URL을 입력합니다. URL은 커넥터에서 사용하는 Workday Web Services API의 버전을 결정합니다. 
   
     | URL 형식 | 사용되는 WWS API 버전 | XPATH 변경 필요 |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v21.1 | 예 |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v21.1 | 예 |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v##.# | yes |

      > [!NOTE]
     > 버전 정보가 URL에 지정되지 않으면 앱에서 WWS(Workday Web Services) v21.1을 사용하며 앱과 함께 제공되는 기본 XPATH API 식을 변경할 필요가 없습니다. 특정 WWS API 버전을 사용하려면 URL에서 버전 번호를 지정합니다. <br>
     > 예: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> WWS API v30.0 이상을 사용하는 경우 프로비저닝 작업을 설정하기 전에 [구성 관리](workday-inbound-tutorial.md#managing-your-configuration) 및 [Workday 특성 참조](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) 섹션을 참조하여 **특성 매핑 -> 고급 옵션 -> Workday 특성 목록 편집** 아래에서 **XPATH API 식** 을 업데이트하세요.  

   * **알림 메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.

   * **연결 테스트** 단추를 클릭합니다.

   * 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 연결 테스트가 실패하면 Workday에서 Workday URL 및 자격 증명이 유효한지 다시 확인합니다.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>2부: Workday 및 Azure AD 특성 매핑 구성

이 섹션에서는 클라우드 전용 사용자의 사용자 데이터가 Workday에서 Azure Active Directory로 흐르는 방식을 구성하겠습니다.

1. **매핑** 아래의 프로비전 탭에서 **Workday 작업자를 Azure AD와 동기화** 를 클릭합니다.

2. **원본 개체 범위** 필드에서 특성 기반 필터 세트를 정의하여 Azure AD 프로비전 범위에 포함할 Workday 사용자 세트를 선택할 수 있습니다. 기본 범위는 "Workday의 모든 사용자"입니다. 예제 필터:

   * 예제: 작업자 ID가 1000000-2000000 사이인 사용자를 범위에 포함

      * 특성: WorkerID

      * 연산자: REGEX 일치

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예제: 비정규직 직원만 포함하고 정규직 직원은 포함하지 않음

      * 특성: ContingentID

      * 연산자: NULL이 아님

3. **대상 개체 작업** 필드에서 Active AD에서 수행할 작업을 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트** 가 가장 일반적입니다.

4. **특성 매핑** 섹션에서 개별 Workday 특성이 Active Directory 특성에 매핑되는 방식을 정의할 수 있습니다.

5. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가** 를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

   * **매핑 유형**

      * **직접** – Workday 특성 값을 변경하지 않고 AD 특성에 씁니다.

      * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

      * **식** – 하나 이상의 Workday 특성에 따라 AD 특성에 사용자 지정 값을 쓸 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](../app-provisioning/functions-for-customizing-application-data.md).

   * **원본 특성** - Workday의 사용자 특성입니다. 원하는 특성이 없는 경우 [Workday 사용자 특성 목록 사용자 지정](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)을 참조하세요.

   * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

   * **대상 특성** – Azure AD의 사용자 특성입니다.

   * **이 특성을 사용하여 개체 일치** – Workday와 Azure AD 간에 사용자를 고유하게 식별하는 데 이 특성을 사용할지 여부를 나타냅니다. 이 값은 일반적으로 Active AD의 직원 ID 특성(신규) 또는 확장 특성에 매핑되는 Workday의 작업자 ID 필드에서 설정됩니다.

   * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

   * **이 매핑 적용**

     * **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.

     * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

6. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장** 을 클릭합니다.


## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

Workday 프로비전 앱 구성이 완료되면 Azure Portal에서 프로비전 서비스를 켤 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 오류 또는 Workday 데이터 문제가 있는 경우 프로비전 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태** 를 **켜기** 로 설정합니다.

2. **저장** 을 클릭합니다.

3. 이 작업을 수행하면 초기 동기화가 시작되고, Workday 테넌트에 있는 사용자 수에 따라 동기화에 걸리는 시간이 달라질 수 있습니다. 진행률 표시줄을 확인하여 동기화 주기의 진행 상황을 추적할 수 있습니다. 

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그에는 Workday에서 읽은 다음, Azure Active Directory에 추가 또는 업데이트되는 사용자와 같이 프로비저닝 서비스에서 수행되는 모든 개별 동기화 이벤트가 나열됩니다. 

5. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

   > [!div class="mx-imgBorder"]
   > ![프로비저닝 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>다음 단계

* [인바운드 프로비저닝에 지원되는 Workday 특성에 대해 자세히 알아봅니다.](../app-provisioning/workday-attribute-reference.md)
* [Workday 쓰기 저장을 구성하는 방법을 알아봅니다.](workday-writeback-tutorial.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [Workday와 Azure Active Directory 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.](workday-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로비저닝 구성을 내보내고 가져오는 방법을 알아봅니다.](../app-provisioning/export-import-provisioning-configuration.md)


