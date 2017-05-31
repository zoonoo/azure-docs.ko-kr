---
title: "Azure AD Connect: 통과 인증 문제 해결 | Microsoft 문서"
description: "이 문서에서는 Azure AD(Azure Active Directory) 통과 인증 문제를 해결하는 방법에 대해 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증 문제 해결, Active Directory 설치, Azure AD에 필요한 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: aceba2cfeac03eae78e89ef46926b2a388663f09
ms.contentlocale: ko-kr
ms.lasthandoff: 05/13/2017

---

# <a name="how-to-troubleshoot-azure-active-directory-pass-through-authentication"></a>Azure Active Directory 통과 인증 문제를 해결하는 방법

이 문서에서는 Azure AD Connect 또는 독립 실행형을 통해 통과 인증 커넥터를 설치, 등록 또는 제거하는 동안 발생하는 일반적인 문제에 대한 해결 정보를 찾을 수 있습니다. 그리고 테넌트에서 Azure AD(Azure Active Directory) 통과 인증 기능을 사용하도록 설정하고 작동하는 동안 발생하는 문제에 대한 해결 정보도 다루고 있습니다.

## <a name="issues-during-installation-of-connectors-either-via-azure-ad-connect-or-standalone"></a>커넥터 설치 중에 발생하는 문제(Azure AD Connect 또는 독립 실행형을 통해)

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Azure AD Application Proxy 커넥터가 이미 있음

통과 인증 커넥터는 [Azure AD 응용 프로그램 프록시](../../active-directory/active-directory-application-proxy-get-started.md) 커넥터와 동일한 서버에 설치할 수 없습니다. 통과 인증 커넥터는 별도의 서버에 설치해야 합니다.

### <a name="an-unexpected-error-occured"></a>예기치 않은 오류가 발생함

서버에서 [커넥터 로그를 수집](#collecting-pass-through-authentication-connector-logs)하고 문제와 관련하여 Microsoft 지원에 문의하세요.

## <a name="issues-during-registration-of-connectors"></a>커넥터 등록 중에 발생하는 문제

### <a name="registration-of-the-connecter-failed-due-to-blocked-ports"></a>차단된 포트로 인해 커넥터 등록에 실패함

커넥터가 설치된 서버가 [여기](active-directory-aadconnect-pass-through-authentication.md#prerequisites)에 나열된 서비스 URL 및 포트와 통신할 수 있는지 확인합니다.

### <a name="registration-of-the-connector-failed-due-to-token-or-account-authorization-errors"></a>토큰 또는 계정 권한 부여 오류로 인해 커넥터 등록에 실패했습니다.

모든 Azure AD Connect 또는 독립 실행형 커넥터 설치 및 등록 작업에 대해 클라우드 전용 전역 관리자 계정을 사용해야 합니다. MFA 사용 전역 관리자 계정에 알려진 문제점이 있습니다. 해결 방법으로 MFA를 일시적으로 해제하세요(작업 완료를 위해서만).

### <a name="an-unexpected-error-occurred"></a>예기치 않은 오류가 발생함

서버에서 [커넥터 로그를 수집](#collecting-pass-through-authentication-connector-logs)하고 문제와 관련하여 Microsoft 지원에 문의하세요.

## <a name="issues-during-uninstallation-of-connectors"></a>커넥터 제거 중에 발생하는 문제

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Azure AD Connect 제거 시 나타나는 경고 메시지

테넌트에서 통과 인증을 사용하도록 설정했고 Azure AD Connect를 제거하려고 하면 "다른 통과 인증 에이전트가 다른 서버에 설치되어 있지 않으면 사용자가 Azure AD에 로그인할 수 없습니다."라는 경고 메시지가 표시됩니다. .

사용자 로그인이 중단되지 않도록 방지하려면 Azure AD Connect를 제거하기 전에 [고가용성](active-directory-aadconnect-pass-through-authentication.md) 설정이 있어야 합니다.

## <a name="issues-with-enabling-the-pass-through-authentication-feature"></a>통과 인증 기능 사용과 관련된 문제

### <a name="the-enabling-of-the-feature-failed-because-there-were-no-connectors-available"></a>사용 가능한 커넥터가 없어서 기능을 사용하도록 설정하지 못함

테넌트에서 통과 인증을 사용하도록 설정하려면 하나 이상의 활성 커넥터가 있어야 합니다. Azure AD Connect 또는 독립 실행형 커넥터를 설치하여 커넥터를 설치할 수 있습니다.

### <a name="the-enabling-of-the-feature-failed-due-to-blocked-ports"></a>차단된 포트로 인해 기능을 사용하도록 설정하지 못함

Azure AD Connect가 설치된 서버가 [여기](active-directory-aadconnect-pass-through-authentication.md#prerequisites)에 나열된 서비스 URL 및 포트와 통신할 수 있는지 확인합니다.

### <a name="the-enabling-of-the-feature-failed-due-to-token-or-account-authorization-errors"></a>토큰 또는 계정 권한 부여 오류로 인해 기능 활성화에 실패했습니다.

기능을 활성화할 때는 클라우드 전용 전역 관리자 계정을 사용해야 합니다. MFA(Multi-Factor Authentication) 사용 전역 관리자 계정에 알려진 문제점이 있습니다. 해결 방법으로 MFA를 일시적으로 해제하세요(작업 완료를 위해서만).

## <a name="issues-while-operating-the-pass-through-authentication-feature"></a>통과 인증 기능 작동과 관련된 문제

### <a name="user-facing-sign-in-errors"></a>사용자용 로그인 오류

이 기능이 Azure AD 로그인 화면에서 다음 사용자용 오류를 보고합니다. 이러한 오류는 적절한 해결 단계와 함께 아래에서 자세히 설명합니다.

|오류|설명|해결 방법
| --- | --- | ---
|AADSTS80001|Active Directory에 연결할 수 없음|커넥터 서버가 암호의 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트의 멤버이고 Active Directory에 연결할 수 있는지 확인합니다.  
|AADSTS8002|Active Directory에 연결하는 동안 시간 초과 발생|Active Directory를 사용할 수 있고 커넥터의 요청에 응답하는지 확인합니다.
|AADSTS80004|커넥터에 전달된 사용자 이름이 유효하지 않음|사용자가 올바른 사용자 이름을 사용하여 로그인을 시도하는지 확인합니다.
|AADSTS80005|유효성 검사 중 예측할 수 없는 WebException 발생|일시적인 오류일 수 있습니다. 요청을 다시 시도하십시오. 계속 실패할 경우 Microsoft 지원에 문의하세요.
|AADSTS80007|Active Directory와 통신 중 오류 발생|커넥터 로그에서 자세한 내용을 확인하고 Active Directory가 예상대로 작동하는지 확인합니다.

## <a name="collecting-pass-through-authentication-connector-logs"></a>통과 인증 커넥터 로그 수집

발생하는 문제의 유형에 따라 다른 위치에서 통과 인증 커넥터 로그를 확인해야 합니다.

### <a name="connector-event-logs"></a>커넥터 이벤트 로그

커넥터 관련 오류의 경우 서버에서 이벤트 뷰어 응용 프로그램을 열고 **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**에서 확인합니다.

자세한 분석 및 디버깅 로그를 보려면 "세션" 로그를 사용하도록 설정합니다. 정상적으로 작동하는 동안에는 이 로그를 사용하도록 설정한 채 커넥터를 실행하지 마세요. 이 로그는 문제 해결에만 사용하세요. 로그 콘텐츠는 로그가 다시 사용하지 않도록 설정된 후에만 표시됩니다.

### <a name="detailed-trace-logs"></a>자세한 추적 로그

사용자 로그인 실패 문제를 해결하려면 **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**에서 추적 로그를 검색합니다. 이러한 로그에는 통과 인증 기능을 통해 특정 사용자 로그인이 실패한 이유가 포함되어 있습니다. 로그 항목의 예는 다음과 같습니다.

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

명령 프롬프트를 열고 다음 명령을 실행하면 오류(위 예제의 경우 '1328')에 대한 자세한 설명을 볼 수 있습니다. 참고: '1328'을 로그에 표시되는 실제 오류 번호로 바꿔야 합니다.

`Net helpmsg 1328`

결과는 다음과 같이 표시됩니다.

![통과 인증](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>도메인 컨트롤러 로그

감사 로깅이 사용되는 경우에는 도메인 컨트롤러의 보안 로그에서도 추가 정보를 찾을 수 있습니다. 통과 인증 커넥터에서 보낸 로그인 요청을 쿼리하는 간단한 방법은 다음과 같습니다.

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

