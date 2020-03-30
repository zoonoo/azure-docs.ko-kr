---
title: 애플리케이션 인사이트를 통해 사용자 지정 정책 문제 해결
titleSuffix: Azure AD B2C
description: 사용자 지정 정책의 실행을 추적하기 위해 응용 프로그램 인사이트를 설정하는 방법
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186270"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>응용 프로그램 인사이트를 통해 Azure Active Directory B2C 로그 수집

이 문서에서는 사용자 지정 정책의 문제를 진단할 수 있도록 Active Directory B2C(Azure AD B2C)에서 로그를 수집하는 단계를 제공합니다. Application Insights는 예외를 진단하고 애플리케이션 성능 문제를 시각화하는 방법을 제공합니다. Azure AD B2C에는 응용 프로그램 인사이트로 데이터를 보내는 기능이 포함되어 있습니다.

여기에 설명된 자세한 활동 로그는 사용자 지정 정책을 개발하는 **동안에만** 활성화되어야 합니다.

> [!WARNING]
> 프로덕션 환경에서 개발 모드를 활성화하지 마십시오. 로그는 ID 공급자와 주고받는 모든 클레임을 수집합니다. 개발자는 Application Insights 로그에서 수집된 모든 개인 데이터에 대해 책임을 집니다. 이러한 자세한 로그는 정책이 **개발자 모드에**배치된 경우에만 수집됩니다.

## <a name="set-up-application-insights"></a>애플리케이션 인사이트 설정

아직 없는 경우 구독에서 응용 프로그램 인사이트 인스턴스를 만듭니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음 Azure AD B2C 디렉터리가 아닌 Azure 구독이 포함된 디렉터리를 선택합니다.
1. 왼쪽 탐색 메뉴에서 **리소스 만들기를** 선택합니다.
1. 응용 프로그램 **정보를**검색하고 선택한 다음 **을**선택합니다.
1. 양식을 작성하고 **검토 + 만들기를**선택한 다음 **을 선택합니다.**
1. 배포가 완료되면 **리소스로 이동을**선택합니다.
1. 응용 프로그램 인사이트 에서 **구성** 메뉴에서 **속성을**선택합니다.
1. 이후 단계에서 사용할 **계측 키를** 기록합니다.

## <a name="configure-the-custom-policy"></a>사용자 지정 정책 구성

1. 의존 자 (RP) 파일을 엽니 다( 예: *SignUpOrSignin.xml*.
1. 다음 속성을 `<TrustFrameworkPolicy>` 요소에 추가합니다.

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. 아직 존재하지 않는 경우 노드에 `<UserJourneyBehaviors>` 자식 `<RelyingParty>` 노드를 추가합니다. 바로 그 후에 `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`위치해야 합니다.
1. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다. 이전에 기록한 `{Your Application Insights Key}` 응용 프로그램 인사이트 **계측 키로** 바꿔야 합니다.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`ApplicationInsights는 처리 파이프라인을 통해 원격 분석을 신속하게 처리하도록 지시합니다. 개발에 적합하지만 대량으로 제한됩니다.
    * `ClientEnabled="true"`페이지 뷰 및 클라이언트 측 오류를 추적하기 위해 ApplicationInsights 클라이언트 측 스크립트를 보냅니다. 응용 프로그램 인사이트 포털의 **브라우저타이밍** 테이블에서 이러한 정보를 볼 수 있습니다. 을 `ClientEnabled= "true"`설정하면 페이지 스크립트에 Application Insights를 추가하고 페이지 로드 및 AJAX 호출, 개수, 브라우저 예외 및 AJAX 오류에 대한 세부 정보, 사용자 및 세션 수의 타이밍을 얻을 수 있습니다. 이 필드는 **선택 사항이며** `false` 기본적으로 설정됩니다.
    * `ServerEnabled="true"`는 Application Insights에 기존 UserJourneyRecorder JSON을 사용자 지정 이벤트로 보냅니다.

    예를 들어:

    ```XML
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. 정책을 업로드합니다.

## <a name="see-the-logs-in-application-insights"></a>Application Insights에서 로그를 참조하세요.

응용 프로그램 인사이트에서 새 로그를 볼 수 있기까지 는 5분 미만의 짧은 지연이 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 만든 Application Insights 리소스를 엽니다.
1. **개요** 메뉴에서 **애널리틱스를**선택합니다.
1. Application Insights에서 새 탭을 엽니다.

다음은 로그를 확인하는 데 사용할 수 있는 쿼리 목록입니다.

| 쿼리 | 설명 |
|---------------------|--------------------|
`traces` | Azure AD B2C에서 생성된 모든 로그를 참조하세요. |
`traces | where timestamp > ago(1d)` | 마지막 날에 Azure AD B2C에서 생성된 모든 로그를 참조하세요.

항목이 길어질 수 있습니다. 자세히 보기 위해 CSV로 내보냅니다.

쿼리에 대한 자세한 내용은 [Azure Monitor의 로그 쿼리 개요를](../azure-monitor/log-query/log-query-overview.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

커뮤니티는 ID 개발자에게 도움을 주는 사용자 경험 뷰어를 개발했습니다. Application Insights 인스턴스에서 읽고 사용자 경험 이벤트의 올바른 구조 보기를 제공합니다. 소스 코드를 가져오고 고유한 솔루션에 배포합니다.

사용자 이동 플레이어는 Microsoft에서 지원하지 않으며 있는 그대로 엄격하게 사용할 수 있습니다.

GitHub의 응용 프로그램 인사이트에서 이벤트를 읽는 뷰어 버전을 찾을 수 있습니다.

[Azure-샘플/활성 디렉터리-b2c-고급 정책](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
