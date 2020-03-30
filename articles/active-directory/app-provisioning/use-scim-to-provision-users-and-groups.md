---
title: Azure AD에서 앱으로 사용자 프로비전을 위한 SCIM 엔드포인트 개발
description: SCIM(도메인 간 ID 관리)을 위한 시스템은 자동 사용자 프로비저닝을 표준화합니다. SCIM 엔드포인트를 개발하고, SCIM API를 Azure Active Directory와 통합하고, 사용자 및 그룹을 클라우드 애플리케이션에 프로비저닝자동화하는 방법을 알아봅니다.
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
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297672"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>SCIM 끝점을 구축하고 Azure Active Directory(Azure AD)를 사용 하 고 사용자 프로비저닝을 구성 합니다.

응용 프로그램 개발자는 SCIM(도메인 간 ID 관리) 사용자 관리 API를 사용하여 응용 프로그램과 Azure AD 간에 사용자와 그룹을 자동으로 프로비저닝할 수 있습니다. 이 문서에서는 SCIM 끝점을 빌드하고 Azure AD 프로비전 서비스와 통합하는 방법을 설명합니다. SCIM 사양은 프로비저닝을 위한 일반적인 사용자 스키마를 제공합니다. SCIM은 SAML 또는 OpenID Connect와 같은 페더레이션 표준과 함께 사용할 경우 관리자에게 액세스 관리를 위한 엔드 투 엔드 표준 기반 솔루션을 제공합니다.

SCIM은 /Users 끝점과 /그룹 끝점이라는 두 끝점에 대한 표준화된 정의입니다. 일반적인 REST 동사를 사용하여 개체를 생성, 업데이트 및 삭제하고 그룹 이름, 사용자 이름, 이름, 성 및 전자 메일과 같은 일반적인 특성에 대해 미리 정의된 스키마를 사용합니다. SCIM 2.0 REST API를 제공하는 앱은 독점 사용자 관리 API로 작업하는 데 있어 고통을 줄이거나 없앨 수 있습니다. 예를 들어, 모든 호환 SCIM 클라이언트는 JSON 개체의 HTTP POST를 /Users 끝점에 만들어 새 사용자 항목을 만드는 방법을 알고 있습니다. 동일한 기본 작업에 대해 약간 다른 API를 필요로 하는 대신 SCIM 표준을 준수하는 앱은 기존 클라이언트, 도구 및 코드를 즉시 활용할 수 있습니다. 

![Azure AD에서 SCIM을 사용하여 앱으로 프로비전](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0(RFC [7642,](https://tools.ietf.org/html/rfc7642) [7643](https://tools.ietf.org/html/rfc7643), [7644)에](https://tools.ietf.org/html/rfc7644)정의된 관리를 위한 표준 사용자 개체 스키마 및 나머지 API를 사용하면 ID 공급자와 앱이 서로 보다 쉽게 통합할 수 있습니다. SCIM 엔드포인트를 빌드하는 응용 프로그램 개발자는 사용자 지정 작업을 수행할 필요 없이 모든 SCIM 호환 클라이언트와 통합할 수 있습니다.

응용 프로그램에 프로비저닝을 자동화하려면 AZURE AD SCIM 클라이언트와 SCIM 끝점을 빌드하고 통합해야 합니다. 다음 단계를 수행하여 사용자 및 그룹을 응용 프로그램에 프로비전하기 시작합니다. 
    
  * **[1단계: 사용자 및 그룹 스키마를 디자인합니다.](#step-1-design-your-user-and-group-schema)** 응용 프로그램에 필요한 개체 및 특성을 식별하고 Azure AD SCIM 구현에서 지원하는 사용자 및 그룹 스키마에 매핑하는 방법을 결정합니다.

  * **[2단계: Azure AD SCIM 구현이해.](#step-2-understand-the-azure-ad-scim-implementation)** Azure AD SCIM 클라이언트가 구현되는 방법을 이해하고 SCIM 프로토콜 요청 처리 및 응답을 모델링합니다.

  * **[3단계: SCIM 끝점을 작성합니다.](#step-3-build-a-scim-endpoint)** Azure AD 프로비저닝 서비스와 통합하려면 끝점이 SCIM 2.0 호환되어야 합니다. 옵션으로 Microsoft 공통 언어 인프라(CLI) 라이브러리 및 코드 샘플을 사용하여 엔드포인트를 작성할 수 있습니다. 이러한 샘플은 참조 및 테스트용입니다. 프로덕션 앱에 종속성을 가지도록 코딩하지 않는 것이 좋습니다.

  * **[4단계: SCIM 끝점을 Azure AD SCIM 클라이언트와 통합합니다.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 조직에서 Azure AD가 지원하는 SCIM 2.0의 프로필을 구현하는 타사 응용 프로그램을 사용하는 경우 사용자 및 그룹의 프로비저닝 및 프로비저닝 을 모두 자동화할 수 있습니다.

  * **[5단계: 응용 프로그램을 Azure AD 응용 프로그램 갤러리에 게시합니다.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 고객이 응용 프로그램을 쉽게 검색하고 프로비저닝을 쉽게 구성할 수 있도록 합니다. 

![SCIM 끝점을 Azure AD와 통합하기 위한 단계](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>1단계: 사용자 및 그룹 스키마 디자인

모든 응용 프로그램에는 사용자 또는 그룹을 만들기 위해 서로 다른 특성이 필요합니다. 응용 프로그램에 필요한 개체(사용자, 그룹) 및 특성(이름, 관리자, 직책 등)을 식별하여 통합을 시작합니다. SCIM 표준은 사용자 및 그룹 관리를 위한 스키마를 정의합니다. 핵심 사용자 스키마에는 **id(서비스** 공급자 정의 식별자), **externalId(클라이언트** 정의 식별자) 및 **메타(서비스** 공급자가 유지 관리하는 읽기 전용 메타데이터)의 세 가지 특성만 필요합니다. 다른 모든 특성은 선택 사항입니다. SCIM 표준은 핵심 사용자 스키마 외에도 엔터프라이즈 사용자 확장 및 응용 프로그램의 요구 사항을 충족하도록 사용자 스키마를 확장하기 위한 모델을 정의합니다. 예를 들어 응용 프로그램에 사용자 관리자가 필요한 경우 엔터프라이즈 사용자 스키마를 사용하여 사용자의 관리자와 핵심 스키마를 수집하여 사용자의 전자 메일을 수집할 수 있습니다. 스키마를 디자인하려면 다음 단계를 따르십시오.
  1. 응용 프로그램에 필요한 특성을 나열합니다. 요구 사항을 인증에 필요한 특성(예: loginName 및 전자 메일), 사용자의 수명 주기 관리에 필요한 특성(예: 상태/활성) 및 특정 응용 프로그램이 작동하는 데 필요한 기타 특성(예: 관리자, 태그)으로 세분화하는 것이 유용할 수 있습니다.
  2. 이러한 특성이 핵심 사용자 스키마 또는 엔터프라이즈 사용자 스키마에 이미 정의되어 있는지 확인합니다. 핵심 또는 엔터프라이즈 사용자 스키마에서 필요하고 다루지 않는 특성이 있는 경우 필요한 특성을 포함하는 사용자 스키마에 대한 확장을 정의해야 합니다. 아래 예제에서는 사용자에 대한 "태그"를 프로비전할 수 있도록 확장 프로그램을 추가했습니다. 핵심 및 엔터프라이즈 사용자 스키마로 시작하여 나중에 추가 사용자 지정 스키마로 확장하는 것이 가장 좋습니다.  
  3. SCIM 특성을 Azure AD의 사용자 특성에 매핑합니다. SCIM 끝점에 정의한 특성 중 하나에 Azure AD 사용자 스키마에 대한 명확한 대응이 없는 경우 대부분의 테넌트에서 데이터가 사용자 개체에 전혀 저장되지 않을 가능성이 높습니다. 사용자를 만들기 위해 이 특성을 선택사항으로 사용할 수 있는지 여부를 고려합니다. 응용 프로그램이 작동하는 데 중요한 특성인 경우 테넌트 관리자에게 스키마를 확장하거나 "태그" 속성에 대해 아래와 같이 확장 특성을 사용하도록 안내합니다.

### <a name="table-1-outline-the-attributes-that-you-need"></a>표 1: 필요한 속성의 개요 
| 1단계: 앱에 필요한 특성 결정| 2단계: 앱 요구 사항을 SCIM 표준에 매핑| 3단계: Azure AD 특성에 SCIM 특성 매핑|
|--|--|--|
|Loginname|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|네스트.성이름|lastName|
|작업 메일|이메일[eq "작업"을 입력].값|Mail|
|manager|manager|manager|
|tag|항아리:ietf:params:scim:스키마:확장:2.0:사용자 지정 확장:태그|extensionAttribute1|
|상태|활성|isSoftDeleted (계산 된 값은 사용자에 저장되지 않음)|

위에 정의된 스키마는 아래 Json 페이로드를 사용하여 표시됩니다. JSON 표현에는 응용 프로그램에 필요한 특성 외에도 필요한 "id", "externalId" 및 "메타" 특성이 포함됩니다.

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
그런 다음 아래 표를 사용하여 응용 프로그램에 필요한 특성이 Azure AD 및 SCIM RFC의 특성에 매핑되는 방법을 이해할 수 있습니다. Azure AD와 SCIM 끝점 간에 특성이 매핑되는 방법을 [사용자 지정할](customize-application-attributes.md) 수 있습니다. 사용자와 그룹 또는 아래에 표시된 모든 속성을 모두 지원할 필요는 없습니다. Azure AD의 특성이 SCIM 프로토콜의 속성에 매핑되는 방법에 대한 참조입니다. 

| Azure Active Directory 사용자 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |활성 |
|department|urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:부서|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:직원번호|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:확장:엔터프라이즈:2.0:사용자:관리자 |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>표 3: 기본 그룹 특성 매핑

| Azure Active Directory 그룹 | 항아리:ietf:params:scim:스키마:코어:2.0:그룹 |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC에는 정의된 여러 끝점이 있습니다. /User 끝점으로 시작한 다음 거기에서 확장할 수 있습니다. /Schemas 끝점은 사용자 지정 특성을 사용하거나 스키마가 자주 변경되는 경우에 유용합니다. 클라이언트가 최신 스키마를 자동으로 검색할 수 있습니다. /Bulk 끝점은 그룹을 지원할 때 특히 유용합니다. 아래 표는 SCIM 표준에 정의된 다양한 끝점을 설명합니다. /Schemas 끝점은 사용자 지정 특성을 사용하거나 스키마가 자주 변경되는 경우에 유용합니다. 클라이언트가 최신 스키마를 자동으로 검색할 수 있습니다. /Bulk 끝점은 그룹을 지원할 때 특히 유용합니다. 아래 표는 SCIM 표준에 정의된 다양한 끝점을 설명합니다. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>표 4: 개발할 끝점 결정
|엔드포인트|설명|
|--|--|
|/user|사용자 개체에서 CRUD 작업을 수행합니다.|
|/Group|그룹 개체에서 CRUD 작업을 수행합니다.|
|/서비스제공자구성|지원되는 SCIM 표준의 기능(예: 지원되는 리소스 및 인증 방법)에 대한 세부 정보를 제공합니다.|
|/리소스 유형|각 리소스에 대한 메타데이터 지정|
|/스키마|각 클라이언트 및 서비스 공급자가 지원하는 특성 집합은 다를 수 있습니다. 한 서비스 공급자는 "이름", "제목" 및 "전자 메일"을 포함할 수 있지만 다른 서비스 공급자는 "이름", "제목" 및 "phoneNumbers"를 사용합니다. 스키마 끝점을 사용하면 지원되는 특성을 검색할 수 있습니다.|
|/벌크 (1)|대량 작업을 사용하면 단일 작업(예: 대규모 그룹의 멤버 자격 업데이트)에서 대규모 리소스 개체 컬렉션에 대한 작업을 수행할 수 있습니다.|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>2단계: Azure AD SCIM 구현 이해
> [!IMPORTANT]
> Azure AD SCIM 구현 동작이 2018년 12월 18일에 마지막으로 업데이트되었습니다. 변경된 내용에 대한 자세한 내용은 [Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 규정 준수](application-provisioning-config-problem-scim-compatibility.md)를 참조하세요.

SCIM 2.0 사용자 관리 API를 지원하는 응용 프로그램을 빌드하는 경우 이 섹션에서는 Azure AD SCIM 클라이언트가 구현되는 방법을 자세히 설명합니다. 또한 SCIM 프로토콜 요청 처리 및 응답을 모델링하는 방법도 보여 줍니다. SCIM 끝점을 구현한 후에는 이전 섹션에서 설명한 절차에 따라 테스트할 수 있습니다.

[SCIM 2.0 프로토콜 사양](http://www.simplecloud.info/#Specification)내에서 응용 프로그램은 다음 요구 사항을 충족해야 합니다.

* [SCIM 프로토콜의 섹션 3.3에](https://tools.ietf.org/html/rfc7644#section-3.3)따라 사용자 생성및 선택적으로 그룹 생성을 지원합니다.  
* [SCIM 프로토콜의 섹션 3.5.2에](https://tools.ietf.org/html/rfc7644#section-3.5.2)따라 패치 요청을 사용하여 사용자 또는 그룹 수정을 지원합니다.  
* [SCIM 프로토콜의 섹션 3.4.1에](https://tools.ietf.org/html/rfc7644#section-3.4.1)따라 이전에 만든 사용자 또는 그룹에 대해 알려진 리소스 검색을 지원합니다.  
* [SCIM 프로토콜의 섹션 3.4.2에](https://tools.ietf.org/html/rfc7644#section-3.4.2)따라 사용자 또는 그룹 쿼리를 지원합니다.  기본적으로 사용자는 `id` 해당 사용자에 의해 검색되고 및 `username` `externalid`에 의해 쿼리되고 `displayName`그룹은 에 의해 쿼리됩니다.  
* SCIM 프로토콜의 섹션 3.4.2에 따라 ID 및 관리자별로 사용자 쿼리를 지원합니다.  
* SCIM 프로토콜의 섹션 3.4.2에 따라 ID 및 구성원별로 쿼리 그룹을 지원합니다.  
* 응용 프로그램에 Azure AD의 인증 및 권한 부여를 위해 단일 베어러 토큰을 수락합니다.

AZURE AD와의 호환성을 보장하기 위해 SCIM 끝점을 구현할 때 다음 일반 지침을 따릅니다.

* `id`은 모든 리소스에 필요한 속성입니다. 리소스를 반환하는 모든 응답은 멤버가 0인 `ListResponse` 경우를 제외하고 각 리소스에 이 속성이 있는지 확인해야 합니다.
* 쿼리/필터 요청에 대한 응답은 `ListResponse`항상 .
* 그룹은 선택 사항이지만 SCIM 구현에서 PATCH 요청을 지원하는 경우에만 지원됩니다.
* 전체 리소스를 PATCH 응답에 포함할 필요는 없습니다.
* Microsoft Azure AD는 다음 연산자만 사용합니다.  
    - `eq`
    - `and`
* 에 정의된 대로 SCIM의 구조 요소, 특히 PATCH `op` 작업 값에 대/소문자를 구분하는 일치가 https://tools.ietf.org/html/rfc7644#section-3.5.2필요하지 않습니다. Azure AD는 'op' 값을 `Add`으로 `Replace`내보며 `Remove`.
* Microsoft Azure AD는 엔드포인트와 자격 증명이 유효한지 확인하기 위해 임의의 사용자 및 그룹을 가져오도록 요청합니다. 또한 [Azure 포털에서](https://portal.azure.com)테스트 **연결** 흐름의 일부로 수행됩니다. 
* 리소스를 쿼리할 수 있는 특성은 [Azure Portal의](https://portal.azure.com)응용 프로그램에서 일치하는 특성으로 설정해야 합니다. 자세한 내용은 [사용자 프로비저닝 특성 매핑 사용자 지정을 참조하세요.](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>사용자 프로비전 및 프로비저닝 해제

다음 그림에서는 Azure Active Directory가 응용 프로그램의 ID 저장소에서 사용자의 수명 주기를 관리하기 위해 SCIM 서비스에 보내는 메시지를 보여 줍니다.  

![사용자 프로비저닝 및 프로비저닝 해제 시퀀스 표시](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*사용자 프로비저닝 및 프로비저닝 순서 해제*

### <a name="group-provisioning-and-deprovisioning"></a>그룹 프로비저닝 및 프로비저닝 해제

그룹 프로비저닝 및 프로비저닝 해제는 선택 사항입니다. 다음 그림에서는 Azure AD가 SCIM 서비스에 전송하여 응용 프로그램의 ID 저장소에서 그룹의 수명 주기를 관리하는 메시지를 보여 줍니다.  이러한 메시지는 두 가지 방법으로 사용자에 대한 메시지와 다릅니다.

* 그룹 검색 요청은 멤버 특성이 요청에 대한 응답으로 제공된 모든 리소스에서 제외되도록 지정합니다.  
* 참조 특성에 특정 값이 있는지를 확인하는 요청은 멤버 특성에 대한 요청입니다.  

![그룹 프로비저닝 및 프로비저닝 해제 시퀀스 표시](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*그룹 프로비저닝 및 프로비저닝 해제 시퀀스*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 프로토콜 요청 및 응답
이 섹션에서는 Azure AD SCIM 클라이언트에서 내보낸 SCIM 요청 예제와 예상 응답 예제를 제공합니다. 최상의 결과를 얻으려면 앱을 코딩하여 이러한 요청을 이 형식으로 처리하고 예상응답을 내보내야 합니다.

> [!IMPORTANT]
> Azure AD 사용자 프로비저닝 서비스가 아래에 설명된 작업을 내림차호를 내림차호를 이해하는 방법과 방법을 이해하려면 [프로비저닝 주기: 프로비저닝](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [작동 방식의](how-provisioning-works.md)초기 및 증분 섹션을 참조하십시오.

[사용자 작업](#user-operations)
  - [사용자 만들기(응답](#create-user) [요청)](#request) / [Response](#response)
  - [사용자 받기(응답](#get-user) [요청)](#request-1) / [Response](#response-1)
  - [쿼리별 사용자](#get-user-by-query) [받기(응답 요청)](#request-2) / [Response](#response-2)
  - [쿼리별 사용자 받기 - 결과 0(응답](#get-user-by-query---zero-results) [요청)](#request-3)
/ [Response](#response-3)
  - [업데이트 사용자 [다중 값 속성]](#update-user-multi-valued-properties) [(요청](#request-4) /  [응답)](#response-4)
  - [업데이트 사용자 [단일 값 속성]](#update-user-single-valued-properties) [(요청](#request-5)
/ [응답)](#response-5) 
  - [사용자 사용 안 함(응답](#disable-user) [요청)](#request-14) / 
[Response](#response-14)
  - [사용자 삭제(응답](#delete-user) [요청)](#request-6) / 
[Response](#response-6)


[그룹 운영](#group-operations)
  - [그룹 만들기(응답](#create-group) [요청)](#request-7) / [Response](#response-7)
  - [그룹 받기(응답](#get-group) [요청)](#request-8) / [Response](#response-8)
  - [표시이름(요청](#get-group-by-displayname) [Request](#request-9) / [응답)별로](#response-9)그룹 받기
  - [업데이트 그룹 [비구성원 특성]](#update-group-non-member-attributes) [(요청](#request-10) /
  [응답)](#response-10)
  - [업데이트 그룹 [구성원 추가]](#update-group-add-members) [(요청](#request-11) /
[응답)](#response-11)
  - [업데이트 그룹 [구성원 제거]](#update-group-remove-members) [(요청](#request-12) /
[응답)](#response-12)
  - [그룹 삭제(응답](#delete-group) [요청)](#request-13) /
[Response](#response-13)

### <a name="user-operations"></a>사용자 작업

* 사용자는 또는 `email[type eq "work"]` 특성에 `userName` 의해 쿼리될 수 있습니다.  

#### <a name="create-user"></a>사용자 만들기

###### <a name="request"></a>요청

*게시물 /사용자*
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

*HTTP/1.1 201 생성됨*
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

#### <a name="get-user"></a>사용자 얻기

###### <a name="request"></a><a name="request-1"></a>요청
*GET /사용자/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>응답(사용자 검색)
*HTTP/1.1 200 확인*
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
*GET /사용자/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>응답(사용자를 찾을 수 없습니다. 세부 정보는 필요하지 않으며 상태만 필요합니다.

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>쿼리별로 사용자 받기

##### <a name="request"></a><a name="request-2"></a>요청

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Response

*HTTP/1.1 200 확인*
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

#### <a name="get-user-by-query---zero-results"></a>쿼리별 사용자 받기 - 결과 0

##### <a name="request"></a><a name="request-3"></a>요청

*GET /Users?filter=userName eq "존재하지 않는 사용자"*

##### <a name="response"></a><a name="response-3"></a>Response

*HTTP/1.1 200 확인*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>사용자 업데이트 [다중 값 속성]

##### <a name="request"></a><a name="request-4"></a>요청

*패치 /사용자/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a><a name="response-4"></a>Response

*HTTP/1.1 200 확인*
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

##### <a name="request"></a><a name="request-5"></a>요청

*패치 /사용자/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-5"></a>Response

*HTTP/1.1 200 확인*
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

##### <a name="request"></a><a name="request-14"></a>요청

*패치 /사용자/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-14"></a>Response

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

##### <a name="request"></a><a name="request-6"></a>요청

*삭제 /사용자/5171a35d82074e68ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Response

*HTTP/1.1 204 콘텐츠 없음*

### <a name="group-operations"></a>그룹 운영

* 그룹은 항상 빈 구성원 목록으로 만들어야 합니다.
* 특성으로 `displayName` 그룹을 쿼리할 수 있습니다.
* 그룹 PATCH 요청에 대한 업데이트는 응답에서 *HTTP 204 콘텐츠 없음을* 생성해야 합니다. 모든 멤버 의 목록이 있는 본문을 반환하는 것은 바람직하지 않습니다.
* 그룹의 모든 구성원을 반환하는 것을 지원할 필요는 없습니다.

#### <a name="create-group"></a>그룹 만들기

##### <a name="request"></a><a name="request-7"></a>요청

*게시물 /그룹 HTTP/1.1*
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

##### <a name="response"></a><a name="response-7"></a>Response

*HTTP/1.1 201 생성됨*
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

##### <a name="request"></a><a name="request-8"></a>요청

*GET /그룹/40734ae655284ad3abcc?제외속성=멤버 HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Response
*HTTP/1.1 200 확인*
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

#### <a name="get-group-by-displayname"></a>표시이름으로 그룹 받기이름

##### <a name="request"></a><a name="request-9"></a>요청
*GET /그룹?제외특성=멤버&필터=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Response

*HTTP/1.1 200 확인*
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

#### <a name="update-group-non-member-attributes"></a>그룹 업데이트 [비구성원 특성]

##### <a name="request"></a><a name="request-10"></a>요청

*패치 /그룹/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Response

*HTTP/1.1 204 콘텐츠 없음*

### <a name="update-group-add-members"></a>그룹 업데이트 [구성원 추가]

##### <a name="request"></a><a name="request-11"></a>요청

*패치 /그룹/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Response

*HTTP/1.1 204 콘텐츠 없음*

#### <a name="update-group-remove-members"></a>그룹 업데이트 [구성원 제거]

##### <a name="request"></a><a name="request-12"></a>요청

*패치 /그룹/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Response

*HTTP/1.1 204 콘텐츠 없음*

#### <a name="delete-group"></a>그룹 삭제

##### <a name="request"></a><a name="request-13"></a>요청

*삭제 /그룹/cdb1ce18f6594079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Response

*HTTP/1.1 204 콘텐츠 없음*

### <a name="security-requirements"></a>보안 요구 사항
**TLS 프로토콜 버전**

허용되는 유일한 TLS 프로토콜 버전은 TLS 1.2 및 TLS 1.3입니다. 다른 버전의 TLS는 허용되지 않습니다. SSL 버전은 허용되지 않습니다. 
- RSA 키는 2,048비트 이상이어야 합니다.
- ECC 키는 승인된 타원 곡선을 사용하여 생성된 256비트 이상이어야 합니다.


**키 길이**

모든 서비스는 충분한 길이의 암호화 키를 사용하여 생성된 X.509 인증서를 사용해야 합니다.

**시퍼 스위트**

모든 서비스는 아래 명시된 순서대로 다음 암호 제품군을 사용하도록 구성되어야 합니다. RSA 인증서만 있는 경우 ECDSA 암호 제품군을 설치해도 아무런 영향도 미치지 않습니다. </br>

TLS 1.2 암호 스위트 최소 바:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>3단계: SCIM 끝점 구축

스키마를 설계하고 Azure AD SCIM 구현을 이해한 이제 SCIM 끝점 개발을 시작할 수 있습니다. 처음부터 시작하여 구현을 완전히 구축하는 대신 SCIM 커뮤니티에서 게시한 여러 오픈 소스 SCIM 라이브러리에 의존할 수 있습니다.

Azure AD 프로비저닝 팀에서 게시한 오픈 소스 .NET Core [참조 코드는](https://aka.ms/SCIMReferenceCode) 개발을 시작할 수 있는 리소스 중 하나입니다. SCIM 끝점을 구축한 후에는 테스트할 수 있습니다. 참조 코드의 일부로 제공된 [우체부 테스트](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) 컬렉션을 사용하거나 [위에](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)제공된 샘플 요청/응답을 통해 실행할 수 있습니다.  

   > [!Note]
   > 참조 코드는 SCIM 끝점 빌드를 시작하는 데 도움이 되며 "AS IS"가 제공됩니다. 커뮤니티의 기여는 코드를 구축하고 유지하는 데 도움을 줄 수 있습니다.

이 솔루션은 두 개의 프로젝트인 _Microsoft.SCIM과_ _Microsoft.SCIM.WebHostSample으로_구성됩니다.

_Microsoft.SCIM_ 프로젝트는 SCIM 사양을 준수하는 웹 서비스의 구성 요소를 정의하는 라이브러리입니다. _Microsoft.SCIM.IProvider,_ 요청은 ID 저장소에서 작동하도록 프로그래밍된 공급자의 메서드에 대한 호출로 변환됩니다.

![분석 분석: 공급자의 메서드에 대한 호출로 변환된 요청](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft.SCIM.WebHostSample_ 프로젝트는 _빈_ 템플릿을 기반으로 ASP.NET 핵심 웹 응용 프로그램입니다. 이렇게 하면 샘플 코드를 독립 실행형으로 배포하거나 컨테이너에서 호스팅하거나 인터넷 정보 서비스 내에서 배포할 수 있습니다. 또한 샘플 ID 저장소로 메모리에 클래스를 유지 _하는 Microsoft.SCIM.IProvider_ 인터페이스를 구현 합니다.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>사용자 지정 SCIM 끝점 작성

SCIM 서비스에는 루트 인증 기관이 다음 이름 중 하나인 HTTP 주소 및 서버 인증 인증서가 있어야 합니다.

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK에는 개발 중에 사용할 수 있는 HTTPS 개발 인증서가 포함되어 있으며, 인증서는 첫 실행 환경의 일부로 설치됩니다. ASP.NET 코어 웹 응용 프로그램을 실행하는 방법에 따라 다른 포트를 수신대기합니다.

* 마이크로소프트.SCIM.웹 호스트 샘플:https://localhost:5001
* IIS 익스프레스:https://localhost:44359/

ASP.NET 코어의 HTTPS에 대한 자세한 내용은 다음 링크를 사용합니다 [ASP.NET.](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>엔드포인트 인증 처리

Azure Active Directory에서 요청은 OAuth 2.0 전달자 토큰을 포함합니다. 요청을 받는 모든 서비스는 발급자가 예상되는 Azure Active Directory 테넌트에 대해 Azure Active Directory인 것으로 인증해야 합니다.

토큰에서 발급기는 iss 클레임과 같이 `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`식별됩니다. 이 예제에서 클레임 값의 기본 `https://sts.windows.net`주소는 Azure Active Directory를 발급자로 식별하고 상대 주소 세그먼트인 _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422는_토큰이 발급된 Azure Active Directory 테넌트의 고유 식별자입니다.

토큰의 대상은 갤러리의 애플리케이션에 대한 애플리케이션 템플릿 ID가 되며, 단일 테넌트에 등록된 `iss` 각 애플리케이션은 SCIM 요청과 동일한 클레임을 받을 수 있습니다. 갤러리의 각 응용 프로그램에 대한 응용 프로그램 [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) 템플릿 ID는 다양, 갤러리 응용 프로그램에 대한 응용 프로그램 템플릿 ID 주위에 대한 질문이 문의하시기 바랍니다. 모든 사용자 지정 앱의 응용 프로그램 템플릿 ID는 _8adf8e6e-67b2-4cf2-a259-e3dc5476c621입니다._

샘플 코드에서 요청은 Microsoft.AspNetCore.Authentication.Jwt베어러 패키지를 사용하여 인증됩니다. 다음 코드는 지정된 테넌트에 대해 Azure Active Directory에서 발급한 bearer 토큰을 사용하여 서비스의 끝점에 대한 요청이 인증되도록 적용합니다.

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

제공된 [우체부 테스트를](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) 사용하고 localhost를 사용하여 로컬 디버깅을 수행하려면 베어러 토큰도 필요합니다. 샘플 코드는 ASP.NET Core 환경을 사용하여 개발 단계에서 인증 옵션을 변경하고 자체 서명된 토큰을 사용할 수 있도록 합니다.

ASP.NET 코어의 여러 환경에 대한 자세한 내용은 다음 링크를 사용 [ASP.NET하십시오.](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

다음 코드는 사용자 지정 키로 서명된 베어러 토큰을 사용하여 서비스의 끝점에 대한 요청이 인증되도록 적용합니다.

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

유효한 베어러 토큰을 얻기 위해 토큰 컨트롤러에 GET 요청을 보내면 _GenerateJSONWebToken_ 메서드는 개발을 위해 구성된 매개 변수와 일치하는 토큰을 만들 책임이 있습니다.

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>사용자 프로비저닝 및 프로비저닝 해제 처리

***실시예 1. 일치하는 사용자에 대한 서비스 쿼리***

Azure Active Directory는 externalId 특성 값이 Azure AD의 사용자 mailNickname 특성 값과 일치하는 사용자를 서비스에 쿼리합니다. 쿼리는 이 예제와 같은 하이퍼텍스트 전송 프로토콜(HTTP) 요청으로 표현되며, 여기서 jyoung은 Azure Active Directory에 있는 사용자의 mailNickname 샘플입니다.

>[!NOTE]
> 이것은 단지 예제입니다. 모든 사용자에게 mail@Mail@adaily.co.kr] 또한 일치하는 데 사용되는 특성(이 경우 externalId)은 [Azure AD 특성 매핑에서](customize-application-attributes.md)구성할 수 있습니다.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 QueryAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

샘플 쿼리에서 externalId 특성에 대해 지정된 값을 가진 사용자의 경우 QueryAsync 메서드에 전달된 인수의 값은 다음과 같습니다.

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***실시예 2. 사용자 프로비전***

mailAny 특성 값과 일치하는 외부 Id 특성 값을 가진 사용자의 웹 서비스에 대한 쿼리에 대한 응답이 사용자를 반환하지 않는 경우 Azure Active Directory는 해당 사용자에 해당하는 사용자를 프로비전하도록 요청합니다. Azure Active 디렉터리에서  다음은 그러한 요청의 예제입니다. 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

샘플 코드에서 요청은 서비스 공급자의 CreateAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

사용자 프로비전 요청에서 리소스 인수의 값은 Microsoft.SCIM.Schemas 라이브러리에 정의된 Microsoft.SCIM.Core2EnterpriseUser 클래스의 인스턴스입니다.  사용자 프로비저닝 요청이 성공하면 메서드의 구현은 Microsoft.SCIM.Core2EnterpriseUser 클래스의 인스턴스를 반환하고 식별자 속성 값은 새로 프로비전된 프로비저닝의 고유 식별자로 설정됩니다. 사용자.  

***실시예 3. 사용자의 현재 상태 쿼리*** 

SCIM에 의해 제어되는 ID 저장소에 있는 것으로 알려진 사용자를 업데이트하기 위해 Azure Active Directory는 다음과 같은 요청으로 서비스에서 해당 사용자의 현재 상태를 요청합니다. 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 RetrieveAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

사용자의 현재 상태를 검색하는 요청 예제에서 매개 변수 인수 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
* 식별자: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***실시예 4. 업데이트할 참조 특성의 값을 쿼리합니다.*** 

참조 특성을 업데이트할 경우 Azure Active Directory는 서비스를 쿼리하여 서비스가 프런트된 ID 저장소의 참조 특성의 현재 값이 Azure Active에서 해당 특성의 값과 이미 일치하는지 여부를 결정합니다. 디렉터리. 사용자의 경우 현재 값이 이 방식으로 쿼리된 유일한 특성은 관리자 특성입니다. 다음은 사용자 개체의 관리자 특성에 현재 특정 값이 있는지 여부를 확인하는 요청의 예입니다. 매개 변수 인수의 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

여기서, 인덱스 x의 값은 0이고 인덱스 y의 값은 1일 수 있고, x의 값은 1일 수 있고 y의 값은 0일 수 있으며, 필터 쿼리 파라미터의 표현의 순서에 따라 0이 될 수 있다.   

***실시예 5. Azure AD에서 사용자를 업데이트하기 위해 SCIM 서비스에 요청*** 

다음은 Azure Active Directory에서 SCIM 서비스로 사용자를 업데이트하는 요청의 예입니다. 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

샘플 코드에서 요청은 서비스 공급자의 UpdateAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

사용자를 업데이트하는 요청의 예에서 패치 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***실시예 6. 사용자 프로비저닝 해제***

SCIM 서비스가 앞에 있는 ID 저장소에서 사용자를 프로비저닝 해제하기 위해 Azure AD는 다음과 같은 요청을 보냅니다.

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 DeleteAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

resourceIdentifier 인수의 값으로 제공 된 개체는 사용자를 프로비저닝 해제 하는 요청의 예에서 이러한 속성 값을 가지고 있습니다. 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>4단계: SCIM 엔드포인트를 Azure AD SCIM 클라이언트와 통합

Azure AD는 [SCIM 2.0 프로토콜의](https://tools.ietf.org/html/rfc7644)특정 프로필을 구현하는 응용 프로그램에 할당된 사용자 및 그룹을 자동으로 프로비전하도록 구성할 수 있습니다. 프로필의 세부 사항은 [2단계: Azure AD SCIM 구현 이해에](#step-2-understand-the-azure-ad-scim-implementation)설명되어 있습니다.

애플리케이션 공급자 또는 애플리케이션 공급자의 설명서를 통해 이러한 요구 사항과의 호환성을 확인합니다.

> [!IMPORTANT]
> Azure AD SCIM 구현은 Azure AD 와 대상 응용 프로그램 간에 사용자를 지속적으로 동기화하도록 설계되고 매우 구체적인 표준 작업 집합을 구현하는 Azure AD 사용자 프로비전 서비스 위에 구축됩니다. Azure AD SCIM 클라이언트의 동작을 이해하려면 이러한 동작을 이해하는 것이 중요합니다. 자세한 내용은 [프로비저닝 주기 섹션: 초기 및 증분](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) [프로비저닝 작동 방식에서](how-provisioning-works.md)를 참조하십시오.

### <a name="getting-started"></a>시작

Azure AD 애플리케이션 갤러리에 있는 "비-갤러리 애플리케이션" 기능을 사용하여 이 문서에서 설명한 SCIM 프로필을 지원하는 애플리케이션을 Azure Active Directory에 연결할 수 있습니다. 일단 연결되면 Azure AD는 할당된 사용자 및 그룹에 대해 애플리케이션의 SCIM 엔드포인트를 쿼리하고, 할당 세부 정보에 따라 이러한 사용자 및 그룹을 만들거나 수정하는 동기화 프로세스를 40분마다 실행합니다.

**SCIM을 지원하는 애플리케이션을 연결하려면:**

1. Azure Active [디렉터리 포털에](https://aad.portal.azure.com)로그인합니다. [개발자 프로그램에](https://developer.microsoft.com/office/dev-program) 등록하여 P2 라이선스가 있는 Azure Active Directory에 대한 무료 평가판에 액세스할 수 있습니다.
2. 왼쪽 창에서 **엔터프라이즈 응용 프로그램을** 선택합니다. 갤러리에서 추가된 앱을 포함하여 구성된 모든 앱 목록이 표시됩니다.
3. 선택 **+ 새 응용 프로그램** > **모든** > **비 갤러리 응용 프로그램.**
4. 응용 프로그램의 이름을 입력하고 **추가를** 선택하여 앱 개체를 만듭니다. 새 앱이 엔터프라이즈 응용 프로그램 목록에 추가되고 앱 관리 화면으로 열립니다.

   ![스크린샷은 Azure AD 응용 프로그램 갤러리를 보여 주며](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD 응용 프로그램 갤러리*

5. 앱 관리 화면에서 왼쪽 패널에서 **프로비저닝을 선택합니다.**
6. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.

   ![예: Azure 포털의 앱 프로비저닝 페이지](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Azure 포털에서 프로비저닝 구성*

7. **테넌트 URL** 필드에 애플리케이션의 SCIM 엔드포인트 URL을 입력합니다. 예: `https://api.contoso.com/scim/`
8. SCIM 엔드포인트에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 이 필드가 비어 있는 경우 Azure AD에는 각 요청과 함께 Azure AD에서 발급된 OAuth 베어러 토큰이 포함됩니다. ID 공급자로 Azure AD를 사용하는 앱은 Azure AD에서 발급한 토큰의 유효성을 검사할 수 있습니다. 
   > [!NOTE]
   > 이 필드를 비워 두고 Azure AD에서 생성된 토큰에 의존하는 것은 ***권장되지 않습니다.*** 이 옵션은 주로 테스트 목적으로 사용할 수 있습니다.
9. Azure Active Directory가 SCIM 끝점에 연결하도록 **테스트 연결을** 선택합니다. 시도가 실패하면 오류 정보가 표시됩니다.  

    > [!NOTE]
    > **테스트 연결**은 Azure AD 구성에서 선택된 일치하는 속성으로 임의 GUID를 사용하여 존재하지 않는 사용자의 SCIM 엔드포인트를 쿼리합니다. 예상되는 올바른 응답은 SCIM ListResponse 메시지가 비어 있는 HTTP 200 OK입니다.

10. 응용 프로그램에 연결하려는 시도가 성공하면 **저장을** 선택하여 관리자 자격 증명을 저장합니다.
11. **매핑** 섹션에는 선택 가능한 [특성 매핑](customize-application-attributes.md)집합이 두 개 있습니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    > [!NOTE]
    > 필요에 따라 "그룹" 매핑을 사용하지 않도록 설정하여 그룹 개체의 동기화를 비활성화할 수 있습니다.

12. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. **사용자** 및 그룹 탭에 **할당된 사용자 및 그룹만** 동기화하려면 할당된 사용자 및 그룹(권장)만 동기화를 선택합니다.
13. 구성이 완료되면 **프로비저닝 상태를** **설정**합니다.
14. Azure AD 프로비저닝 서비스를 시작하려면 **저장을** 선택합니다.
15. 할당된 사용자 및 그룹만 동기화하는 경우(권장) **사용자 및 그룹** 탭을 선택하고 동기화할 사용자 또는 그룹을 할당해야 합니다.

초기 주기가 시작되면 왼쪽 패널에서 **프로비저닝 로그를 선택하여 진행률을** 모니터링할 수 있으며, 이 로그는 앱의 프로비저닝 서비스에서 수행한 모든 작업을 보여 줍니다. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](check-status-user-account-provisioning.md)를 참조하세요.

> [!NOTE]
> 초기 주기는 서비스가 실행되는 동안 약 40분마다 발생하는 이후 동기화보다 수행하는 데 시간이 오래 걸립니다.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>5단계: Azure AD 응용 프로그램 갤러리에 응용 프로그램 게시

두 명 이상의 테넌트에서 사용할 응용 프로그램을 빌드하는 경우 Azure AD 응용 프로그램 갤러리에서 사용할 수 있도록 할 수 있습니다. 이렇게 하면 조직에서 응용 프로그램을 쉽게 검색하고 프로비저닝을 구성할 수 있습니다. Azure AD 갤러리에 앱을 게시하고 다른 사용자가 프로비저닝을 쉽게 사용할 수 있습니다. [여기](../develop/howto-app-gallery-listing.md)서 단계를 확인하세요. Microsoft는 고객과 협력하여 응용 프로그램을 갤러리에 통합하고, 엔드포인트를 테스트하고, 고객이 사용할 수 있도록 온보딩 [설명서를](../saas-apps/tutorial-list.md) 릴리스합니다. 

### <a name="gallery-onboarding-checklist"></a>갤러리 온보딩 체크리스트
아래 체크리스트를 따라 응용 프로그램이 빠르게 온보딩되고 고객이 원활한 배포 환경을 제공하는지 확인합니다. 갤러리에 온보딩 할 때 정보가 수집됩니다. 
> [!div class="checklist"]
> * [SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) 사용자 및 그룹 끝점 지원(하나만 필요하지만 둘 다 권장)
> * 테넌트당 초당 25개 이상의 요청 지원(필수)
> * 고객이 갤러리 온보딩을 게시하도록 엔지니어링 및 지원 연락처를 설정합니다(필수)
> * 3 응용 프로그램에 대한 만료되지 않는 테스트 자격 증명(필수)
> * 아래에 설명된 대로 OAuth 권한 부여 코드 부여 또는 수명이 긴 토큰 지원(필수)
> * 고객이 갤러리 온보딩을 게시할 수 있도록 엔지니어링 및 지원 지점을 설정합니다(필수)
> * 단일 PATCH로 여러 그룹 멤버십 업데이트 지원(권장) 
> * SCIM 끝점을 공개적으로 문서화(권장) 
> * [스키마 검색](https://tools.ietf.org/html/rfc7643#section-6) 지원(권장)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>응용 프로그램 갤러리에서 커넥터 프로비저닝에 대한 권한 부여
SCIM 사양은 인증 및 권한 부여를 위한 SCIM 별 체계를 정의하지 않습니다. 기존 산업 표준의 사용에 의존합니다. Azure AD 프로비전 클라이언트는 갤러리의 응용 프로그램에 대한 두 가지 권한 부여 메서드를 지원합니다. 

|권한 부여 방법|장점|단점|고객 지원팀|
|--|--|--|--|
|사용자 이름 및 암호(Azure AD에서 권장하거나 지원하지 않음)|구현하기 쉬운|안전하지 않은 - [Pa$$word 중요하지 않습니다.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|갤러리 앱에 대해 사례별로 지원됩니다. 갤러리가 아닌 앱은 지원되지 않습니다.|
|수명이 긴 베어러 토큰|수명이 긴 토큰은 사용자가 존재할 필요가 없습니다. 관리자는 프로비저닝을 설정할 때 쉽게 사용할 수 있습니다.|수명이 긴 토큰은 전자 메일과 같은 안전하지 않은 방법을 사용하지 않으면 관리자와 공유하기 어려울 수 있습니다. |갤러리 및 비갤러리 앱에서 지원됩니다. |
|OAuth 권한 부여 코드 부여|액세스 토큰은 암호보다 수명이 훨씬 짧으며 수명이 긴 보유자 토큰에는 없는 자동 새로 고침 메커니즘이 있습니다.  실제 사용자는 초기 권한 부여 중에 있어야 하며 책임 수준을 추가해야 합니다. |사용자가 있어야 합니다. 사용자가 조직을 떠나면 토큰이 유효하지 않으며 권한 부여를 다시 완료해야 합니다.|갤러리 앱에서 지원됩니다. 갤러리가 아닌 앱에 대한 지원이 진행 중입니다.|
|OAuth 클라이언트 자격 증명 부여|액세스 토큰은 암호보다 수명이 훨씬 짧으며 수명이 긴 보유자 토큰에는 없는 자동 새로 고침 메커니즘이 있습니다. 권한 부여 코드 부여와 클라이언트 자격 증명 부여 모두 동일한 유형의 액세스 토큰을 만들므로 이러한 메서드 간에 이동하는 것은 API에 투명합니다.  프로비저닝은 완전히 자동화될 수 있으며 사용자 상호 작용 없이 새 토큰을 자동으로 요청할 수 있습니다. ||갤러리 및 비갤러리 앱은 지원되지 않습니다. 지원은 백로그에 있습니다.|

[!NOTE] Azure AD 프로비저닝 구성 사용자 지정 앱 UI에서 토큰 필드를 비워 두는 것은 권장되지 않습니다. 생성된 토큰은 주로 테스트 목적으로 사용할 수 있습니다.

**OAuth 권한 부여 코드 부여 흐름:** 프로비저닝 서비스는 [권한 부여 코드 부여를](https://tools.ietf.org/html/rfc6749#page-24)지원합니다. 갤러리에서 앱 게시 요청을 제출한 후 저희 팀은 귀하와 협력하여 다음 정보를 수집합니다.
*  권한 부여 URL: 사용자 에이전트 리디렉션을 통해 리소스 소유자로부터 권한을 얻기 위한 클라이언트의 URL입니다. 사용자가 액세스 권한을 부여하기 위해 이 URL로 리디렉션됩니다. 
*  토큰 교환 URL: 일반적으로 클라이언트 인증을 사용하여 액세스 토큰에 대한 권한 부여 권한을 교환하는 클라이언트의 URL입니다.
*  클라이언트 ID: 권한 부여 서버는 등록된 클라이언트에 클라이언트 식별자를 발급하며, 이 식별자는 클라이언트가 제공한 등록 정보를 나타내는 고유한 문자열입니다.  클라이언트 식별자는 비밀이 아닙니다. 리소스 소유자에게 노출되며 클라이언트 인증에 단독으로 **사용해서는 안 됩니다.**  
*  클라이언트 보안: 클라이언트 보안 은 권한 부여 서버에서 생성된 비밀입니다. 권한 부여 서버에만 알려진 고유 값이어야 합니다. 

OAuth v1은 클라이언트 보안 정보의 노출로 인해 지원되지 않습니다. OAuth v2가 지원됩니다.  

모범 사례(권장되지만 필수는 아님):
* 여러 리디렉션 URL을 지원합니다. 관리자는 "portal.azure.com"과 "aad.portal.azure.com"에서 프로비전을 구성할 수 있습니다. 여러 리디렉션 URL을 지원하면 사용자가 두 포털에서 액세스 권한을 부여할 수 있습니다.
* 가동 중지 시간 없이 원활한 비밀 갱신을 보장하기 위해 여러 비밀을 지원합니다. 

**오래 살았던 OAuth 베어러 토큰:** 응용 프로그램에서 OAuth 권한 부여 코드 부여 흐름을 지원하지 않는 경우 관리자가 프로비저닝 통합을 설정하는 데 사용할 수 있는 것보다 오래 지속되는 OAuth 베어러 토큰을 생성할 수도 있습니다. 토큰은 영구적이어야 하며, 그렇지 않으면 토큰이 만료되면 프로비저닝 작업이 [격리됩니다.](application-provisioning-quarantine-status.md) 이 토큰의 크기는 1KB 미만이어야 합니다.  

추가 인증 및 권한 부여 방법에 대한 자세한 내용은 [UserVoice](https://aka.ms/appprovisioningfeaturerequest)에 알려주십시오.

### <a name="gallery-go-to-market-launch-check-list"></a>갤러리 시장 출시 체크리스트
공동 통합에 대한 인식과 수요를 유도하려면 기존 문서를 업데이트하고 마케팅 채널의 통합을 증폭하는 것이 좋습니다.  아래는 출시를 지원하기 위해 완료하는 것이 좋습니다 체크리스트 활동 세트입니다.

* **영업 및 고객 지원 준비.** 영업 및 지원 팀이 통합 기능을 인식하고 말할 수 있는지 확인합니다. 영업 및 지원 팀에 대한 간략한 설명, FAQ 제공 및 판매 자료에 통합 포함 
* **블로그 게시물 및/또는 보도 자료.** 공동 통합, 이점 및 시작 방법을 설명하는 블로그 게시물 또는 보도 자료를 제작합니다. [예: 임프리바타 및 Azure 활성 디렉토리 보도 자료](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **소셜 미디어.** 트위터, 페이스북, 링크드인 과 같은 소셜 미디어를 활용하여 고객과의 통합을 홍보하세요. 게시물을 리트윗할 수 있도록 포함해야 @AzureAD 합니다. [예: 임프리바타 트위터 포스트](https://twitter.com/azuread/status/1123964502909779968)
* **마케팅 웹 사이트.** 공동 통합의 가용성을 포함하도록 마케팅 페이지(예: 통합 페이지, 파트너 페이지, 가격 책정 페이지 등)를 만들거나 업데이트합니다. [예: Pingboard 통합 페이지,](https://pingboard.com/org-chart-for) [스마트 시트 통합 페이지,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [Monday.com 가격 페이지](https://monday.com/pricing/) 
* **기술 문서.** 고객의 시작 방법에 대한 도움말 센터 문서 또는 기술 문서를 만듭니다. [예: Envoy + Microsoft Azure Active 디렉터리 통합.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **고객 커뮤니케이션.** 고객 커뮤니케이션(월간 뉴스레터, 이메일 캠페인, 제품 릴리스 정보)을 통해 고객에게 새로운 통합을 알릴 수 있습니다. 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Azure AD 프로비저닝 서비스에서 사용하는 IP 주소가 SCIM 요청을 할 수 있도록 허용

특정 앱에서는 앱으로의 인바운드 트래픽을 허용합니다. Azure AD 프로비저닝 서비스가 예상대로 작동하려면 사용된 IP 주소를 허용해야 합니다. 각 서비스 태그/지역의 IP 주소 목록은 JSON 파일 - [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요. 필요에 따라 이러한 IP를 다운로드하여 방화벽에 프로그래밍할 수 있습니다. Azure AD 프로비저닝에 대한 예약된 IP 범위는 "AzureActiveDirectoryDomainServices"에서 찾을 수 있습니다.

## <a name="related-articles"></a>관련 문서

* [사용자 프로비저닝 및 SaaS 앱 프로비저닝 해제 자동화](user-provisioning.md)
* [사용자 프로비전을 위한 사용자 지정 특성 매핑](customize-application-attributes.md)
* [특성 매핑에 대한 표현식 작성](functions-for-customizing-application-data.md)
* [사용자 프로비저닝을 위한 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)
* [계정 프로비전 알림](user-provisioning.md)
* [SaaS 앱을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
