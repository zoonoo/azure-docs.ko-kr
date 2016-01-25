<properties
	pageTitle="Azure AD Connect 복수 도메인"
	description="이 문서에는 O365 및 Azure AD를 사용하여 여러 최상위 도메인을 설정하고 구성하는 방법이 설명되어 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/11/2016"
	ms.author="billmath"/>

#복수 도메인 지원

많은 사용자가 페더레이션이 포함된 여러 최상위 Office 365 또는 Azure AD 도메인 및 하위 도메인을 구성할 수 있는 방법을 요청했습니다. 백그라운드에서 수행되는 작업들로 인해 대부분 매우 간단한 방법으로 작업이 수행되는 동안 다음과 같은 문제를 방지하기 위해 알아야 할 몇 가지 팁과 요령이 있습니다.

- 페더레이션에 대한 추가 도메인 구성을 시도하는 동안 나타나는 오류 메시지
- 페더레이션을 위해 여러 최상위 도메인을 구성한 후 하위 도메인의 사용자가 로그인할 수 없는 경우

## 여러 최상위 도메인
fabrikam.com이라는 추가 도메인을 필요로 하는 예제 조직 contoso.com의 설정을 안내합니다.

내 온-프레미스 시스템에서 AD FS가 fs.contoso100.com이라는 페더레이션 서비스로 구성된 경우를 가정해 봅니다.

처음으로 Office 365 또는 Azure AD에 등록하는 경우 내 첫 번째 로그온 도메인으로 contoso.com을 구성하도록 선택합니다. New-MsolFederatedDomain을 사용하여 Azure AD Connect 또는 Azure AD Powershell을 통해 이 작업을 수행할 수 있습니다.

이 작업이 완료되면 새 Azure AD 도메인의 새 구성 속성(Get-MsolDomainFederationSettings를 사용하여 쿼리될 수 있는)에 대한 두 개의 기본값을 살펴보겠습니다.

| 속성 이름 | 값 | 설명|
| ----- | ----- | -----|
|IssuerURI | http://fs.contoso100.com/adfs/services/trust| URL과 같이 표시되는 동안 이 속성은 실제로 온-프레미스 인증 시스템의 이름이므로 이 경로는 확인할 필요가 없습니다. 기본적으로 Azure AD는 이 값을 내 온-프레미스 AD FS 구성에서 페더레이션 서비스 식별자의 값으로 설정합니다.
|PassiveClientSignInUrl|https://fs.contoso100.com/adfs/ls/|This은 수동 로그인 요청이 보내지는 위치이며 내 실제 AD FS 시스템으로 확인됩니다. 실제로 여러 가지 “*Url” 속성이 있지만 여기서는 이 속성과 IssuerURI와 같은 URI의 차이점을 설명하는 하나의 예를 살펴보기만 하면 됩니다.

이제 내 두 번째 도메인 fabrikam.com을 추가한다고 가정해 봅니다. Azure AD Connect 마법사를 다시 실행하거나 PowerShell을 통해 이 작업을 다시 수행할 수 있습니다.

Azure AD PowerShell을 사용하여 페더레이션으로 두 번째 도메인을 추가하려고 하는 경우 오류가 발생합니다.

이러한 이유로 Azure AD의 제약 조건은 IssuerURI에서 둘 이상의 도메인에 같은 값을 가질 수 없습니다. 이러한 제한 사항을 극복하기 위해 새 도메인에 대해 다른 IssuerURI를 사용해야 합니다. 이 작업은 SupportMultipleDomain 매개 변수가 수행하는 것이 효과적입니다. 페더레이션(New-, Convert- 및 Update-MsolFederatedDomain)을 구성하기 위해 cmdlet과 함께 사용하는 경우, 이 매개 변수는 Azure AD가 Azure AD의 테넌트에서 고유해야 하는 도메인 이름에 기반한 IssuerURI를 구성하게 할 수 있으므로 고유해야 합니다. 또한 클레임 규칙에 대한 변경 내용이 있지만 잠시 후 살펴보겠습니다.

따라서 Powershell에서 SupportMultipleDomain 매개 변수를 사용하여 fabrikam.com을 추가하는 경우 다음과 같습니다.

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

Azure AD에서 다음 구성을 제공합니다.

- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/ 

IssuerURI가 내 도메인을 기반으로 한 값으로 설정되므로 고유한 끝점 url 값은 원래 contoso.com 도메인의 경우와 마찬가지로 fs.contoso100.com의 내 페더레이션 서비스를 가리키도록 여전히 구성됩니다. 따라서 모든 도메인은 여전히 같은 AD FS 시스템을 가리킵니다.

SupportMultipleDomain이 하는 다른 일은 AD FS 시스템이 Azure AD에 대해 발급된 토큰에서 적절한 발급자 값을 포함하는지 확인하는 것입니다. 사용자 upn의 도메인 부분을 가져오거나 issuerURI 즉, https://{upn suffix}/adfs/services/trust의 도메인으로 이를 설정하여 이 작업을 수행합니다. 따라서 Azure AD 또는 Office 365에 인증하는 동안 사용자 토큰의 발급자 요소는 Azure AD에서 도메인을 찾는 데 사용됩니다. 일치하는 항목이 없는 경우 인증이 실패합니다.

예를 들어 사용자의 UPN이 johndoe@fabrikam.com인 경우 토큰 AD FS 이슈의 발급자 요소는 http://fabrikam.com/adfs/services/trust로 설정됩니다. 이는 Azure AD 구성에 일치하며 인증이 성공합니다.

다음은 이 논리를 구현하는 사용자 지정된 클레임 규칙입니다.

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

이제 내 설정에서 supportMultipleDomains 스위치 없이 기본 IssuerURI 값을 사용하여 처음으로 contoso.com에 등록합니다. fabrikam.com을 추가할 때 실제로 contoso.com이 또한 더 이상 기본 IssuerURI를 전송하지 않아 IssuerURI가 일치하지 않는 문제로 인증이 실패하는 클레임 규칙 업데이트로 SupportMultiple Domains 스위치를 사용하도록 구성되었는지 확인해야 합니다. 다른 도메인에 supportMultipleDomain 스위치를 사용하도록 허용하기 전에 이에 대한 경고 메시지가 나타나므로 걱정하지 마세요.

이 문제를 해결하려면 도메인 contoso.com에 대한 구성도 업데이트해야 합니다. Azure AD Connect 마법사는 두 번째 도메인을 추가할 때 위의 작업을 수행해야 하는 시기와 올바른 작업을 수행하는 지에 대해 파악하는 훌륭한 기능을 가지고 있습니다. 첫 번째 패스에서 SupportMultipleDomain 구성에 이미 있는 경우 덮어쓰지 않습니다.

PowerShell에서 SupportMultipleDomain 스위치를 수동으로 제공해야 합니다.

단일 도메인에서 복수 도메인으로 전환하는 모든 단계에 대한 자세한 내용은 다음을 참조하세요.

이 작업을 수행하면 Azure AD에서 두 개의 도메인에 대한 구성을 갖게 됩니다.

- DomainName: contoso.com
- IssuerURI: http://contoso.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/ 
- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/ 

이제 contoso.com 및 fabrikam.com 도메인에서 사용자에 대한 페더레이션된 로그온이 작동됩니다. 남아 있는 단 하나의 문제는 하위 도메인의 사용자에 대한 로그온 문제입니다.

##하위 도메인
내 하위 도메인 sub.contoso.com을 Azure AD에 추가하는 경우를 가정해 보겠습니다. Azure AD가 도메인을 관리하는 방식으로 인해 하위 도메인은 부모 도메인, 이 경우 contoso.com의 설정을 상속합니다. 즉, user@sub.contoso.com에 대한 IssuerURI는 http://contoso.com/adfs/services/trust이어야 합니다. 그러나 위에서 구현된 표준 규칙

Azure AD는 발급자를 도메인의 필수 값이 일치하지 않아 인증이 실패하는 http://sub.contoso.com/adfs/services/trust로 가진 토큰을 생성합니다. 다행히도 이 문제에 대한 해결 방법도 있지만 해당 도구에 대해 기본 제공되지는 않습니다. Microsoft Online용 AD FS 신뢰 당사자 트러스트를 수동으로 업데이트해야 합니다.

사용자 지정 클레임 규칙이 사용자 지정 발급자 값을 생성할 때 사용자의 UPN 접미사에서 모든 하위 도메인을 제거하도록 구성해야 합니다. 아래 단계에서 이 작업을 수행하는 정확한 단계를 찾을 수 있습니다.

요약하면 같은 AD FS 서버에 연결된 모든 하위 도메인뿐만 아니라 서로 다른 이름을 가진 여러 도메인을 가질 수 있도록 발급자 값이 모든 사용자에 대해 올바르게 설정되어 있는지 확인하려면 몇 가지 추가 단계만 수행하면 됩니다.

<!---HONumber=AcomDC_0114_2016-->