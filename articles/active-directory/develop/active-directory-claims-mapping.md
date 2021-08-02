---
title: Azure AD 테넌트 앱 클레임 사용자 지정(PowerShell)
titleSuffix: Microsoft identity platform
description: 특정 Azure Active Directory 테넌트의 애플리케이션에 대한 토큰에서 내보낸 클레임을 사용자 지정하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 06/10/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: bb44904379e7a9b784f4e2d9bb7c93673718ed37
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983045"
---
# <a name="customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant"></a>테넌트의 특정 앱용 토큰에 내보내는 클레임 사용자 지정

클레임 사용자 지정은 테넌트 관리자가 테넌트의 특정 애플리케이션에 대한 토큰에 내보내는 클레임을 사용자 지정하는 데 사용됩니다. 클레임 매핑 정책을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- 토큰에 포함할 클레임을 선택합니다.
- 아직 존재하지 않는 클레임 형식을 만듭니다.
- 특정 클레임에 내보내는 데이터 원본을 선택하거나 변경합니다.

클레임 사용자 지정은 WS-Fed, SAML, OAuth 및 OpenID Connect 프로토콜용 클레임 매핑 정책 구성을 지원합니다.

> [!NOTE]
> 이 기능은 Azure Portal을 통해 제공되는 [클레임 사용자 지정](active-directory-saml-claims-customization.md)을 바꾸고 대체합니다. 이 문서에 자세히 설명된 Microsoft Graph/PowerShell 방법과 함께 포털을 사용하여 동일한 애플리케이션에서 클레임을 사용자 지정하는 경우 해당 애플리케이션용으로 발급된 토큰은 포털의 구성을 무시합니다. 이 문서에 설명된 방법을 통해 만들어진 구성은 포털에서 반영되지 않습니다.

이 문서에서는 [클레임 매핑 정책 형식](reference-claims-mapping-policy-type.md)을 사용하는 방법을 이해하는 데 도움이 되는 몇 가지 일반적인 시나리오를 설명합니다.

클레임 매핑 정책을 만들 때 토큰의 디렉터리 스키마 확장 특성에서 클레임을 내보낼 수도 있습니다. `ClaimsSchema` 요소에서 *ID* 대신 확장의 *ExtensionID* 를 사용합니다.  확장 특성에 대한 자세한 내용은 [디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

다음 예제에서는 서비스 주체에 대한 정책을 만들고, 업데이트, 연결 및 삭제합니다. 클레임 매핑 정책은 서비스 사용자 개체에만 할당할 수 있습니다. Azure AD를 처음 접하는 분들은 [Azure AD 테넌트를 가져오는 방법](quickstart-create-new-tenant.md)을 살펴본 후 예제를 진행하는 것이 좋습니다.

> [!NOTE]
> 클레임 매핑 정책을 구성하려면 [Azure AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)가 필요합니다. PowerShell 모듈은 미리 보기로 제공되며 변경 내용을 되돌리거나 제거할 수 있습니다. 

시작하려면 다음 단계 중 하나를 수행합니다.

1. 최신 [Azure AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드합니다.
1. Connect 명령을 실행하여 Azure AD 관리자 계정에 로그인합니다. 새 세션을 시작할 때마다 이 명령을 실행합니다.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 조직에서 만든 모든 정책을 확인하려면 다음 명령을 실행합니다. 다음 시나리오에서 대부분의 작업 후에 이 명령을 실행하여 정책이 예상대로 생성되는지 확인하는 것이 좋습니다.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>토큰에서 기본 클레임 생략

이 예제에서는 연결된 서비스 주체에 발급된 토큰에서 [기본 클레임 집합](reference-claims-mapping-policy-type.md#claim-sets)을 제거하는 정책을 만듭니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에서 기본 클레임 집합을 제거합니다.
   1. 정책을 만들려면 이 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>토큰에 EmployeeID 및 TenantCountry를 클레임으로 포함

이 예제에서는 연결된 서비스 주체에 발급된 토큰에 EmployeeID 및 TenantCountry를 추가하는 정책을 만듭니다. EmployeeID는 SAML 토큰 및 JWT 둘 다에서 이름 클레임 형식으로 내보내집니다. TenantCountry는 SAML 토큰 및 JWT 둘 다에서 국가/지역 클레임 형식으로 내보내집니다. 이 예제에서는 토큰에 기본 클레임 집합을 계속 포함합니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에 EmployeeID 및 TenantCountry 클레임을 추가합니다.
   1. 정책을 만들려면 다음 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>토큰에서 클레임 변환 사용

이 예제에서는 연결된 서비스 주체에 발급된 JWT에 사용자 지정 클레임 “JoinedData”를 내보내는 정책을 만듭니다. 이 클레임에는 사용자 개체의 extensionattribute1 특성에 저장된 데이터와 “.sandbox”를 조인하여 만든 값이 포함됩니다. 이 예제에서는 토큰에 기본 클레임 집합을 제외합니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에 EmployeeID 및 TenantCountry 클레임을 추가합니다.
   1. 정책을 만들려면 다음 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>보안 고려 사항

토큰을 수신하는 애플리케이션은 클레임 값이 Azure AD에서 정식으로 발행되었고 변조될 수 없다는 사실을 바탕으로 합니다. 그러나 클레임 매핑 정책을 통해 토큰 콘텐츠를 수정하면 이러한 가정이 올바르지 않게 될 수도 있습니다. 악의적인 행위자가 만든 클레임 매핑 정책으로부터 보호받으려면, 애플리케이션은 클레임 매핑 정책의 생성자가 토큰을 수정했음을 명시적으로 인정해야 합니다. 이 작업은 다음 방법으로 수행할 수 있습니다.

- 사용자 지정 서명 키 구성
- 매핑된 클레임을 허용하도록 애플리케이션 매니페스트를 업데이트합니다.
 
업데이트하지 않으면 Azure AD에서는 [`AADSTS50146` 오류 코드](reference-aadsts-error-codes.md#aadsts-error-codes)를 반환합니다.

### <a name="custom-signing-key"></a>사용자 지정 서명 키

서비스 주체 개체에 사용자 지정 서명 키를 추가하려면 Azure PowerShell cmdlet [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential)을 사용하여 애플리케이션 개체에 대한 인증서 키 자격 증명을 만들면 됩니다.

클레임 매핑을 사용하도록 설정된 앱은 [OpenID Connect 메타데이터 요청](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)에 `appid={client_id}`를 추가하여 토큰 서명 키의 유효성을 검사해야 합니다. 사용해야 하는 OpenID Connect 메타데이터 문서의 형식은 다음과 같습니다.

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>애플리케이션 매니페스트 업데이트

또는 `acceptMappedClaims` 속성을 [애플리케이션 매니페스트](reference-app-manifest.md)의 `true`에 설정해도 됩니다. [Apiapplication 리소스 형식](/graph/api/resources/apiapplication#properties)에서 설명하는 것처럼, 애플리케이션은 사용자 지정 서명 키를 지정하지 않고도 클레임 매핑을 사용할 수 있습니다.

이렇게 하려면 요청받은 토큰 사용자가 Azure AD 테넌트의 확인된 도메인 이름을 사용해야 합니다. 따라서 사용자는 (애플리케이션 매니페스트에서 `identifierUris`로 표시하는) `Application ID URI`를 `https://contoso.com/my-api`로 설정해야 합니다(또는 기본 테넌트 이름을 사용해야 합니다)`https://contoso.onmicrosoft.com/my-api`.

확인된 도메인을 사용하지 않는 경우 Azure AD는 "AcceptMappedClaims는 애플리케이션 GUID가 일치하는 토큰 사용자나 테넌트의 확인된 도메인에 있는 사용자에만 지원됩니다. 리소스 식별자를 변경하거나 애플리케이션별 서명 키를 사용하세요."라는 메시지를 포함하는 `AADSTS501461` 오류를 반환합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [클레임 매핑 정책 유형](reference-claims-mapping-policy-type.md) 참조 문서를 참조하세요.
- Azure Portal을 통해 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법에 대한 자세한 내용은 [방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정](active-directory-saml-claims-customization.md)을 참조하세요.
- 확장 특성에 대해 자세히 알아보려면 [클레임에서 디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조하세요.
