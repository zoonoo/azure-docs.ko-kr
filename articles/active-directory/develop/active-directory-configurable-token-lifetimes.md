---
title: 구성 가능한 토큰 수명
titleSuffix: Microsoft identity platform
description: Microsoft id 플랫폼에서 발급 한 토큰의 수명을 설정 하는 방법에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperfq1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 4dab75a4e95a7561bc86176816cb402c10de781e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077424"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Microsoft id 플랫폼 (미리 보기)의 구성 가능한 토큰 수명

> [!IMPORTANT]
> 2021 년 1 월 30 일 후에는 테 넌 트가 더 이상 새로 고침 및 세션 토큰 수명을 구성할 수 없으며, 해당 날짜 이후의 정책에서 새로 고침 및 세션 토큰 구성을 Azure Active Directory 다시 설정 하는 것이 중지 됩니다.
>
> 사용자가 다시 로그인 하도록 요청 하는 시간 간격을 계속 정의 해야 하는 경우 조건부 액세스에서 로그인 빈도를 구성 합니다. 조건부 액세스에 대 한 자세한 내용을 보려면 [AZURE AD 가격 책정 페이지](https://azure.microsoft.com/en-us/pricing/details/active-directory/)를 방문 하세요.
>
> 사용 중지 날짜 후 조건부 액세스를 사용 하지 않으려는 테 넌 트의 경우 Azure AD가 다음 섹션에 설명 된 기본 구성을 사용할 것으로 예측할 수 있습니다.

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>사용 중지 후 구성 가능한 토큰 수명 속성
새로 고침 및 세션 토큰 구성은 다음 속성과 각각의 설정 값에 영향을 받습니다. 새로 고침 및 세션 토큰 구성이 중지 된 후에는 정책에 사용자 지정 값이 구성 되어 있는지 여부에 관계 없이 Azure AD는 아래에 설명 된 기본값을 그대로 사용 합니다. 사용 중지 후에도 액세스 토큰 수명을 구성할 수 있습니다. 

|속성   |정책 속성 문자열    |영향 |기본값 |
|----------|-----------|------------|------------|
|새로 고침 토큰 최대 비활성 시간 |MaxInactiveTime  |새로 고침 토큰 |90일  |
|단일 단계 새로 고침 토큰 최대 기간  |MaxAgeSingleFactor  |새로 고침 토큰(모든 사용자)  |Until-revoked  |
|다단계 새로 고침 토큰 최대 기간  |MaxAgeMultiFactor  |새로 고침 토큰(모든 사용자) |180일  |
|단일 단계 세션 토큰 최대 기간  |MaxAgeSessionSingleFactor |세션 토큰(영구 및 비영구)  |Until-revoked |
|다단계 세션 토큰 최대 기간  |MaxAgeSessionMultiFactor  |세션 토큰(영구 및 비영구)  |180일 |

## <a name="identify-configuration-in-scope-of-retirement"></a>사용 중지 범위에서 구성 식별

시작하려면 다음 단계를 수행합니다.

1. 최신 [AZURE AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드 합니다.
1. `Connect` 명령을 실행하여 Azure AD 관리자 계정에 로그인합니다. 새 세션을 시작할 때마다 이 명령을 실행합니다.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. 조직에서 만든 모든 정책을 확인 하려면 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.  위에 나열 된 기본값과 다른 정의 된 속성 값이 있는 결과는 사용 중지 범위에 있습니다.

    ```powershell
    Get-AzureADPolicy -All
    ```

1. 지정 된 특정 정책에 연결 된 앱 및 서비스 주체를 확인 하려면 **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** 를 정책 id로 바꿔서 다음 [AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다. 그런 다음 조건부 액세스 로그인 빈도를 구성할 지 아니면 Azure AD 기본값을 유지할지 결정할 수 있습니다.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

테 넌 트에 새로 고침 및 세션 토큰 구성 속성에 대 한 사용자 지정 값을 정의 하는 정책이 있는 경우 이러한 정책을 위에서 설명한 기본값을 반영 하는 값으로 업데이트 하는 것이 좋습니다. 변경 내용이 없는 경우 Azure AD는 기본값을 자동으로 적용 합니다.  

## <a name="overview"></a>개요

Microsoft id 플랫폼에서 발급 한 토큰의 수명을 지정할 수 있습니다. 조직의 모든 앱, 다중 테넌트(다중 조직) 애플리케이션 또는 조직의 특정 서비스 주체에 대해 토큰 수명을 구성할 수 있습니다. 그러나 현재 [관리 되는 id 서비스 사용자](../managed-identities-azure-resources/overview.md)에 대 한 토큰 수명 구성은 지원 되지 않습니다.

Azure AD에서 정책 개체는 개별 애플리케이션 또는 조직의 모든 애플리케이션에 적용되는 규칙 집합을 나타냅니다. 각 정책 유형에는 할당된 개체에 적용되는 속성 집합이 포함된 고유의 구조가 있습니다.

조직의 기본 정책이 될 정책을 지정할 수 있습니다. 해당 정책은 더 높은 우선 순위의 정책으로 재정의되지 않는 한 조직 내의 모든 애플리케이션에 적용됩니다. 특정 애플리케이션에 정책을 할당할 수도 있습니다. 우선 순위는 정책 유형에 따라 다릅니다.

예제를 보려면 [토큰 수명을 구성 하는 방법의 예제](configure-token-lifetimes.md)를 참조 하세요.

> [!NOTE]
> 구성 가능한 토큰 수명 정책은 SharePoint Online 및 OneDrive for Business 리소스에 액세스 하는 모바일 및 데스크톱 클라이언트에만 적용 되며 웹 브라우저 세션에는 적용 되지 않습니다.
> SharePoint Online 및 OneDrive for Business에 대 한 웹 브라우저 세션의 수명을 관리 하려면 [조건부 액세스 세션 수명](../conditional-access/howto-conditional-access-session-lifetime.md) 기능을 사용 합니다. 유휴 세션 제한 시간을 구성하는 방법에 대한 자세한 내용은 [SharePoint Online 블로그](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)를 참조하세요.

## <a name="token-types"></a>토큰 형식

새로 고침 토큰, 액세스 토큰, SAML 토큰, 세션 토큰 및 ID 토큰에 대 한 토큰 수명 정책을 설정할 수 있습니다.

### <a name="access-tokens"></a>액세스 토큰

클라이언트는 액세스 토큰을 사용하여 보호된 리소스에 액세스합니다. 액세스 토큰은 사용자, 클라이언트 및 리소스의 특정 조합에만 사용할 수 있습니다. 액세스 토큰은 해지될 수 없으며 만료될 때까지 유효합니다. 액세스 토큰을 획득한 악의적인 행위자는 수명 범위 동안 이를 사용할 수 있습니다. 액세스 토큰의 수명을 조정하는 것은 시스템 성능을 개선하는 것과 사용자 계정이 비활성화된 후 클라이언트에서 액세스를 유지하는 기간을 늘리는 것 간의 절충 작업입니다. 시스템 성능을 개선하려면 클라이언트에서 새 액세스 토큰을 획득해야 하는 횟수를 줄이면 됩니다.  기본값은 1시간입니다. 1시간 후 클라이언트는 새로 고침 토큰을 사용하여 새로운 새로 고침 토큰을 획득(보통 자동으로)하고 토큰에 액세스해야 합니다. 

### <a name="saml-tokens"></a>SAML 토큰

SAML 토큰은 많은 웹 기반 SAAS 응용 프로그램에서 사용 되며 Azure Active Directory의 SAML2 프로토콜 끝점을 사용 하 여 가져옵니다. WS-FEDERATION을 사용 하는 응용 프로그램 에서도 사용 됩니다. 토큰의 기본 수명은 1 시간입니다. 응용 프로그램의 관점에서 토큰의 유효 기간은 토큰의 요소에 대 한 NotOnOrAfter 값으로 지정 됩니다 `<conditions …>` . 토큰의 유효 기간이 끝난 후 클라이언트는 새 인증 요청을 시작 해야 합니다 .이 요청은 SSO (Single Sign On) 세션 토큰의 결과로 대화형 로그인 없이 자주 충족 됩니다.

NotOnOrAfter의 값은의 매개 변수를 사용 하 여 변경할 수 있습니다 `AccessTokenLifetime` `TokenLifetimePolicy` . 정책에 구성 된 수명 및 5 분의 클록 오차 계수로 설정 됩니다.

요소에 지정 된 주체 확인 NotOnOrAfter는 `<SubjectConfirmationData>` 토큰 수명 구성의 영향을 받지 않습니다. 

### <a name="refresh-tokens"></a>새로 고침 토큰

클라이언트가 보호된 리소스에 액세스하기 위해 액세스 토큰을 획득할 때 새로 고침 토큰도 받습니다. 새로 고침 토큰은 현재 액세스 토큰이 만료된 경우 새 액세스/새로 고침 토큰 쌍을 얻는 데 사용됩니다. 새로 고침 토큰은 사용자와 클라이언트 조합에 바인딩됩니다. 새로 고침 토큰은 [언제든지 해지 가능](access-tokens.md#token-revocation)하며 사용될 때마다 토큰의 유효성이 검사됩니다.  새로 고침 토큰이 새 액세스 토큰을 인출하는 데 사용된 경우 해지하지 못합니다. 따라서 새 토큰을 가져올 때 이전 토큰을 안전하게 삭제하는 것이 가장 좋습니다.

새로 고침 토큰을 사용할 수 있는 기간에 영향을 미치기 때문에 비밀 클라이언트와 공용 클라이언트를 구분하는 것이 중요합니다. 다양한 유형의 클라이언트에 대한 자세한 내용은 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)를 참조하세요.

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>비밀 클라이언트 새로 고침 토큰의 토큰 수명
비밀 클라이언트는 클라이언트 암호(비밀)를 안전하게 저장할 수 있는 애플리케이션입니다. 비밀 클라이언트는 요청이 악의적인 작업자가 아닌 안전한 클라이언트 애플리케이션에서 오는 것을 증명할 수 있습니다. 예를 들어 웹앱은 웹 서버에 클라이언트 비밀을 저장할 수 있으므로 비밀 클라이언트입니다. 따라서 노출되지 않습니다. 이러한 흐름은 보다 안전하기 때문에 이러한 흐름에 발급된 새로 고침 토큰의 기본 수명은 `until-revoked`이며 정책을 사용하여 변경할 수 없고 자발적인 암호 재설정에서 취소되지 않습니다.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>공용 클라이언트 새로 고침 토큰의 토큰 수명

공용 클라이언트는 클라이언트 암호(비밀)를 안전하게 저장할 수 없습니다. 예를 들어 iOS/Android 앱은 리소스 소유자의 비밀을 난독 처리할 수 없으므로 공용 클라이언트로 간주됩니다. 리소스에 정책을 설정하여 지정된 기간보다 오래된 공용 클라이언트의 새로 고침 토큰이 새 액세스/새로 고침 토큰 쌍을 얻지 못하게 할 수 있습니다. 이렇게 하려면 [새로 고침 토큰 최대 비활성 시간 속성](#refresh-token-max-inactive-time) ()을 사용 `MaxInactiveTime` 합니다. 특정 기간이 지나면 더 이상 새로 고침 토큰이 허용되지 않도록 정책을 사용하여 기간을 설정할 수도 있습니다. 이렇게 하려면 [단일 단계 새로 고침 토큰 최대 기간](#single-factor-session-token-max-age) 또는 [다단계 세션 토큰 최대 기간](#multi-factor-refresh-token-max-age) 속성을 사용 합니다. 새로 고침 토큰의 수명을 조정하여 공용 클라이언트 애플리케이션을 사용할 때 자동으로 재인증되는 대신 사용자가 자격 증명을 다시 입력해야 하는 시기 및 빈도를 제어할 수 있습니다.

> [!NOTE]
> 최대 기간 속성은 단일 토큰을 사용할 수 있는 시간입니다. 

### <a name="id-tokens"></a>ID 토큰
ID 토큰은 웹 사이트 및 기본 클라이언트에 전달됩니다. ID 토큰은 사용자에 대한 프로필 정보를 포함합니다. ID 토큰은 사용자와 클라이언트의 특정 조합에 바인딩되며, ID 토큰은 만료될 때까지 유효한 것으로 간주됩니다. 일반적으로 웹 애플리케이션은 애플리케이션의 사용자 세션 수명을 해당 사용자에 대해 발급된 ID 토큰의 수명과 일치시킵니다. ID 토큰의 수명을 조정 하 여 웹 응용 프로그램이 응용 프로그램 세션을 만료 하는 빈도 및 사용자가 자동 또는 대화형으로 Microsoft id 플랫폼을 사용 하 여 다시 인증 해야 하는 빈도를 제어할 수 있습니다.

### <a name="single-sign-on-session-tokens"></a>Single Sign-On 세션 토큰
사용자가 Microsoft id 플랫폼을 사용 하 여 인증 하는 경우 사용자의 브라우저 및 Microsoft id 플랫폼을 사용 하 여 SSO (Single Sign-On 세션)가 설정 됩니다. 쿠키 형식의 SSO 토큰은 이 세션을 나타냅니다. SSO 세션 토큰은 특정 리소스/클라이언트 응용 프로그램에 바인딩되지 않습니다. SSO 세션 토큰은 해지 가능하며 사용될 때마다 토큰의 유효성이 검사됩니다.

Microsoft id 플랫폼은 영구 및 비영구의 두 가지 SSO 세션 토큰을 사용 합니다. 영구 세션 토큰은 브라우저에 영구 쿠키로 저장됩니다. 비영구 세션 토큰은 세션 쿠키로 저장됩니다. 세션 쿠키는 브라우저를 닫을 때 삭제 됩니다. 일반적으로 비영구 세션 토큰이 저장 됩니다. 그러나 사용자가 인증 중에 **로그인 유지** 확인란을 선택하면 영구 세션 토큰이 저장됩니다.

비영구 세션 토큰의 수명은 24시간입니다. 영구 토큰의 수명은 90 일입니다. 유효 기간 내에 SSO 세션 토큰을 사용 하는 경우에는 유효 기간이 토큰 유형에 따라 24 시간 또는 90 일로 연장 됩니다. 유효 기간 내에 SSO 세션 토큰을 사용하지 않으면 만료된 것으로 간주하여 더 이상 허용되지 않습니다.

첫 번째 세션 토큰이 발급된 후 특정 시간이 지나면 더 이상 세션 토큰이 허용되지 않도록 정책을 사용하여 시간을 설정할 수 있습니다. 이렇게 하려면 세션 토큰 최대 기간 속성을 사용 합니다. 세션 토큰의 수명을 조정 하 여 웹 응용 프로그램을 사용할 때 자동으로 인증 되는 대신 사용자가 자격 증명을 다시 입력 해야 하는 시기와 빈도를 제어할 수 있습니다.

### <a name="token-lifetime-policy-properties"></a>토큰 수명 정책 속성
토큰 수명 정책은 토큰 수명 규칙을 포함하는 정책 개체의 형식입니다. 정책의 속성을 사용하여 지정된 토큰 수명을 제어할 수 있습니다. 설정된 정책이 없는 경우 시스템에서 기본 수명값을 적용합니다.

### <a name="configurable-token-lifetime-properties"></a>구성 가능한 토큰 수명 속성
| 속성 | 정책 속성 문자열 | 영향 | 기본값 | 최소 | 최대 |
| --- | --- | --- | --- | --- | --- |
| 액세스 토큰 수명 |AccessTokenLifetime<sup>2</sup> |액세스 토큰, ID 토큰, SAML2 토큰 |1시간 |10분 |1일 |
| 새로 고침 토큰 최대 비활성 시간 |MaxInactiveTime |새로 고침 토큰 |90일 |10분 |90일 |
| 단일 단계 새로 고침 토큰 최대 기간 |MaxAgeSingleFactor |새로 고침 토큰(모든 사용자) |Until-revoked |10분 |Until-revoked<sup>1</sup> |
| 다단계 새로 고침 토큰 최대 기간 |MaxAgeMultiFactor |새로 고침 토큰(모든 사용자) | 180일 |10분 |180일<sup>1</sup> |
| 단일 단계 세션 토큰 최대 기간 |MaxAgeSessionSingleFactor |세션 토큰(영구 및 비영구) |Until-revoked |10분 |Until-revoked<sup>1</sup> |
| 다단계 세션 토큰 최대 기간 |MaxAgeSessionMultiFactor |세션 토큰(영구 및 비영구) | 180일 |10분 | 180일<sup>1</sup> |

* <sup>1</sup>이러한 특성에 대해 설정할 수 있는 명시적인 최대 기간은 365일입니다.
* <sup>2</sup> Microsoft 팀 웹 클라이언트의 작동을 보장 하려면 Microsoft 팀에 대해 15 분 이상 AccessTokenLifetime를 유지 하는 것이 좋습니다.

### <a name="exceptions"></a>예외
| 속성 | 영향 | 기본값 |
| --- | --- | --- |
| 새로 고침 토큰 최대 기간(해지 정보가 부족한 페더레이션된 사용자에 대해 발급됨<sup>1</sup>) |새로 고침 토큰(해지 정보가 부족한 페더레이션된 사용자에 대해 발급됨<sup>1</sup>) |12시간 |
| 새로 고침 토큰 최대 비활성 시간(비밀 클라이언트에 대해 발급됨) |새로 고침 토큰(비밀 클라이언트에 대해 발급됨) |90일 |
| 새로 고침 토큰 최대 기간(비밀 클라이언트에 대해 발급됨) |새로 고침 토큰(비밀 클라이언트에 대해 발급됨) |Until-revoked |

* <sup>1</sup> 해지 정보가 충분 하지 않은 페더레이션된 사용자에 게는 "LastPasswordChangeTimestamp" 특성이 동기화 되지 않은 모든 사용자가 포함 됩니다. Azure Active Directory에서 이전 자격 증명 (예: 변경 된 암호)에 연결 된 토큰을 해지 하는 시기를 확인할 수 없으며, 사용자 및 연결 된 토큰이 여전히 양호한 상태 인지 확인 하기 위해 더 자주 다시 확인 해야 하기 때문에 이러한 사용자에 게는이 짧은 최대 나이가 제공 됩니다. 이 환경을 개선 하기 위해 테 넌 트 관리자는 "LastPasswordChangeTimestamp" 특성을 동기화 하 고 있는지 확인 해야 합니다 (PowerShell 또는 AADSync를 사용 하 여 사용자 개체에 설정할 수 있음).

### <a name="policy-evaluation-and-prioritization"></a>정책 평가 및 우선 순위 지정
토큰 수명 정책을 만들어서 특정 애플리케이션, 조직 및 서비스 주체에 할당할 수 있습니다. 특정 애플리케이션에 여러 정책이 적용될 수 있습니다. 적용되는 토큰 수명 정책은 다음 규칙을 따릅니다.

* 정책이 서비스 주체에 명시적으로 할당된 경우 해당 정책이 적용됩니다.
* 서비스 주체에 명시적으로 할당된 정책이 없는 경우 서비스 주체의 상위 조직에 명시적으로 할당된 정책이 적용됩니다.
* 서비스 주체 또는 조직에 명시적으로 할당된 정책이 없는 경우 애플리케이션에 할당된 정책이 적용됩니다.
* 서비스 주체, 조직 또는 응용 프로그램 개체에 할당 된 정책이 없는 경우 기본값이 적용 됩니다. [구성 가능한 토큰 수명 속성](#configurable-token-lifetime-properties)의 테이블을 참조하세요.

애플리케이션 개체와 서비스 주체 간의 관계에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

토큰의 유효성은 토큰이 사용되는 시점에 평가됩니다. 액세스하는 애플리케이션에 대한 우선 순위가 가장 높은 정책이 적용됩니다.

여기에 사용되는 시간 범위는 C# [TimeSpan](/dotnet/api/system.timespan) 개체 - D.HH:MM:SS에 따라 서식이 지정됩니다.  따라서 80일 및 30분은 `80.00:30:00`입니다.  앞에 오는 D는 0인 경우 삭제할 수 있으므로 90분은 `00:90:00`입니다.  

> [!NOTE]
> 다음은 예제 시나리오입니다.
>
> 한 사용자가 두 개의 웹 애플리케이션에 액세스하려고 하며 하나는 웹 애플리케이션 A이고 다른 하나는 웹 애플리케이션 B입니다.
> 
> 요소:
> * 두 웹 애플리케이션은 모두 동일한 상위 조직에 있습니다.
> * 세션 토큰 최대 기간이 8시간인 토큰 수명 정책 1은 상위 조직의 기본값으로 설정됩니다.
> * 웹 애플리케이션 A는 어떤 정책에도 연결되지 않은 일반적인 용도의 웹 애플리케이션입니다.
> * 웹 애플리케이션 B는 매우 중요한 프로세스에 사용됩니다. 서비스 주체는 세션 토큰 최대 기간이 30분인 토큰 수명 정책 2에 연결됩니다.
>
> 오후 12:00 시 사용자는 새 브라우저 세션을 시작 하 고 웹 응용 프로그램 A에 액세스 하려고 시도 합니다. 사용자가 Microsoft id 플랫폼으로 리디렉션되고 로그인 하 라는 메시지가 표시 됩니다. 그러면 세션 토큰이 있는 쿠키가 브라우저에 생성됩니다. 사용자는 애플리케이션에 액세스할 수 있는 ID 토큰으로 웹 애플리케이션 A로 다시 리디렉션됩니다.
>
> 오후 12:15 시에 사용자는 웹 응용 프로그램 B에 액세스 하려고 시도 합니다. 브라우저는 세션 쿠키를 검색 하는 Microsoft id 플랫폼으로 리디렉션됩니다. 웹 애플리케이션 B의 서비스 주체는 토큰 수명 정책 2에 연결되지만 기본 토큰 수명 정책 1이 적용되는 상위 조직의 일부이기도 합니다. 서비스 주체에 연결된 정책이 조직 기본 정책보다 우선 순위가 높기 때문에 토큰 수명 정책 2가 적용됩니다. 세션 토큰은 지난 30분 이내에 처음 발급되었기 때문에 유효한 것으로 간주됩니다. 사용자는 액세스 권한을 부여하는 ID 토큰으로 웹 애플리케이션 B로 리디렉션됩니다.
>
> 오후 1:00 시에 사용자는 웹 응용 프로그램 A에 액세스 하려고 시도 합니다. 사용자가 Microsoft id 플랫폼으로 리디렉션됩니다. 웹 애플리케이션 A는 어떤 정책에도 연결되어 있지 않지만 기본 토큰 수명 정책 1을 사용하는 조직에 있기 때문에 해당 정책이 적용됩니다. 지난 8시간 이내에 발급된 세션 쿠키가 검색됩니다. 사용자가 새 ID 토큰을 사용하여 자동으로 웹 애플리케이션 A로 다시 리디렉션됩니다. 사용자가 인증할 필요가 없습니다.
>
> 즉시 사용자는 웹 응용 프로그램 B에 액세스 하려고 합니다. 사용자가 Microsoft id 플랫폼으로 리디렉션됩니다. 이전과 마찬가지로 토큰 수명 정책 2가 적용됩니다. 토큰이 발급된 시간이 30분을 넘었기 때문에 사용자에게 로그인 자격 증명을 다시 입력하라는 메시지가 표시됩니다. 완전히 새로운 세션 토큰 및 ID 토큰이 발급됩니다. 이제 사용자는 웹 애플리케이션 B에 액세스할 수 있습니다.
>
>

## <a name="configurable-policy-property-details"></a>구성 가능한 정책 속성 세부 정보
### <a name="access-token-lifetime"></a>액세스 토큰 수명
**문자열:** AccessTokenLifetime

**영향을 줍니다.** 액세스 토큰, ID 토큰, SAML 토큰

**요약:** 이 정책은 이 리소스에 대한 액세스 및 ID 토큰이 유효한 것으로 간주되는 기간을 제어합니다. 액세스 토큰 수명 속성을 줄이면 악의적인 행위자가 액세스 토큰 또는 ID 토큰을 장시간 사용할 위험을 완화할 수 있습니다. 이러한 토큰은 취소할 수 없습니다. 즉, 토큰을 더 자주 바꾸어야 하므로 성능이 저하 됩니다.

예를 들어 [웹 로그인에 대 한 정책 만들기](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)를 참조 하세요.

### <a name="refresh-token-max-inactive-time"></a>새로 고침 토큰 최대 비활성 시간
**문자열:** MaxInactiveTime

**영향:** 새로 고침 토큰

**요약:** 이 정책은 클라이언트에서 이 리소스에 액세스하려고 할 때 새 액세스/새로 고침 토큰을 검색하는 데 새로 고침 토큰을 더 이상 사용할 수 없을 때까지의 기간을 제어합니다. 새로 고침 토큰이 사용될 때 일반적으로 새로운 새로 고침 토큰이 반환되기 때문에 클라이언트가 지정된 기간 동안 현재 새로 고침 토큰을 사용하여 리소스에 액세스하려고 시도하면 이 정책에 따라 액세스가 차단됩니다.

이 정책은 클라이언트에서 활성화되지 않은 사용자가 새로운 새로 고침 토큰을 검색하려면 다시 인증하도록 합니다.

새로 고침 토큰 최대 비활성 시간 속성은 단일 단계 토큰 최대 기간 및 다단계 새로 고침 토큰 최대 기간 속성보다 낮은 값으로 설정해야 합니다.

예제는 [WEB API를 호출 하는 네이티브 앱에 대 한 정책 만들기](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)를 참조 하세요.

### <a name="single-factor-refresh-token-max-age"></a>단일 단계 새로 고침 토큰 최대 기간
**문자열:** MaxAgeSingleFactor

**영향:** 새로 고침 토큰

**요약:** 이 정책은 사용자가 단일 단계만 사용하여 마지막으로 인증에 성공한 후 새로 고침 토큰을 사용하여 새로운 액세스/새로 고침 토큰을 얻을 수 있는 기간을 제어합니다. 사용자는 인증하여 새로운 새로 고침 토큰을 얻은 후 지정된 기간 동안 새로 고침 토큰 흐름을 사용할 수 있습니다. 이는 현재 새로 고침 토큰이 해지 되지 않고 비활성 시간 보다 오랫동안 사용 하지 않도록 남아 있는 경우에만 적용 됩니다. 이 시점에서 사용자는 다시 인증 하 여 새로운 새로 고침 토큰을 받아야 합니다.

최대 기간을 줄이면 사용자가 좀 더 자주 인증해야 합니다. 단일 단계 인증은 Multi-Factor Authentication보다 안전하지 않으므로 이 속성을 다단계 새로 고침 토큰 최대 기간 속성보다 작거나 같은 값으로 설정하는 것이 좋습니다.

예제는 [WEB API를 호출 하는 네이티브 앱에 대 한 정책 만들기](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)를 참조 하세요.

### <a name="multi-factor-refresh-token-max-age"></a>다단계 새로 고침 토큰 최대 기간
**문자열:** MaxAgeMultiFactor

**영향:** 새로 고침 토큰

**요약:** 이 정책은 사용자가 다단계를 사용하여 마지막으로 인증에 성공한 후 새로 고침 토큰을 사용하여 새로운 액세스/새로 고침 토큰을 얻을 수 있는 기간을 제어합니다. 사용자는 인증하여 새로운 새로 고침 토큰을 얻은 후 지정된 기간 동안 새로 고침 토큰 흐름을 사용할 수 있습니다. 이는 현재 새로 고침 토큰이 해지 되지 않고, 비활성 시간 보다 오랫동안 사용 되지 않는 경우에만 적용 됩니다. 이 시점에서 사용자는 다시 인증 하 여 새로운 새로 고침 토큰을 받아야 합니다.

최대 기간을 줄이면 사용자가 좀 더 자주 인증해야 합니다. 단일 단계 인증은 Multi-Factor Authentication보다 안전하지 않으므로 이 속성을 단일 단계 새로 고침 토큰 최대 기간 속성보다 크거나 같은 값으로 설정하는 것이 좋습니다.

예제는 [WEB API를 호출 하는 네이티브 앱에 대 한 정책 만들기](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api)를 참조 하세요.

### <a name="single-factor-session-token-max-age"></a>단일 단계 세션 토큰 최대 기간
**문자열:** MaxAgeSessionSingleFactor

**영향:** 세션 토큰(영구 및 비영구)

**요약:** 이 정책은 사용자가 단일 단계만 사용하여 마지막으로 인증에 성공한 후 세션 토큰을 사용하여 새로운 ID 및 세션 토큰을 얻을 수 있는 기간을 제어합니다. 사용자는 인증하여 새로운 세션 토큰을 얻은 후 지정된 기간 동안 세션 토큰 흐름을 사용할 수 있습니다. 이는 현재 세션 토큰이 해지 되지 않고 만료 되지 않은 경우에만 적용 됩니다. 지정 된 시간 후에는 사용자가 새 세션 토큰을 받기 위해 다시 인증 해야 합니다.

최대 기간을 줄이면 사용자가 좀 더 자주 인증해야 합니다. 단일 단계 인증은 Multi-Factor Authentication보다 안전하지 않으므로 이 속성을 다단계 세션 토큰 최대 기간 속성보다 작거나 같은 값으로 설정하는 것이 좋습니다.

예를 들어 [웹 로그인에 대 한 정책 만들기](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)를 참조 하세요.

### <a name="multi-factor-session-token-max-age"></a>다단계 세션 토큰 최대 기간
**문자열:** MaxAgeSessionMultiFactor

**영향:** 세션 토큰(영구 및 비영구)

**요약:** 이 정책은 사용자가 다단계를 사용하여 마지막으로 인증에 성공한 후 세션 토큰을 사용하여 새로운 ID 및 세션 토큰을 얻을 수 있는 기간을 제어합니다. 사용자는 인증하여 새로운 세션 토큰을 얻은 후 지정된 기간 동안 세션 토큰 흐름을 사용할 수 있습니다. 이는 현재 세션 토큰이 해지 되지 않고 만료 되지 않은 경우에만 적용 됩니다. 지정 된 시간 후에는 사용자가 새 세션 토큰을 받기 위해 다시 인증 해야 합니다.

최대 기간을 줄이면 사용자가 좀 더 자주 인증해야 합니다. 단일 단계 인증은 Multi-Factor Authentication보다 안전하지 않으므로 이 속성을 단일 단계 세션 토큰 최대 기간 속성보다 크거나 같은 값으로 설정하는 것이 좋습니다.

## <a name="cmdlet-reference"></a>Cmdlet 참조

[Azure Active Directory PowerShell For Graph Preview 모듈](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true)의 cmdlet입니다.

### <a name="manage-policies"></a>정책 관리

다음 cmdlet을 사용하여 정책을 관리할 수 있습니다.

| cmdlet | 설명 | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 새 정책을 만듭니다. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 모든 Azure AD 정책 또는 지정된 정책을 가져옵니다. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | 정책에 연결된 모든 앱 및 서비스 주체를 가져옵니다. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 기존 정책을 업데이트합니다. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 정책을 삭제합니다. |

### <a name="application-policies"></a>애플리케이션 정책
애플리케이션 정책에 다음 cmdlet을 사용할 수 있습니다.</br></br>

| cmdlet | 설명 | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 정책을 애플리케이션에 연결합니다. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 애플리케이션에 할당된 정책을 가져옵니다. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 애플리케이션에서 정책을 제거합니다. |

### <a name="service-principal-policies"></a>서비스 사용자 정책
서비스 주체 정책에 다음 cmdlet을 사용할 수 있습니다.

| cmdlet | 설명 | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 정책을 서비스 주체에 연결합니다. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 서비스 주체에 연결된 모든 정책을 가져옵니다.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 지정된 서비스 주체에서 정책을 제거합니다.|

## <a name="license-requirements"></a>라이선스 요구 사항

이 기능을 사용하려면 Azure AD Premium P1 라이선스가 필요합니다. 요구 사항에 맞는 적절 한 라이선스를 찾으려면 [무료 및 프리미엄 버전의 일반 공급 기능 비교](https://azure.microsoft.com/pricing/details/active-directory/)를 참조 하세요.

[Microsoft 365 Business 라이선스](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)가 있는 고객은 조건부 액세스 기능에도 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [토큰 수명을 구성 하는 방법의 예제](configure-token-lifetimes.md)를 참조 하세요.