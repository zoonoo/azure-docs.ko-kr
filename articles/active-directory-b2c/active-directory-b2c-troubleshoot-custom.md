---
title: "Azure Active Directory B2C: 사용자 지정 정책의 문제를 해결하기 위한 Application Insights | Microsoft Docs"
description: "Application Insights를 설정하여 사용자 지정 정책의 실행을 추적하는 방법"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 125f05f5dce5a0e4127348de5b280f06c3491d84
ms.openlocfilehash: db658319637f1a45616091e034a0fc4db44885d4
ms.contentlocale: ko-kr
ms.lasthandoff: 05/22/2017

---

# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: 로그 수집

이 문서에서는 사용자 지정 정책을 사용하여 문제를 진단할 수 있도록 Azure AD B2C에서 로그를 수집하는 단계를 설명합니다.

## <a name="use-application-insights"></a>Application Insights 사용

Azure AD B2C는 Application Insights로 데이터를 보내는 기능을 지원합니다.  Application Insights는 예외를 진단하고 응용 프로그램 성능 문제를 시각화하는 방법을 제공합니다.

### <a name="setup-application-insights"></a>Application Insights 설정

1. [Azure 포털](https://portal.azure.com)로 이동합니다. Azure 구독(Azure AD B2C 테넌트가 아님)을 사용하여 테넌트에 위치하도록 합니다.
1. 왼쪽 탐색 메뉴에서 **+ 새로 만들기**를 클릭합니다.
1. **Application Insights**를 검색하고 선택한 다음 **만들기**를 클릭합니다.
1. 양식을 완료하고 **만들기**를 클릭합니다. **응용 프로그램 형식**에서 **일반**을 선택합니다.
1. 리소스를 만들게 되면 Application Insights 리소스를 엽니다.
1. 왼쪽 메뉴에서 **속성**을 찾고 클릭합니다.
1. **계측 키**를 복사하고 다음 섹션에 저장합니다.

### <a name="set-up-the-custom-policy"></a>사용자 지정 정책 설정

1. RP 파일(예: SignUpOrSignin.xml)을 엽니다.
1. 다음 속성을 `<TrustFrameworkPolicy>` 요소에 추가합니다.

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. 이미 존재하지 않는 경우 자식 노드 `<UserJourneyBehaviors>`을 `<RelyingParty>` 노드에 추가합니다. `<DefaultUserJourney ReferenceId="YourPolicyName" />`의 바로 다음에 위치해야 합니다.
2. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다. `{Your Application Insights Key}`를 이전 섹션의 Application Insights에서 가져온 **계측 키**로 바꿔야 합니다.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"`은 ApplicationInsights가 프로세스 파이프라인을 통해 원격 분석 데이터를 신속하게 처리하도록 지시하여 개발에는 적합하지만 높은 볼륨에서 제한됩니다.
  * `ClientEnabled="true"`은 페이지 보기 및 클라이언트 쪽 오류(필요 없음)을 추적하는 ApplicationInsights 클라이언트 쪽 스크립트를 보냅니다.
  * `ServerEnabled="true"`는 Application Insights에 기존 UserJourneyRecorder JSON을 사용자 지정 이벤트로 보냅니다.
  최종 XML은 다음과 같이 표시됩니다.

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
      <DefaultUserJourney ReferenceId="YourPolicyName" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. 정책을 업로드합니다.

### <a name="see-the-logs-in-application-insights"></a>Application Insights에서 로그를 참조하세요.

>[!NOTE]
> Application Insights에서 새 로그가 표시되기까지 짧은 지연 시간이 발생합니다(5분 미만).

1. [Azure Portal](https://portal.azure.com)에서 만든 Application Insights 리소스를 엽니다.
1. **개요** 메뉴에서 **분석**을 클릭합니다.
1. Application Insights에서 새 탭을 엽니다.
1. 로그를 참조하는 데 사용할 수는 쿼리 목록은 다음과 같습니다.

| 쿼리 | 설명 |
|---------------------|--------------------|
traces | Azure AD B2C에서 생성된 모든 로그를 참조하세요. |
traces \| where timestamp > ago(1d) | 마지막 날에 Azure AD B2C에서 생성된 모든 로그를 참조하세요.

항목이 길어질 수 있습니다.  자세히 보기 위해 CSV로 내보냅니다.

분석 도구에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)에서 알아볼 수 있습니다.

>[!NOTE]
>커뮤니티는 ID 개발자에게 도움을 주는 사용자 경험 뷰어를 개발했습니다.  Microsoft에서 지원되지 않고 엄격하게 그대로 사용할 수 없습니다.  Application Insights 인스턴스에서 읽고 사용자 경험 이벤트의 올바른 구조 보기를 제공합니다.  소스 코드를 가져오고 고유한 솔루션에 배포합니다.

[지원되지 않는 사용자 지정 정책 샘플 및 관련된 도구에 대한 GitHub 리포지토리](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)





