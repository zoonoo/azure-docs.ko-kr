---
title: Azure Active Directory ID 보호를 위한 Microsoft Graph | Microsoft Docs
description: Azure Active Directory에서 위험 이벤트 및 관련된 정보의 목록에 대한 Microsoft Graph를 쿼리하는 방법을 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 위험 이벤트, 취약점, 보안 정책, Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36a2fc9d1ab364d44bd5dd6e7930e5d8ae3fb78d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204749"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory ID 보호 및 Microsoft Graph 시작

Microsoft Graph는 Microsoft의 통합된 API 엔드포인트이며 [Azure Active Directory ID 보호](../active-directory-identityprotection.md) API의 시작점입니다. 위험한 사용자 및 로그인에 대한 정보를 노출하는 세 가지 API가 있습니다. 첫 번째 API인 **identityRiskEvents**를 사용하면 [위험 이벤트](../reports-monitoring/concept-risk-events.md) 및 관련 정보의 목록에 대한 Microsoft Graph를 쿼리할 수 있습니다. 두 번째 API인 **riskyUsers**를 사용하면 위험으로 검색된 사용자 ID 보호에 대한 정보에 관하여 Microsoft Graph를 쿼리할 수 있습니다. 세 번째 API인 **signIn**을 사용하면 위험 상태, 세부 정보 및 수준과 관련된 특정 속성을 사용하여 Azure AD 로그인의 정보에 대한 Microsoft Graph를 쿼리할 수 있습니다. 이 문서에서는 [Microsoft Graph에 연결](#Connect-to-Microsoft-Graph) 및 [이러한 API 쿼리](#Query-the-APIs)를 사용하여 시작합니다. 자세한 소개, 전체 설명서 및 Graph Explorer에 대한 액세스는 [Microsoft Graph 사이트](https://graph.microsoft.io/) 또는 이러한 API에 대한 특정 참조 설명서를 참조하세요.

* [identityRiskEvents API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [riskyUsers API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)


## <a name="connect-to-microsoft-graph"></a>Microsoft Graph에 연결

Microsoft Graph를 통해 ID 보호 데이터에 액세스하려면 네 가지 단계가 있습니다.

1. 도메인 이름을 검색합니다.
2. 새 앱 등록을 만듭니다. 
3. 이 암호와 다른 몇 가지 정보를 사용하여 Microsoft Graph에 인증하면 인증 토큰을 받게됩니다. 
4. 이 토큰을 사용하여 API 엔드포인트에 요청을 수행하고 ID 보호 데이터를 다시 가져옵니다.

시작하기 전에 다음 항목이 필요합니다.

* Azure AD에서 애플리케이션을 만드는 관리자 권한
* 테넌트의 도메인 이름(예: contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>도메인 이름 검색 

1. Azure Portal에 관리자로 [로그인](https://portal.azure.com)합니다. 

2. 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![애플리케이션 만들기](./media/graph-get-started/41.png)


3. **관리** 섹션에서 **속성**을 클릭합니다.

    ![애플리케이션 만들기](./media/graph-get-started/42.png)

4. 도메인 이름을 복사합니다.


## <a name="create-a-new-app-registration"></a>새 앱 등록 만들기

1. **Active Directory** 페이지의 **관리** 섹션에서 **앱 등록**을 클릭합니다.

    ![애플리케이션 만들기](./media/graph-get-started/42.png)


2. 위에 있는 메뉴에서 **새 애플리케이션 등록**을 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/43.png)

3. **만들기** 페이지에서 다음 단계를 수행합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/44.png)

    a. **이름** 텍스트 상자에 애플리케이션 이름을 입력합니다(예: AADIP Risk Event API 애플리케이션).
   
    b. **유형**으로 **웹 애플리케이션 및/또는 Web API**를 선택합니다.
   
    다. **로그온 URL** 텍스트 상자에 `http://localhost`을(를) 입력합니다.

    d. **만들기**를 클릭합니다.

4. **설정** 페이지를 열려면 애플리케이션 목록에서 새로 만든 앱 등록을 클릭합니다. 

5. **애플리케이션 ID**를 복사합니다.


## <a name="grant-your-application-permission-to-use-the-api"></a>API를 사용하도록 애플리케이션에 권한 부여

1. **설정** 페이지에서 **필요한 권한**을 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/15.png)

2. **필요한 권한** 페이지의 위쪽 도구 모음에서 **추가**를 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/16.png)
   
3. **API 액세스 추가** 페이지에서 **API 선택**을 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/17.png)

4. **API 선택** 페이지에서 **Microsoft Graph**를 선택한 다음, **선택**을 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/18.png)

5. **API 액세스 추가** 페이지에서 **권한 선택**을 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/19.png)

6. **액세스 허용** 페이지에서 **모든 ID 위험 정보 읽기**, **선택**을 차례로 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/20.png)

7. **API 액세스 추가** 페이지에서 **완료**를 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/21.png)

8. **필요한 권한** 페이지에서 **사용 권한 부여**를 클릭한 다음 **예**를 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>선택키 가져오기

1. **설정** 페이지에서 **키**를 클릭합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/23.png)

2. **키** 페이지에서 다음 단계를 수행합니다.
   
    ![애플리케이션 만들기](./media/graph-get-started/24.png)

    a. **키 설명** 텍스트 상자에서 설명을 입력합니다(예: *AADIP 위험 이벤트*).
    
    b. **기간**으로 **1년**을 선택합니다.

    다. **저장**을 클릭합니다.
   
    d. 키 값을 복사한 다음 안전한 위치에 붙여넣습니다.   
   
   > [!NOTE]
   > 이 키를 분실하면 이 섹션으로 돌아와서 새 키를 만들어야 합니다. 이 키의 비밀을 유지합니다. 키를 가진 사람은 누구나 데이터에 액세스할 수 있습니다.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Microsoft Graph에 인증하고 ID 위험 이벤트 API를 쿼리합니다.
이 시점에서 다음 항목이 만들어 집니다.

- 테넌트 도메인의 이름

- 클라이언트 ID 

- 키 


인증하려면 본문에서 다음 매개 변수를 ㅏ용하여 `https://login.microsoft.com` 에 게시 요청을 전송합니다.

- grant_type: “**client_credentials**”

-  resource: “**https://graph.microsoft.com**”

- client_id: \<클라이언트 ID\>

- client_secret: \<키\>


성공하면 인증 토큰을 반환합니다.  
API를 호출하려면 다음 매개 변수를 사용하여 헤더를 만듭니다.

    `Authorization`=”<token_type> <access_token>"


인증할 경우 반환된 토큰에서 토큰 유형 및 액세스 토큰을 찾을 수 있습니다.

다음 API URL에 대한 요청으로 이 헤더를 보냅니다. `https://graph.microsoft.com/beta/identityRiskEvents`

성공한 경우 응답은 ID 위험 이벤트 및 OData JSON 형식으로 연결된 데이터의 컬렉션입니다. 이를 구문 분석하고 적절하게 처리할 수 있습니다.

다음은 PowerShell을 사용하여 API를 인증하고 호출하는 데 대한 샘플 코드입니다.  
클라이언트 ID, 비밀 키 및 테넌트 도메인만 추가하면 됩니다.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 

## <a name="query-the-apis"></a>API 쿼리

이러한 세 가지 API는 조직의 위험한 사용자 및 로그인에 대한 정보를 가져오는 다양한 기회를 제공합니다. 다음은 이러한 API 및 관련된 샘플 요청에 대한 몇 가지 일반적인 사용 사례입니다. 위의 샘플 코드 또는 [Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 이러한 쿼리를 실행할 수 있습니다.

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>높은 위험 및 보통 위험 이벤트 가져오기(identityRiskEvents API)

보통 및 높은 위험 이벤트는 ID 보호 로그인 또는 사용자 위험 정책을 트리거하는 기능이 있을 수도 있는 이벤트를 나타냅니다. 로그인을 시도하는 사용자가 합법적인 ID 소유자가 아닐 보통 또는 높은 가능성이 있으므로 이러한 이벤트 수정은 우선 순위를 갖습니다. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>위험한 로그인 정책에 의해 트리거되는 MFA 챌린지를 성공적으로 통과한 모든 사용자 가져오기(riskyUsers API)

조직에 대한 ID 보호 위험 기반 정책의 영향을 이해하려면 위험한 로그인 정책에 의해 트리거되는 MFA 챌린지를 성공적으로 통과한 모든 사용자를 쿼리할 수 있습니다. 이 정보는 위험에서 잘못 감지했을 수 있는 사용자 ID 보호 및 AI에서 위험하다고 판단하는 작업을 수행할 수 있는 합법적인 사용자를 이해하는 데 도움이 될 수 있습니다.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>특정 사용자에 대한 모든 위험한 로그인 가져오기(signIn API)

사용자가 손상되었다고 생각하는 경우 모든 해당 위험한 로그인을 검색하여 해당 위험 상태를 더 잘 이해할 수 있습니다. 
```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```




# <a name="next-steps"></a>다음 단계

축하합니다! Microsoft Graph에 대한 호출을 처음으로 만들었습니다.  
이제 ID 위험 이벤트를 쿼리하고 적절하게 데이터를 사용할 수 있습니다.


Microsoft Graph 및 Graph API를 사용하여 애플리케이션을 구축하는 방법에 대한 자세한 내용은 [설명서](https://docs.microsoft.com/graph/overview) 및 [Microsoft Graph 사이트](https://developer.microsoft.com/graph)에서 확인합니다. 


관련 정보는 다음을 참조하세요.

-  [Azure Active Directory ID 보호](../active-directory-identityprotection.md)

-  [Azure Active Directory ID 보호에서 검색한 위험 이벤트의 유형](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Microsoft Graph 개요](https://developer.microsoft.com/graph/docs)

- [Azure AD ID 보호 서비스 루트](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
