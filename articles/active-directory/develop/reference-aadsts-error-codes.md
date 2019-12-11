---
title: Azure AD 인증 및 권한 부여 오류 코드 | Microsoft
description: Azure AD STS (보안 토큰 서비스)에서 반환 되는 AADSTS 오류 코드에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ed0b84a29ea0c3ce3b58db1c029798655bfb06
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965809"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Azure AD 인증 및 권한 부여 오류 코드

Azure Active Directory (Azure AD) STS (보안 토큰 서비스)에서 반환 되는 AADSTS 오류 코드에 대 한 정보를 찾고 있나요? 이 문서를 읽고 AADSTS 오류 설명, 수정 사항 및 몇 가지 제안 된 해결 방법을 찾을 수 있습니다.

> [!NOTE]
> Ezek az információk előzetesek, és változhat a tartalmuk. 궁금한 점이 있거나 원하는 항목을 찾을 수 없습니까? GitHub 문제를 만들거나 [개발자를 위한 지원 및 도움말 옵션](active-directory-develop-help-support.md) 을 참조 하 여 도움말과 지원을 받을 수 있는 다른 방법에 대해 알아보세요.
>
> 이 설명서는 개발자 및 관리 지침에 제공 되지만 클라이언트 자체에서 사용해 서는 안 됩니다. 오류 코드는 개발자가 응용 프로그램을 빌드하는 동안 도움을 주기 위해 사용할 수 있는 보다 세부적인 오류 메시지를 제공 하기 위해 언제 든 지 변경 될 수 있습니다. 텍스트 또는 오류 코드 번호에 대 한 종속성을 사용 하는 앱은 시간이 지남에 따라 분할 됩니다.

## <a name="lookup-current-error-code-information"></a>현재 오류 코드 정보 조회
오류 코드와 메시지는 변경 될 수 있습니다.  최신 정보는 [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) 페이지를 확인 하 여 AADSTS 오류 설명, 수정 사항 및 제안 된 몇 가지 해결 방법을 찾아보십시오.  

반환 된 오류 코드의 숫자 부분을 검색 합니다.  예를 들어 "AADSTS16000" 오류 코드를 받은 경우 "16000"에 대 한 [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) 검색을 수행 합니다.  URL: [https://login.microsoftonline.com/error?code=16000](https://login.microsoftonline.com/error?code=16000)에 오류 코드 번호를 추가 하 여 특정 오류에 직접 연결할 수도 있습니다.

## <a name="aadsts-error-codes"></a>AADSTS 오류 코드

| Hiba | Leírás |
|---|---|
| AADSTS16000 | SelectUserAccount-Azure AD에서 발생 하는 인터럽트 이며, 사용자가 여러 유효한 SSO 세션 중에서 선택할 수 있도록 하는 UI를 생성 합니다. 이 오류는 매우 일반적 이며 `prompt=none` 지정 된 경우 응용 프로그램에 반환 될 수 있습니다. |
| AADSTS16001 | UserAccountSelectionInvalid-사용자가 세션 선택 논리가 거부 한 타일을 클릭 하면이 오류가 표시 됩니다. 트리거된 경우이 오류를 통해 사용자는 업데이트 된 타일/세션 목록에서 선택 하거나 다른 계정을 선택 하 여 복구할 수 있습니다. 이 오류는 코드 오류 또는 경합 상태 때문에 발생할 수 있습니다. |
| AADSTS16002 | AppSessionSelectionInvalid-앱에서 지정한 SID 요구 사항이 충족 되지 않았습니다.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant-사용자가 테 넌 트에 명시적으로 추가 되지 않았음을 나타냅니다. |
| AADSTS17003 | CredentialKeyProvisioningFailed-Azure AD에서 사용자 키를 프로 비전 할 수 없습니다. |
| AADSTS20001 | WsFedSignInResponseError-페더레이션 Id 공급자에 문제가 있습니다. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20012 | WsFedMessageInvalid-페더레이션 Id 공급자에 문제가 있습니다. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS20033 | FedMetadataInvalidTenantName-페더레이션 Id 공급자에 문제가 있습니다. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40008 | OAuth2IdPUnretryableServerError-페더레이션 Id 공급자에 문제가 있습니다. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError-페더레이션 Id 공급자에 문제가 있습니다. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40010 | OAuth2IdPRetryableServerError-페더레이션 Id 공급자에 문제가 있습니다. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError-페더레이션 Id 공급자에 문제가 있습니다. A probléma megoldásához lépjen kapcsolatba az identitásszolgáltatójával. |
| AADSTS50000 | TokenIssuanceError-로그인 서비스에 문제가 있습니다. [Hozzon létre támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a probléma megoldásához. |
| AADSTS50001 | InvalidResource-리소스가 비활성화 되었거나 존재 하지 않습니다. 앱의 코드를 확인 하 여 액세스 하려는 리소스에 대 한 정확한 리소스 URL을 지정 했는지 확인 합니다.  |
| AADSTS50002 | NotAllowedTenant-테 넌 트에 대 한 제한 된 프록시 액세스로 인해 로그인 하지 못했습니다. 사용자 고유의 테 넌 트 정책 인 경우 제한 된 테 넌 트 설정을 변경 하 여이 문제를 해결할 수 있습니다. |
| AADSTS50003 | MissingSigningKey-서명 키 또는 인증서가 누락 되어 로그인이 실패 했습니다. 앱에서 서명 키가 구성 되지 않았기 때문일 수 있습니다. Tekintse meg a következő helyen ismertetett megoldásokat: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). 문제가 계속 되 면 앱 소유자 또는 앱 관리자에 게 문의 하세요. |
| AADSTS50005 | DevicePolicyError-사용자가 현재 조건부 액세스 정책을 통해 지원 되지 않는 플랫폼에서 장치에 로그인 하려고 했습니다. |
| AADSTS50006 | 잘못 된 서명 때문에 InvalidSignature 서명 확인에 실패 했습니다. |
| AADSTS50007 | PartnerEncryptionCertificateMissing-이 앱에 대 한 파트너 암호화 인증서를 찾을 수 없습니다. Microsoft에서 [지원 티켓을 열어](../fundamentals/active-directory-troubleshooting-support-howto.md) 이 문제를 해결 하세요. |
| AADSTS50008 | InvalidSamlToken-SAML 어설션이 토큰에서 누락 되었거나 잘못 구성 되었습니다. Forduljon az összevonási szolgáltatójához. |
| AADSTS50010 | 토큰 대상이 구성 되지 않아 앱에 대 한 AudienceUriValidationFailed URI 유효성 검사가 실패 했습니다. |
| AADSTS50011 | InvalidReplyTo-회신 주소가 누락 되었거나, 잘못 구성 되었거나, 앱에 대해 구성 된 회신 주소와 일치 하지 않습니다.  해결 방법으로이 누락 된 회신 주소를 Azure Active Directory 응용 프로그램에 추가 하거나 Active Directory에서 응용 프로그램을 관리할 수 있는 권한이 있는 사용자에 게이 작업을 수행할 수 있도록 합니다.|
| AADSTS50012 | AuthenticationFailed-다음 이유 중 하나로 인해 인증 하지 못했습니다.<ul><li>서명 인증서의 주체 이름에 권한이 없습니다.</li><li>권한 있는 주체 이름에 대해 일치 하는 신뢰할 수 있는 인증 기관 정책을 찾을 수 없습니다.</li><li>인증서 체인이 올바르지 않습니다.</li><li>서명 인증서가 유효 하지 않습니다.</li><li>정책이 테 넌 트에서 구성 되지 않았습니다.</li><li>서명 인증서의 지문이 인증 되지 않았습니다.</li><li>클라이언트 어설션에 잘못 된 서명이 포함 되어 있습니다.</li></ul> |
| AADSTS50013 | InvalidAssertion은 다양 한 이유로 인해 잘못 된 것입니다. 즉, 토큰 발급자가 유효한 시간 범위 내에 있는 api 버전과 일치 하지 않습니다. 어설션의 형식이 잘못 됨-새로 고침 토큰이 기본 새로 고침 토큰이 아닙니다. |
| AADSTS50014 | GuestUserInPendingState-사용자의 상환 상태가 보류 중입니다. 게스트 사용자 계정이 아직 완전히 만들어지지 않았습니다. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState-사용자에 게 법적 연령 그룹 동의가 필요 합니다. |
| AADSTS50017 | CertificateValidationFailed-인증 유효성 검사에 실패 했습니다. 이유는 다음과 같습니다.<ul><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A várt CrlSegment nem található</li><li>A kiállítási tanúsítvány nem szerepel a megbízható tanúsítványok listájában</li><li>A különbözeti CRL terjesztési pontja megfelelő CRL terjesztési pont nélkül lett konfigurálva</li><li>시간 초과 문제로 인해 유효한 CRL 세그먼트를 검색할 수 없습니다.</li><li>A CRL letöltése nem sikerült</li></ul>Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50020 | UserUnauthorized 없는 사용자-이 끝점을 호출할 수 있는 권한이 없습니다. |
| AADSTS50027 | InvalidJwtToken-다음 이유로 인해 JWT 토큰이 잘못 되었습니다.<ul><li>nem tartalmazza az egyszeri kulcs jogcímét vagy a tárgy jogcímét</li><li>tárgyazonosító eltérése</li><li>dupla jogcím az idToken-jogcímekben</li><li>nem várt kiállító</li><li>nem várt célközönség</li><li>nem az érvényes időtartományon belül van </li><li>a jogkivonat formátuma nem megfelelő</li><li>A kiállító külső azonosító jogkivonata nem felelt meg az aláírás-ellenőrzésen.</li></ul> |
| AADSTS50029 | Érvénytelen URI – a tartománynév érvénytelen karaktert tartalmaz. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50032 | WeakRsaKey-잘못 된 사용자가 약한 RSA 키를 사용 하려고 시도 했음을 나타냅니다. |
| AADSTS50033 | RetryableError-데이터베이스 작업과 관련이 없는 일시적인 오류를 나타냅니다. |
| AADSTS50034 | UserAccountNotFound-이 응용 프로그램에 로그인 하려면 계정을 디렉터리에 추가 해야 합니다. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt-쌍으로 된 식별자를 생성 하는 데 필요한 솔트가 원칙에 없습니다. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer-클라이언트 어설션의 발급자 클레임을 일치 하지 않습니다. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50049 | NoSuchInstanceForDiscovery-알 수 없거나 잘못 된 인스턴스입니다. |
| AADSTS50050 | MalformedDiscoveryRequest-요청의 형식이 잘못 되었습니다. |
| AADSTS50053 | IdsLocked-사용자가 잘못 된 사용자 ID 또는 암호로 로그인을 너무 많이 시도 하 여 계정이 잠겼습니다. |
| AADSTS50055 | InvalidPasswordExpiredPassword-암호가 만료 되었습니다. |
| AADSTS50056 | 이 사용자의 저장소에 잘못 된 암호나 null 암호가 있습니다. |
| AADSTS50057 | UserDisabled-사용자 계정을 사용할 수 없습니다. Egy rendszergazda letiltotta a fiókot. |
| AADSTS50058 | UserInformationNotProvided-사용자가 로그인 하지 않았음을 의미 합니다. 이 오류는 사용자가 인증 되지 않고 아직 로그인 하지 않은 경우에 예상 되는 일반적인 오류입니다.</br>사용자가 이전에 로그인 한 SSO 컨텍스트에서이 오류가 발생 하는 경우 SSO 세션이 없거나 잘못 되었음을 의미 합니다.</br>이 오류는 prompt = none이 지정 된 경우 응용 프로그램에 반환 될 수 있습니다. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided-요청 또는 제공 된 자격 증명에 포함 된 자격 증명에 포함 된 정보를 찾을 수 없습니다. 사용자는 테 넌 트 관리자에 게 연락 하 여 문제를 해결할 수 있습니다. |
| AADSTS50061 | SignoutInvalidRequest-로그 아웃 요청이 잘못 되었습니다. |
| AADSTS50064 | CredentialAuthenticationError-사용자 이름 또는 암호에 대 한 자격 증명 유효성 검사에 실패 했습니다. |
| AADSTS50068 | SignoutInitiatorNotParticipant-Signout에 실패 했습니다. Signout를 시작한 앱은 현재 세션의 참가자가 아닙니다. |
| AADSTS50070 | SignoutUnknownSessionIdentifier-Signout에 실패 했습니다. Signout 요청에서 지정 된 이름 식별자가 기존 세션과 일치 하지 않습니다. |
| AADSTS50071 | SignoutMessageExpired-로그 아웃 요청이 만료 되었습니다. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt-사용자는 두 번째 단계 인증 (대화형)을 등록 해야 합니다. |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt-강력한 인증이 필요 하며 사용자는 MFA 챌린지를 통과 하지 못했습니다. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired-관리자의 구성 변경으로 인해 또는 새 위치로 이동 했기 때문에 사용자는 multi-factor authentication을 사용 하 여 리소스에 액세스 해야 합니다. 리소스에 대 한 새 권한 부여 요청을 다시 시도 합니다. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired-관리자의 구성 변경으로 인해 또는 사용자가 새 위치로 이동 했기 때문에 사용자는 multi-factor authentication을 사용 해야 합니다. |
| AADSTS50085 | A frissítési jogkivonathoz közösségi identitásszolgáltatói bejelentkezés szükséges. A felhasználónak újra be kell jelentkeznie a felhasználónévvel és jelszóval |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError-서비스를 일시적으로 사용할 수 없습니다. 다시 한 번 시도해 보세요. |
| AADSTS50089 | A folyamat jogkivonata lejárt – a hitelesítés nem sikerült. 사용자가 사용자 이름-암호를 사용 하 여 다시 로그인을 시도 하도록 합니다. |
| AADSTS50097 | DeviceAuthenticationRequired-장치 인증이 필요 합니다. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized-JWT 서명이 잘못 되었습니다. |
| AADSTS50105 | EntitlementGrantsNotFound-로그인 한 사용자가 로그인 한 앱에 대 한 역할에 할당 되지 않았습니다. 사용자를 앱에 할당 합니다. 자세한 내용은를[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri-요청 된 페더레이션 영역 개체가 없습니다. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat-JWT 헤더에 문제가 있습니다. Lépjen kapcsolatba a bérlő rendszergazdájával. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter-클레임 변환에 잘못 된 입력 매개 변수가 있습니다. Lépjen kapcsolatba a bérlő rendszergazdájával a szabályzat frissítése érdekében. |
| AADSTS50125 | 암호 재설정 또는 암호 등록 항목으로 인해 PasswordResetRegistrationRequiredInterrupt-로그인이 중단 되었습니다. |
| AADSTS50126 | InvalidUserNameOrPassword-사용자 이름 또는 암호가 잘못 되어 자격 증명의 유효성을 검사 하는 동안 오류가 발생 했습니다. |
| AADSTS50127 | BrokerAppNotInstalled-사용자가이 콘텐츠에 대 한 액세스 권한을 얻으려면 broker 앱을 설치 해야 합니다. |
| AADSTS50128 | 도메인 이름이 잘못 되었습니다. 요청에 없거나 제공 된 자격 증명으로 암시 된 테 넌 트를 식별 하는 정보가 없습니다. |
| AADSTS50129 | 장치를 등록 하려면 DeviceIsNotWorkplaceJoined-작업 공간 조인이 필요 합니다. |
| AADSTS50131 | ConditionalAccessFailed-잘못 된 Windows 장치 상태, 의심 스러운 활동, 액세스 정책 또는 보안 정책 결정으로 인해 차단 된 요청 등 다양 한 조건부 액세스 오류를 나타냅니다. |
| AADSTS50132 | SsoArtifactInvalidOrExpired-암호가 만료 되었거나 최근 암호가 변경 되어 세션이 유효 하지 않습니다. |
| AADSTS50133 | SsoArtifactRevoked-암호가 만료 되었거나 최근 암호가 변경 되어 세션이 유효 하지 않습니다. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter-잘못 된 데이터 센터입니다. OAuth 2.0 장치 흐름에서 앱에 의해 시작 된 요청에 권한을 부여 하려면 권한 부여 당사자가 원래 요청이 있는 동일한 데이터 센터에 있어야 합니다. |
| AADSTS50135 | PasswordChangeCompromisedPassword-계정 위험 때문에 암호 변경이 필요 합니다. |
| AADSTS50136 | RedirectMsaSessionToApp-단일 MSA 세션이 검색 되었습니다. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken-외부 새로 고침 토큰이 누락 되어 세션이 잘못 되었습니다. |
| AADSTS50140 | KmsiInterrupt-사용자가 로그인 할 때 "로그인 유지" 중단으로 인해이 오류가 발생 했습니다. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a korrelációs azonosítóval, a kérelemazonosítóval és a hibakóddal a részletek megismeréséhez. |
| AADSTS50143 | 세션 불일치-사용자 테 넌 트가 다른 리소스로 인해 도메인 힌트와 일치 하지 않아 세션이 잘못 되었습니다. 세부 정보를 보려면 상관 관계 ID, 요청 ID 및 오류 코드를 사용 하 여  [지원 티켓을 엽니다](../fundamentals/active-directory-troubleshooting-support-howto.md) . |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword-사용자의 Active Directory 암호가 만료 되었습니다. 사용자에 대 한 새 암호를 생성 하거나 사용자가 셀프 서비스 다시 설정 도구를 사용 하 여 암호를 재설정 하도록 합니다. |
| AADSTS50146 | MissingCustomSigningKey-이 앱은 앱 별 서명 키로 구성 해야 합니다. Az alkalmazás nem ilyennel van konfigurálva, vagy a kulcs lejárt vagy még nem érvényes. |
| AADSTS50147 | MissingCodeChallenge-코드 챌린지 매개 변수의 크기가 잘못 되었습니다. |
| AADSTS50155 | DeviceAuthenticationFailed-이 사용자에 대 한 장치 인증에 실패 했습니다. |
| AADSTS50158 | ExternalSecurityChallenge-외부 보안 챌린지가 충족 되지 않았습니다. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration-외부 공급자가 보낸 클레임이 외부 공급자에 요청 된 클레임 또는 누락 된 클레임입니다. |
| AADSTS50166 | ExternalClaimsProviderThrottled-클레임 공급자에 게 요청을 보내지 못했습니다. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired-클라이언트가 Windows 10 계정 확장을 통해 SSO 토큰을 가져올 수 있지만 토큰을 요청에서 찾을 수 없거나 제공 된 토큰이 만료 되었습니다. |
| AADSTS50169 | InvalidRequestBadRealm-영역이 현재 서비스 네임 스페이스의 구성 된 영역이 아닙니다. |
| AADSTS50170 | MissingExternalClaimsProviderMapping-외부 컨트롤 매핑이 없습니다. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers-통과 사용자는 외부 챌린지를 지원 하지 않습니다. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers-세션 제어가 통과 사용자에 대해 지원 되지 않습니다. |
| AADSTS50180 | WindowsIntegratedAuthMissing-Windows 통합 인증이 필요 합니다. Engedélyezze a Seamless SSO-t a bérlőn. |
| AADSTS50187 | DeviceInformationNotProvided-서비스에서 장치 인증을 수행 하지 못했습니다. |
| AADSTS51000 | RequiredFeatureNotEnabled-기능을 사용할 수 없습니다. |
| AADSTS51001 | DomainHintMustbePresent-온-프레미스 보안 식별자 또는 온-프레미스 UPN에는 도메인 힌트가 있어야 합니다. |
| AADSTS51004 | UserAccountNotInDirectory-사용자 계정이 디렉터리에 없습니다. |
| AADSTS51005 | TemporaryRedirect-요청 된 정보가 location 헤더에 지정 된 URI에 있음을 나타내는 HTTP 상태 307에 해당 합니다. 이 상태가 표시 되 면 응답과 연결 된 location 헤더를 따릅니다. 원래의 요청 메서드가 POST 인 경우 리디렉션된 요청도 POST 메서드를 사용 됩니다. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth-Windows 통합 인증이 필요 합니다. 사용자가 Windows 통합 인증 클레임이 없는 세션 토큰을 사용 하 여 로그인 했습니다. 사용자에 게 다시 로그인 하도록 요청 합니다. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn-사용자가 LinkedIn 리소스에 대 한 액세스에 대 한 동의를 제공 하지 않았습니다. |
| AADSTS53000 | DeviceNotCompliant-조건부 액세스 정책에는 준수 장치가 필요 하며 장치는 호환 되지 않습니다. 사용자는 Intune과 같은 승인 된 MDM 공급자를 사용 하 여 장치를 등록 해야 합니다. |
| AADSTS53001 | DeviceNotDomainJoined-조건부 액세스 정책에 도메인 가입 장치가 필요 하며 장치가 도메인에 가입 되어 있지 않습니다. 사용자가 도메인에 가입 된 장치를 사용 하도록 합니다. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp-사용 되는 앱은 조건부 액세스에 대해 승인 된 앱이 아닙니다. 사용자는 액세스 권한을 얻기 위해 승인 된 앱 목록에서 앱 중 하나를 사용 해야 합니다. |
| AADSTS53003 | BlockedByConditionalAccess-조건부 액세스 정책에 의해 액세스가 차단 되었습니다. 액세스 정책에서 토큰 발급을 허용 하지 않습니다. |
| AADSTS53004 | ProofUpBlockedDueToRisk-사용자가이 콘텐츠에 액세스 하기 전에 multi-factor authentication 등록 프로세스를 완료 해야 합니다. A felhasználónak regisztrálnia kell többtényezős hitelesítésre. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist-사용자 또는 관리자는 ID가 X 인 응용 프로그램을 사용 하도록 동의한 하지 않았습니다 .이 사용자 및 리소스에 대 한 대화형 권한 부여 요청을 보내세요. |
| AADSTS65004 | UserDeclinedConsent-사용자가 앱에 대 한 액세스를 승인 하지 않았습니다. Kérje meg a felhasználót, hogy jelentkezzen be újra, és hagyja jóvá az alkalmazást|
| AADSTS65005 | MisconfiguredApplication-앱에 필요한 리소스 액세스 목록에 리소스에서 검색 가능한 앱이 포함 되어 있지 않거나, 클라이언트 앱이 리소스에 대 한 액세스를 요청 했습니다. 필요한 리소스 액세스 목록에 지정 되지 않았거나 Graph 서비스에서 잘못 된 기능을 반환 했습니다. 요청 또는 리소스를 찾을 수 없습니다. 앱이 SAML을 지 원하는 경우 잘못 된 식별자 (엔터티)를 사용 하 여 앱을 구성 했을 수 있습니다. Próbálja ki az SAML-lel kapcsolatos megoldásokat, amelyek a következő hivatkozáson találhatók: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant-인증에 실패 했습니다. 새로 고침 토큰이 잘못 되었습니다. 오류는 다음과 같은 이유로 인해 발생할 수 있습니다.<ul><li>토큰 바인딩 헤더가 비어 있습니다.</li><li>토큰 바인딩 해시가 일치 하지 않습니다.</li></ul> |
| AADSTS70001 | UnauthorizedClient-응용 프로그램을 사용할 수 없습니다. |
| AADSTS70002 | InvalidClient-자격 증명의 유효성을 검사 하는 동안 오류가 발생 했습니다. 지정 된 client_secret이이 클라이언트에 대 한 예상 값과 일치 하지 않습니다. Client_secret를 수정 하 고 다시 시도 하세요. 자세한 내용은 [권한 부여 코드를 사용 하 여 액세스 토큰 요청](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)을 참조 하세요. |
| AADSTS70003 | UnsupportedGrantType-앱에서 지원 되지 않는 권한 유형을 반환 했습니다. |
| AADSTS70004 | InvalidRedirectUri-앱에서 잘못 된 리디렉션 URI를 반환 했습니다. Az ügyfél által megadott átirányítási cím nem egyezik meg egyik konfigurált címmel sem, illetve az OIDC jóváhagyási listán szereplő címekkel sem. |
| AADSTS70005 | UnsupportedResponseType-다음 이유로 인해 앱에서 지원 되지 않는 응답 유형을 반환 했습니다.<ul><li>응용 프로그램에 대해 ' token ' 응답 유형을 사용할 수 없습니다.</li><li>Az id_token típusú válaszhoz az OpenID hatókör szükséges – a kódolt wctx nem támogatott OAuth paraméterértéket tartalmaz</li></ul> |
| AADSTS70007 | UnsupportedResponseMode-토큰을 요청할 때 앱에서 지원 되지 않는 `response_mode` 값을 반환 했습니다.  |
| AADSTS70008 | ExpiredOrRevokedGrant-비활성화 되어 새로 고침 토큰이 만료 되었습니다. 토큰은 XXX에서 발급 되었으며 특정 시간 동안 비활성 상태 였습니다. |
| AADSTS70011 | InvalidScope-앱에서 요청 된 범위가 잘못 되었습니다. |
| AADSTS70012 | MsaServerError-MSA (소비자) 사용자를 인증 하는 동안 서버 오류가 발생 했습니다. 다시 한 번 시도해 보세요. 계속 실패 하 [는 경우 지원 티켓을 엽니다](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending-OAuth 2.0 장치 흐름 오류입니다. 권한 부여가 보류 중입니다. 장치에서 요청 폴링을 다시 시도 합니다. |
| AADSTS70018 | BadVerificationCode-사용자가 장치 코드 흐름에 대해 잘못 된 사용자 코드를 입력 하기 때문에 잘못 된 확인 코드입니다. 승인이 승인 되지 않았습니다. |
| AADSTS70019 | CodeExpired 됨-확인 코드가 만료 되었습니다. 사용자가 로그인을 다시 시도 하도록 합니다. |
| AADSTS75001 | BindingSerializationError-SAML 메시지를 바인딩하는 동안 오류가 발생 했습니다. |
| AADSTS75003 | UnsupportedBindingError-앱에서 지원 되지 않는 바인딩과 관련 된 오류를 반환 했습니다 (HTTP POST 이외의 바인딩을 통해 SAML 프로토콜 응답을 보낼 수 없음). |
| AADSTS75005 | Saml2MessageInvalid-Azure AD는 SSO 용 앱에서 보낸 SAML 요청을 지원 하지 않습니다. |
| AADSTS75008 | RequestDeniedError-SAML 요청에 예기치 않은 대상이 있으므로 앱의 요청이 거부 되었습니다. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims-서비스에 인증 된 사용자가 요청한 인증 방법과 일치 하지 않는 인증 방법입니다. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy-SAML2 Authentication 요청에 잘못 된 NameIdPolicy가 있습니다. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable-인증 에이전트가 Active Directory에 연결할 수 없습니다. 에이전트 서버가 암호의 유효성을 검사 해야 하 고 Active Directory에 연결할 수 있는 사용자와 동일한 AD 포리스트의 구성원 인지 확인 합니다. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout-암호 유효성 검사 요청 시간이 초과 되었습니다. Active Directory 사용 가능 하 고 에이전트의 요청에 응답 하는지 확인 합니다. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException-인증 에이전트에서 응답을 처리 하는 동안 알 수 없는 오류가 발생 했습니다. 요청을 다시 시도 하세요. 그래도 오류가 계속 발생 하면 [지원 티켓을 열어](../fundamentals/active-directory-troubleshooting-support-howto.md) 오류에 대 한 자세한 정보를 확인 하세요. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem-인증 에이전트가 사용자 암호의 유효성을 검사할 수 없습니다. 에이전트 로그에서 자세한 정보를 확인 하 고 Active Directory 정상적으로 작동 하는지 확인 합니다. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException-인증 에이전트가 암호를 해독할 수 없습니다. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours-사용자가 허용 된 시간 (AD에 지정 된 시간)을 벗어나 로그온을 시도 했습니다. |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew-인증 에이전트와 AD를 실행 하는 컴퓨터 간의 시간차로 인해 인증 시도를 완료할 수 없습니다. 시간 동기화 문제를 해결 합니다. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated-Kerberos 인증 시도가 실패 했습니다. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported-인증 패키지가 지원 되지 않습니다. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader-인증 헤더를 찾을 수 없습니다. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn-테 넌 트가 원활한 SSO를 사용 하도록 설정 되어 있지 않습니다. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat-사용자의 Kerberos 티켓에 대 한 유효성을 검사할 수 없습니다. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid-사용자의 Kerberos 티켓이 만료 되었거나 잘못 되어 원활한 SSO가 실패 했습니다. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed-사용자의 Kerberos 티켓 정보에 기반 하 여 사용자 개체를 찾을 수 없습니다. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn-Azure AD에 로그인 하려는 사용자가 장치에 로그인 한 사용자와 다릅니다. |
| AADSTS90002 | InvalidTenantName-데이터 저장소에서 테 넌 트 이름을 찾을 수 없습니다. 올바른 테 넌 트 ID가 있는지 확인 합니다. |
| AADSTS90004 | InvalidRequestFormat-요청의 형식이 잘못 되었습니다. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements-요청을 완료할 수 없습니다. 식별자와 로그인 힌트를 함께 사용할 수 없으므로 요청이 유효 하지 않습니다.  |
| AADSTS90006 | ExternalServerRetryableError-서비스를 일시적으로 사용할 수 없습니다.|
| AADSTS90007 | InvalidSessionId-잘못 된 요청입니다. 전달 된 세션 ID를 구문 분석할 수 없습니다. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission-사용자 또는 관리자가 응용 프로그램을 사용 하도록 동의한 하지 않았습니다. 최소한 응용 프로그램은 로그인 및 사용자 프로필 읽기 권한을 지정 하 여 Azure AD에 액세스 해야 합니다. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier-응용 프로그램이 자체에 대 한 토큰을 요청 하 고 있습니다. 이 시나리오는 지정 된 리소스가 GUID 기반 응용 프로그램 ID를 사용 하는 경우에만 지원 됩니다. |
| AADSTS90010 | NotSupported-알고리즘을 만들 수 없습니다. |
| AADSTS90012 | RequestTimeout-요청 시간이 초과 되었습니다. |
| AADSTS90013 | InvalidUserInput-사용자의 입력이 잘못 되었습니다. |
| AADSTS90014 | MissingRequiredField-이 오류 코드는 필요한 필드가 자격 증명에 없는 경우 다양 한 경우에 나타날 수 있습니다. |
| AADSTS90015 | QueryStringTooLong-쿼리 문자열이 너무 깁니다. |
| AADSTS90016 | MissingRequiredClaim-액세스 토큰이 유효 하지 않습니다. 필요한 클레임이 누락 되었습니다. |
| AADSTS90019 | MissingTenantRealm-Azure AD가 요청에서 테 넌 트 식별자를 확인할 수 없습니다. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat-보안 주체 이름 형식이 잘못 되었거나 예상 되는 `name[/host][@realm]` 형식을 충족 하지 않습니다. 사용자 이름은 필수 이며, 호스트 및 영역은 선택 사항이 며 null로 설정할 수 있습니다. |
| AADSTS90023 | InvalidRequest-인증 서비스 요청이 잘못 되었습니다. |
| AADSTS9002313 | InvalidRequest-요청의 형식이 잘못 되었거나 잘못 되었습니다. -특정 끝점에 대 한 요청에 잘못 된 문제가 있기 때문입니다. 이 문제에 대 한 제안 사항은 발생 한 오류에 대 한 fiddler 추적을 가져오고 요청이 실제로 올바른 형식 인지 여부를 확인 하는 것입니다. |
| AADSTS90024 | RequestBudgetExceededError-일시적인 오류가 발생 했습니다. 다시 한 번 시도해 보세요. |
| AADSTS90033 | MsodsServiceUnavailable 수 없음-MSODS (Microsoft Online Directory Service)를 사용할 수 없습니다. |
| AADSTS90036 | MsodsServiceUnretryableFailure-MSODS에서 호스트 하는 WCF 서비스에서 재시도 불가능 한 예기치 않은 오류가 발생 했습니다. [Küldjön támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a hiba részleteinek megismeréséhez |
| AADSTS90038 | NationalCloudTenantRedirection-지정 된 테 넌 트 ' Y '는 국가별 클라우드 ' X '에 속합니다. 현재 클라우드 인스턴스 ' Z '는 X와 페더레이션 되지 않습니다. 클라우드 리디렉션 오류가 반환 됩니다. |
| AADSTS90043 | NationalCloudAuthCodeRedirection-기능을 사용할 수 없습니다. |
| AADSTS90051 | InvalidNationalCloudId-국가별 클라우드 식별자에 잘못 된 클라우드 식별자가 포함 되어 있습니다. |
| AADSTS90055 | TenantThrottlingError-들어오는 요청이 너무 많습니다. 이 예외는 차단 된 테 넌 트에 대해 throw 됩니다. |
| AADSTS90056 | BadResourceRequest-액세스 토큰에 대 한 코드를 사용 하려면 앱이 `/token` 끝점에 POST 요청을 보내야 합니다. 또한 이전에는 인증 코드를 제공 하 고 `/token` 끝점에 대 한 POST 요청에서 전송 해야 합니다. OAuth 2.0 인증 코드 흐름: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)의 개요는이 문서를 참조 하세요. Authorization_code을 반환 하는 `/authorize` 끝점으로 사용자를 보냅니다. `/token` 끝점에 요청을 게시 하면 사용자가 액세스 토큰을 가져옵니다. Azure Portal에 로그인 하 고 **앱 등록 > 끝점** 을 검사 하 여 두 끝점이 올바르게 구성 되었는지 확인 합니다. |
| AADSTS90072 | PassThroughUserMfaError-사용자가 로그인 하는 외부 계정이 로그인 한 테 넌 트에 존재 하지 않습니다. 따라서 사용자는 테 넌 트에 대 한 MFA 요구 사항을 충족할 수 없습니다. 이 계정은 먼저 테 넌 트에서 외부 사용자로 추가 해야 합니다. 로그 아웃 하 고 다른 Azure AD 사용자 계정으로 로그인 합니다. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid-서비스에서 WS-FEDERATION 메시지를 처리 하려고 할 때 오류가 발생 했습니다. 메시지가 잘못 되었습니다. |
| AADSTS90082 | OrgIdWsFederationNotSupported-요청에 대해 선택한 인증 정책이 현재 지원 되지 않습니다. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed-이 사이트에는 게스트 계정이 허용 되지 않습니다. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed-회사 개체가 아직 프로 비전 되지 않았기 때문에 서비스에서 토큰을 발급할 수 없습니다. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired-사용자 DA 토큰이 만료 되었습니다. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed-URI에서 WS-FEDERATION 메시지를 만드는 동안 오류가 발생 했습니다. |
| AADSTS90090 | GraphRetryableError-서비스를 일시적으로 사용할 수 없습니다. |
| AADSTS90091 | GraphServiceUnreachable 수 없음 |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized 없음-요청에 대해 사용할 수 없는 오류 코드로 반환 된 그래프입니다. |
| AADSTS90094 | AdminConsentRequired-관리자 동의가 필요 합니다. |
| AADSTS90100 | InvalidRequestParameter-매개 변수가 비어 있거나 잘못 되었습니다. |
| AADSTS901002 | AADSTS901002: ' resource ' 요청 매개 변수는 지원 되지 않습니다. |
| AADSTS90101 | InvalidEmailAddress-제공 된 데이터가 유효한 전자 메일 주소가 아닙니다. 전자 메일 주소는 `someone@example.com`형식 이어야 합니다. |
| AADSTS90102 | InvalidUriParameter-값은 올바른 절대 URI 여야 합니다. |
| AADSTS90107 | InvalidXml-요청이 잘못 되었습니다. 데이터에 잘못 된 문자가 없는지 확인 합니다.|
| AADSTS90114 | InvalidExpiryDate-대량 토큰 만료 타임 스탬프로 인해 만료 된 토큰이 발급 됩니다. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode-사용자 코드가 null 이거나 비어 있습니다.|
| AADSTS90120 | InvalidDeviceFlowRequest-요청이 이미 승인 되었거나 거부 되었습니다. |
| AADSTS90121 | InvalidEmptyRequest-빈 요청이 잘못 되었습니다.|
| AADSTS90123 | IdentityProviderAccessDenied-id 또는 클레임 발급 공급자가 요청을 거부 했기 때문에 토큰을 발급할 수 없습니다. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported-리소스는 `/common` 또는 `/consumers` 끝점을 통해 지원 되지 않습니다. 대신 `/organizations` 또는 테 넌 트 별 끝점을 사용 합니다. |
| AADSTS90125 | DebugModeEnrollTenantNotFound-사용자가 시스템에 있지 않습니다. 사용자 이름을 올바르게 입력 했는지 확인 합니다. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred-사용자 형식이이 끝점에서 지원 되지 않습니다. 시스템이 사용자 이름에서 사용자의 테 넌 트를 유추할 수 없습니다. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported-`/common` 또는 `/consumers` 끝점에서 응용 프로그램이 지원 되지 않습니다. 대신 `/organizations` 또는 테 넌 트 별 끝점을 사용 합니다. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated-다시 시도 불가능 한 오류가 발생 했습니다.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound-사용자 보안 주체에 게 구성 된 NGC ID 키가 없습니다. |
| AADSTS130005 | NgcInvalidSignature-NGC 키 서명을 확인 하지 못했습니다.|
| AADSTS130006 | NgcTransportKeyNotFound-장치에 NGC 전송 키가 구성 되어 있지 않습니다. |
| AADSTS130007 | NgcDeviceIsDisabled-장치를 사용할 수 없습니다. |
| AADSTS130008 | NgcDeviceIsNotFound-NGC 키에서 참조 하는 장치를 찾을 수 없습니다. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce-nonce 요청은 제공 되지 않습니다. |
| AADSTS140001 | InvalidSessionKey-세션 키가 잘못 되었습니다.|
| AADSTS165900 | InvalidApiRequest-잘못 된 요청입니다. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion-Chrome 웹 보기 버전이 지원 되지 않습니다. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource-리소스가 장치 전용 토큰을 허용 하도록 구성 되어 있지 않습니다. |
| AADSTS240001 | BulkAADJTokenUnauthorized-사용자에 게 Azure AD에서 장치를 등록할 수 있는 권한이 없습니다. |
| AADSTS240002 | RequiredClaimIsMissing-id_token를 `urn:ietf:params:oauth:grant-type:jwt-bearer` grant로 사용할 수 없습니다.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy-테 넌 트 관리자가이 요청을 차단 하는 보안 정책을 구성 했습니다. 테 넌 트 수준에 정의 된 보안 정책을 확인 하 여 요청이 정책 요구 사항을 충족 하는지 확인 합니다. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest-디렉터리/테 넌 트에서 응용 프로그램을 찾을 수 없습니다. 이는 테 넌 트의 관리자가 응용 프로그램을 설치 하지 않았거나 테 넌 트의 모든 사용자가에 동의한 하는 경우에 발생할 수 있습니다. 응용 프로그램에 대 한 식별자 값이 잘못 구성 되었거나 인증 요청을 잘못 된 테 넌 트에 게 보냈습니다. |
| AADSTS700020 | InteractionRequired-액세스 권한 부여에는 상호 작용이 필요 합니다. |
| AADSTS700022 | InvalidMultipleResourcesScope-입력 매개 변수 범위에 제공 된 값은 둘 이상의 리소스를 포함 하므로 유효 하지 않습니다. |
| AADSTS700023 | InvalidResourcelessScope-액세스 토큰을 요청할 때 입력 매개 변수 범위에 대해 제공 된 값이 유효 하지 않습니다. |
| AADSTS1000000 | UserNotBoundError-바인딩 API는 Azure AD 사용자가 아직 발생 하지 않은 외부 IDP를 사용 하 여 인증 해야 합니다. |
| AADSTS1000002 | BindCompleteInterruptError-바인딩이 성공적으로 완료 되었지만 사용자에 게 알려 주어 야 합니다. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled-응용 프로그램을 사용할 수 없습니다. |

## <a name="next-steps"></a>Következő lépések

* 궁금한 점이 있거나 원하는 항목을 찾을 수 없습니까? GitHub 문제를 만들거나 [개발자를 위한 지원 및 도움말 옵션](active-directory-develop-help-support.md) 을 참조 하 여 도움말과 지원을 받을 수 있는 다른 방법에 대해 알아보세요.
