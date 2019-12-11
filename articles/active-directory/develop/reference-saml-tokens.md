---
title: Azure AD 토큰 & 클레임 유형 | Microsoft Docs
description: Azure Active Directory (AAD)에서 발급 한 SAML 2.0 및 JWT (JSON 웹 토큰) 토큰의 클레임을 이해 하 고 평가 하기 위한 가이드입니다.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2018
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4acac79d79b584dac93d63f6d478627f7e953f81
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965759"
---
# <a name="azure-ad-saml-token-reference"></a>Azure AD SAML 토큰 참조

Azure AD (Azure Active Directory)는 각 인증 흐름의 처리 과정에서 여러 유형의 보안 토큰을 내보냅니다. 이 문서에서는 각 토큰 유형의 형식, 보안 특성 및 내용에 대해 설명 합니다.

## <a name="claims-in-saml-tokens"></a>SAML 토큰의 클레임

> [!div class="mx-codeBreakAll"]
> | Név | 동일한 JWT 클레임 | Leírás | Példa |
> | --- | --- | --- | ------------|
> |Közönség | `aud` |토큰의 의도 된 받는 사람입니다. 토큰을 수신 하는 응용 프로그램은 대상 값이 올바른지 확인 하 고 다른 사용자에 게 적합 한 토큰을 거부 해야 합니다. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Hitelesítési időpont | |인증이 발생 한 날짜 및 시간을 기록 합니다. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | 
> |Hitelesítési módszer | `amr` |토큰의 주체가 인증 된 방법을 식별 합니다. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |Utónév | `given_name` |Azure AD 사용자 개체에 설정 된 대로 사용자의 첫 번째 또는 "지정 된" 이름을 제공 합니다. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Csoportok | `groups` |주체의 그룹 멤버 자격을 나타내는 개체 Id를 제공 합니다. 이러한 값은 고유 하며 (개체 ID 참조) 리소스에 액세스 하는 권한 부여를 적용 하는 등의 액세스를 관리 하는 데 안전 하 게 사용할 수 있습니다. 그룹 클레임에 포함 된 그룹은 응용 프로그램 매니페스트의 "groupMembershipClaims" 속성을 통해 응용 프로그램 별로 구성 됩니다. 값이 null 이면 모든 그룹이 제외 되 고, 값이 "SecurityGroup" 이면 Active Directory 보안 그룹 멤버 자격만 포함 되 고, 값이 "All" 이면 보안 그룹과 Office 365 메일 그룹이 모두 포함 됩니다. <br><br> **참고**: <br> 사용자가 있는 그룹 수가 한도 (SAML의 경우 150, JWT의 경우 200)를 초과 하면 초과분 클레임이 사용자의 그룹 목록을 포함 하는 그래프 끝점을 가리키는 클레임 원본에 추가 됩니다. 진행. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Groups 초과분 표시기 | `groups:src1` | 길이가 제한 되지 않은 토큰 요청 (위의 `hasgroups` 참조) 이지만 토큰에 대해 너무 크면 사용자의 전체 그룹 목록에 대 한 링크가 포함 됩니다. SAML의 경우 `groups` 클레임 대신 새 클레임으로 추가 됩니다. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Identitásszolgáltató | `idp` |A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. 사용자 계정이 발급자와 다른 테 넌 트에 있지 않으면이 값은 발급자 클레임의 값과 동일 합니다. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |IssuedAt | `iat` |토큰이 발급 된 시간을 저장 합니다. 토큰 새로 고침을 측정 하는 데 종종 사용 됩니다. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Kiállító | `iss` |토큰을 생성 하 고 반환 하는 STS (보안 토큰 서비스)를 식별 합니다. Azure AD가 반환 하는 토큰에서 발급자는 sts.windows.net입니다. 발급자 클레임 값의 GUID는 Azure AD 디렉터리의 테 넌 트 ID입니다. 테 넌 트 ID는 디렉터리의 변경 불가능 하 고 신뢰할 수 있는 식별자입니다. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Vezetéknév | `family_name` |Azure AD 사용자 개체에 정의 된 사용자의 성, 성 또는 패밀리 이름을 제공 합니다. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Név | `unique_name` |A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. 이 값은 테 넌 트 내에서 고유 하지 않을 수 있으며 표시 목적 으로만 사용 하도록 설계 되었습니다. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |Objektumazonosító | `oid` |Azure AD의 개체에 대 한 고유 식별자를 포함 합니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 개체 ID를 사용 하 여 Azure AD에 대 한 쿼리에서 개체를 식별 합니다. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Szerepkörök | `roles` |주체가 그룹 멤버 자격을 통해 간접적으로 부여 된 모든 응용 프로그램 역할을 나타내며 역할 기반 액세스 제어를 적용 하는 데 사용할 수 있습니다. 응용 프로그램 역할은 응용 프로그램 매니페스트의 `appRoles` 속성을 통해 응용 프로그램 별로 정의 됩니다. 각 응용 프로그램 역할의 `value` 속성은 역할 클레임에 표시 되는 값입니다. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Tárgy | `sub` |응용 프로그램의 사용자와 같이 토큰이 정보를 어설션하는 보안 주체를 식별 합니다. 이 값은 변경할 수 없으며 다시 할당 하거나 다시 사용할 수 없으므로 권한 부여 검사를 안전 하 게 수행 하는 데 사용할 수 있습니다. 주체는 항상 Azure AD에서 발급 하는 토큰에 존재 하기 때문에 범용 권한 부여 시스템에서이 값을 사용 하는 것이 좋습니다. <br> `SubjectConfirmation` 클레임이 아닙니다. 토큰의 주체를 확인 하는 방법을 설명 합니다. `Bearer`는 주체가 토큰의 소유에 의해 확인 됨을 나타냅니다. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |Bérlőazonosító | `tid` |토큰을 발급 한 디렉터리 테 넌 트를 식별 하는 변경할 수 없는 다시 사용할 수 없는 식별자입니다. 이 값을 사용 하 여 다중 테 넌 트 응용 프로그램에서 테 넌 트 별 디렉터리 리소스에 액세스할 수 있습니다. 예를 들어이 값을 사용 하 여 Graph API에 대 한 호출에서 테 넌 트를 식별할 수 있습니다. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Jogkivonat élettartama | `nbf`, `exp` |A jogkivonat érvényességi idejét határozza meg. 토큰의 유효성을 검사 하는 서비스는 현재 날짜가 토큰 수명 내에 있는지 확인 해야 합니다. 그렇지 않으면 토큰을 거부 해야 합니다. 서비스는 Azure AD와 서비스 간의 시계 시간 차이 ("시간차")를 고려 하 여 토큰 수명 범위를 벗어나 최대 5 분 정도 걸릴 수 있습니다. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>샘플 SAML 토큰

이는 일반적인 SAML 토큰의 샘플입니다.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

## <a name="related-content"></a>관련 내용

* Azure AD Graph API를 통해 토큰 수명 정책을 관리 하는 방법에 대해 자세히 알아보려면 Azure AD Graph [정책 작업](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) 및 [정책 엔터티](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)를 참조 하세요.
* 샘플을 비롯 하 여 PowerShell cmdlet을 통해 정책을 관리 하는 방법에 대 한 자세한 내용 및 예제는 [AZURE AD의 구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조 하세요. 
* 응용 프로그램에 대 한 토큰에 [사용자 지정 및 선택적 클레임](active-directory-optional-claims.md) 을 추가 합니다.
* [SAML을 사용 하 여 sso (Single Sign On)](single-sign-on-saml-protocol.md)를 사용 합니다.
* [Azure Single Sign OUT SAML 프로토콜](single-sign-out-saml-protocol.md) 사용
