<properties
   pageTitle="페더레이션 메타데이터"
   description="Azure AD 토큰을 허용하는 서비스가 사용해야 하는 메타데이터 문서의 내용 및 메타데이터 끝점을 설명합니다."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/30/2015"
   ms.author="mbaldwin"/>

# 페더레이션 메타데이터
Azure Active Directory(Azure AD)는 Azure Active Directory가 발급하는 보안 토큰을 수락하도록 구성된 서비스에 대한 페더레이션 메타데이터 문서를 게시합니다. 페더레이션 메타데이터 문서 형식은 [Web Services Federation Language (WS-Federation) 버전 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)에서 설명되며, [OASIS SAML(Security Assertion Markup Language) V2.0의 메타데이터](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)를 확장합니다.

이 항목은 메타데이터 끝점을 나열하고 사용해야 하는 Azure AD 토큰을 허용하는 서비스의 메타데이터 문서의 콘텐츠를 설명합니다.

##테넌트별 및 테넌트에 독립적인 메타 데이터 끝점

Azure AD 테넌트별 및 테넌트 독립적 끝점을 게시합니다. 테넌트별 끝점은 특정 테넌트에 대해 설계되었습니다. 테넌트별 페더레이션 메타데이터에는 테넌트별 발급자 및 끝점 정보를 포함하는 테넌트에 관한 정보가 포함됩니다. 테넌트별 끝점을 사용하는 단일 테넌트에 대한 액세스를 제한하는 응용 프로그램입니다.

테넌트 독립적 끝점은 모든 Azure AD 테넌트에 공통된 정보를 제공합니다. 이 정보는 *login.windows.net*에서 호스트된 테넌트에 적용되며 테넌트 간에 공유됩니다. 테넌트 독립적 끝점은 특정 테넌트와 연결되어있지 않으므로 다중 테넌트 응용 프로그램에 좋습니다.

## 페더레이션 메타데이터 끝점

Azure AD는 페더레이션 메타데이터를 *https://login.windows.net/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml*에 게시하며, 여기서 값 <TenantDomainName>는 "일반" 또는 테넌트별 값이 될 수 있습니다. 끝점은 주소가 지정되므로 테넌트에 대한 페더레이션 메타데이터를 보려면 주소 사이트로 이동할 수 있습니다.

**테넌트별 끝점**의 경우, <TenantDomainName>는 다음 유형 중 하나가 될 수 있습니다.

- Azure AD 테넌트의 등록된 도메인 이름(예: contoso.onmicrosoft.com).

- 도메인의 변경 불가능 테넌트 ID(예: 72f988bf-86f1-41af-91ab-2d7cd011db45)

**테넌트 독립적 끝점**의 경우 <TenantDomainName>은 **일반**입니다. 이 이름은 login.windows.net에서 호스팅되는 모든 Azure AD 테넌트에 공통된 페더레이션 메타데이터 요소만을 나타냅니다.

예를 들어, 테넌트별 끝점은 *https://login.windows.net/contoso.onmicrosoft.comFederationMetadata/2007-06/FederationMetadata.xml*일 수 있습니다. 테넌트 독립적 끝점은 *https://login.windows.net/common/FederationMetadata/2007-06/FederationMetadata.xml*입니다.

## 페더레이션 메타데이터의 내용

다음 섹션에는 Azure AD에서 발급한 토큰을 사용하는 서비스에 필요한 정보를 제공합니다.

### EntityID

**EntityDescriptor** 요소는 **EntityID** 특성을 포함합니다. **EntityID** 특성의 값은 발급자, 즉, 토큰을 발행하는 보안 토큰 서비스(STS)를 나타냅니다. 테넌트가 토큰을 발급했는지 확인할 수 있도록 발급자의 유효성을 검사하는 것이 중요합니다.

다음 메타데이터는 **EntityID** 요소가 있는 샘플 테넌트별 **EntityDescriptor** 요소를 보여줍니다.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b" 
    entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">

테넌트 독립적 끝점에서 **EntityID**는 테넌트별 **EntityID** 값을 제공하는 데 사용될 수 있는 템플릿을 제공합니다. 테넌트 독립적 끝점의 "{tenant}" 리터럴을 토큰의 **TenantID** 클레임의 값으로 대체합니다. 결과 값은 토큰 발급자와 동일합니다. 이 전략을 통해 다중 테넌트 응용 프로그램이 지정된 테넌트에 대한 발급자의 유효성을 검사할 수 있습니다.

다음 메타데이터는 샘플 테넌트 독립적 **EntityID** 요소를 보여줍니다. 이 요소에서 {tenant}는 자리 표시자가 아닌 리터럴입니다.

    <EntityDescriptor 
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata" 
    ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd" 
    entityID="https://sts.windows.net/{tenant}/">

### 토큰 서명 인증서
서비스는 Azure AD 테넌트에서 발급한 토큰을 받으면, 페더레이션 메타데이터 문서에 게시된 서명 키로 토큰의 서명 유효성을 검사해야 합니다.

페더레이션 메타데이터는 테넌트를 토큰 서명에 사용하는 인증서의 공개 부분을 포함합니다. 인증서 원시 바이트는 **KeyDescriptor** 요소에 표시됩니다. 토큰 서명 인증서는 **use** 특성의 값이 **signing**인 경우의 서명에만 유효합니다.

Azure AD를 통해 게시된 페더레이션 메타데이터 문서에는 Azure AD가 서명 인증서 업데이트를 준비하는 경우와 같은 여러 서명 키가 있을 수 있습니다. 페더레이션 메타데이터 문서가 둘 이상의 인증서를 포함하는 경우, 토큰의 유효성을 검사하는 서비스는 문서에서 모든 인증서를 지원해야 합니다.

다음 메타데이터는 서명 키가 있는 샘플 **KeyDescriptor** 요소를 보여줍니다.

    <KeyDescriptor use="signing">
    <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
    <X509Data>
    <X509Certificate>
    MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
    </X509Certificate>
    </X509Data>
    </KeyInfo>
    </KeyDescriptor>

**KeyDescriptor** 요소는 페더레이션 메타데이터 문서에서 WS-Federation 관련 섹션 및 SAML 관련 섹션의 두 곳에 표시됩니다. 두 섹션 모두에서 게시된 인증서는 동일해야 합니다.

WS-Federation 관련 섹션에서 WS-Federation 메타데이터 판독기는 **SecurityTokenServiceType** 유형이 있는 **RoleDescriptor** 요소에서 인증서를 읽습니다.

다음 메타데이터는 샘플 **RoleDescriptor** 요소를 보여줍니다.

    <RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706"
    xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">`

SAML 관련 섹션에서 WS-Federation 메타 데이터 판독기는 **IDPSSODescriptor** 요소에서 인증서를 읽습니다.

다음 메타데이터는 샘플 **IDPSSODescriptor** 요소를 보여줍니다.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">

테넌트 관련 및 테넌트 독립적 인증서의 형식에는 차이가 없습니다.

### WS-Federation 끝점 URL
페더레이션 메타데이터는 WS-Federation 프로토콜에서 단일 로그인 및 단일 로그 아웃에 Azure AD가 사용하는 URL을 포함합니다. 이 끝점은 **PassiveRequestorEndpoint** 요소에 표시됩니다.

다음 메타데이터는 테넌트 별 끝점에 대한 샘플 **PassiveRequestorEndpoint** 요소를 보여줍니다.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

테넌트 독립적 끝점의 경우, 다음 예제와 같이 WS-Federation URL은 WS-Federation 끝점에 나타납니다.

    <fed:PassiveRequestorEndpoint>
    <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
    <Address>
    https://login.windows.net/common/wsfed
    </Address>
    </EndpointReference>
    </fed:PassiveRequestorEndpoint>

### SAML 프로토콜 끝점 URL

페더레이션 메타데이터는 SAML 2.0 프로토콜에서 단일 로그인 및 단일 로그아웃에 Azure AD가 사용하는 URL을 포함합니다. 이 끝점은 **IDPSSODescriptor** 요소에 표시됩니다.

로그인 및 로그 아웃 URL은 **SingleSignOnService** 및 **SingleLogoutService** 요소에 표시됩니다. 다음 메타데이터는 테넌트 별 끝점에 대한 샘플 **PassiveResistorEndpoint** 요소를 보여줍니다.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https:// login.windows.net/contoso.onmicrosoft.com /saml2" />
    </IDPSSODescriptor>

마찬가지로 다음 샘플과 같이 일반 SAML 2.0 프로토콜 끝점에 대한 끝점은 테넌트 독립적 페더레이션 메타데이터에 게시됩니다.

    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    …
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.windows.net/common/saml2" />
    </IDPSSODescriptor>

## 참고 항목
[Azure Active Directory 인증 프로토콜](active-directory-authentication-protocols.md)

[Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)

<!---HONumber=62-->