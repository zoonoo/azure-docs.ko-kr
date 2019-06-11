---
title: Azure Active Directory에서 SCIM을 사용하여 앱 프로비전 자동화 | Microsoft Docs
description: Azure Active Directory는 SCIM 프로토콜 사양에 정의된 인터페이스를 가진 웹 서비스가 향하는 애플리케이션 또는 ID 저장소에 사용자 및 그룹을 자동으로 프로비전할 수 있습니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: a62f44783d63131812794a4b55f0e9f9f3b45f27
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742464"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>SCIM(System for Cross-Domain Identity Management)을 사용하여 사용자 및 그룹을 Azure Active Directory에서 애플리케이션으로 자동 프로비전

## <a name="overview"></a>개요

SCIM에 표준화 된 프로토콜 이며 드라이브 identities 시스템 전체에서 관리 하는 방법에 일관성 향상 하려고 하는 스키마입니다. 응용 프로그램에서는 사용자 관리 SCIM 끝점을 프로 비전 서비스는 Azure AD 사용자 만들기, 수정 또는 삭제이 끝점에 할당 된 사용자 및 그룹에 대 한 요청을 보낼 수 있습니다. 

Azure AD 지 원하는 응용 프로그램의 많은 [미리 통합 된 자동 사용자 프로 비전](../saas-apps/tutorial-list.md) SCIM 사용자를 수신 하는 방법을 변경 알림을 구현 합니다.  이 외에도 고객의 특정 프로필을 지 원하는 응용 프로그램을 연결할 수는 [SCIM 2.0 프로토콜 사양](https://tools.ietf.org/html/rfc7644) Azure portal에서 제네릭 "비 갤러리" 통합 옵션을 사용 합니다. 

이 기사의 초점은 비 갤러리 응용 프로그램에 대 한 제네릭 SCIM 커넥터의 일부분으로 Azure AD를 구현 하는 SCIM 2.0의 프로필 켜져 있습니다. 그러나 응용 프로그램의 테스트 성공 SCIM을 지 일반 Azure AD를 사용 하 여 커넥터는 사용자 프로 비전을 지 원하는 것으로 Azure AD 갤러리에 나열 된 앱을 시작 하는 단계입니다. Azure AD 응용 프로그램 갤러리에 나열 된 응용 프로그램을 가져오는 방법에 대 한 자세한 내용은 참조 하세요. [방법: Azure AD 응용 프로그램 갤러리에 응용 프로그램 나열](../develop/howto-app-gallery-listing.md)합니다.
 

>[!IMPORTANT]
>Azure AD SCIM 구현 동작이 2018년 12월 18일에 마지막으로 업데이트되었습니다. 변경된 내용에 대한 자세한 내용은 [Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 규정 준수](application-provisioning-config-problem-scim-compatibility.md)를 참조하세요.

![][0]
*그림 1: Azure Active Directory에서 SCIM을 구현 하는 응용 프로그램 또는 id 저장소에 프로 비전*

이 문서는 네 개의 섹션으로 분할 됩니다.

* **[SCIM 2.0을 지 원하는 타사 응용 프로그램에 사용자 및 그룹 프로 비전](#provisioning-users-and-groups-to-applications-that-support-scim)**  -조직에 Azure AD는 SCIM 2.0의 프로필에서 지 원하는 구현, 시작할 수 있는 모두 자동화 타사 응용 프로그램을 사용 하는 경우 프로비저닝 및 현재 사용자 및 그룹의 프로비저닝 해제 합니다.

* **[Azure AD SCIM 구현을 이해](#understanding-the-azure-ad-scim-implementation)**  -이 섹션에서는 Azure AD SCIM 클라이언트 구현 하는 방법 및 모델링 해야 하는 방법을 자세히 설명 SCIM 2.0 사용자 관리 API를 지 원하는 응용 프로그램을 빌드하는 경우 SCIM 프로토콜 처리 및 응답을 요청 합니다.
  
* **[Microsoft CLI 라이브러리를 사용 하는 SCIM 끝점 빌드](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  -코드 샘플과 함께 공용 언어 인프라 (CLI) 라이브러리는 SCIM 끝점 개발 하 고 SCIM 메시지를 변환 하는 방법을 표시 합니다.  

* **[사용자 및 그룹 스키마 참조](#user-and-group-schema-reference)**  -비 갤러리 응용 프로그램에 대 한 Azure AD SCIM 구현에서 지 원하는 사용자 및 그룹 스키마를 설명 합니다. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>SCIM을 지원하는 애플리케이션에 사용자 및 그룹 프로비전
Azure AD의 특정 프로필을 구현 하는 응용 프로그램에 자동으로 할당 하는 프로 비전 사용자 및 그룹을 구성할 수 있습니다 합니다 [SCIM 2.0 프로토콜](https://tools.ietf.org/html/rfc7644)합니다. 프로필의 세부 정보에 설명 되어 있습니다 [Azure AD SCIM 구현을 이해](#understanding-the-azure-ad-scim-implementation)합니다.

애플리케이션 공급자 또는 애플리케이션 공급자의 설명서를 통해 이러한 요구 사항과의 호환성을 확인합니다.

>[!IMPORTANT]
>프로 비전 서비스를 지속적으로 사용자를 Azure AD 간의 동기화 유지 하도록 설계 된 Azure AD 사용자를 기반으로 하는 Azure AD SCIM 구현 및 대상 응용 프로그램을 표준 작업의 매우 구체적인 구현입니다. Azure AD SCIM 클라이언트의 동작을 이해 하려면 이러한 동작을 이해 하는 것이 반드시 합니다. 자세한 내용은 [사용자 프로 비전 하는 동안 어떻게 되나요?](user-provisioning.md#what-happens-during-provisioning)합니다.

### <a name="getting-started"></a>시작
Azure AD 애플리케이션 갤러리에 있는 "비-갤러리 애플리케이션" 기능을 사용하여 이 문서에서 설명한 SCIM 프로필을 지원하는 애플리케이션을 Azure Active Directory에 연결할 수 있습니다. 일단 연결되면 Azure AD는 할당된 사용자 및 그룹에 대해 애플리케이션의 SCIM 엔드포인트를 쿼리하고, 할당 세부 정보에 따라 이러한 사용자 및 그룹을 만들거나 수정하는 동기화 프로세스를 40분마다 실행합니다.

**SCIM을 지원하는 애플리케이션을 연결하려면:**

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com)합니다. 

1. 선택 **엔터프라이즈 응용 프로그램** 왼쪽된 창에서. 갤러리에서 추가 된 앱을 비롯 한 모든 구성 된 앱의 목록이 표시 됩니다.

1. 선택 **+ 새 응용 프로그램** > **모든** > **비 갤러리 응용 프로그램**합니다.

1. 응용 프로그램에 대 한 이름을 입력 하 고 선택 **추가** 앱 개체를 만듭니다. 새 앱을 엔터프라이즈 응용 프로그램 목록에 추가 되 고 해당 앱 관리 화면으로 열립니다.
    
   ![][1]
   *그림 2: Azure AD 애플리케이션 갤러리*
    
1. 앱 관리 화면에서 선택 **프로 비전** 왼쪽된 패널에서 합니다.
1. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.
    
   ![][2]
   *그림 3: Azure Portal에서 프로비전 구성*
    
1. **테넌트 URL** 필드에 애플리케이션의 SCIM 엔드포인트 URL을 입력합니다. 예제: https://api.contoso.com/scim/v2/
1. SCIM 엔드포인트에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 
1. 선택 **연결 테스트** Azure Active Directory에서 SCIM 끝점에 연결을 시도 하도록 합니다. 시도가 실패 하면 오류 정보가 표시 됩니다.  

    >[!NOTE]
    >**테스트 연결**은 Azure AD 구성에서 선택된 일치하는 속성으로 임의 GUID를 사용하여 존재하지 않는 사용자의 SCIM 엔드포인트를 쿼리합니다. 예상되는 올바른 응답은 SCIM ListResponse 메시지가 비어 있는 HTTP 200 OK입니다. 

1. 응용 프로그램 성공에 연결 하려고 선택한 경우 **저장할** 관리자 자격 증명을 저장 합니다.
1. **매핑** 섹션에는 선택 가능한 특성 매핑 집합이 두 개 있는데, 하나는 사용자 개체용이고 다른 하나는 그룹 개체용입니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    >[!NOTE]
    >필요에 따라 "그룹" 매핑을 사용하지 않도록 설정하여 그룹 개체의 동기화를 비활성화할 수 있습니다. 

1. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. 선택 **동기화만 할당 된 사용자 및 그룹** (권장)만 사용자 및 그룹 할당을 동기화 하는 **사용자 및 그룹** 탭 합니다.
1. 구성을 완료 되 면 설정 합니다 **프로 비전 상태** 하 **에서**합니다.
1. 선택 **저장할** 를 Azure AD 프로 비전 서비스를 시작 합니다. 
1. 동기화만 사용자 및 그룹 (권장)에 할당 하는 경우 선택 해야 합니다 **사용자 및 그룹** 탭 하 고 사용자 또는 동기화 할 그룹을 할당 합니다.

초기 동기화가 시작 되 면 선택할 수 있습니다 **감사 로그** 앱에서 프로 비전 서비스에 의해 수행 되는 모든 작업을 보여 주는 진행률을 모니터링 하려면 왼쪽된 패널에서. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](check-status-user-account-provisioning.md)를 참조하세요.

> [!NOTE]
> 초기 동기화 서비스가 실행 되는 동안 약 40 분 마다 발생 하는 이후 동기화 보다 더 오래 걸립니다. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Azure AD SCIM 구현을 이해

이 섹션에서는 Azure AD SCIM 클라이언트, 구현 방식 및 프로그램 SCIM 프로토콜을 모델링 해야 하는 방법을 자세히 설명 SCIM 2.0 사용자 관리 API를 지 원하는 응용 프로그램을 빌드하는 경우 처리 및 응답을 요청 합니다. SCIM 끝점에 구현한 후에 이전 섹션에서 설명 된 절차를 수행 하 여 테스트할 수 있습니다.

내 합니다 [SCIM 2.0 프로토콜 사양](http://www.simplecloud.info/#Specification), 응용 프로그램에 이러한 요구 사항을 충족 해야 합니다.

* 사용자, 그리고 필요에 따라 섹션에 따라 그룹을 만들 수 [SCIM 프로토콜의 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)합니다.  
* 기준으로 PATCH 요청에 있는 사용자 또는 그룹 수정을 지원 [3.5.2 SCIM 프로토콜의 섹션](https://tools.ietf.org/html/rfc7644#section-3.5.2)합니다.  
* 사용자 또는 앞에서 만든 그룹에 대 한 알려진된 리소스 검색을 지원 별로 [3.4.1 SCIM 프로토콜의 섹션](https://tools.ietf.org/html/rfc7644#section-3.4.1)합니다.  
* 사용자 또는 그룹 섹션에 따라 쿼리를 지원 [SCIM 프로토콜의 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2)합니다.  기본적으로 사용자를 검색 해당 `id` 쿼리하여 및 해당 `username` 하 고 `externalid`, 그룹에서 쿼리 됩니다 `displayName`합니다.  
* 사용자 ID 및 SCIM 프로토콜의 섹션 3.4.2에 따라 관리자가 쿼리를 지원 합니다.  
* 그룹 ID 및 멤버에 SCIM 프로토콜의 섹션 3.4.2에 따라 쿼리를 지원 합니다.  
* 인증 및 응용 프로그램에 Azure AD의 권한 부여에 대 한 단일 전달자 토큰을 허용합니다.

Azure AD와 호환성을 위해 SCIM 끝점을 구현 하는 경우 다음 일반 지침을 따르세요.

* `id` 모든 리소스에 대 한 필수 속성이입니다. 리소스를 각 리소스를 확인 해야 반환 하는 모든 응답에이 속성을 제외 하 고 `ListResponse` 0 멤버를 사용 하 여 합니다.
* 쿼리/필터링 요청에 대 한 응답은 항상 한 `ListResponse`합니다.
* 그룹은 선택 사항 이지만 유일한 SCIM 구현 PATCH 요청을 지 원하는 경우 지원 합니다.
* 전체 리소스 패치 응답에 포함할 필요는 없습니다.
* Microsoft Azure AD는 다음 연산자만 사용합니다.  
     - `eq`
     - `and`
* 대/소문자 구분 일치를 사용 하는 특정 패치에 SCIM의 구조적 요소 필요가 `op` 에 정의 된 작업 값 https://tools.ietf.org/html/rfc7644#section-3.5.2합니다. Azure AD로 'op'의 값을 내보냅니다 `Add`하십시오 `Replace`, 및 `Remove`합니다.
* Microsoft Azure AD를 사용 하면 끝점 및 자격 증명이 유효한 지 확인 합니다. 임의 사용자 및 그룹을 가져올 요청 합니다. 일부로 수행 됩니다 **연결 테스트** 흐름에 [Azure portal](https://portal.azure.com)합니다. 
* 리소스에 쿼리할 수 있는 특성을 응용 프로그램에서 일치 하는 특성으로 설정 해야 합니다 [Azure portal](https://portal.azure.com)합니다. 자세한 내용은 참조 하세요. [사용자 지정 사용자 프로 비전 특성 매핑](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>사용자 프로비저닝 및 프로비전 해제
다음 그림에서는 응용 프로그램의 id 저장소에 사용자의 수명 주기를 관리 하려면 Azure Active Directory가 SCIM 서비스를는 메시지를 보여 줍니다.  

![][4]
*그림 4: 사용자 프로비전 및 시퀀스 프로비전 해제*

### <a name="group-provisioning-and-de-provisioning"></a>그룹 프로비전 및 프로비전 해제
그룹 프로 비전 및 프로 비전 해제는 선택적입니다. 구현 하 고 사용 하도록 설정 하는 경우 다음 그림과 메시지를 Azure AD 응용 프로그램의 id 저장소에 있는 그룹의 수명 주기를 관리 하려면 SCIM 서비스에 보냅니다.  두 가지 방법으로 사용자에 대 한 메시지에서 해당 메시지가 서로 다릅니다. 

* 그룹을 검색 하는 요청은 멤버 특성이 요청에 대 한 응답으로 제공 된 리소스 로부터 제외 되도록 지정 합니다.  
* 참조 특성에 특정 값이 있는지를 확인하는 요청은 멤버 특성에 대한 요청입니다.  

![][5]
*그림 5: 그룹 프로비전 및 시퀀스 프로비전 해제*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 프로토콜 요청 및 응답
이 섹션에서는 Azure AD SCIM 클라이언트 및 예제에서 내보내는 예제 SCIM 요청 예상된 응답을 제공 합니다. 최상의 결과이 형식으로 이러한 요청을 처리 하 고 예상된 응답을 내보낼 앱을 코드를 작성 해야 합니다.

>[!IMPORTANT]
>Azure AD 사용자 프로 비전 서비스 아래에 설명 된 작업을 내보내는 방법 및 시기를 이해 하려면 참조 [사용자 프로 비전 하는 동안 어떻게 되나요?](user-provisioning.md#what-happens-during-provisioning)합니다.

- [사용자 작업](#user-operations)
  - [사용자 만들기](#create-user)
    - [요청](#request)
    - [응답](#response)
  - [사용자 가져오기](#get-user)
    - [요청](#request-1)
    - [응답](#response-1)
  - [쿼리에서 사용자 가져오기](#get-user-by-query)
    - [요청](#request-2)
    - [응답](#response-2)
  - [쿼리-결과에서 사용자 가져오기](#get-user-by-query---zero-results)
    - [요청](#request-3)
    - [응답](#response-3)
  - [사용자 업데이트 [다중값된 속성]](#update-user-multi-valued-properties)
    - [요청](#request-4)
    - [응답](#response-4)
  - [사용자 업데이트 [단일 값 속성]](#update-user-single-valued-properties)
    - [요청](#request-5)
    - [응답](#response-5)
  - [사용자 삭제](#delete-user)
    - [요청](#request-6)
    - [응답](#response-6)
- [그룹 작업](#group-operations)
  - [그룹 만들기](#create-group)
    - [요청](#request-7)
    - [응답](#response-7)
  - [그룹 가져오기](#get-group)
    - [요청](#request-8)
    - [응답](#response-8)
  - [Displayname 그룹 가져오기](#get-group-by-displayname)
    - [요청](#request-9)
    - [응답](#response-9)
  - [업데이트 그룹 [비 멤버 특성]](#update-group-non-member-attributes)
    - [요청](#request-10)
    - [응답](#response-10)
  - [업데이트 그룹 [구성원을 추가 합니다.]](#update-group-add-members)
    - [요청](#request-11)
    - [응답](#response-11)
  - [그룹 [멤버 제거]를 업데이트 합니다.](#update-group-remove-members)
    - [요청](#request-12)
    - [응답](#response-12)
  - [그룹 삭제](#delete-group)
    - [요청](#request-13)
    - [응답](#response-13)

### <a name="user-operations"></a>사용자 작업

* 사용자를 쿼리할 수 있습니다 `userName` 또는 `email[type eq "work"]` 특성입니다.  

#### <a name="create-user"></a>사용자 만들기

###### <a name="request"></a>요청
*POST/사용자*
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

##### <a name="response"></a>response
*Http/1.1 201 생성 됨*
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

###### <a name="request"></a>요청
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response"></a>response
*HTTP/1.1 200 OK*
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
#### <a name="get-user-by-query"></a>쿼리에서 사용자 가져오기

##### <a name="request"></a>요청
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
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

#### <a name="get-user-by-query---zero-results"></a>쿼리-결과에서 사용자 가져오기

##### <a name="request"></a>요청
*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>사용자 업데이트 [다중값된 속성]

##### <a name="request"></a>요청
*PATCH /Users/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
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

##### <a name="request"></a>요청
*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
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

#### <a name="delete-user"></a>사용자 삭제

##### <a name="request"></a>요청
*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a>response
*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>그룹 작업

* 그룹은 항상 빈 멤버 목록을 사용 하 여 생성 됩니다.
* 그룹을 쿼리할 수는 `displayName` 특성입니다.
* 업데이트 그룹 PATCH 요청을 양보 해야는 *HTTP 204 No Content* 응답에서 합니다. 모든 멤버 목록 사용 하 여 본문을 반환 하지 않습니다 것이 좋습니다.
* 그룹의 모든 멤버 반환을 지원 하기 위해 필요는 없습니다.

#### <a name="create-group"></a>그룹 만들기

##### <a name="request"></a>요청
*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a>response
*Http/1.1 201 생성 됨*
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

##### <a name="request"></a>요청
*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
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

#### <a name="get-group-by-displayname"></a>Displayname 그룹 가져오기

##### <a name="request"></a>요청
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a>response
*HTTP/1.1 200 OK*
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
#### <a name="update-group-non-member-attributes"></a>업데이트 그룹 [비 멤버 특성]

##### <a name="request"></a>요청
*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a>response
*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>업데이트 그룹 [구성원을 추가 합니다.]

##### <a name="request"></a>요청
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a>response
*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>그룹 [멤버 제거]를 업데이트 합니다.

##### <a name="request"></a>요청
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a>response
*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>그룹 삭제

##### <a name="request"></a>요청
*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a>response
*HTTP/1.1 204 No Content*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Microsoft CLI 라이브러리를 사용 하는 SCIM 끝점 빌드
Azure Active Directory와 인터페이스 하는 SCIM 웹 서비스를 만들어 거의 모든 응용 프로그램 또는 id 저장소에 대 한 자동 사용자 프로 비전을 사용할 수 있습니다.

방법은 다음과 같습니다.

1. Azure AD Microsoft.SystemForCrossDomainIdentityManagement, 아래 설명 된 샘플 코드에 포함 된 명명 된 공용 언어 인프라 (CLI) 라이브러리를 제공 합니다. 시스템 통합 업체 및 개발자 만들기 및 배포 응용 프로그램 id 저장소에 Azure AD를 연결할 수 있는 SCIM 기반 웹 서비스 끝점에이 라이브러리를 사용할 수 있습니다.
2. 매핑은 웹 서비스에서 구현되어 스키마에 애플리케이션에서 필요한 사용자 스키마 및 프로토콜에 표준화된 사용자 스키마를 매핑합니다. 
3. 엔드포인트 URL은 애플리케이션 갤러리에서 사용자 지정 애플리케이션의 일부로 Azure AD에 등록됩니다.
4. 사용자 및 그룹은 Azure AD에서 이 애플리케이션에 할당됩니다. 할당 시 이러한 대상 응용 프로그램에 동기화 되도록 큐에 추가 하는 합니다. 큐를 처리하는 동기화 프로세스는 40분마다 실행됩니다.

### <a name="code-samples"></a>코드 샘플
이 프로세스는 쉽게 [코드 샘플](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) 제공 하는 SCIM 웹 서비스 끝점을 만들고 자동 프로 비전을 보여 줍니다. 샘플은 사용자 및 그룹을 나타내는 쉼표로 구분 된 값의 행을 사용 하 여 파일을 유지 관리 하는 공급자입니다.    

**필수 구성 요소**

* Visual Studio 2013 이상
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* ASP.NET framework 4.5를 SCIM 엔드포인트로 사용하도록 지원하는 Windows 컴퓨터입니다. 이 컴퓨터는 클라우드에서 액세스할 수 있어야 합니다.
* [Azure AD Premium의 평가판 또는 사용이 허가된 버전의 Azure 구독](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>시작하기
Azure AD에서 프로비전 요청을 수락할 수 있는 SCIM 엔드포인트를 구현하는 가장 쉬운 방법은 쉼표로 구분된 값(CSV) 파일에 프로비전된 사용자를 출력하는 코드 샘플을 빌드하고 배포하는 것입니다.

#### <a name="to-create-a-sample-scim-endpoint"></a>샘플 SCIM 엔드포인트를 만들려면

1. [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)에서 코드 샘플 패키지를 다운로드합니다.
1. 패키지의 압축을 풀고 C:\AzureAD-BYOA-Provisioning-Samples와 같은 위치에서 Windows 컴퓨터에 넣습니다.
1. Visual Studio에서 이 폴더의 FileProvisioning\Host\FileProvisioningService.csproj 프로젝트를 시작합니다.
1. 선택 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**에 대해 다음 명령을 실행 합니다 솔루션 파일에 대 한 참조를 해결 하려면 FileProvisioningService 프로젝트:

   ```powershell
    Update-Package -Reinstall
   ```

1. FileProvisioningService 프로젝트를 빌드합니다.
1. Windows에서 명령 프롬프트 애플리케이션을 관리자 권한으로 시작하고, **cd** 명령을 사용하여 디렉터리를 **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** 폴더로 변경합니다.
1. 다음 명령을 실행 합니다. 대체 `<ip-address>` Windows 컴퓨터의 IP 주소 또는 도메인 이름:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. Windows 아래에서 **Windows 설정** > **네트워크 및 인터넷 설정**을 선택 합니다 **Windows 방화벽**  >   **고급 설정**을 만들고는 **인바운드 규칙** 포트 9000에 인바운드 액세스를 허용 하는 합니다.
1. Windows 컴퓨터가 라우터 뒤에 있는 경우 라우터는 Windows 컴퓨터에서 인터넷에 노출 되 고 있는 포트 9000과 포트 9000 사이의 네트워크 액세스 변환을 실행 되도록 구성 해야 합니다. 이 구성은 클라우드에서이 끝점에 액세스 하려면 Azure AD에 대 한 필요 합니다.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Azure AD에서 샘플 SCIM 엔드포인트를 등록하려면

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com)합니다. 

1. 선택 **엔터프라이즈 응용 프로그램** 왼쪽된 창에서. 갤러리에서 추가 된 앱을 비롯 한 모든 구성 된 앱의 목록이 표시 됩니다.

1. 선택 **+ 새 응용 프로그램** > **모든** > **비 갤러리 응용 프로그램**합니다.

1. 응용 프로그램에 대 한 이름을 입력 하 고 선택 **추가** 앱 개체를 만듭니다. 만든 애플리케이션 개체는 SCIM 엔드포인트뿐 아니라 Single Sign-On을 프로비전하고 구현하려는 대상 앱을 나타내는 데 사용됩니다.

1. 앱 관리 화면에서 선택 **프로 비전** 왼쪽된 패널에서 합니다.

1. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.
    
   ![][2]
   *그림 6: Azure Portal에서 프로비전 구성*
    
1. **테넌트 URL** 필드에 인터넷에 노출된 URL 및 SCIM 엔드포인트의 포트를 입력합니다. 이 항목은 http://testmachine.contoso.com:9000 또는 http://\<ip-address>:9000/과 유사합니다. 여기서 \<ip-address>는 인터넷에 노출된 IP 주소입니다. 

1. SCIM 엔드포인트에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 
1. 선택 **연결 테스트** Azure Active Directory에서 SCIM 끝점에 연결을 시도 하도록 합니다. 시도가 실패 하면 오류 정보가 표시 됩니다.  

    >[!NOTE]
    >**테스트 연결**은 Azure AD 구성에서 선택된 일치하는 속성으로 임의 GUID를 사용하여 존재하지 않는 사용자의 SCIM 엔드포인트를 쿼리합니다. 예상되는 올바른 응답은 SCIM ListResponse 메시지가 비어 있는 HTTP 200 OK입니다.

1. 응용 프로그램 성공에 연결 하려고 선택한 경우 **저장할** 관리자 자격 증명을 저장 합니다.

1. **매핑** 섹션에는 선택 가능한 특성 매핑 집합이 두 개 있는데, 하나는 사용자 개체용이고 다른 하나는 그룹 개체용입니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

1. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. 선택 **"동기화만 할당 된 사용자 및 그룹** (권장)만 사용자 및 그룹 할당을 동기화 하는 **사용자 및 그룹** 탭 합니다.

1. 구성을 완료 되 면 설정 합니다 **프로 비전 상태** 하 **에서**합니다.

1. 선택 **저장할** 를 Azure AD 프로 비전 서비스를 시작 합니다. 

1. 동기화만 사용자 및 그룹 (권장)에 할당 하는 경우 선택 해야 합니다 **사용자 및 그룹** 탭 하 고 사용자 또는 동기화 할 그룹을 할당 합니다.

초기 동기화가 시작 되 면 선택할 수 있습니다 **감사 로그** 앱에서 프로 비전 서비스에 의해 수행 되는 모든 작업을 보여 주는 진행률을 모니터링 하려면 왼쪽된 패널에서. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](check-status-user-account-provisioning.md)를 참조하세요.

이 샘플을 확인하는 마지막 단계는 Windows 컴퓨터에서 \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug 폴더에 TargetFile.csv 파일을 여는 것입니다. 프로비전 프로세스가 실행되면 이 파일은 할당되고 프로비전된 모든 사용자 및 그룹의 세부 사항을 표시합니다.

### <a name="development-libraries"></a>개발 라이브러리
SCIM 사양을 준수하는 웹 서비스를 개발하려면 먼저 개발 프로세스를 가속화하기 위해 Microsoft에서 제공하는 다음 라이브러리를 숙지합니다. 

- CLI(공용 언어 인프라) 라이브러리는 C#과 같은 해당 인프라에 따라 언어와 함께 사용하기 위해 제공됩니다. Microsoft.SystemForCrossDomainIdentityManagement.Service, 이러한 라이브러리 중 하나는 인터페이스 Microsoft.SystemForCrossDomainIdentityManagement.IProvider를 다음 그림과에서 같이 선언 합니다. 라이브러리를 사용하는 개발자는 일반적으로 공급자로 참조될 수 있는 클래스를 사용하여 해당 인터페이스를 구현합니다. 라이브러리 개발자를 SCIM 사양을 준수 하는 웹 서비스를 배포할 수 있습니다. 웹 서비스 중 실행 CLI 어셈블리 또는 인터넷 정보 서비스 내에서 호스트할 수 있습니다. 요청은 공급자의 메서드에 대한 호출로 변환되며, 개발자에 의해 일부 ID 저장소에서 작동하도록 프로그래밍됩니다.
  
   ![][3]
  
- [기본 경로 처리기](https://expressjs.com/guide/routing.html)는 node.js 웹 서비스에 수행된(SCIM 사양에 정의된 대로) 호출을 나타내는 node.js 요청 개체를 구문 분석하는 데 사용할 수 있습니다.   

### <a name="building-a-custom-scim-endpoint"></a>사용자 지정 SCIM 엔드포인트 빌드
CLI 라이브러리를 사용 하는 개발자는 모든 실행 CLI 어셈블리 내에서 또는 인터넷 정보 서비스 내에서 해당 서비스를 호스트할 수 있습니다. 다음은 http://localhost:9000: 주소에 있는 실행 가능한 어셈블리 내에서 서비스를 호스트하기 위한 샘플 코드입니다. 

   ```csharp
    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }
   ```

이 서비스에는 루트 인증 기관의 이름이 다음 중 하나인 HTTP 주소 및 서버 인증 인증서가 있어야 합니다. 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

서버 인증 인증서는 네트워크 셸 유틸리티를 사용하여 다음과 같이 Windows 호스트의 포트에 바인딩될 수 있습니다. 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

여기서 appid 인수에 제공된 값은 임의의 GUID(Globally Unique Identifier)인 반면 certhash 인수에 제공된 값은 인증서의 지문입니다.  

인터넷 정보 서비스 내에서 서비스를 호스트 하는 개발자는 어셈블리의 기본 네임 스페이스에 있는 Startup 이라는 클래스를 사용 하 여 CLI 코드 라이브러리 어셈블리를 빌드합니다.  이러한 클래스의 샘플은 다음과 같습니다. 

   ```csharp
    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }
   ```

### <a name="handling-endpoint-authentication"></a>엔드포인트 인증 처리
Azure Active Directory에서 요청은 OAuth 2.0 전달자 토큰을 포함합니다.   요청을 받는 모든 서비스는 발급자 필요한 Azure Active Directory 테 넌 트를 Azure Active Directory Graph 웹 서비스에 대 한 액세스를 Azure Active Directory로 인증 해야 합니다.  토큰에서 발급자는 "iss"와 같은 iss 클레임으로 식별 됩니다. "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"입니다.  이 예제에서는 클레임 값의 기본 주소 https://sts.windows.net상대 주소 세그먼트인 cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 Azure Active Directory를 발급자로 식별에 대 한 Azure Active Directory 테 넌 트의 고유 식별자 토큰을 발급 합니다.  토큰이 Azure Active Directory Graph 웹 서비스에 액세스하기 위해 발급되었다면 해당 서비스의 식별자인 00000002-0000-0000-c000-000000000000는 토큰의 aud 클레임의 값에 있어야 합니다.  동일한 단일 테 넌 트에 등록 된 응용 프로그램의 각 나타날 `iss` SCIM 요청과 클레임입니다.

SCIM 서비스 구축을 위해 Microsoft에서 제공 하는 CLI 라이브러리를 사용 하는 개발자는 Microsoft.Owin.Security.ActiveDirectory 패키지로 다음 단계를 수행 하 여 Azure Active Directory에서 요청을 인증할 수 있습니다. 

1. 공급자에서 서비스가 시작할 때 마다 호출되는 메서드를 반환함으로써 Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior 속성을 구현합니다. 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. 해당 메서드에 지정 된 테 넌 트를 Azure AD Graph 웹 서비스에 대 한 액세스에 대 한 Azure Active Directory에서 발급 한 토큰을 갖도록 인증 서비스의 끝점 중 하나가 요청에 다음 코드를 추가 합니다. 

   ```csharp
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>처리 프로 비전 및 사용자 프로 비전 해제

1. Azure Active Directory는 externalId 특성 값이 Azure AD의 사용자 mailNickname 특성 값과 일치하는 사용자를 서비스에 쿼리합니다. 쿼리는이 예제에서는 여기서 jyoung은 Azure Active Directory에서 사용자의 mailnickname 샘플 같은 하이퍼텍스트 전송 프로토콜 (HTTP) 요청으로 표현 됩니다.

    >[!NOTE]
    > 예제 일 뿐입니다. 모든 사용자가 mailNickname 특성을 갖습니다 하 고 사용자가 값을 디렉터리에 고유 하지 않을 합니다. (경우에이 externalId) 일치 하는 데 사용 된 특성에서 구성할 수는 또한 합니다 [Azure AD 특성 매핑](customize-application-attributes.md)합니다.

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공 하는 CLI 라이브러리를 사용 하 여 빌드된 경우에 요청이 서비스 공급자의 쿼리 메서드 호출으로 변환 됩니다.  해당 메서드의 서명은 다음과 같습니다. 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 인터페이스의 정의는 다음과 같습니다. 
   ```csharp
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }
   ```

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 쿼리 메서드 호출로 번역됩니다.  해당 메서드의 서명은 다음과 같습니다. 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters 인터페이스의 정의는 다음과 같습니다. 

   ```csharp
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   externalId 특성의 값이 지정된 사용자에 대한 다음 쿼리 샘플에서 쿼리 메서드에 전달된 인수의 값은 다음과 같습니다. 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. 쿼리할 때 응답으로 사용자의 mailNickname 특성 값과 일치 하는 externalId 특성 값을 사용 하 여 사용자에 대 한 웹 서비스에 사용자를 반환 하지 않으면, 그런 다음 Azure Active Directory 요청 서비스는 해당 하는 사용자를 프로 비전 Azure Active directory 의미 합니다.  다음은 그러한 요청의 예제입니다. 

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
   SCIM 서비스 구현에 대해 Microsoft에서 제공 하는 CLI 라이브러리는 서비스 공급자의 만들기 메서드에 호출으로 해당 요청을 변환 합니다.  만들기 메서드에는 다음 서명이 있습니다. 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   사용자를 프로비전하는 요청에서 리소스 인수의 값은 Microsoft.SystemForCrossDomainIdentityManagement의 인스턴스입니다. Microsoft.SystemForCrossDomainIdentityManagement.Schemas 라이브러리에 정의된 Core2EnterpriseUser 클래스입니다.  사용자를 프로비전하는 요청이 성공하는 경우 메서드의 구현은 Microsoft.SystemForCrossDomainIdentityManagement의 인스턴스를 반환할 것으로 예상됩니다. 새로 프로비전된 사용자의 고유 식별자에 설정된 식별자 속성의 값을 가진 Core2EnterpriseUser 클래스입니다.  

3. SCIM에 의해 제어되는 ID 저장소에 있는 것으로 알려진 사용자를 업데이트하기 위해 Azure Active Directory는 다음과 같은 요청으로 서비스에서 해당 사용자의 현재 상태를 요청합니다. 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   SCIM 서비스 구현에 대해 Microsoft에서 제공 하는 CLI 라이브러리를 사용 하 여 빌드된 서비스에서 요청은 서비스 공급자의 검색 메서드에 호출으로 변환 됩니다.  해당 검색 메서드의 서명은 다음과 같습니다. 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   사용자의 현재 상태를 검색하는 요청 예제에서 매개 변수 인수 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
   * 식별자: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. 참조 특성을 업데이트할 경우 다음 Azure Active Directory 서비스를 쿼리하여 일치 여부를 확인에 의해 제어 되는 id 저장소에서 참조 특성의 현재 값을 서비스 이미 Azure Active의 해당 특성 값 디렉터리입니다. 사용자의 경우 현재 값이 이 방식으로 쿼리된 유일한 특성은 관리자 특성입니다. 특정 사용자 개체의 관리자 특성 값에 현재 특정 값이 있는지 여부를 결정하는 요청의 예는 다음과 같습니다. 

   서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공 하는 CLI 라이브러리를 사용 하 여 빌드된 경우에 요청이 서비스 공급자의 쿼리 메서드 호출으로 변환 됩니다. 매개 변수 인수의 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   여기에서 x 인덱스의 값에는 0 일 수 있습니다 인덱스 y의 값에 1을 포함할 수 있습니다 또는 x의 값 1을 포함할 수 있습니다 및 y의 값은 필터 쿼리 매개 변수의 식의 순서에 따라 0, 될 수 있습니다.   

5. 다음은 Azure Active Directory에서 SCIM 서비스로 사용자를 업데이트하는 요청의 예입니다. 
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
   SCIM 서비스 구현에 대 한 Microsoft CLI 라이브러리 요청이 서비스 공급자의 Update 메서드 호출으로 변환 합니다. Update 메서드의 서명은 다음과 같습니다. 
   ```csharp
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}
   ```

   서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 쿼리 메서드 호출로 번역됩니다. 매개 변수 인수의 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  여기에서 x 인덱스의 값에는 0 일 수 있습니다 인덱스 y의 값에 1을 포함할 수 있습니다 또는 x의 값 1을 포함할 수 있습니다 및 y의 값은 필터 쿼리 매개 변수의 식의 순서에 따라 0, 될 수 있습니다.   

1. 다음은 Azure Active Directory에서 SCIM 서비스로 사용자를 업데이트하는 요청의 예입니다. 

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

   SCIM 서비스 구현에 대한 Microsoft 공용 언어 인프라 라이브러리는 요청이 서비스 공급자의 업데이트 메서드에 호출을 요청하도록 번역됩니다. Update 메서드의 서명은 다음과 같습니다. 

   ```csharp
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
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

1. SCIM 서비스에 의해 제어되는 ID 저장소에서 사용자의 프로비전을 해제하기 위해 Azure AD에서 다음과 같은 요청을 보냅니다. 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공하는 공용 언어 인프라 라이브러리를 사용하여 작성되면 요청이 서비스 공급자의 삭제 메서드 호출로 번역됩니다.   해당 메서드에는 다음 서명이 있습니다. 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   사용자의 프로비전을 해제하는 요청의 예에서 resourceIdentifier 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 

1. SCIM 서비스에 의해 제어되는 ID 저장소에서 사용자의 프로비전을 해제하기 위해 Azure AD에서 다음과 같은 요청을 보냅니다. 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   서비스가 SCIM 서비스 구현에 대해 Microsoft에서 제공 하는 CLI 라이브러리를 사용 하 여 빌드된 경우에 요청이 서비스 공급자의 삭제 메서드 호출으로 변환 됩니다.   해당 메서드에는 다음 서명이 있습니다. 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   사용자의 프로비전을 해제하는 요청의 예에서 resourceIdentifier 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>사용자 및 그룹 스키마 참조
Azure Active Directory는 두 형식의 리소스를 SCIM 웹 서비스에 프로비전할 수 있습니다.  이러한 형식의 리소스는 사용자 및 그룹입니다.  

사용자 리소스는 스키마 식별자로 식별 됩니다 `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`,이 프로토콜 사양에 포함 되어 있는: https://tools.ietf.org/html/rfc7643합니다.  사용자 리소스의 특성에 Azure Active Directory에서 사용자의 특성의 기본 매핑 표 1에 제공 됩니다.  

그룹 리소스는 스키마 식별자 `urn:ietf:params:scim:schemas:core:2.0:Group`으로 식별됩니다. 표 2 리소스 그룹의 특성에 Azure Active Directory에서 그룹의 특성 간의 기본 매핑을 보여 줍니다.  

### <a name="table-1-default-user-attribute-mapping"></a>표 1: 기본 사용자 특성 매핑

| Azure Active Directory 사용자 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |활성 |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| 메일 |emails[type eq "work"].value |
| mailNickname |externalId |
| 관리자 |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |ID |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>표 2: 기본 그룹 특성 매핑

| Azure Active Directory 그룹 | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| 메일 |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |ID |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>IP 허용 SCIM 요청을 Azure AD 프로 비전 서비스에서 사용 되는 주소
특정 앱에는 해당 앱에 인바운드 트래픽을 허용합니다. 예상 대로 작동 하려면 Azure AD 프로 비전 서비스에 대 한 순서를 사용 하는 IP 주소를 허용 합니다. 각 서비스 태그/지역에 대 한 IP 주소의 목록을 JSON 파일을 참조 하세요 [Azure IP 범위 및 서비스 태그와 공용 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)합니다. 다운로드 하 고 필요에 따라 이러한 Ip 방화벽으로 프로그래밍할 수 있습니다. Azure AD 프로 비전에 대 한 예약 된 IP 범위 "AzureActiveDirectoryDomainServices."에서 찾을 수 있습니다.
 

## <a name="related-articles"></a>관련 문서
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](user-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](customize-application-attributes.md)
* [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
* [계정 프로비전 알림](user-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
