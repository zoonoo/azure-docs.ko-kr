---
title: 사용자 지정 정책에서 Application Insights 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책에 Application Insights 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201415"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 Application Insights 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C (Azure Active Directory B2C)는 Azure AD B2C에 제공 된 계측 키를 사용 하 여 [Application Insights](../azure-monitor/app/app-insights-overview.md) 으로 직접 이벤트 데이터를 보내는 것을 지원 합니다.  Application Insights 기술 프로필을 사용 하 여 사용자 경험에 대 한 자세한 사용자 지정 이벤트 로그를 얻을 수 있습니다.

* 사용자 동작에 대한 정보를 얻습니다.
* 개발에서 또는 프로덕션 환경에서 사용자 고유의 정책 문제를 해결합니다.
* 성능을 측정합니다.
* Application Insights에서 알림을 만듭니다.


## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **Handler** 특성은 Application Insights에 대해 Azure AD B2C에서 사용 하는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함 해야 합니다.`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 일반적인 Application Insights 기술 프로필을 보여 줍니다. 기타 Application Insights 기술 프로필에는 해당 구성을 활용 하기 위한 AzureInsights-공통이 포함 됩니다.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 Application Insights 보낼 클레임 목록을 포함 합니다. Application Insights에 표시 하려는 이름으로 클레임 이름을 매핑할 수도 있습니다. 다음 예에서는 Application Insights에 원격 분석를 보내는 방법을 보여 줍니다. 이벤트의 속성은 구문을 통해 추가 됩니다 `{property:NAME}` . 여기서 NAME은 이벤트에 추가 되는 속성입니다. DefaultValue는 정적 값 이거나 지원 되는 [클레임 해결 프로그램](claim-resolver-overview.md)중 하나에서 해결 된 값일 수 있습니다.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations** 요소는 Application Insights로 보내기 전에 입력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

## <a name="persist-claims"></a>영구 클레임

PersistedClaims 요소는 사용 되지 않습니다.

## <a name="output-claims"></a>출력 클레임

OutputClaims 및 OutputClaimsTransformations 요소는 사용 되지 않습니다.

## <a name="cryptographic-keys"></a>암호화 키

CryptographicKeys 요소는 사용되지 않습니다.


## <a name="metadata"></a>메타데이터

| attribute | 필요한 공간 | 설명 |
| --------- | -------- | ----------- |
| InstrumentationKey| 예 | 이벤트를 기록 하는 데 사용 되는 Application Insights [계측 키](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)입니다. | 
| DeveloperMode| 아니요 | 개발자 모드를 사용 하는지 여부를 나타내는 부울입니다. 가능한 값: `true` 또는 `false` (기본값) 이 메타 데이터는 이벤트가 버퍼링 되는 방식을 제어 합니다. 최소 이벤트 볼륨이 있는 개발 환경에서 개발자 모드를 사용 하도록 설정 하면 이벤트가 즉시 Application Insights 전송 됩니다.|  
|DisableTelemetry |아니요 |원격 분석을 사용 하도록 설정할지 여부를 나타내는 부울입니다. 가능한 값: `true` 또는 `false` (기본값)| 


## <a name="next-steps"></a>다음 단계

- [Application Insights 리소스 만들기](../azure-monitor/app/create-new-resource.md)
- 을 [사용 하 여 Azure Active Directory B2C에서 사용자 동작을 추적](analytics-with-application-insights.md) 하는 방법을 알아봅니다 Application Insights
