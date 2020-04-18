---
title: Azure AD 특성 매핑 사용자 지정 | 마이크로 소프트 문서
description: Azure Active Directory의 Saas 앱에 대한 어떤 특성 매핑이 있고 어떻게 비즈니스 요구 사항에 맞게 수정하는지를 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfee19e9cfd1def71ebad82c2210ffc10146c896
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639753"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Azure Active Directory에서 SaaS 응용 프로그램에 대한 사용자 프로비저닝 특성 매핑 사용자 지정

Microsoft Azure AD는 Salesforce, G Suite 등과 같은 타사 SaaS 응용 프로그램에 대한 사용자 프로비저닝을 지원합니다. 타사 SaaS 응용 프로그램에 대한 사용자 프로비전을 사용하도록 설정하면 Azure 포털은 특성 매핑을 통해 특성 값을 제어합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 간에 미리 구성된 특성 및 특성 매핑 집합이 있습니다. 일부 앱은 그룹과 같은 사용자와 함께 다른 유형의 개체를 관리합니다.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존 특성 매핑을 변경하거나 삭제하거나 새 특성 매핑을 만들 수 있습니다.

## <a name="editing-user-attribute-mappings"></a>사용자 특성 매핑 편집

다음 단계에 따라 사용자 프로비저닝의 **매핑** 기능에 액세스합니다.

1. Azure Active [디렉터리 포털에](https://aad.portal.azure.com)로그인합니다.
1. 왼쪽 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다. 갤러리에서 추가된 앱을 포함하여 구성된 모든 앱 목록이 표시됩니다.
1. 보고서를 보고 앱 설정을 관리할 수 있는 앱 관리 창을 로드하려면 앱을 선택합니다.
1. 선택한 앱에 대한 사용자 계정 프로비저닝 설정을 관리하려면 **프로비저닝을** 선택합니다.
1. **매핑을** 확장하여 Azure AD와 대상 응용 프로그램 간에 흐르는 사용자 특성을 보고 편집합니다. 대상 응용 프로그램이 지원하는 경우 이 섹션에서는 선택적으로 그룹 및 사용자 계정의 프로비저닝을 구성할 수 있습니다.

   ![매핑을 사용하여 사용자 속성 보고 편집](./media/customize-application-attributes/21.png)

1. 매핑 **구성을** 선택하여 관련 **특성 매핑** 화면을 엽니다. 일부 특성 매핑은 SaaS 응용 프로그램에서 제대로 작동하려면 필요합니다. 필수 특성인 경우 **삭제** 기능을 사용할 수 없습니다.

   ![속성 매핑을 사용하여 앱에 대한 특성 매핑 구성](./media/customize-application-attributes/22.png)

   이 스크린샷에서 Salesforce에서 관리되는 개체의 **사용자 이름** 특성이 연결된 Azure Active Directory 개체의 **userPrincipalName** 값으로 채워지는 것을 볼 수 있습니다.

1. 기존 **특성 매핑을** 선택하여 특성 편집 화면을 **엽니다.** 여기서 Azure AD와 대상 응용 프로그램 간에 흐르는 사용자 특성을 편집할 수 있습니다.

   ![특성 편집을 사용하여 사용자 속성 편집](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>특성 매핑 유형 이해

특성 매핑으로 타사 SaaS 애플리케이션에서 특성이 채워지는 법을 제어합니다.
4 가지의 다른 매핑 형식이 지원됩니다.

- **직접** - Azure AD에서 대상 특성이 연결된 개체의 특성 값으로 채워집니다.
- **상수** - 대상 특성은 지정한 특정 문자열로 채워집니다.
- **식** - 대상 특성이 스크립트와 유사한 식 결과를 기준으로 채워집니다.
  자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)을 참조하세요.
- **없음** - 대상 특성이 수정되지 않습니다. 그러나 대상 특성이 비어 있으면 지정한 기본값으로 채워집니다.

이러한 네 가지 기본 형식과 함께 사용자 지정 특성 매핑은 선택적 **기본값** 할당의 개념을 지원합니다. 기본값 할당은 Azure AD 또는 대상 개체에 값이 없는 경우 대상 특성이 값으로 채워지도록 합니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.

### <a name="understanding-attribute-mapping-properties"></a>특성 매핑 속성 이해

이전 섹션에서는 특성 매핑 유형 속성이 이미 도입되었습니다.
이 속성과 함께 특성 매핑은 다음 특성도 지원합니다.

- **원본 특성** - 원본 시스템의 사용자 특성(예: Azure Active Directory).
- **대상 특성** – 대상 시스템의 사용자 특성(예: ServiceNow).
- **기본값 if null(선택 사항)** - 소스 특성이 null인 경우 대상 시스템에 전달되는 값입니다. 이 값은 사용자가 만들어질 때만 프로비전됩니다. 기존 사용자를 업데이트할 때 "null 때 기본값"이 프로비전되지 않습니다. 예를 들어 특정 작업 제목으로 대상 시스템의 모든 기존 사용자를 프로비전하려는 경우(원본 시스템에서 null인 경우), 다음 [표현식을](../app-provisioning/functions-for-customizing-application-data.md)사용할 수 있습니다: 스위치([jobTitle]), "DefaultValue", "True", [jobTitle]. "기본값"을 소스 시스템에서 null일 때 프로비전할 내용으로 바꿔야 합니다. 
- **이 특성을 사용하여 개체 일치** – 이 매핑을 사용하여 원본과 대상 시스템 간의 사용자를 고유하게 식별할 수 있는지 여부입니다. 일반적으로 Azure AD의 userPrincipalName 또는 메일 특성에 설정되며, 일반적으로 대상 응용 프로그램의 사용자 이름 필드에 매핑됩니다.
- **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 여러 개있는 경우 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.
- **이 매핑 적용**
  - **항상** – 사용자 만들기 및 업데이트 작업 모두에 이 매핑을 적용합니다.
  - **만드는 동안에만** - 사용자 만들기 작업에만 이 매핑을 적용합니다.

## <a name="matching-users-in-the-source-and-target--systems"></a>소스 및 대상 시스템의 사용자 일치
Azure AD 프로비저닝 서비스는 "greenfield" 시나리오(사용자가 대상 시스템에서 종료되지 않는 경우) 및 "브라운필드" 시나리오(사용자가 대상 시스템에 이미 있는 경우)에 배포할 수 있습니다. 두 시나리오를 모두 지원하기 위해 프로비저닝 서비스는 일치하는 특성의 개념을 사용합니다. 일치하는 특성을 사용하면 원본에서 사용자를 고유하게 식별하고 대상에서 사용자를 일치시키는 방법을 결정할 수 있습니다. 배포 계획의 일환으로 원본 및 대상 시스템에서 사용자를 고유하게 식별하는 데 사용할 수 있는 특성을 식별합니다. 주의해야 할 사항:

- **일치하는 특성은 고유해야 합니다.** 고객은 종종 userPrincipalName, 메일 또는 개체 ID와 같은 특성을 일치 특성으로 사용합니다.
- **여러 특성을 일치하는 특성으로 사용할 수 있습니다.** 사용자와 일치하는 순서(UI에서 일치하는 우선 순위로 정의)를 일치시킬 때 평가할 여러 특성을 정의할 수 있습니다. 예를 들어 세 가지 특성을 일치하는 특성으로 정의하고 처음 두 특성을 평가한 후 사용자가 고유하게 일치하는 경우 서비스는 세 번째 특성을 평가하지 않습니다. 서비스는 지정된 순서로 일치하는 특성을 평가하고 일치가 발견될 때 평가를 중지합니다.  
- **원본과 대상의 값이 정확히 일치할 필요는 없습니다.** 대상의 값은 원본에서 값의 몇 가지 간단한 함수일 수 있습니다. 따라서 원본에 emailAddress 특성이 있고 대상의 userPrincipalName이 있고 일부 문자를 일정한 값으로 대체하는 emailAddress 특성의 함수와 일치할 수 있습니다.  
- **특성 조합에 따른 일치는 지원되지 않습니다.** 대부분의 응용 프로그램은 두 속성을 기반으로 쿼리를 지원하지 않습니다. 따라서 특성의 조합에 따라 일치시킬 수 없습니다. 단일 속성을 차례로 평가할 수 있습니다.
- **모든 사용자는 하나 이상의 일치하는 특성에 대한 값을 가져야 합니다.** 일치하는 특성을 정의하는 경우 모든 사용자는 원본 시스템에서 해당 특성에 대한 값을 가져야 합니다. 예를 들어 userPrincipalName을 일치하는 특성으로 정의하는 경우 모든 사용자에게 userPrincipalName이 있어야 합니다. 여러 일치 특성(예: extensionAttribute1 및 mail)을 정의하는 경우 모든 사용자가 동일한 일치 특성을 가질 필요는 없습니다. 한 사용자는 다른 사용자가 메일을 가질 수 있지만 extensionAttribute1은 메일을 가질 수 있지만 메일은 메일을 가질 수 없지만 메일은 메일을 가질 수는 없지만 Mail은 할 수 없습니다. 
- **대상 응용 프로그램은 일치 특성에 대한 필터링을 지원해야 합니다.** 응용 프로그램 개발자는 사용자 또는 그룹 API에서 특성의 하위 집합을 필터링할 수 있습니다. 갤러리의 응용 프로그램의 경우 기본 특성 매핑이 대상 응용 프로그램의 API에서 필터링을 지원하는 특성에 대한 것이있는지 확인합니다. 대상 응용 프로그램에 대한 기본 일치 특성을 변경할 때 타사 API 설명서를 확인하여 특성을 필터링할 수 있는지 확인합니다.  

## <a name="editing-group-attribute-mappings"></a>그룹 특성 매핑 편집

ServiceNow, Box 및 G Suite와 같은 선택한 수의 응용 프로그램은 그룹 개체 및 사용자 개체를 프로비전하는 기능을 지원합니다. 그룹 개체에는 그룹 구성원과 함께 표시 이름 및 전자 메일 별칭과 같은 그룹 속성이 포함될 수 있습니다.

![예제는 프로비저닝된 그룹 및 사용자 개체가 있는 ServiceNow를 보여 주며](./media/customize-application-attributes/24.png)

매핑에서 그룹 매핑을 선택하고 **속성 매핑** 화면에서 원하는 옵션으로 **활성화를** 설정하여 그룹 프로비저닝을 선택적으로 활성화하거나 비활성화할 수 **있습니다.**

그룹 개체의 일부로 프로비전된 특성은 이전에 설명한 사용자 개체와 동일한 방식으로 사용자 지정할 수 있습니다. 

> [!TIP]
> 그룹 개체(속성 및 멤버) 프로비전은 애플리케이션에 [그룹을 할당](../manage-apps/assign-user-or-group-access-portal.md)하는 것과 별개의 개념입니다. 애플리케이션에 그룹을 할당할 수 있지만 그룹에 포함된 사용자 개체만 프로비전할 수 있습니다. 할당에서 그룹을 사용하기 위해 전체 그룹 개체를 프로비전할 필요는 없습니다.

## <a name="editing-the-list-of-supported-attributes"></a>지원되는 특성 목록 편집

지정된 애플리케이션에 지원되는 사용자 특성은 미리 구성됩니다. 대부분의 응용 프로그램의 사용자 관리 API는 스키마 검색을 지원하지 않습니다. 따라서 Azure AD 프로비전 서비스는 응용 프로그램을 호출하여 지원되는 특성 목록을 동적으로 생성할 수 없습니다.

그러나 일부 응용 프로그램은 사용자 지정 특성을 지원하며 Azure AD 프로비저닝 서비스는 사용자 지정 특성을 읽고 쓸 수 있습니다. Azure 포털에 정의를 입력하려면 **특성 매핑** 화면 하단의 고급 **옵션 표시** 확인란을 선택한 다음 앱의 특성 **목록 편집을** 선택합니다.

특성 목록의 사용자 지정을 지원하는 애플리케이션 및 시스템은 다음과 같습니다.

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory(Microsoft[그래프 REST API v1.0 참조](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) 및 사용자 지정 디렉터리 확장이 지원됩니다).
- [SCIM 2.0](https://tools.ietf.org/html/rfc7643)을 지원하는 앱([코어 스키마](https://tools.ietf.org/html/rfc7643)에 정의된 특성을 추가해야 함)

> [!NOTE]
> 애플리케이션과 시스템의 스키마를 사용자 지정했으며, 사용자 지정 특성이 정의된 방식에 대한 실무 지식이 있는 관리자일 경우에만 지원되는 특성 목록을 편집하는 것이 좋습니다. 애플리케이션이나 시스템에서 제공하는 API 및 개발자 도구를 잘 알아야 하는 경우가 많습니다.

지원되는 특성 목록을 편집할 때는 다음과 같은 속성이 제공됩니다.

- **이름** - 대상 개체의 스키마에 정의된 특성의 시스템 이름입니다.
- **유형** 형식 - 다음 유형 중 하나가 될 수 있는 대상 개체의 스키마에 정의된 대로 특성이 저장하는 데이터 형식입니다.
  - *이진* - 특성에 이진 데이터가 포함됩니다.
  - *부울* - 특성에 True 또는 False 값이 포함됩니다.
  - *날짜/시간* - 특성에 날짜 문자열이 포함됩니다.
  - *정수* - 특성에 정수가 포함됩니다.
  - *참조* - 특성에 대상 애플리케이션의 다른 테이블에 저장된 값을 참조하는 ID가 포함됩니다.
  - *문자열* - 특성에 텍스트 문자열이 포함됩니다.
- **기본 키?** - 특성이 대상 개체의 스키마에서 기본 키 필드로 정의되는지 여부입니다.
- **필수?** - 특성이 대상 응용 프로그램 또는 시스템에 채워져야 하는지 여부입니다.
- **다중 값?** - 특성이 여러 값을 지원하는지 여부입니다.
- **대소문자 구분 여부** - 특성 값이 대/소문자에 민감한 방식으로 평가되는지 여부입니다.
- **API 표현식** - 특정 프로비저닝 커넥터(예: Workday)에 대한 설명서에서 지시하지 않는 한 사용하지 마십시오.
- **참조된 개체 특성** - 참조 형식 특성인 경우 이 메뉴를 사용하면 특성과 연결된 값을 포함하는 대상 응용 프로그램에서 테이블과 특성을 선택할 수 있습니다. 예를 들어 "Department"라는 특성의 저장된 값이 별도의 "Departments" 테이블에 있는 개체를 참조하는 경우 "Departments.Name"을 선택합니다. 지정된 응용 프로그램에 대해 지원되는 참조 테이블과 기본 ID 필드는 미리 구성되며 현재 Azure 포털을 사용하여 편집할 수 없지만 [Microsoft 그래프 API를](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)사용하여 편집할 수 있습니다.

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>SCIM 호환 응용 프로그램에 사용자 지정 확장 특성 프로비전
SCIM RFC는 핵심 사용자 및 그룹 스키마를 정의하는 동시에 응용 프로그램의 요구 사항을 충족하기 위해 스키마에 대한 확장을 허용합니다. SCIM 응용 프로그램에 사용자 지정 특성을 추가하려면 다음을 수행합니다.
   1. Azure Active [Directory 포털에](https://aad.portal.azure.com)로그인하고 **엔터프라이즈 응용 프로그램을**선택하고 응용 프로그램을 선택한 다음 **프로비저닝을 선택합니다.**
   2. **매핑에서**사용자 지정 특성을 추가할 개체(사용자 또는 그룹)를 선택합니다.
   3. 페이지 하단에서 **고급 옵션 표시를**선택합니다.
   4. **AppName에 대한 특성 편집을**선택합니다.
   5. 특성 목록 의 맨 아래에 제공된 필드에 사용자 지정 특성에 대한 정보를 입력합니다. 그런 다음 **특성 추가를**선택합니다.

SCIM 응용 프로그램의 경우 특성 이름은 아래 예제에 표시된 패턴을 따라야 합니다. "CustomExtensionName" 및 "CustomAttribute"는 응용 프로그램의 요구 사항에 따라 사용자 지정할 수 있습니다(예:  
 * urn:ietf:params:scim:schemas:확장:사용자 지정 확장 이름:2.0:사용자:사용자 속성 
 * 항아리:ietf:params:scim:스키마:확장:2.0:사용자 지정 확장 이름:사용자 지정 속성  
 * urn:ietf:params:scim:schemas:확장:사용자 지정 확장 이름:2.0:사용자.사용자 속성 이름:값

이 지침은 SCIM 지원 응용 프로그램에만 적용됩니다. ServiceNow 및 Salesforce와 같은 응용 프로그램은 SCIM을 사용하여 Azure AD와 통합되지 않으므로 사용자 지정 특성을 추가할 때 이 특정 네임스페이스가 필요하지 않습니다.

사용자 지정 특성은 참조 특성 또는 다중 값 특성이 될 수 없습니다. 사용자 지정 다중 값 확장 특성은 현재 갤러리의 응용 프로그램에 대해서만 지원됩니다.  
 
**확장 특성이 있는 사용자의 예:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>SCIM 앱에 역할 프로비전
아래 단계를 사용하여 사용자의 역할을 응용 프로그램에 프로비전합니다. 아래 설명은 사용자 지정 SCIM 응용 프로그램에만 해당됩니다. Salesforce 및 ServiceNow와 같은 갤러리 응용 프로그램의 경우 미리 정의된 역할 매핑을 사용합니다. 아래 글머리 기호는 AppRoleAssignments 특성을 응용 프로그램에서 기대하는 형식으로 변환하는 방법을 설명합니다.

- Azure AD의 appRoleAssignment를 응용 프로그램의 역할에 매핑하려면 [식을](../app-provisioning/functions-for-customizing-application-data.md)사용하여 특성을 변환해야 합니다. appRoleAssignment 특성은 역할 세부 정보를 구문 분석하는 식을 사용하지 않고 역할 특성에 **직접 매핑되어서는 안** 됩니다. 

- **SingleAppRoleAssignment** 
  - **사용 시기:** SingleAppRoleAssignment 식을 사용하여 사용자에 대한 단일 역할을 프로비전하고 기본 역할을 지정합니다. 
  - **구성 방법:** 위에서 설명한 단계를 사용하여 특성 매핑 페이지로 이동한 다음 SingleAppRoleAssignment 식을 사용하여 역할 특성에 매핑합니다. 선택할 수 있는 세 가지 역할 특성(역할[기본 eq "True"].display, 역할[기본 eq "True].type"및 역할[기본 eq "True"].value)에서 선택할 수 있습니다. 매핑에 역할 특성 중 또는 전부를 포함하도록 선택할 수 있습니다. 두 개 이상을 포함하려면 새 매핑을 추가하고 대상 특성으로 포함하면 됩니다.  
  
  ![단일 앱 롤 할당 추가](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **고려해야 할 사항**
    - 여러 역할이 사용자에게 할당되지 않았는지 확인합니다. 프로비저닝할 역할을 보장할 수 없습니다.
    
  - **예제 출력** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **앱롤할당콤플렉스** 
  - **사용 시기:** AppRoleAssignmentsComplex 식을 사용하여 사용자에 대해 여러 역할을 프로비전합니다. 
  - **구성 방법:** 역할에 대한 새 특성을 포함하도록 위에서 설명한 대로 지원되는 특성 목록을 편집합니다. 
  
    ![역할 추가](./media/customize-application-attributes/add-roles.png)<br>

    그런 다음 AppRoleAssignmentsComplex 식을 사용하여 아래 이미지와 같이 사용자 지정 역할 특성에 매핑합니다.

    ![앱역할할당스 추가복합](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **고려해야 할 사항**
    - 모든 역할은 기본 = false로 프로비전됩니다.
    - POST에는 역할 유형이 포함되어 있습니다. PATCH 요청에는 형식이 포함되어 있지 않습니다. POST 및 PATCH 요청 모두에서 형식을 전송하기 위해 노력하고 있습니다.
    
  - **예제 출력** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>다중 값 특성 프로비전
phoneNumbers 및 이메일과 같은 특정 속성은 다양한 유형의 전화 번호 또는 전자 메일을 지정해야 하는 다중 값 특성입니다. 다중 값 특성에 아래 식을 사용합니다. 이를 통해 속성 유형을 지정하고 해당 Azure AD 사용자 특성에 해당 값을 매핑할 수 있습니다. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>기본 특성 및 특성 매핑 복원

기존 매핑을 다시 시작하고 기본 상태로 다시 설정해야 하는 경우 **기본 매핑 복원** 확인란을 선택하고 구성을 저장할 수 있습니다. 이렇게 하면 응용 프로그램이 응용 프로그램 갤러리에서 Azure AD 테넌트에 방금 추가된 것처럼 모든 매핑 및 범위 지정 필터가 설정됩니다.

이 옵션을 선택하면 프로비저닝 서비스가 실행되는 동안 모든 사용자가 효과적으로 재동기화됩니다.

> [!IMPORTANT]
> 이 옵션을 호출하기 전에 **프로비저닝 상태를** **Off로** 설정하는 것이 좋습니다.

## <a name="what-you-should-know"></a>알아야 할 사항

- Microsoft Azure AD는 동기화 프로세스의 효과적인 구현을 제공합니다. 초기화된 환경에서 동기화 주기 중에는 업데이트가 필요한 개체만 처리됩니다.
- 특성 매핑 업데이트는 동기화 주기의 성능에 영향을 줍니다. 특성 매핑 구성의 업데이트는 모든 관리된 개체를 다시 평가해야 합니다.
- 특성 매핑에 대한 연속적인 변경 횟수를 최소한으로 유지하는 것이 좋습니다.
- 사진을 동기화할 형식을 지정할 수 없으므로 앱에 프로비전할 사진 속성을 추가하는 것은 현재 지원되지 않습니다. [사용자 음성에서](https://feedback.azure.com/forums/169401-azure-active-directory) 이 기능을 요청할 수 있습니다.
- IsSoftDeleted 특성은 종종 응용 프로그램에 대 한 기본 매핑의 일부입니다. IsSoftdeleted는 네 가지 시나리오 중 하나에서 true일 수 있습니다 (사용자가 응용 프로그램에서 할당되지 않았기 때문에 범위를 벗어났거나, 사용자가 범위 지정 필터를 충족하지 않아 범위가 벗어났거나, 사용자가 Azure AD에서 일시 삭제되었거나, 속성 AccountEnabled가 사용자에 대해 false로 설정됨). 
- Azure AD 프로비저닝 서비스는 null 값 프로비전을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](user-provisioning.md)
- [특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)
- [사용자 프로 비전에 대 한 필터 범위 지정](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](use-scim-to-provision-users-and-groups.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
