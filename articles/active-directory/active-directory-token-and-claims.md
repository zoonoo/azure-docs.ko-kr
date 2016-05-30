 <properties
   pageTitle="지원되는 토큰 및 클레임 유형 | Microsoft Azure"
   description="AAD(Azure Active Directory)에서 발급하는 SAML 2.0 및 JWT(JSON 웹 토큰)를 이해하고 평가하기 위한 가이드입니다."
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/16/2016"
   ms.author="mbaldwin"/>

# 지원되는 토큰 및 클레임 유형

이 항목은 Azure AD(Azure Active Directory)에서 발급하는 SAML 2.0 및 JWT(JSON 웹 토큰) 토큰의 클레임을 이해하고 평가할 수 있도록 설계되었습니다.

항목의 첫 부분에서는 각 토큰 클레임에 대해 설명하고 SAML 토큰 및 JWT 토큰의 클레임 예를 적절하게 보여줍니다. 미리 보기 상태의 클레임은 별도로 나열됩니다. 마지막 부분에는 클레임의 전체적인 맥락을 살펴볼 수 있도록 샘플 토큰이 제공됩니다.

Azure는 시간이 지남에 따라 새로운 시나리오를 지원하도록 토큰에 클레임을 추가하고 있습니다. 일반적으로 이러한 클레임은 미리 보기 상태로 도입되어 테스트 기간을 거친 후 완전하게 지원되도록 변환됩니다. 클레임 변경에 대비하려면 새 클레임이 응용 프로그램을 중단하지 않도록 Azure AD의 토큰을 허용하는 응용 프로그램에서 생소한 토큰 클레임을 무시해야 합니다. 미리 보기 상태의 클레임을 사용하는 응용 프로그램에서 이러한 클레임을 사용하지 말아야 하며 클레임이 토큰에 표시되지 않더라도 예외를 발생하면 안 됩니다. Azure AD에서 발급하는 SAML 또는 JWT 토큰에 제공되지 않는 클레임이 응용 프로그램에 필요한 경우 새 클레임 유형을 제안하고 설명해 주는 이 페이지 하단의 커뮤니티 추가 사항 섹션을 참조하세요.

## 토큰 클레임 참조

이 섹션에서는 Azure AD에서 반환하는 토큰의 클레임을 나열하고 그에 대해 설명합니다. 클레임의 SAML 버전 및 JWT 버전과 클레임에 대한 설명 및 사용법이 포함되어 있습니다. 클레임은 사전순으로 나열됩니다.

### 응용 프로그램 UI

응용 프로그램 ID 클레임은 토큰을 사용하여 리소스에 액세스하는 응용 프로그램을 식별합니다. 응용 프로그램은 자체적으로 작동할 수도 있고 사용자를 대신하여 작동할 수도 있습니다. 응용 프로그램 ID는 일반적으로 응용 프로그램 개체를 나타내지만 Azure AD의 서비스 사용자 개체를 나타낼 수도 있습니다.

Azure AD는 SAML 토큰의 응용 프로그램 ID 클레임을 지원하지 않습니다.

JWT 토큰에서 응용 프로그램 ID는 appid 클레임에 표시됩니다.

    "appid":"15CB020F-3984-482A-864D-1D92265E8268"

### 대상
토큰의 대상은 토큰의 올바른 받는 사람입니다. 토큰을 받는 응용 프로그램에서는 대상 값이 올바른지 확인하여 대상이 다른 모든 토큰을 거부해야 합니다.

대상 값은 문자열로, 일반적으로 "https://contoso.com"과 같이 액세스되는 리소스의 기본 주소입니다. Azure AD 토큰에서 대상은 토큰을 요청한 응용 프로그램의 앱 ID URI입니다. 응용 프로그램, 다시 말해서 대상에 앱 ID URI가 여러 개 있으면 토큰의 대상 클레임에 있는 앱 ID URI가 토큰 요청의 앱 ID URI와 일치합니다. SAML 토큰에서 대상 클레임은 AudienceRestriction 요소의 Audience 요소에 정의됩니다.

    <AudienceRestriction>
    <Audience>https://contoso.com</Audience>
    </AudienceRestriction>

JWT 토큰에서 대상은 aud 클레임에 표시됩니다.

    "aud":"https://contoso.com"

### 응용 프로그램 인증 컨텍스트 클래스 참조

응용 프로그램 인증 컨텍스트 클래스 참조 클레임은 클라이언트가 인증된 방법을 나타냅니다. 공용 클라이언트의 경우 값이 0입니다. 클라이언트 ID 및 클라이언트 암호가 사용되면 값이 1입니다.

JWT 토큰에서 인증 컨텍스트 클래스 참조 값은 appidacr(응용 프로그램별 ACR 값) 클레임에 표시됩니다.

    "appidacr": "0"

### 인증 컨텍스트 클래스 참조
인증 컨텍스트 클래스 참조 클레임은 응용 프로그램 인증 컨텍스트 클래스 참조 클레임의 클라이언트와는 반대로 주체가 인증된 방법을 나타냅니다. 값 "0"은 최종 사용자 인증이 ISO/IEC 29115 요구 사항을 충족하지 못했다는 뜻입니다.

- JWT 토큰에서 인증 컨텍스트 클래스 참조 클레임은 acr(사용자별 ACR 값) 클레임에 표시됩니다.

    "acr": "0"

### 인증 인스턴트

인증 인스턴트 클레임은 날짜 및 인증이 발생한 날짜와 시간을 기록합니다.

SAML 토큰에서 인증 인스턴트는 AuthnStatement 요소의 AuthnInstant 특성에 표시됩니다. 날짜/시간을 UTC(Z) 시간으로 나타냅니다.

    <AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">

Azure AD의 JWT 토큰에는 이에 상응하는 클레임이 없습니다.

### 인증 방법

인증 방법 클레임은 토큰 주체가 인증된 방법을 알려줍니다. 이 예의 ID 공급자는 암호를 사용하여 사용자를 인증했습니다. http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password

SAML 토큰에서 인증 방법 값은 AuthnContextClassRef 요소에 표시됩니다.

    <AuthnContextClassRef>http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password</AuthnContextClassRef>

JWT 토큰에서 인증 방법 값은 amr 클레임에 표시됩니다.

    “amr”: ["pwd"]

###이름

이름 또는 "지정된 이름" 클레임은 Azure AD 사용자 개체에 설정된 대로 사용자의 이름 또는 "지정된 이름"을 제공합니다.

SAML 토큰에서 이름(또는 "지정 된 이름")은 givenname SAML Attribute 요소의 클레임에 표시됩니다.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>
    <AttributeValue>Frank<AttributeValue>

JWT 토큰에서 이름은 given\_name 클레임에 표시됩니다.

    "given_name": "Frank"

### 그룹

그룹 클레임은 주체의 그룹 멤버 자격을 나타내는 개체 ID를 제공합니다. 이러한 값은 고유하며(개체 ID 참조) 리소스 액세스 시 강제로 인증하게 하는 경우처럼 액세스 관리에 안전하게 사용할 수 있습니다. 그룹 클레임에 포함된 그룹은 응용 프로그램 매니페스트의 "groupMembershipClaims" 속성을 통해 응용 프로그램별로 구성됩니다. Null 값은 모든 그룹을 제외하고, "SecurityGroup" 값은 Active Directory 보안 그룹 멤버 자격만 포함하고, "All" 값은 보안 그룹과 Office 365 메일 그룹을 모두 포함합니다. 모든 구성에서 그룹 클레임은 주체의 전이적 그룹 멤버 자격을 나타냅니다.

SAML 토큰에서 그룹 클레임은 groups 특성에 표시됩니다.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
    <AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>

JWT 토큰에서 그룹 클레임은 groups 클레임에 표시됩니다.

    “groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]

### ID 공급자

ID 공급자 클레임은 토큰의 주체를 인증한 ID 공급자를 기록합니다. 이 값은 사용자 계정이 발급자가 아닌 다른 테넌트에 있는 경우를 제외하고 발급자 클레임의 값과 동일합니다.

SAML 토큰에서 ID 공급자는 identityprovider SAML Attribute 요소의 클레임에 표시됩니다.

    <Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>
    <AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>

JWT 토큰에서 ID 공급자는 idp 클레임에 표시됩니다.

    "idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### IssuedAt

IssuedAt 클레임은 토큰이 발급된 시간을 저장합니다. 토큰 만료 전 시간을 측정하는 데 주로 사용됩니다. SAML 토큰에서 IssuedAt 값은 IssueInstant 어설션에 표시됩니다.

    <Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">

JWT 토큰에서 IssuedAt 값은 iat 클레임에 표시됩니다. 값은 UTC(협정 세계시) 1970-01-010:0:0Z 이후에 경과된 시간(초)으로 표시됩니다.

    "iat": 1390234181

### 발급자

발급자 클레임은 토큰 및 Azure AD 디렉터리 테넌트를 생성하고 반환하는 STS(보안 토큰 서비스)를 식별합니다. Azure AD가 반환하는 토큰에서 발급자는 sts.windows.net입니다. 발급자 클레임 값의 GUID는 Azure AD 디렉터리의 테넌트 ID입니다. 테넌트 ID는 디렉터리의 변경 불가능하고 안정적인 식별자입니다.

SAML 토큰에서 발급자 클레임은 Issuer 요소에 표시됩니다.

    <Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>

JWT 토큰에서 발급자는 iss 클레임에 표시됩니다.

    "iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”

### 성

성 클레임은 Azure AD 사용자 개체에 정의된 사용자의 성을 제공합니다. SAML 토큰에서 성은 surname SAML Attribute 요소의 클레임에 표시됩니다.

    <Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>
    <AttributeValue>Miller<AttributeValue>

JWT 토큰에서 성은 family\_name 클레임에 표시됩니다.

    "family_name": "Miller"

### 이름

이름 클레임은 토큰의 주체를 식별하는, 사람이 읽을 수 있는 값을 제공합니다. 이 값은 테넌트 내에서 반드시 고유한 것은 아니며 표시 용도로만 사용하도록 디자인되었습니다. SAML 토큰에서 이름은 Name 특성에 표시됩니다.

    <Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>
    <AttributeValue>frankm@contoso.com<AttributeValue>

JWT 클레임에서 이름은 unique\_name 클레임에 표시됩니다.

    "unique_name": "frankm@contoso.com"

### 개체 ID

개체 ID 클레임은 Azure AD 개체의 고유 식별자를 포함합니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 따라서 예를 들어 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행할 수 있습니다. Azure AD에 대한 쿼리의 개체를 식별할 개체 ID를 사용하세요. SAML 토큰에서 개체 ID는 objectidentifier 특성에 표시됩니다.

    <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
    <AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>

JWT 토큰에서 개체 ID는 oid 클레임에 표시됩니다.

    "oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"

### 역할

역할 클레임은 Azure AD에서 주체의 응용 프로그램 역할 할당을 나타내는 친숙한 문자열을 제공하며, 역할 기반 액세스 제어를 적용하는 데 사용할 수 있습니다. 응용 프로그램 역할은 응용 프로그램 매니페스트의 "appRoles" 속성을 통해 응용 프로그램별로 정의됩니다. 각 응용 프로그램 역할의 "value" 속성은 역할 클레임에 표시되는 값입니다. 역할 클레임에 포함된 역할은 주체가 그룹 멤버 자격을 통해 직간접적으로 부여받은 모든 응용 프로그램 역할을 나타냅니다. SAML 토큰에서 역할 클레임은 roles 특성에 표시됩니다.

    <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">
    <AttributeValue>Admin</AttributeValue>

JWT 토큰에서 역할 클레임은 roles 클레임에 표시됩니다.

    “roles”: ["Admin", … ]

### 범위

토큰의 범위는 클라이언트 응용 프로그램에 부여된 가장 권한을 나타냅니다. 기본 권한은 user\_impersonation입니다. 보안 리소스의 소유자는 Azure AD에서 추가 값을 등록할 수 있습니다.

JWT 토큰에서 토큰의 범위는 scp 클레임에 지정됩니다.

    "scp": "user_impersonation"

### 제목  


토큰의 주체는 응용 프로그램 사용자처럼 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 따라서 예를 들어 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행할 수 있습니다. Azure AD에서 발급하는 토큰에는 항상 주체가 있기 때문에 이 값을 일반 용도의 인증 시스템에 사용하는 것이 좋습니다.

SAML 토큰에서 토큰의 주체는 Subject 요소의 NameID 요소에 지정됩니다. NameID는 주체를 식별하는 고유하고 다시 사용할 수 없는 식별자이며 사용자, 응용 프로그램 또는 서비스일 수 있습니다.

SubjectConfirmation은 클레임이 아닙니다. SubjectConfirmation은 토큰의 주체를 확인하는 방법을 설명합니다. "Bearer"는 주체가 소유한 토큰을 통해 주체를 확인한다는 뜻입니다.

    <Subject>
    <NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>
    <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
    </Subject>

JWT 토큰에서 주체는 sub 클레임에 표시됩니다.

    "sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"

### 테넌트 ID
테넌트 ID는 토큰을 발급한 디렉터리 테넌트를 식별하는 변경할 수 없고 다시 사용할 수 없는 식별자입니다. 이 값을 사용하여 다중 테넌트 응용 프로그램의 테넌트별 디렉터리 리소스에 액세스할 수 있습니다. 예를 들어 이 값을 사용하여 Graph API 호출의 테넌트를 식별할 수 있습니다.

SAML 토큰에서 테넌트 ID는 tenantid SAML Attribute 요소의 클레임에 표시됩니다.

    <Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>
    <AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>

JWT 토큰에서 테넌트 ID는 tid 클레임에 표시됩니다.

    "tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"

### 토큰 수명
토큰 수명 클레임은 토큰이 유효한 시간 간격을 정의합니다. 토큰의 유효성을 검사하는 서비스에서는 현재 날짜가 토큰 수명 내에 있는지 확인해야 합니다. 그렇지 않으면 해당 토큰을 거부해야 합니다. Azure AD와 서비스 간의 시계 시간 차이("시간차")를 고려하여 서비스에서 토큰 수명 범위를 벗어나 최대 5분까지 여유 시간을 제공할 수 있습니다.

SAML 토큰에서 토큰 수명 클레임은 NotBefore 및 NotOnOrAfter 특성을 사용하여 Conditions 요소에 정의됩니다.

    <Conditions
    NotBefore="2013-03-18T21:32:51.261Z"
    NotOnOrAfter="2013-03-18T22:32:51.261Z"
    >

JWT 토큰에서 토큰 수명은 nbf(이전은 아님) 및 exp(만료 시간)에 의해 정의됩니다. 이러한 클레임 값은 UTC(협정 세계시) 1970-01-010:0:0Z 이후에 경과된 시간(초)으로 표시됩니다. 자세한 내용은 RFC 3339를 참조하세요.

    "nbf":1363289634,
    "exp":1363293234

### 사용자 계정 이름
사용자 계정 이름 클레임은 사용자 보안 주체의 사용자 이름을 저장합니다.

JWT 토큰에서 사용자 계정 이름은 upn 클레임에 표시됩니다.

    "upn": frankm@contoso.com

### 버전
버전 클레임은 토큰의 버전 번호를 저장합니다. JWT 토큰에서 사용자 계정 이름은 ver 클레임에 표시됩니다.

    "ver": "1.0"

## 샘플 토큰

이 섹션에서는 Azure AD가 반환하는 SAML 및 JWT 토큰 샘플을 보여 줍니다. 이러한 샘플을 통해 컨텍스트 내에서 클레임을 볼 수 있습니다. SAML 토큰

다음은 일반적인 SAML 토큰 샘플입니다.

	<?xml version="1.0" encoding="UTF-8"?>
	<t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
	  <t:Lifetime>
		<wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
		<wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
	  </t:Lifetime>
	  <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
		<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
		  <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
		</EndpointReference>
	  </wsp:AppliesTo>
	  <t:RequestedSecurityToken>
		<Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
		  <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
		  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
			<ds:SignedInfo>
			  <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
			  <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
			  <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
				<ds:Transforms>
				  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
				  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
				<ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
			  </ds:Reference>
			</ds:SignedInfo>
			<ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
			<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
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
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedAttachedReference>
	  <t:RequestedUnattachedReference>
		<SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
		  <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
		</SecurityTokenReference>
	  </t:RequestedUnattachedReference>
	  <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
	  <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
	  <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
	</t:RequestSecurityTokenResponse>

### JWT 토큰 - 사용자 가장

다음은 사용자 가장 웹 흐름에 사용되는 일반적인 JWT(JSON 웹 토큰) 샘플입니다. 클레임 외에도 토큰에는 클라이언트가 인증된 방법을 나타내는 인증 컨텍스트 클래스 참조 **ver** 및 **appidacr**에 버전 번호가 포함됩니다. 공용 클라이언트의 경우 값이 0입니다. 클라이언트 ID 또는 클라이언트 암호가 사용되면 값이 1입니다.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

##참고 항목

[Azure Active Directory 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn151124.aspx)

<!---HONumber=AcomDC_0518_2016-->