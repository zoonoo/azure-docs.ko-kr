---
title: 사용자 지정 정책에서 애플리케이션 인사이트 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 응용 프로그램 인사이트 기술 프로필을 정의합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108576"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 응용 프로그램 인사이트 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C(Azure AD B2C)는 Azure AD B2C에 제공된 계측 키를 사용하여 [응용 프로그램 인사이트로](../azure-monitor/app/app-insights-overview.md) 직접 이벤트 데이터를 전송하는 것을 지원합니다.  Application Insights 기술 프로필을 사용하면 다음과 같은 사용자 지정 이벤트 로그를 얻을 수 있습니다.

* 사용자 동작에 대한 정보를 얻습니다.
* 개발에서 또는 프로덕션 환경에서 사용자 고유의 정책 문제를 해결합니다.
* 성능을 측정합니다.
* Application Insights에서 알림을 만듭니다.


## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **처리기** 특성은 응용 프로그램 인사이트에 대 한 Azure AD B2C에서 사용 하는 프로토콜 처리기 어셈블리의 완전 한 자격을 갖춘 된 이름을 포함 해야 합니다.`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 일반적인 응용 프로그램 인사이트 기술 프로필을 보여 주며 있습니다. 다른 응용 프로그램 인사이트 기술 프로필에는 해당 구성을 활용하는 AzureInsights-Common이 포함됩니다.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>입력 클레임

**InputClaims** 요소에는 응용 프로그램 인사이트로 보낼 클레임 목록이 포함되어 있습니다. 클레임 이름을 Application Insights에 표시하려는 이름에 매핑할 수도 있습니다. 다음 예제에서는 원격 분석을 응용 프로그램 인사이트로 보내는 방법을 보여 주었습니다. 이벤트의 속성은 NAME이 이벤트에 `{property:NAME}`추가되는 속성인 구문을 통해 추가됩니다. DefaultValue는 정적 값 또는 지원되는 [클레임 확인자](claim-resolver-overview.md)중 하나에 의해 해결되는 값일 수 있습니다.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaims변환** 요소에는 입력 클레임을 수정하거나 응용 프로그램 인사이트로 보내기 전에 새 클레임을 생성하는 데 사용되는 **입력 클레임변환** 요소의 컬렉션이 포함될 수 있습니다.

## <a name="persist-claims"></a>영구 클레임

Persisted클레임 요소는 사용되지 않습니다.

## <a name="output-claims"></a>출력 클레임

출력클레임 및 출력변환 요소는 사용되지 않습니다.

## <a name="cryptographic-keys"></a>암호화 키

CryptographicKeys 요소는 사용되지 않습니다.


## <a name="metadata"></a>메타데이터

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| InstrumentationKey| yes | 이벤트를 로깅하는 데 사용되는 응용 프로그램 인사이트 [계측 키입니다.](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key) | 
| DeveloperMode| 예 | 개발자 모드가 활성화되어 있는지 여부를 나타내는 부울입니다. 가능한 `true` 값: `false` 또는 (기본값). 이 메타데이터는 이벤트가 버퍼링되는 방법을 제어합니다. 최소한의 이벤트 볼륨을 가진 개발 환경에서는 개발자 모드를 사용하도록 설정하면 이벤트가 즉시 응용 프로그램 인사이트로 전송됩니다.|  
|텔레메트리 사용 안 함 |예 |원격 분석을 사용하도록 설정할지 여부를 나타내는 부울입니다. 가능한 `true` 값: `false` 또는 (기본값).| 


## <a name="next-steps"></a>다음 단계

- [애플리케이션 인사이트 리소스 만들기](../azure-monitor/app/create-new-resource.md)
- [응용 프로그램 통찰력을 사용하여 Azure Active Directory B2C에서 사용자 동작을 추적하는](analytics-with-application-insights.md) 방법 알아보기
