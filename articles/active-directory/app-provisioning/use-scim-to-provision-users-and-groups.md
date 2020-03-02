---
title: Azure AD에서 앱에 대 한 사용자 프로 비전을 위한 SCIM 끝점 개발
description: SCIM (도메인 간 Id 관리)에 대 한 시스템에서 자동 사용자 프로 비전을 표준화 합니다. SCIM 끝점을 개발 하 고, Azure Active Directory SCIM API를 통합 하 고, 클라우드 응용 프로그램에 사용자 및 그룹을 프로 비전 하는 자동화를 시작 하는 방법을 알아봅니다.
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
ms.date: 02/18/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e4768c5cf6df784c8f32aff2f884adfa6b68ab
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78204857"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>SCIM 끝점을 빌드하고 Azure Active Directory (Azure AD)를 사용 하 여 사용자 프로 비전 구성

응용 프로그램 개발자는 SCIM (도메인 간 Id 관리) 사용자 관리 API에 대 한 시스템을 사용 하 여 응용 프로그램과 Azure AD 간에 사용자 및 그룹의 자동 프로 비전을 사용 하도록 설정할 수 있습니다. 이 문서에서는 SCIM 끝점을 빌드하고 Azure AD 프로 비전 서비스와 통합 하는 방법을 설명 합니다. SCIM 사양은 프로 비전을 위한 공용 사용자 스키마를 제공 합니다. SAML 또는 Openid connect Connect와 같은 페더레이션 표준과 함께 사용 하는 경우 SCIM은 관리자에 게 액세스 관리를 위한 종단 간 표준 기반 솔루션을 제공 합니다.

SCIM은 두 끝점의 표준화 된 정의입니다./사용자 끝점 및/Tgroups 끝점입니다. 일반적인 REST 동사를 사용 하 여 개체를 만들고, 업데이트 하 고, 삭제 하며 그룹 이름, 사용자 이름, 이름, 성, 전자 메일 등의 공통 특성에 대해 미리 정의 된 스키마를 사용 합니다. SCIM 2.0 REST API을 제공 하는 앱은 독점적인 사용자 관리 API를 사용 하 여 작업 하는 어려움을 줄이거나 없앨 수 있습니다. 예를 들어 모든 규격 SCIM 클라이언트는 JSON 개체의 HTTP POST를/사용자 끝점으로 만들어 새 사용자 항목을 만드는 방법을 알고 있습니다. 동일한 기본 작업에 대해 약간 다른 API를 필요로 하는 대신 SCIM 표준을 준수 하는 앱은 기존 클라이언트, 도구 및 코드를 즉시 활용할 수 있습니다. 

![SCIM을 사용 하 여 Azure AD에서 앱으로 프로 비전](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644))에 정의 된 관리용 표준 사용자 개체 스키마 및 rest api를 사용 하 여 id 공급자와 앱을 서로 쉽게 통합할 수 있습니다. SCIM 끝점을 빌드하는 응용 프로그램 개발자는 사용자 지정 작업을 수행 하지 않고도 SCIM 호환 클라이언트와 통합할 수 있습니다.

응용 프로그램에 프로 비전을 자동화 하려면 SCIM 끝점을 빌드하고 Azure AD SCIM과 통합 해야 합니다. 응용 프로그램에 사용자 및 그룹 프로 비전을 시작 하려면 다음 단계를 수행 합니다. 
    
  * **[1 단계: 사용자 및 그룹 스키마를 디자인 합니다.](#step-1-design-your-user-and-group-schema)** 응용 프로그램에 필요한 개체 및 특성을 확인 하 고 Azure AD SCIM 구현에서 지 원하는 사용자 및 그룹 스키마에 매핑되는 방법을 결정 합니다.

  * **[2 단계: Azure AD SCIM 구현 이해](#step-2-understand-the-azure-ad-scim-implementation)** Azure AD SCIM 클라이언트를 구현 하는 방법을 이해 하 고 SCIM 프로토콜 요청 처리 및 응답을 모델링 합니다.

  * **[3 단계: SCIM 끝점을 빌드합니다.](#step-3-build-a-scim-endpoint)** Azure AD 프로 비전 서비스와 통합 하려면 끝점이 SCIM 2.0 호환 이어야 합니다. 옵션으로 Microsoft CLI (공용 언어 인프라) 라이브러리 및 코드 샘플을 사용 하 여 끝점을 빌드할 수 있습니다. 이러한 샘플은 참조 및 테스트용 으로만 사용할 수 있습니다. 프로덕션 앱을 코딩 하 여 종속성을 사용 하지 않는 것이 좋습니다.

  * **[4 단계: SCIM 끝점을 Azure AD SCIM 클라이언트와 통합 합니다.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 조직에서 Azure AD가 지 원하는 SCIM 2.0의 프로필을 구현 하는 타사 응용 프로그램을 사용 하는 경우 사용자 및 그룹의 프로 비전 및 프로 비전 해제를 모두 바로 자동화할 수 있습니다.

  * **[5 단계: Azure AD 응용 프로그램 갤러리에 응용 프로그램을 게시 합니다.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 고객이 응용 프로그램을 쉽게 검색 하 고 프로 비전을 쉽게 구성할 수 있도록 합니다. 

![SCIM 끝점을 Azure AD와 통합 하는 단계](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>1 단계: 사용자 및 그룹 스키마 디자인

모든 응용 프로그램에는 사용자 또는 그룹을 만들기 위한 다른 특성이 필요 합니다. 응용 프로그램에 필요한 개체 (사용자, 그룹) 및 특성 (이름, 관리자, 직함 등)을 식별 하 여 통합을 시작 합니다. SCIM 표준은 사용자 및 그룹을 관리 하기 위한 스키마를 정의 합니다. 핵심 사용자 스키마에는 **id** (서비스 공급자 정의 식별자), **externalid** (클라이언트 정의 식별자) 및 **메타** (서비스 공급자가 유지 관리 하는 읽기 전용 메타 데이터)의 세 가지 특성만 필요 합니다. 다른 모든 특성은 선택 사항입니다. SCIM 표준은 핵심 사용자 스키마 외에도 응용 프로그램의 요구에 맞게 사용자 스키마를 확장 하는 모델 및 엔터프라이즈 사용자 확장을 정의 합니다. 예를 들어 응용 프로그램에 사용자의 관리자가 필요한 경우 엔터프라이즈 사용자 스키마를 사용 하 여 사용자의 관리자와 핵심 스키마를 수집 하 여 사용자의 메일을 수집할 수 있습니다. 스키마를 디자인 하려면 다음 단계를 수행 합니다.
  1. 응용 프로그램에 필요한 특성을 나열 합니다. 요구 사항을 인증에 필요한 특성 (예: loginName 및 전자 메일), 사용자의 수명 주기를 관리 하는 데 필요한 특성 (예: 상태/활성) 및 특정 응용 프로그램이 작동 하는 데 필요한 기타 특성 (예: 관리자, 태그)으로 구분 하는 것이 유용할 수 있습니다.
  2. 이러한 특성이 핵심 사용자 스키마 또는 엔터프라이즈 사용자 스키마에 이미 정의 되어 있는지 확인 합니다. 필요한 특성이 핵심 또는 엔터프라이즈 사용자 스키마에 포함 되어 있지 않은 경우 필요한 특성을 포함 하는 사용자 스키마에 대 한 확장을 정의 해야 합니다. 아래 예제에서는 사용자에 게 "태그"를 프로 비전 할 수 있도록 사용자에 대 한 확장을 추가 했습니다. 핵심 및 엔터프라이즈 사용자 스키마로 시작 하 고 나중에 추가 사용자 지정 스키마를 확장 하는 것이 가장 좋습니다.  
  3. SCIM 특성을 Azure AD의 사용자 특성에 매핑합니다. SCIM 끝점에 정의 된 특성 중 하나에 Azure AD 사용자 스키마에 대 한 명확한 대응 항목이 없으면 대부분의 테 넌 트에 데이터를 사용자 개체에 저장 하지 않는 것이 좋습니다. 사용자를 만들기 위해이 특성을 선택적으로 사용할 수 있는지 여부를 고려 합니다. 응용 프로그램이 작동 하는 데 중요 한 특성이 면 테 넌 트 관리자에 게 해당 스키마를 확장 하거나 아래와 같이 "tags" 속성에 대 한 확장 특성을 사용 하도록 안내 합니다.

### <a name="table-1-outline-the-attributes-that-you-need"></a>표 1: 필요한 특성 윤곽선 
| 1 단계: 앱에 필요한 특성 결정| 2 단계: SCIM 표준에 앱 요구 사항 매핑| 3 단계: Azure AD 특성에 SCIM 특성 매핑|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|이름. lastName|lastName|
|회사 메일|전자 메일 [type eq "work"]. value|Mail|
|manager|manager|manager|
|tag|urn: ietf: params: scim: 스키마: 확장: 2.0: CustomExtension: tag|extensionAttribute1|
|상태|활성|Is소프트 삭제 (사용자에 게 저장 되지 않은 계산 값)|

위에서 정의한 스키마는 아래 Json 페이로드를 사용 하 여 표시 됩니다. 응용 프로그램에 필요한 특성 외에도 JSON 표현에는 필수 "id", "externalId" 및 "meta" 특성이 포함 되어 있습니다.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>표 2: 기본 사용자 특성 매핑
그런 다음 아래 표를 사용 하 여 응용 프로그램에 필요한 특성을 Azure AD의 특성과 SCIM RFC에 매핑할 수 있는 방법을 이해할 수 있습니다. Azure AD와 SCIM 끝점 간에 특성이 매핑되는 방식을 [사용자 지정할](customize-application-attributes.md) 수 있습니다. 사용자와 그룹 또는 아래에 표시 된 모든 특성을 모두 지원할 필요는 없습니다. Azure AD의 특성이 SCIM 프로토콜의 속성에 매핑되는 방식에 대 한 참조입니다. 

| Azure Active Directory 사용자 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |활성 |
|department|urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: User: 부서|
| displayName |displayName |
|employeeId|urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: User: 예제|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: User: manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>표 3: 기본 그룹 특성 매핑

| Azure Active Directory 그룹 | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC에는 여러 끝점이 정의 되어 있습니다. /User 끝점을 시작 하 고 거기에서 확장할 수 있습니다. /Schemas 끝점은 사용자 지정 특성을 사용 하거나 스키마가 자주 변경 되는 경우에 유용 합니다. 이를 통해 클라이언트는 최신 스키마를 자동으로 검색할 수 있습니다. /대량 끝점은 그룹을 지원할 때 특히 유용 합니다. 다음 표에서는 SCIM 표준에 정의 된 다양 한 끝점에 대해 설명 합니다. /Schemas 끝점은 사용자 지정 특성을 사용 하거나 스키마가 자주 변경 되는 경우에 유용 합니다. 이를 통해 클라이언트는 최신 스키마를 자동으로 검색할 수 있습니다. /대량 끝점은 그룹을 지원할 때 특히 유용 합니다. 다음 표에서는 SCIM 표준에 정의 된 다양 한 끝점에 대해 설명 합니다. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>표 4: 개발 하려는 끝점 결정
|엔드포인트|설명|
|--|--|
|/User|사용자 개체에 대 한 CRUD 작업을 수행 합니다.|
|/Group|그룹 개체에 대 한 CRUD 작업을 수행 합니다.|
|/ServiceProviderConfig|지원 되는 리소스 및 인증 방법과 같이 지원 되는 SCIM 표준의 기능에 대 한 세부 정보를 제공 합니다.|
|/ResourceTypes|각 리소스에 대 한 메타 데이터를 지정 합니다.|
|/스키마|각 클라이언트 및 서비스 공급자가 지 원하는 특성 집합은 다를 수 있습니다. 한 서비스 공급자는 "name", "title" 및 "전자 메일"을 포함할 수 있지만 다른 서비스 공급자는 "name", "title" 및 "phoneNumbers"를 사용 합니다. 스키마 끝점은 지원 되는 특성의 검색을 허용 합니다.|
|/대량|대량 작업을 사용 하면 대규모 그룹의 멤버 자격 업데이트와 같이 단일 작업으로 대규모 리소스 개체 컬렉션에 대 한 작업을 수행할 수 있습니다.|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>2 단계: Azure AD SCIM 구현 이해
> [!IMPORTANT]
> Azure AD SCIM 구현 동작이 2018년 12월 18일에 마지막으로 업데이트되었습니다. 변경된 내용에 대한 자세한 내용은 [Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 규정 준수](application-provisioning-config-problem-scim-compatibility.md)를 참조하세요.

SCIM 2.0 사용자 관리 API를 지 원하는 응용 프로그램을 빌드하는 경우이 섹션에서는 Azure AD SCIM 클라이언트를 구현 하는 방법에 대해 자세히 설명 합니다. 또한 SCIM 프로토콜 요청 처리 및 응답을 모델링 하는 방법을 보여 줍니다. SCIM 끝점을 구현한 후에는 이전 섹션에서 설명한 절차에 따라 테스트할 수 있습니다.

[Scim 2.0 프로토콜 사양](http://www.simplecloud.info/#Specification)내에서 응용 프로그램은 다음 요구 사항을 충족 해야 합니다.

* [SCIM 프로토콜의 섹션 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)에 따라 사용자 만들기 및 선택적으로 그룹도 지원 합니다.  
* 는 [SCIM 프로토콜의 3.5.2 섹션](https://tools.ietf.org/html/rfc7644#section-3.5.2)에 따라 패치 요청으로 사용자 또는 그룹을 수정할 수 있도록 지원 합니다.  
* 는 [SCIM 프로토콜의 3.4.1 섹션](https://tools.ietf.org/html/rfc7644#section-3.4.1)에 따라 앞에서 만든 사용자나 그룹에 대 한 알려진 리소스 검색을 지원 합니다.  
* [SCIM 프로토콜의 섹션 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2)사용자 또는 그룹 쿼리를 지원 합니다.  기본적으로 사용자는 `id`에 의해 검색 되 고 `username` 및 `externalid`에서 쿼리 되며 그룹은 `displayName`에서 쿼리 됩니다.  
* SCIM 프로토콜의 섹션 3.4.2에서 ID 및 관리자 별로 사용자 쿼리를 지원 합니다.  
* SCIM 프로토콜의 섹션 3.4.2 ID 및 멤버 별로 그룹 쿼리를 지원 합니다.  
* 응용 프로그램에 대 한 Azure AD의 인증 및 권한 부여에 대 한 단일 전달자 토큰을 허용 합니다.

Azure AD와의 호환성을 보장 하기 위해 SCIM 끝점을 구현할 때 다음 일반 지침을 따릅니다.

* `id`는 모든 리소스에 대 한 필수 속성입니다. 리소스를 반환 하는 모든 응답은 멤버가 0 인 `ListResponse`를 제외 하 고 각 리소스에이 속성이 있는지 확인 해야 합니다.
* 쿼리/필터 요청에 대 한 응답은 항상 `ListResponse`이어야 합니다.
* 그룹은 선택 사항 이지만 SCIM 구현이 패치 요청을 지 원하는 경우에만 지원 됩니다.
* 패치 응답에 전체 리소스를 포함 하는 것은 필요 하지 않습니다.
* Microsoft Azure AD는 다음 연산자만 사용 합니다.  
    - `eq`
    - `and`
* SCIM의 구조적 요소에 대 한 대/소문자를 구분 하지 않아도 됩니다. 특히 패치 `op` 작업 값은 https://tools.ietf.org/html/rfc7644#section-3.5.2에 정의 되어 있습니다. Azure AD는 `Add`, `Replace`및 `Remove`으로 ' op '의 값을 내보냅니다.
* Microsoft Azure AD은 끝점 및 자격 증명이 유효한 지 확인 하기 위해 임의 사용자 및 그룹을 인출 하도록 요청 합니다. [Azure Portal](https://portal.azure.com)에서 **연결 테스트** 흐름의 일부로도 수행 됩니다. 
* 리소스를 쿼리할 수 있는 특성은 [Azure Portal](https://portal.azure.com)응용 프로그램에서 일치 하는 특성으로 설정 해야 합니다. 자세한 내용은 [사용자 프로 비전 특성 매핑 사용자 지정](customize-application-attributes.md) 을 참조 하세요.

### <a name="user-provisioning-and-deprovisioning"></a>사용자 프로 비전 및 프로 비전 해제

다음 그림에서는 응용 프로그램의 id 저장소에서 사용자의 수명 주기를 관리 하기 위해 SCIM 서비스로 보내는 Azure Active Directory 메시지를 보여 줍니다.  

![은 사용자 프로 비전 및 프로 비전 해제 시퀀스를 보여 줍니다](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*사용자 프로 비전 및 프로 비전 해제 시퀀스*

### <a name="group-provisioning-and-deprovisioning"></a>그룹 프로 비전 및 프로 비전 해제

그룹 프로 비전 및 프로 비전 해제는 선택 사항입니다. 구현 및 사용 하는 경우 다음 그림에서는 Azure AD가 응용 프로그램의 id 저장소에서 그룹의 수명 주기를 관리 하기 위해 SCIM 서비스에 보내는 메시지를 보여 줍니다.  이러한 메시지는 다음과 같은 두 가지 방법으로 사용자에 대 한 메시지와 다릅니다.

* 그룹 검색 요청은 멤버 특성이 요청에 대 한 응답으로 제공 된 리소스에서 제외 되도록 지정 합니다.  
* 참조 특성에 특정 값이 있는지를 확인하는 요청은 멤버 특성에 대한 요청입니다.  

![그룹 프로 비전 및 프로 비전 해제 시퀀스를 보여 줍니다](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*그룹 프로 비전 및 프로 비전 해제 시퀀스*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 프로토콜 요청 및 응답
이 섹션에서는 Azure AD SCIM 클라이언트에서 내보낸 예제 SCIM 요청 및 예상 응답 예제를 제공 합니다. 최상의 결과를 위해서는 이러한 요청을이 형식으로 처리 하 고 예상 된 응답을 내보내는 앱을 코딩 해야 합니다.

> [!IMPORTANT]
> Azure AD 사용자 프로 비전 서비스에서 아래에 설명 된 작업을 내보내는 방법과 시기를 이해 하려면 프로 비전 [주기: 초기 및 증분](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) 프로 [비전 작동 방법](how-provisioning-works.md)섹션을 참조 하세요.

[사용자 작업](#user-operations)
  - [사용자 만들기](#create-user) ([요청](#request) / [응답](#response))
  - [사용자 가져오기](#get-user) ([요청](#request-1) / [응답](#response-1))
  - [쿼리로 사용자 가져오기](#get-user-by-query) ([요청](#request-2) / [응답](#response-2))
  - [쿼리로 사용자 가져오기-0 개 결과](#get-user-by-query---zero-results) ([요청](#request-3)
/ [응답](#response-3))
  - [사용자 업데이트 [다중 값 속성]](#update-user-multi-valued-properties) ([요청](#request-4) /  [응답](#response-4))
  - [사용자 업데이트 [단일 값 속성]](#update-user-single-valued-properties) ([요청](#request-5)
/ [응답](#response-5)) 
  - [사용자 사용 안 함](#disable-user) ([요청](#request-14) / 
[응답](#response-14))
  - [사용자 삭제](#delete-user) ([요청](#request-6) / 
[응답](#response-6))


[그룹 작업](#group-operations)
  - [그룹 만들기](#create-group) ( [요청](#request-7) / [응답](#response-7))
  - [그룹 가져오기](#get-group) ( [요청](#request-8) / [응답](#response-8))
  - [Group By DisplayName 가져오기](#get-group-by-displayname) ([요청](#request-9) / [응답](#response-9))
  - [업데이트 그룹 [멤버가 아닌 특성]](#update-group-non-member-attributes) ([요청](#request-10) /
 [응답](#response-10))
  - [업데이트 그룹 [멤버 추가]](#update-group-add-members) ( [요청](#request-11) /
[응답](#response-11))
  - [업데이트 그룹 [구성원 제거]](#update-group-remove-members) ( [요청](#request-12) /
[응답](#response-12))
  - [그룹 삭제](#delete-group) ([요청](#request-13) /
[응답](#response-13))

### <a name="user-operations"></a>사용자 작업

* `userName` 또는 `email[type eq "work"]` 특성을 통해 사용자를 쿼리할 수 있습니다.  

#### <a name="create-user"></a>사용자 만들기

###### <a name="request"></a>요청

*/사용자 게시*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>응답

*HTTP/1.1 201 생성*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>사용자 가져오기

###### <a name="request-1"></a>요구
*/Users/5d48a0a8e9f04aa38008 가져오기* 

###### <a name="response-1"></a>응답 (사용자 찾음)
*HTTP/1.1 200 정상*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>요청
*/Users/5171a35d82074e068ce2 가져오기* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>응답 (사용자를 찾을 수 없습니다. 세부 정보는 필요 하지 않으며 상태만 볼 수 있습니다.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>쿼리로 사용자 가져오기

##### <a name="request-2"></a>요구

*GET/사용자? filter = userName eq "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response-2"></a>응답이

*HTTP/1.1 200 정상*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>쿼리로 사용자 가져오기-0 개 결과

##### <a name="request-3"></a>요구

*GET/사용자? filter = userName eq "존재 하지 않는 사용자"*

##### <a name="response-3"></a>응답이

*HTTP/1.1 200 정상*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>사용자 업데이트 [다중값 속성]

##### <a name="request-4"></a>요구

*PATCH/users/6764549Bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>응답이

*HTTP/1.1 200 정상*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>사용자 업데이트 [단일 값 속성]

##### <a name="request-5"></a>요구

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>응답이

*HTTP/1.1 200 정상*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>사용자 사용 안 함

##### <a name="request-14"></a>요구

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response-14"></a>응답이

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>사용자 삭제

##### <a name="request-6"></a>요구

*/Users/5171a35d82074e068ce2 HTTP/1.1 삭제*

##### <a name="response-6"></a>응답이

*HTTP/1.1 204 내용 없음*

### <a name="group-operations"></a>그룹 작업

* 그룹은 항상 빈 멤버 목록으로 만들어야 합니다.
* `displayName` 특성을 통해 그룹을 쿼리할 수 있습니다.
* 그룹 패치 요청을 업데이트 하면 응답에 *HTTP 204 콘텐츠가 생성 되지 않습니다* . 모든 멤버 목록이 포함 된 본문을 반환 하는 것은 바람직하지 않습니다.
* 그룹의 모든 구성원 반환을 지원할 필요는 없습니다.

#### <a name="create-group"></a>그룹 만들기

##### <a name="request-7"></a>요구

*POST/Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>응답이

*HTTP/1.1 201 생성*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>그룹 가져오기

##### <a name="request-8"></a>요구

*GET/Groups/40734ae655284ad3abcc? excludedAttributes = members HTTP/1.1*

##### <a name="response-8"></a>응답이
*HTTP/1.1 200 정상*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>DisplayName by displayName 가져오기

##### <a name="request-9"></a>요구
*/Groups? excludedAttributes = members & filter = displayName eq "displayName" HTTP/1.1*

##### <a name="response-9"></a>응답이

*HTTP/1.1 200 정상*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>업데이트 그룹 [멤버가 아닌 특성]

##### <a name="request-10"></a>요구

*PATCH/G/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>응답이

*HTTP/1.1 204 내용 없음*

### <a name="update-group-add-members"></a>업데이트 그룹 [멤버 추가]

##### <a name="request-11"></a>요구

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>응답이

*HTTP/1.1 204 내용 없음*

#### <a name="update-group-remove-members"></a>업데이트 그룹 [구성원 제거]

##### <a name="request-12"></a>요구

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>응답이

*HTTP/1.1 204 내용 없음*

#### <a name="delete-group"></a>그룹 삭제

##### <a name="request-13"></a>요구

*/Groups/cdb1ce18f65944079d37 HTTP/1.1 삭제*

##### <a name="response-13"></a>응답이

*HTTP/1.1 204 내용 없음*

### <a name="security-requirements"></a>보안 요구 사항
**TLS 프로토콜 버전**

허용 되는 TLS 프로토콜 버전은 TLS 1.2 및 TLS 1.3 뿐입니다. 다른 버전의 TLS는 허용 되지 않습니다. SSL 버전이 허용 되지 않습니다. 
- RSA 키는 2048 비트 이상 이어야 합니다.
- ECC 키는 승인 된 타원 곡선을 사용 하 여 생성 된 256 비트 이상 이어야 합니다.


**키 길이**

모든 서비스는 충분 한 길이의 암호화 키를 사용 하 여 생성 된 x.509 인증서를 사용 해야 합니다. 의미는 다음과 같습니다.

**암호 그룹**

모든 서비스는 아래 지정 된 순서 대로 다음 암호 그룹을 사용 하도록 구성 되어야 합니다. RSA 인증서만 있는 경우에는 설치 된 ECDSA 암호 그룹에 영향을 주지 않습니다. </br>

TLS 1.2 암호 그룹 최소 막대:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>3 단계: SCIM 끝점 빌드

이제 스키마를 desidned 하 고 Azure AD SCIM 구현을 이해 했으므로 SCIM 끝점 개발을 시작할 수 있습니다. 처음부터 시작 하 여 구현을 완전히 빌드하는 대신 SCIM 주석 uinty에서 게시 한 여러 오픈 소스 SCIM 라이브러리를 사용할 수 있습니다.  
Azure AD 프로 비전 팀에서 게시 한 오픈 소스 .NET Core [참조 코드](https://aka.ms/SCIMReferenceCode) 는 개발을 시작할 수 있는 리소스 중 하나입니다. SCIM 끝점을 빌드한 후에는 테스트 하는 것이 좋습니다. 참조 코드의 일부로 제공 되는 [postman 테스트](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) 컬렉션을 사용 하거나 [위에서](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)제공 된 샘플 요청/응답을 통해 실행할 수 있습니다.  

작동 방식은 다음과 같습니다.

1. Azure AD는 아래에 설명 된 코드 샘플에 포함 된 Microsoft.systemforcrossdomainidentitymanagement 라는 CLI (공용 언어 인프라) 라이브러리를 제공 합니다. 시스템 통합 업체 및 개발자는이 라이브러리를 사용 하 여 Azure AD를 응용 프로그램의 id 저장소에 연결할 수 있는 SCIM 기반 웹 서비스 끝점을 만들고 배포할 수 있습니다.
2. 매핑은 웹 서비스에서 구현되어 스키마에 애플리케이션에서 필요한 사용자 스키마 및 프로토콜에 표준화된 사용자 스키마를 매핑합니다. 
3. 엔드포인트 URL은 애플리케이션 갤러리에서 사용자 지정 애플리케이션의 일부로 Azure AD에 등록됩니다.
4. 사용자 및 그룹은 Azure AD에서 이 애플리케이션에 할당됩니다. 할당 되 면 대상 응용 프로그램에 동기화 되는 큐에 배치 됩니다. 큐를 처리하는 동기화 프로세스는 40분마다 실행됩니다.

### <a name="code-samples"></a>코드 샘플

이 프로세스를 더 쉽게 수행 하기 위해 SCIM 웹 서비스 끝점을 만들고 자동 프로 비전을 보여 주는 [코드 샘플이](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) 제공 됩니다. 이 샘플은 사용자 및 그룹을 나타내는 쉼표로 구분 된 값의 행이 있는 파일을 유지 관리 하는 공급자입니다.

**필수 구성 요소**

* Visual Studio 2013 이상
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* ASP.NET framework 4.5를 SCIM 엔드포인트로 사용하도록 지원하는 Windows 컴퓨터입니다. 이 컴퓨터는 클라우드에서 액세스할 수 있어야 합니다.
* [Azure AD Premium의 평가판 또는 사용이 허가된 버전의 Azure 구독](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>시작

Azure AD에서 프로비전 요청을 수락할 수 있는 SCIM 엔드포인트를 구현하는 가장 쉬운 방법은 쉼표로 구분된 값(CSV) 파일에 프로비전된 사용자를 출력하는 코드 샘플을 빌드하고 배포하는 것입니다.

#### <a name="to-create-a-sample-scim-endpoint"></a>샘플 SCIM 엔드포인트를 만들려면

1. [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)에서 코드 샘플 패키지를 다운로드합니다.
1. 패키지의 압축을 풀고 C:\AzureAD-BYOA-Provisioning-Samples와 같은 위치에서 Windows 컴퓨터에 넣습니다.
1. Visual Studio에서 이 폴더의 FileProvisioning\Host\FileProvisioningService.csproj 프로젝트를 시작합니다.
1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택 하 고 FileProvisioningService 프로젝트에 대해 다음 명령을 실행 하 여 솔루션 참조를 확인 합니다.

   ```powershell
    Update-Package -Reinstall
   ```

1. FileProvisioningService 프로젝트를 빌드합니다.
1. Windows에서 명령 프롬프트 애플리케이션을 관리자 권한으로 시작하고, **cd** 명령을 사용하여 디렉터리를 **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** 폴더로 변경합니다.
1. 다음 명령을 실행 하 여 `<ip-address>`를 Windows 컴퓨터의 IP 주소 또는 도메인 이름으로 바꿉니다.

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. Windows의 windows **설정** > **네트워크 & 인터넷 설정**에서 **windows 방화벽** > **고급 설정**을 선택 하 고 포트 9000에 대 한 인바운드 액세스를 허용 하는 **인바운드 규칙** 을 만듭니다.
1. Windows 컴퓨터가 라우터 뒤에 있는 경우 라우터는 인터넷에 노출 되는 포트 9000과 Windows 컴퓨터의 포트 9000 사이에서 네트워크 액세스 변환을 실행 하도록 구성 해야 합니다. 이 구성은 Azure AD에서 클라우드의이 끝점에 액세스 하는 데 필요 합니다.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Azure AD에서 샘플 SCIM 엔드포인트를 등록하려면

1. [Azure Active Directory 포털](https://aad.portal.azure.com)에 로그인 합니다. 
1. 왼쪽 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다. 갤러리에서 추가 된 앱을 포함 하 여 구성 된 모든 앱의 목록이 표시 됩니다.
1. **+ 새 응용 프로그램** > **모든** > **비 갤러리 응용 프로그램**을 선택 합니다.
1. 응용 프로그램의 이름을 입력 하 고 **추가** 를 선택 하 여 앱 개체를 만듭니다. 만든 애플리케이션 개체는 SCIM 엔드포인트뿐 아니라 Single Sign-On을 프로비전하고 구현하려는 대상 앱을 나타내는 데 사용됩니다.
1. 앱 관리 화면의 왼쪽 패널에서 **프로 비전** 을 선택 합니다.
1. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.    
1. **테넌트 URL** 필드에 애플리케이션의 SCIM 엔드포인트 URL을 입력합니다. 예: https://api.contoso.com/scim/

1. SCIM 엔드포인트에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 이 필드를 비워 두면 Azure AD에 각 요청과 함께 Azure AD에서 발급 한 OAuth 전달자 토큰이 포함 됩니다. ID 공급자로 Azure AD를 사용하는 앱은 Azure AD에서 발급한 토큰의 유효성을 검사할 수 있습니다.
1. **연결 테스트** 를 선택 하 Azure Active Directory scim 끝점에 연결을 시도 합니다. 이 시도가 실패 하면 오류 정보가 표시 됩니다.  

    > [!NOTE]
    > **테스트 연결**은 Azure AD 구성에서 선택된 일치하는 속성으로 임의 GUID를 사용하여 존재하지 않는 사용자의 SCIM 엔드포인트를 쿼리합니다. 예상되는 올바른 응답은 SCIM ListResponse 메시지가 비어 있는 HTTP 200 OK입니다.
1. 응용 프로그램에 연결 하려는 시도가 성공 하면 **저장** 을 선택 하 여 관리자 자격 증명을 저장 합니다.
1. **매핑** 섹션에는 선택 가능한 특성 매핑 집합이 두 개 있는데, 하나는 사용자 개체용이고 다른 하나는 그룹 개체용입니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.
1. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. **사용자** 및 그룹 탭에서 할당 된 사용자 및 그룹만 동기화 하도록 **"할당 된 사용자 및 그룹만 동기화** (권장)"를 선택 합니다.
1. 구성이 완료 되 면 **프로 비전 상태** 를 **켜기**로 설정 합니다.
1. **저장** 을 선택 하 여 Azure AD 프로 비전 서비스를 시작 합니다.
1. 할당 된 사용자 및 그룹만 동기화 하는 경우 (권장) **사용자 및 그룹** 탭을 선택 하 고 동기화 할 사용자 또는 그룹을 할당 해야 합니다. 초기 주기가 시작 되 면 왼쪽 패널에서 **감사 로그** 를 선택 하 여 앱의 프로 비전 서비스에서 수행 하는 모든 작업을 표시 하는 진행률을 모니터링할 수 있습니다. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](check-status-user-account-provisioning.md)를 참조하세요.
이 샘플을 확인하는 마지막 단계는 Windows 컴퓨터에서 \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug 폴더에 TargetFile.csv 파일을 여는 것입니다. 프로비전 프로세스가 실행되면 이 파일은 할당되고 프로비전된 모든 사용자 및 그룹의 세부 사항을 표시합니다.

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>4 단계: SCIM 끝점을 Azure AD SCIM 클라이언트와 통합

[Scim 2.0 프로토콜](https://tools.ietf.org/html/rfc7644)의 특정 프로필을 구현 하는 응용 프로그램에 할당 된 사용자 및 그룹을 자동으로 프로 비전 하도록 Azure AD를 구성할 수 있습니다. 프로필에 대 한 자세한 내용은 [2 단계: AZURE AD SCIM 구현 이해](#step-2-understand-the-azure-ad-scim-implementation)에 설명 되어 있습니다.

애플리케이션 공급자 또는 애플리케이션 공급자의 설명서를 통해 이러한 요구 사항과의 호환성을 확인합니다.

> [!IMPORTANT]
> Azure ad SCIM 구현은 azure ad와 대상 응용 프로그램 간에 지속적으로 사용자의 동기화를 유지 하도록 설계 된 Azure AD 사용자 프로 비전 서비스를 기반으로 하며, 매우 구체적인 표준 작업 집합을 구현 합니다. 이러한 동작을 이해 하 여 Azure AD SCIM 클라이언트의 동작을 이해 하는 것이 중요 합니다. 자세한 내용은 프로 비전 [주기: 초기 및 증분](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) 프로 [비전 작동 방법](how-provisioning-works.md)섹션을 참조 하세요.

### <a name="getting-started"></a>시작

Azure AD 애플리케이션 갤러리에 있는 "비-갤러리 애플리케이션" 기능을 사용하여 이 문서에서 설명한 SCIM 프로필을 지원하는 애플리케이션을 Azure Active Directory에 연결할 수 있습니다. 일단 연결되면 Azure AD는 할당된 사용자 및 그룹에 대해 애플리케이션의 SCIM 엔드포인트를 쿼리하고, 할당 세부 정보에 따라 이러한 사용자 및 그룹을 만들거나 수정하는 동기화 프로세스를 40분마다 실행합니다.

**SCIM을 지원하는 애플리케이션을 연결하려면:**

1. [Azure Active Directory 포털](https://aad.portal.azure.com)에 로그인 합니다. [개발자 프로그램](https://developer.microsoft.com/office/dev-program) 에 등록 하면 P2 라이선스를 사용 하 여 Azure Active Directory에 대 한 무료 평가판에 액세스할 수 있습니다.
2. 왼쪽 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다. 갤러리에서 추가 된 앱을 포함 하 여 구성 된 모든 앱의 목록이 표시 됩니다.
3. **+ 새 응용 프로그램** > **모든** > **비 갤러리 응용 프로그램**을 선택 합니다.
4. 응용 프로그램의 이름을 입력 하 고 **추가** 를 선택 하 여 앱 개체를 만듭니다. 새 앱이 엔터프라이즈 응용 프로그램 목록에 추가 되 고 앱 관리 화면으로 열립니다.

   ![스크린샷은 Azure AD 응용 프로그램 갤러리를 보여 줍니다](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD 응용 프로그램 갤러리*

5. 앱 관리 화면의 왼쪽 패널에서 **프로 비전** 을 선택 합니다.
6. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.

   ![예: Azure Portal에 있는 앱의 프로 비전 페이지](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Azure Portal에서 프로 비전 구성*

7. **테넌트 URL** 필드에 애플리케이션의 SCIM 엔드포인트 URL을 입력합니다. 예: https://api.contoso.com/scim/
8. SCIM 엔드포인트에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 이 필드를 비워 두면 Azure AD에 각 요청과 함께 Azure AD에서 발급 한 OAuth 전달자 토큰이 포함 됩니다. ID 공급자로 Azure AD를 사용하는 앱은 Azure AD에서 발급한 토큰의 유효성을 검사할 수 있습니다. 
   > [!NOTE]
   > Azure AD에서 생성 된 토큰을 사용 하 여이 필드를 비워 두지 ***않는*** 것이 좋습니다. 이 옵션은 주로 테스트 목적으로 사용할 수 있습니다.
9. **연결 테스트** 를 선택 하 Azure Active Directory scim 끝점에 연결을 시도 합니다. 이 시도가 실패 하면 오류 정보가 표시 됩니다.  

    > [!NOTE]
    > **테스트 연결**은 Azure AD 구성에서 선택된 일치하는 속성으로 임의 GUID를 사용하여 존재하지 않는 사용자의 SCIM 엔드포인트를 쿼리합니다. 예상되는 올바른 응답은 SCIM ListResponse 메시지가 비어 있는 HTTP 200 OK입니다.

10. 응용 프로그램에 연결 하려는 시도가 성공 하면 **저장** 을 선택 하 여 관리자 자격 증명을 저장 합니다.
11. **매핑** 섹션에는 사용자 개체 및 그룹 개체에 대 한 두 가지 가능한 [특성 매핑](customize-application-attributes.md)집합이 있습니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    > [!NOTE]
    > 필요에 따라 "그룹" 매핑을 사용하지 않도록 설정하여 그룹 개체의 동기화를 비활성화할 수 있습니다.

12. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. **할당 된 사용자 및 그룹만 동기화** (권장)를 선택 하 여 **사용자** 및 그룹 탭에 할당 된 사용자 및 그룹만 동기화 합니다.
13. 구성이 완료 되 면 **프로 비전 상태** 를 **켜기**로 설정 합니다.
14. **저장** 을 선택 하 여 Azure AD 프로 비전 서비스를 시작 합니다.
15. 할당 된 사용자 및 그룹만 동기화 하는 경우 (권장) **사용자 및 그룹** 탭을 선택 하 고 동기화 할 사용자 또는 그룹을 할당 해야 합니다.

초기 주기가 시작 되 면 왼쪽 패널에서 **프로 비전 로그** 를 선택 하 여 앱에서 프로 비전 서비스에서 수행 하는 모든 작업을 표시 하는 진행률을 모니터링할 수 있습니다. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](check-status-user-account-provisioning.md)를 참조하세요.

> [!NOTE]
> 초기 주기는 서비스가 실행 되는 동안 약 40 분 마다 발생 하는 이후 동기화 보다 수행 하는 데 더 많은 시간이 걸립니다.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>5 단계: Azure AD 응용 프로그램 갤러리에 응용 프로그램 게시

둘 이상의 테 넌 트에서 사용 되는 응용 프로그램을 빌드하는 경우 Azure AD 응용 프로그램 갤러리에서 사용할 수 있도록 설정할 수 있습니다. 이렇게 하면 조직이 응용 프로그램을 쉽게 검색 하 고 프로 비전을 구성할 수 있습니다. Azure AD 갤러리에 앱을 게시 하 고 다른 사용자가 프로 비전을 사용할 수 있도록 하는 것이 쉽습니다. [여기](../develop/howto-app-gallery-listing.md)서 단계를 확인하세요. Microsoft는 사용자와 협력 하 여 응용 프로그램을 갤러리에 통합 하 고, 끝점을 테스트 하 고, 고객이 사용할 수 있도록 온 보 딩 [설명서](../saas-apps/tutorial-list.md) 를 출시 합니다. 

### <a name="gallery-onboarding-checklist"></a>갤러리 등록 검사 목록
아래의 검사 목록에 따라 응용 프로그램이 등록 quicky 이며 고객에 게 원활한 배포 환경이 있는지 확인 하세요. 갤러리에 등록 하면 해당 정보가 수집 됩니다. 
> [!div class="checklist"]
> * [Scim 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) 사용자 및 그룹 끝점을 지원 합니다 (하나만 필요 하지만 둘 다 권장 됨).
> * 테 넌 트 당 최소 25 개의 요청을 지원 합니다 (필수).
> * 엔지니어링 및 지원 연락처를 설정 하 여 고객에 게 갤러리 온 보 딩 (필수)을 안내 합니다.
> * 3 응용 프로그램에 대 한 만료 되지 않은 테스트 자격 증명 (필수)
> * 아래 설명 된 대로 OAuth 인증 코드 부여 또는 수명이 긴 토큰을 지원 합니다 (필수).
> * 고객을 지원 하기 위한 엔지니어링 및 지원 연락 지점 설정 (필수)
> * 단일 패치를 사용 하 여 여러 그룹 구성원 업데이트 지원 (권장) 
> * SCIM 끝점을 공개적으로 문서화 (권장) 
> * [지원 스키마 검색](https://tools.ietf.org/html/rfc7643#section-6) (권장)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>응용 프로그램 갤러리에서 커넥터 프로 비전에 대 한 권한 부여
SCIM 사양에는 인증 및 권한 부여에 대 한 SCIM 관련 체계가 정의 되어 있지 않습니다. 기존 업계 표준의 사용을 기반으로 합니다. Azure AD 프로 비전 클라이언트는 갤러리의 응용 프로그램에 대 한 두 가지 권한 부여 방법을 지원 합니다. 

|권한 부여 방법|장점|단점|지원|
|--|--|--|--|
|사용자 이름 및 암호 (Azure AD에서 권장 되지 않거나 지원 되지 않음)|손쉬운 구현|안전 [하지 않음-Pa $ $word 중요 하지 않습니다](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984) .|갤러리 앱에 대 한 대/소문자를 기준으로 지원 됩니다. 비 갤러리 앱에 대해서는 지원 되지 않습니다.|
|수명이 긴 전달자 토큰|수명이 긴 토큰에는 사용자가 없어도 됩니다. 프로 비전을 설정할 때 관리자가 쉽게 사용할 수 있습니다.|수명이 긴 토큰은 전자 메일과 같은 안전 하지 않은 방법을 사용 하지 않고 관리자와 공유 하기 어려울 수 있습니다. |갤러리 및 비 갤러리 앱에 대해 지원 됩니다. |
|OAuth 인증 코드 부여|액세스 토큰은 암호 보다 수명이 짧고 수명이 긴 전달자 토큰에는 자동화 된 새로 고침 메커니즘이 있습니다.  책임 수준을 추가 하는 초기 권한 부여 중에는 실제 사용자가 있어야 합니다. |사용자가 있어야 합니다. 사용자가 조직을 떠나면 토큰이 유효 하지 않으므로 권한 부여를 다시 완료 해야 합니다.|갤러리 앱에 대해 지원 됩니다. 비 갤러리 앱에 대 한 지원이 진행 중입니다.|
|OAuth 클라이언트 자격 증명 부여|액세스 토큰은 암호 보다 수명이 짧고 수명이 긴 전달자 토큰에는 자동화 된 새로 고침 메커니즘이 있습니다. 권한 부여 코드 grant와 클라이언트 자격 증명 부여는 모두 동일한 형식의 액세스 토큰을 만들기 때문에 이러한 메서드 간에 이동 하는 것은 API에 투명 합니다.  프로 비전은 완전히 자동화 될 수 있으며, 사용자 개입 없이 새 토큰을 자동으로 요청할 수 있습니다. ||갤러리 및 비 갤러리 앱에 대해서는 지원 되지 않습니다. 지원은 백로그에 있습니다.|

[!NOTE] Azure AD 프로 비전 구성 사용자 지정 앱 UI에서 토큰 필드를 비워 두지 않는 것이 좋습니다. 생성 된 토큰은 주로 테스트 목적으로 사용할 수 있습니다.

**OAuth 인증 코드 부여 흐름:** 프로 비전 서비스는 [인증 코드 부여](https://tools.ietf.org/html/rfc6749#page-24)를 지원 합니다. 갤러리에서 앱 게시 요청을 제출 하 고 나면 팀에서 다음 정보를 수집 하는 작업을 수행 합니다.
*  권한 부여 URL: 사용자 에이전트 리디렉션을 통해 리소스 소유자 로부터 인증을 얻기 위해 클라이언트에서 사용 하는 URL입니다. 사용자는 액세스 권한을 부여 하기 위해이 URL로 리디렉션됩니다. 
*  토큰 교환 URL: 일반적으로 클라이언트 인증을 사용 하 여 액세스 토큰에 대 한 권한 부여를 교환 하기 위해 클라이언트에서 사용 하는 URL입니다.
*  클라이언트 ID: 권한 부여 서버는 클라이언트에서 제공 하는 등록 정보를 나타내는 고유한 문자열인 클라이언트 식별자 인 등록 된 클라이언트를 발급 합니다.  클라이언트 식별자가 비밀이 아닙니다. 리소스 소유자에 게 노출 되며 클라이언트 인증에 단독으로 사용 하면 **안** 됩니다.  
*  클라이언트 암호: 클라이언트 암호는 권한 부여 서버에서 생성 하는 암호입니다. 권한 부여 서버에만 알려진 고유한 값 이어야 합니다. 

OAuth v1은 클라이언트 암호의 노출 때문에 지원 되지 않습니다. OAuth v2가 지원 됩니다.  

모범 사례 (권장 되지만 필수는 아님):
* 여러 리디렉션 Url을 지원 합니다. 관리자는 "portal.azure.com" 및 "aad.portal.azure.com" 모두에서 프로 비전을 구성할 수 있습니다. 여러 리디렉션 Url을 지원 하기 때문에 사용자가 두 포털에서 액세스 권한을 부여할 수 있습니다.
* 가동 중지 시간 없이 원활한 비밀 갱신을 보장 하기 위해 여러 비밀을 지원 합니다. 

**수명이 긴 OAuth 전달자 토큰:** 응용 프로그램에서 OAuth 인증 코드 부여 흐름을 지원 하지 않는 경우 관리자가 프로 비전 통합을 설정 하는 데 사용할 수 있는 수명이 긴 OAuth 전달자 토큰을 생성할 수도 있습니다. 토큰은 영구적 이거나 토큰이 만료 될 때 프로 비전 작업이 [격리](application-provisioning-quarantine-status.md) 됩니다. 이 토큰의 크기는 1KB 미만 이어야 합니다.  

추가 인증 및 권한 부여 방법에 대 한 자세한 내용은 [UserVoice](https://aka.ms/appprovisioningfeaturerequest)에서 알려주세요.

### <a name="gallery-go-to-market-launch-check-list"></a>갤러리 출시 전 시작 검사 목록
공동 통합의 인식과 수요를 파악 하는 데 도움이 되도록 기존 문서를 업데이트 하 고 마케팅 채널에서 통합을 강화 하는 것이 좋습니다.  다음은 시작을 지 원하는 데 도움이 되는 검사 목록 작업의 집합입니다.

* **판매 및 고객 지원 준비.** 판매 및 지원 팀이 인식 되 고 통합 기능을 사용할 수 있는지 확인 합니다. 영업 및 지원 팀에 대해 간략하게 설명 하 고 Faq를 제공 하며 판매 자료에 통합을 포함 합니다. 
* **블로그 게시물 및/또는 릴리스를 누릅니다.** 공동 통합, 이점 및 시작 하는 방법을 설명 하는 블로그 게시물을 만들거나 릴리스를 누릅니다. [예: Imprivata 및 Azure Active Directory 보도 릴리스](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **소셜 미디어.** Twitter, Facebook 또는 LinkedIn 같은 소셜 미디어를 활용 하 여 고객에 대 한 통합을 홍보 하세요. 게시물을 제거할 수 있도록 @AzureAD를 포함 해야 합니다. [예: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **마케팅 웹 사이트.** 공동 통합의 가용성을 포함 하도록 마케팅 페이지 (예: 통합 페이지, 파트너 페이지, 가격 페이지 등)를 만들거나 업데이트 합니다. [예: Monday.com 보드 통합 페이지](https://pingboard.com/org-chart-for), [smartsheet 통합 페이지](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [가격 책정 페이지](https://monday.com/pricing/) 
* **기술 문서.** 고객이 시작할 수 있는 방법에 대 한 도움말 센터 문서 또는 기술 설명서를 만듭니다. [예: 엔보이 + Microsoft Azure Active Directory 통합.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **고객 통신.** 고객의 의견 (월간 뉴스레터, 전자 메일 캠페인, 제품 릴리스 정보)을 통해 새로운 통합을 고객에 게 알립니다. 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Azure AD 프로 비전 서비스에서 사용 하는 IP 주소를 사용 하 여 SCIM 요청 만들기

특정 앱은 해당 앱에 대 한 인바운드 트래픽을 허용 합니다. Azure AD 프로 비전 서비스가 예상 대로 작동 하려면 사용 된 IP 주소가 허용 되어야 합니다. 각 서비스 태그/지역의 IP 주소 목록은 JSON 파일 - [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요. 필요에 따라 이러한 Ip를 방화벽으로 다운로드 하 여 프로그래밍할 수 있습니다. Azure AD 프로 비전을 위한 예약 된 IP 범위는 "AzureActiveDirectoryDomainServices"에서 찾을 수 있습니다.

## <a name="related-articles"></a>관련 문서

* [SaaS 앱에 대 한 사용자 프로 비전 및 프로 비전 해제 자동화](user-provisioning.md)
* [사용자 프로비전을 위한 사용자 지정 특성 매핑](customize-application-attributes.md)
* [특성 매핑에 대 한 식 작성](functions-for-customizing-application-data.md)
* [사용자 프로 비전을 위한 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)
* [계정 프로비전 알림](user-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
