---
title: Azure Stack API를 사용 하 여 | Microsoft Docs
description: Azure Stack API 요청을 하려면 Azure에서 인증을 검색 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 3b89564bf17a9884640b51faa1c3966dce93f89a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346793"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Azure Stack API를 사용 합니다.

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 클라우드에 VM을 추가 하는 등 작업을 자동화 하는 응용 프로그램 프로그래밍 인터페이스 (API)를 사용할 수 있습니다.

API를 Microsoft Azure 로그인 끝점에 인증 하 여 클라이언트에 필요 합니다. 끝점의 Azure Stack API로 전송 된 모든 요청 헤더에 사용할 토큰을 반환 합니다. Microsoft Azure는 Oauth 2.0을 사용 합니다.

이 문서에서는 사용 하는 예제를 제공 합니다 **cURL** Azure Stack 요청을 만드는 유틸리티입니다. CURL을 응용 프로그램에는 데이터 전송에 대 한 라이브러리를 사용 하 여 명령줄 도구입니다. 이 예제에서는 Azure Stack API에 액세스 하는 토큰을 검색 하는 과정을 안내 합니다. 대부분의 프로그래밍 언어는 이러한 토큰 새로 고침 토큰으로 실행 되는 강력한 관리 및 처리 작업을 포함 하는 Oauth 2.0 라이브러리를 제공 합니다.

와 같은 제네릭 REST 클라이언트를 사용 하 여 Azure Stack REST API를 사용 하는 전체 프로세스를 검토 **cURL**를 요청 및 응답 페이로드를 수신 하도록 기대할 수 있는 보여 줍니다. 기본을 이해 하는 데 도움이 됩니다.

이 문서에서는 대화형 로그인과 같은 토큰을 검색 하거나 전용된 앱 Id 만들기에 대 한 사용 가능한 모든 옵션을 탐색 하지 않습니다. 이러한 항목에 대 한 정보를 참조 하세요 [Azure REST API 참조](https://docs.microsoft.com/rest/api/)합니다.

## <a name="get-a-token-from-azure"></a>Azure에서 토큰을 가져옵니다.

사용 하 여 지정 된 content-type x-www-형식-urlencoded 액세스 토큰을 가져오려면 요청 본문을 만듭니다. Azure REST 인증 및 로그인 끝점에 요청을 게시 합니다.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**테 넌 트 ID** 입니다.

 - 테 넌 트 도메인에 같은 `fabrikam.onmicrosoft.com`
 - 와 같은 테 넌 트 ID `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - 테 넌 트 독립적 키에 대 한 기본 값: `common`

### <a name="post-body"></a>Post 본문

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

각 값:

 - **grant_type**  
    사용 하는 인증 체계의 형식입니다. 이 예에서 값은 `password`

 - **resource**  
    리소스 토큰에 액세스합니다. Azure Stack 관리 메타 데이터 끝점을 쿼리하여 리소스를 찾을 수 있습니다. 확인 합니다 **대상** 섹션

 - **Azure Stack 관리 끝점**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > 테 넌 트 API에 액세스 하려고 하는 관리자 인 경우 예를 들어 테 넌 트 끝점을 사용 하 여 있어야 있습니다. `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  예를 들어 끝점으로 Azure Stack 개발 키트:

    ```bash
    curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
    ```

  응답:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>예

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  이 값은 하드 코드 된 기본값입니다.

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  대체 옵션은 특정 시나리오에 사용할 수 있습니다.

  
  | 응용 프로그램 | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **사용자 이름**

  예를 들어, Azure Stack AAD 계정:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **암호**

  Azure Stack AAD 관리자 암호입니다.

### <a name="example"></a>예

요청:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

응답:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API 쿼리

액세스 토큰을 받은 후 각 API 요청 헤더로 추가 해야 합니다. 이렇게 하려면 헤더를 만들 필요가 **권한 부여** 값을 사용 하 여: `Bearer <access token>`합니다. 예: 

요청:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

응답:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL 구조 및 쿼리 구문

일반 요청 URI의 구성: {0} URI 체계}:// {URI-host} / {리소스 경로}? {query-string}

- **URI 체계**:  
URI는 요청을 전송 하는 데 사용 되는 프로토콜을 나타냅니다. 예를 들면 `http` 또는 `https`과 같습니다.
- **URI 호스트**:  
호스트 된 도메인 이름 또는 REST 서비스 엔드포인트가 호스팅되는, 같은 서버의 IP 주소를 지정 `graph.microsoft.com` 또는 `adminmanagement.local.azurestack.external`합니다.
- **리소스 경로**:  
경로는 리소스 또는 서비스가 해당 리소스 선택을 결정 하는 데 사용 되는 여러 세그먼트를 포함할 수 있는 리소스 컬렉션을 지정 합니다. 예를 들어: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` 목록을 쿼리 하는 응용 프로그램 컬렉션 내에서 특정 응용 프로그램의 소유자를 사용할 수 있습니다.
- **쿼리 문자열**:  
문자열은 API 버전 또는 리소스 선택 조건과 같은 추가적인 단순 매개 변수를 제공합니다.

## <a name="azure-stack-request-uri-construct"></a>Azure Stack 요청 URI 구문

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI 구문

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>쿼리 URI 예제

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>다음 단계

Azure RESTful 끝점을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure REST API 참조](https://docs.microsoft.com/rest/api/)합니다.
