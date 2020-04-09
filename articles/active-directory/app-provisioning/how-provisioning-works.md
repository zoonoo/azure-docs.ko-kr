---
title: Azure AD 프로비저닝작동 방식 이해 | 마이크로 소프트 문서
description: Azure AD 프로비저닝의 작동 방식 이해
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 241d90981ed9ba54d253e6c22c00f9e5a9197863
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884888"
---
# <a name="how-provisioning-works"></a>프로비저닝 작동 방법

자동 프로비저닝은 사용자가 액세스해야 하는 클라우드 응용 프로그램에서 사용자 ID 및 역할을 만드는 것을 말합니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함됩니다. 배포를 시작하기 전에 이 문서를 검토하여 Azure AD 프로비저닝의 작동 방식을 알아보고 구성 권장 사항을 얻을 수 있습니다. 

**Azure AD 프로비저닝 서비스는** 응용 프로그램 공급업체에서 제공하는 SCIM(도메인 간 ID 관리) 2.0 사용자 관리 API 끝점에 연결하여 사용자를 SaaS 앱 및 기타 시스템에 프로비전합니다. 이 SCIM 끝점을 사용하면 Azure AD가 프로그래밍 방식으로 사용자를 생성, 업데이트 및 제거할 수 있습니다. 선택한 응용 프로그램의 경우 프로비저닝 서비스는 그룹 및 역할과 같은 추가 ID 관련 개체를 생성, 업데이트 및 제거할 수도 있습니다. Azure AD와 응용 프로그램 간에 프로비전하는 데 사용되는 채널은 HTTPS TLS 암호화를 사용하여 암호화됩니다.


![Azure AD 프로비전 서비스](./media/how-provisioning-works/provisioning0.PNG)
그림*1: Azure AD 프로비전 서비스*

![아웃바운드 사용자 프로비저닝 워크플로](./media/how-provisioning-works/provisioning1.PNG)
그림*2: Azure AD에서 인기 있는 SaaS 응용 프로그램에 이르는 "아웃바운드" 사용자 프로비저닝 워크플로우*

![인바운드 사용자 프로비저닝 워크플로](./media/how-provisioning-works/provisioning2.PNG)
그림*3: 인기 있는 HCM(휴먼 캐피탈 관리) 응용 프로그램에서 Azure Active Directory 및 Windows 서버 활성 디렉터리로 "인바운드" 사용자 프로비저닝 워크플로우*

## <a name="provisioning-using-scim-20"></a>SCIM 2.0을 사용한 프로비저닝

Azure AD 프로비전 서비스는 자동 프로비저닝을 위해 [SCIM 2.0 프로토콜을](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) 사용합니다. 이 서비스는 응용 프로그램의 SCIM 끝점에 연결하고 SCIM 사용자 개체 스키마 및 REST API를 사용하여 사용자 및 그룹의 프로비저닝 및 프로비저닝을 자동화합니다. SCIM 기반 프로비저닝 커넥터는 Azure AD 갤러리의 대부분의 응용 프로그램에 대해 제공됩니다. Azure AD용 앱을 빌드할 때 개발자는 SCIM 2.0 사용자 관리 API를 사용하여 프로비저닝을 위해 Azure AD를 통합하는 SCIM 끝점을 작성할 수 있습니다. 자세한 내용은 [SCIM 끝점 빌드 및 사용자 프로비저닝 구성을](../app-provisioning/use-scim-to-provision-users-and-groups.md)참조하십시오.

현재 앱이 없는 앱에 대한 자동 Azure AD 프로비저닝 커넥터를 요청하려면 [Azure Active Directory 응용 프로그램 요청을](https://aka.ms/aadapprequest)작성합니다.

## <a name="authorization"></a>권한 부여

Azure AD가 응용 프로그램의 사용자 관리 API에 연결하려면 자격 증명이 필요합니다. 응용 프로그램에 대한 자동 사용자 프로비저닝을 구성하는 동안 유효한 자격 증명을 입력해야 합니다. 앱 자습서를 참조하여 응용 프로그램에 대한 자격 증명 유형 및 요구 사항을 찾을 수 있습니다. Azure 포털에서 제공된 자격 증명을 사용하여 Azure AD가 앱의 프로비저닝 앱에 연결하도록 하여 자격 증명을 테스트할 수 있습니다.

SAML 기반 단일 사인온도 응용 프로그램에 대해 구성된 경우 Azure AD의 내부 응용 프로그램별 저장소 제한은 1024바이트입니다. 이 제한에는 응용 프로그램의 단일 인스턴스와 연결된 모든 인증서, 비밀 토큰, 자격 증명 및 관련 구성 데이터(Azure AD의 서비스 주체 레코드라고도 함)가 포함됩니다. SAML 기반 단일 사인온이 구성되면 SAML 토큰에 서명하는 데 사용되는 인증서가 공간의 50% 이상을 소비하는 경우가 많습니다. 사용자 프로비저닝 설정 중에 입력하는 모든 추가 항목(비밀 토큰, URIIs, 알림 전자 메일 주소, 사용자 이름 및 암호)은 저장소 제한을 초과할 수 있습니다. 자세한 내용은 [사용자 프로비저닝을 구성하는 동안 관리자 자격 증명을 저장하는 문제를](../manage-apps/application-provisioning-config-problem-storage-limit.md)참조하십시오.

## <a name="mapping-attributes"></a>특성 매핑

타사 SaaS 응용 프로그램에 대한 사용자 프로비전을 사용하도록 설정하면 Azure 포털은 특성 매핑을 통해 해당 특성 값을 제어합니다. 매핑은 사용자 계정을 프로비전하거나 업데이트할 때 Azure AD와 대상 응용 프로그램 간에 흐르는 사용자 특성을 결정합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 간에 미리 구성된 특성 및 특성 매핑 집합이 있습니다. 일부 앱은 그룹과 같은 사용자와 함께 다른 유형의 개체를 관리합니다.

프로비저닝을 설정할 때 Azure AD에서 응용 프로그램으로 흐르는 사용자(또는 그룹) 속성을 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것이 중요합니다. 두 시스템 간에 사용자/그룹을 고유하게 식별하고 일치시키는 데 사용되는 일치하는**속성(이 특성을 사용하여 개체 일치)을**검토하고 구성합니다.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존 특성 매핑을 변경하거나 삭제하거나 새 특성 매핑을 만들 수 있습니다. 자세한 내용은 [SaaS 응용 프로그램에 대한 사용자 프로비저닝 특성 매핑 사용자 지정을](../manage-apps/customize-application-attributes.md)참조하십시오.

SaaS 애플리케이션에 프로비전을 구성하면 식 매핑은 지정할 수 있는 특성 매핑의 유형 중 하나입니다. 이러한 매핑의 경우 사용자의 데이터를 SaaS 응용 프로그램에 더 적합한 형식으로 변환할 수 있는 스크립트와 같은 식을 작성해야 합니다. 자세한 내용은 [특성 매핑에 대한 식 쓰기를](functions-for-customizing-application-data.md)참조하십시오.

## <a name="scoping"></a>범위 지정 
### <a name="assignment-based-scoping"></a>과제 기반 범위 지정

Azure AD에서 SaaS 응용 프로그램으로 아웃바운드 프로비저닝의 경우 [사용자 또는 그룹 할당에](../manage-apps/assign-user-or-group-access-portal.md) 의존하는 것이 프로비저닝 범위에 있는 사용자를 확인하는 가장 일반적인 방법입니다. 사용자 할당은 단일 사인온을 사용하도록 설정하는 데도 사용되므로 액세스 및 프로비저닝을 모두 관리하는 데 동일한 방법을 사용할 수 있습니다. 할당 기반 범위 지정은 Workday 및 성공 요인과 같은 인바운드 프로비저닝 시나리오에는 적용되지 않습니다.

* **그룹.** Azure AD Premium 라이선스 플랜을 사용하여 그룹을 사용하여 SaaS 응용 프로그램에 대한 액세스를 할당할 수 있습니다. 그런 다음 프로비저닝 범위가 **할당된 사용자 및 그룹만 동기화하도록**설정되면 Azure AD 프로비저닝 서비스는 응용 프로그램에 할당된 그룹의 구성원인지 여부에 따라 사용자를 프로비전하거나 프로비저닝 해제합니다. 응용 프로그램이 그룹 개체를 지원하지 않는 한 그룹 개체 자체는 프로비전되지 않습니다. 응용 프로그램에 할당된 그룹에 "SecurityEnabled" 속성이 "False"로 설정되어 있는지 확인합니다.

* **동적 그룹입니다.** Azure AD 사용자 프로비저닝 서비스는 [동적 그룹에서](../users-groups-roles/groups-create-rule.md)사용자를 읽고 프로비전할 수 있습니다. 다음 주의 사항 및 권장 사항을 염두에 두십시오.

  * 동적 그룹은 Azure AD에서 SaaS 응용 프로그램으로 종단 간 프로비저닝의 성능에 영향을 미칠 수 있습니다.

  * 동적 그룹의 사용자가 SaaS 응용 프로그램에서 프로비전 되거나 프로비전 해제되는 속도는 동적 그룹이 멤버 자격 변경을 평가할 수 있는 속도에 따라 달라집니다. 동적 그룹의 처리 상태를 확인하는 방법에 대한 자세한 내용은 [멤버 자격 규칙에 대한 처리 상태 확인을](../users-groups-roles/groups-create-rule.md)참조하십시오.

  * 사용자가 동적 그룹의 멤버 자격을 잃으면 프로비저닝 해제 이벤트로 간주됩니다. 동적 그룹에 대한 규칙을 만들 때 이 시나리오를 고려하십시오.

* **중첩된 그룹입니다.** Azure AD 사용자 프로비전 서비스는 중첩된 그룹에서 사용자를 읽거나 프로비전할 수 없습니다. 서비스는 명시적으로 할당된 그룹의 직속 구성원인 사용자만 읽고 프로비전할 수 있습니다. "응용 프로그램에 대한 그룹 기반 할당"의 이러한 제한은 단일 사인온에도 영향을 [줍니다(그룹 사용으로 SaaS 응용 프로그램에 대한 액세스를 관리).](../users-groups-roles/groups-saasapps.md) 대신 프로비전해야 하는 사용자를 포함하는 그룹에 직접 할당하거나 [범위를](define-conditional-rules-for-provisioning-user-accounts.md) 지정합니다.

### <a name="attribute-based-scoping"></a>특성 기반 범위 지정 

범위 지정 필터를 사용하여 응용 프로그램에 프로비전되는 사용자를 결정하는 특성 기반 규칙을 정의할 수 있습니다. 이 메서드는 일반적으로 HCM 응용 프로그램에서 Azure AD 및 Active Directory로 인바운드 프로비저닝에 사용됩니다. 범위 지정 필터는 각 Azure AD 사용자 프로비전 커넥터에 대해 특성 매핑의 일부로 구성됩니다. 특성 기반 범위 지정 필터 구성에 대한 자세한 내용은 [범위 지정 필터를 사용 하 여 특성 기반 응용 프로그램 프로비저닝을](define-conditional-rules-for-provisioning-user-accounts.md)참조 합니다.

### <a name="b2b-guest-users"></a>B2B(게스트) 사용자

Azure AD 사용자 프로비전 서비스를 사용하여 Azure AD의 B2B(또는 게스트) 사용자를 SaaS 응용 프로그램에 프로비전할 수 있습니다. 그러나 B2B 사용자가 Azure AD를 사용하여 SaaS 응용 프로그램에 로그인하려면 SaaS 응용 프로그램에 SAML 기반 단일 사인온 기능이 특정 방식으로 구성되어야 합니다. B2B 사용자의 로그인을 지원하도록 SaaS 애플리케이션을 구성하는 방법에 대한 자세한 내용은 [B2B 협업을 위한 SaaS 앱 구성](../b2b/configure-saas-apps.md)을 참조하세요.

게스트 사용자의 userPrincipalName은 종종 "별칭#EXT#@domain.com"로 저장됩니다. userPrincipalName이 속성 매핑에 소스 특성으로 포함되면 #EXT#은 userPrincipalName에서 제거됩니다. #EXT#이 있어야 하는 경우 userPrincipalName을 원본UserPrincipalName을 소스 특성으로 바꿉니다. 

## <a name="provisioning-cycles-initial-and-incremental"></a>프로비저닝 주기: 초기 및 증분

Azure AD가 원본 시스템인 경우 프로비저닝 서비스는 사용 델타 쿼리를 사용하여 [Microsoft Graph 데이터의 변경 내용을 추적하여](https://docs.microsoft.com/graph/delta-query-overview) 사용자 및 그룹을 모니터링합니다. 프로비저닝 서비스는 원본 시스템 및 대상 시스템에 대해 초기 주기를 실행한 다음 주기적인 증분 주기를 실행합니다.

### <a name="initial-cycle"></a>초기 주기

프로비저닝 서비스가 시작되면 첫 번째 주기는 다음과 같은 것입니다.

1. [특성 매핑](customize-application-attributes.md)에 정의된 모든 특성을 검색하여 소스 시스템에서 모든 사용자와 그룹을 쿼리합니다.

2. 구성된 [할당](../manage-apps/assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.

3. 사용자가 할당되거나 프로비저닝 범위에 할당되면 서비스는 지정된 일치 특성을 사용하여 일치하는 사용자에 대해 대상 시스템을 [쿼리합니다.](customize-application-attributes.md#understanding-attribute-mapping-properties) 예: 소스 시스템의 userPrincipal 이름이 일치하는 특성이고 대상 시스템의 userName에 매핑되는 경우 프로비저닝 서비스는 대상 시스템에서 소스 시스템의 userPrincipal 이름 값과 일치하는 userNames를 쿼리합니다.

4. 대상 시스템에서 일치하는 사용자를 찾을 수 없는 경우 원본 시스템에서 반환된 특성을 사용하여 만들어집니다. 사용자 계정을 만든 후 프로비저닝 서비스는 새 사용자에 대한 대상 시스템의 ID를 검색하고 캐시합니다. 이 ID는 해당 사용자에 대한 모든 향후 작업을 실행하는 데 사용됩니다.

5. 일치하는 사용자가 발견되면 원본 시스템에서 제공하는 특성을 사용하여 업데이트됩니다. 사용자 계정이 일치하면 프로비저닝 서비스는 새 사용자에 대한 대상 시스템의 ID를 검색하고 캐시합니다. 이 ID는 해당 사용자에 대한 모든 향후 작업을 실행하는 데 사용됩니다.

6. 특성 매핑에 "참조" 특성이 포함된 경우 서비스는 대상 시스템에서 추가 업데이트를 수행하여 참조된 개체를 만들고 연결합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.

7. 초기 주기가 끝날 때 워터마크를 유지하여 이후 증분 주기의 시작점을 제공합니다.

ServiceNow, G Suite 및 Box와 같은 일부 응용 프로그램은 사용자를 프로비전할 뿐만 아니라 그룹 및 해당 구성원을 프로비전하는 것도 지원합니다. 이러한 경우 [매핑에서](customize-application-attributes.md)그룹 프로비저닝이 활성화된 경우 프로비저닝 서비스는 사용자와 그룹을 동기화한 다음 나중에 그룹 구성원을 동기화합니다.

### <a name="incremental-cycles"></a>증분 주기

초기 주기 후, 다른 모든 사이클 은 다음과 같은 것입니다.

1. 소스 시스템에서 마지막 워터마크가 저장된 이후 업데이트된 사용자 및 그룹을 쿼리합니다.

2. 구성된 [할당](../manage-apps/assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.

3. 사용자가 할당되거나 프로비저닝 범위에 할당되면 서비스는 지정된 일치 특성을 사용하여 일치하는 사용자에 대해 대상 시스템을 [쿼리합니다.](customize-application-attributes.md#understanding-attribute-mapping-properties)

4. 대상 시스템에서 일치하는 사용자를 찾을 수 없는 경우 원본 시스템에서 반환된 특성을 사용하여 만들어집니다. 사용자 계정을 만든 후 프로비저닝 서비스는 새 사용자에 대한 대상 시스템의 ID를 검색하고 캐시합니다. 이 ID는 해당 사용자에 대한 모든 향후 작업을 실행하는 데 사용됩니다.

5. 일치하는 사용자가 발견되면 원본 시스템에서 제공하는 특성을 사용하여 업데이트됩니다. 일치하는 새로 할당된 계정인 경우 프로비저닝 서비스는 새 사용자에 대한 대상 시스템의 ID를 검색하고 캐시합니다. 이 ID는 해당 사용자에 대한 모든 향후 작업을 실행하는 데 사용됩니다.

6. 특성 매핑에 "참조" 특성이 포함된 경우 서비스는 대상 시스템에서 추가 업데이트를 수행하여 참조된 개체를 만들고 연결합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.

7. 이전에 프로비저닝 범위에 있던 사용자가 할당되지 않은 것을 포함하여 범위에서 제거된 경우 서비스는 업데이트를 통해 대상 시스템의 사용자를 비활성화합니다.

8. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 해제 또는 일시 삭제되면 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.

9. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 영구 삭제되면 서비스는 대상 시스템에서 사용자를 삭제합니다. Azure AD에서 사용자는 일시 삭제된 후 30일 후에 하드 삭제됩니다.

10. 증분 주기가 끝날 때 새 워터마크를 유지하여 이후 증분 주기의 시작점을 제공합니다.

> [!NOTE]
> [선택적으로 [매핑]](customize-application-attributes.md) 섹션에서 **대상 개체 작업** 확인란을 사용하여 **만들기,** **업데이트**또는 **삭제** 작업을 비활성화할 수 있습니다. 업데이트 중에 사용자를 해제하는 논리도 “accountEnabled”와 같은 필드의 특성 매핑을 통해 제어됩니다.

프로비저닝 서비스는 [각 응용 프로그램에 특정한 자습서에](../saas-apps/tutorial-list.md)정의된 간격으로 백투백 증분 주기를 무기한 계속 실행합니다. 증분 주기는 다음 이벤트 중 하나가 발생할 때까지 계속됩니다.

- 서비스는 Azure 포털사용 또는 적절한 Microsoft 그래프 API 명령을 사용하여 수동으로 중지됩니다.
- Azure 포털에서 **지우기 상태 및 다시 시작** 옵션을 사용하거나 적절한 Microsoft Graph API 명령을 사용하여 새 초기 주기가 트리거됩니다. 이 작업을 수행하면 저장된 워터마크가 지워지고 모든 소스 개체가 다시 평가됩니다.
- 특성 매핑 또는 범위 지정 필터가 변경되어 새 초기 주기가 트리거됩니다. 또한 이 작업을 수행하면 저장된 워터마크가 지워지고 모든 소스 개체가 다시 평가됩니다.
- 프로비저닝 프로세스는 높은 오류율로 인해 검역(아래 참조)으로 전환되며 4주 이상 격리 상태로 유지됩니다. 이 이벤트가 발생하면 자동으로 서비스를 사용할 수 없게 됩니다.

### <a name="errors-and-retries"></a>오류 및 다시 시도

대상 시스템의 오류로 인해 개별 사용자가 대상 시스템에서 추가, 업데이트 또는 삭제되지 않으면 다음 동기화 주기에서 작업이 다시 시도됩니다. 사용자가 계속 실패하면 감소된 빈도로 다시 시도되기 시작하여, 점차 하루에 한 번만 시도되는 빈도까지 축소됩니다. 오류를 해결하려면 관리자는 [프로비저닝 로그를](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 확인하여 근본 원인을 확인하고 적절한 조치를 취해야 합니다. 일반적인 오류는 다음과 같습니다.

- 대상 시스템에서 필요한 특성이 사용자의 소스 시스템에 채워져 있지 않음
- 원본 시스템에 고유한 제약 조건이 있고 다른 사용자 레코드에 동일한 값이 있는 사용자

원본 시스템에서 영향을 받는 사용자의 특성 값을 조정하거나 충돌을 일으키지 않도록 특성 매핑을 조정하여 이러한 오류를 해결합니다.

### <a name="quarantine"></a>격리

오류(예: 잘못된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행된 호출의 대부분 또는 전부가 일관되게 실패하면 프로비저닝 작업은 "격리" 상태로 전환됩니다. 이 상태는 Azure 포털에서 전자 메일 알림이 구성된 경우 [프로비저닝 요약 보고서와](../manage-apps/check-status-user-account-provisioning.md) 전자 메일을 통해 표시됩니다.

격리 시 증분 주기의 빈도가 점차 하루에 한 번으로 줄어듭니다.

모든 잘못된 오류가 수정되고 다음 동기화 주기가 시작된 후 프로비저닝 작업이 격리를 종료합니다. 프로비저닝 작업이 4주 넘게 격리 상태로 유지되면 프로비저닝 작업을 사용할 수 없게 됩니다. 여기에서 격리 [상태에](../manage-apps/application-provisioning-quarantine-status.md)대해 자세히 알아보십시오.

### <a name="how-long-provisioning-takes"></a>프로비저닝 소요 시간

성능은 프로비저닝 작업이 초기 프로비저닝 주기를 실행하고 있는지 또는 증분 주기를 실행하는지에 따라 달라집니다. 프로비저닝에 걸리는 시간 및 프로비저닝 서비스의 상태를 모니터링하는 방법에 대한 자세한 내용은 [사용자 프로비저닝 상태 확인을](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)참조하십시오.

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>사용자가 제대로 프로비전되고 있는지 를 알리는 방법

사용자 프로비저닝 서비스에서 실행되는 모든 작업은 Azure AD [프로비저닝 로그(미리 보기)에 기록됩니다.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 로그는 원본 및 대상 시스템에 대한 모든 읽기 및 쓰기 작업과 각 작업 중에 읽거나 쓴 사용자 데이터를 포함합니다. Azure 포털에서 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [프로비저닝 보고 가이드를](../manage-apps/check-status-user-account-provisioning.md)참조하십시오.

## <a name="de-provisioning"></a>프로비저닝 해제

Azure AD 프로비저닝 서비스는 사용자가 더 이상 액세스 권한이 없어야 할 때 계정을 프로비저닝 해제하여 원본 및 대상 시스템을 동기화합니다. 

Azure AD 프로비저닝 서비스는 응용 프로그램이 소프트 삭제(활성 = false로 업데이트 요청)를 suupports하고 다음 이벤트가 발생할 때 응용 프로그램에서 사용자를 일시 삭제합니다.

* Azure AD에서 사용자 계정이 삭제됩니다.
*   사용자가 응용 프로그램에서 할당되지 않은 경우
*   사용자가 더 이상 범위 지정 필터를 충족하지 않고 범위를 벗어납니다.
    * 기본적으로 Azure AD 프로비저닝 서비스 소프트삭제 또는 범위를 벗어난 사용자를 비활성화합니다. 이 기본 동작을 재정의하려는 경우 [범위를 벗어난 삭제를 건너뛰도록](../app-provisioning/skip-out-of-scope-deletions.md)플래그를 설정할 수 있습니다.
*   AccountEnabled 속성이 False로 설정됩니다.

위의 네 가지 이벤트 중 하나가 발생하고 대상 응용 프로그램이 소프트 삭제를 지원하지 않는 경우 프로비저닝 서비스는 앱에서 사용자를 영구적으로 삭제하는 DELETE 요청을 보냅니다. 

Azure AD에서 사용자가 삭제된 후 30일 후에 테넌트에서 영구적으로 삭제됩니다. 이 시점에서 프로비저닝 서비스는 응용 프로그램에서 사용자를 영구적으로 삭제하는 DELETE 요청을 보냅니다. 30일 기간 동안 언제든지 [수동으로 사용자를 영구적으로 삭제할](../fundamentals/active-directory-users-restore.md)수 있으며, 이 경우 응용 프로그램에 삭제 요청을 보냅니다.

특성 매핑에 IsSoftDeleted 특성이 표시되면 사용자의 상태와 활성 = false 를 사용하여 업데이트 요청을 보낼지 여부를 결정하는 데 사용됩니다. 

## <a name="next-steps"></a>다음 단계

[자동 사용자 프로비저닝 배포 계획](../app-provisioning/plan-auto-user-provisioning.md)

[갤러리 앱 프로비저닝 구성](../manage-apps/configure-automatic-user-provisioning-portal.md)

[SCIM 엔드포인트를 구축하고 자체 앱을 만들 때 프로비저닝 구성](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[응용 프로그램에 사용자를 구성하고 프로비전하는 문제를 해결합니다.](../manage-apps/application-provisioning-config-problem.md)
