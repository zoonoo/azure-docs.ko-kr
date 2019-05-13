---
title: 클라이언트 응용 프로그램 구성 (Microsoft 인증 라이브러리) | Azure
description: 응용 프로그램에서 인증 라이브러리 (MSAL (Microsoft) 공용 클라이언트 및 기밀 클라이언트에 대 한 구성 옵션에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a48eea9fedd2d82f44693d58b31ee0d5c8c288d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138545"
---
# <a name="application-configuration-options"></a>응용 프로그램 구성 옵션

새 공용 또는 기밀 클라이언트 (또는 MSAL.js에 대 한 사용자 에이전트) 초기화 코드에서 응용 프로그램을 인증 하 고 토큰을 획득 합니다.  MSAL에서 클라이언트 응용 프로그램을 초기화할 때 설정할 수 있는 다양 한 구성 옵션의 여러 가지가 있습니다. 이러한 옵션은 두 그룹으로 구분할 수 있습니다.

- 등록 옵션을 포함 합니다.
    - [인증 기관](#authority) (id 공급자 구성 [인스턴스](#cloud-instance) 및 로그인 [audience](#application-audience) 응용 프로그램 및 테 넌 트 ID에 대 한).
    - [클라이언트 ID](#client-id)
    - [리디렉션 URI](#redirect-uri)
    - [클라이언트 암호](#client-secret) (기밀 클라이언트 응용 프로그램)에 대 한 합니다.
- [로깅 옵션](#logging)로그 수준, 개인 데이터를 제어 및 라이브러리를 사용 하 여 구성 요소의 이름을 포함 합니다.

## <a name="authority"></a>Authority
인증 기관은 MSAL에서 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다. 일반적인 기관 다음과 같습니다.

- https://login.microsoftonline.com/&lt테 넌 트&gt;/, 여기서 &lt;테 넌 트&gt; 이 Azure AD 테 넌 트와 연결 된 도메인 또는 Azure AD 테 넌 트의 테 넌 트 ID입니다.  특정 조직의 사용자를 로그인에 사용 합니다.
- https://login.microsoftonline.com/common/. 회사 및 학교 계정 또는 개인 Microsoft 계정을 사용 하 여 사용자를 로그인 하는 데 사용 합니다.
- https://login.microsoftonline.com/organizations/. 사용자가 회사 및 학교 계정으로 로그인 하는 데 사용 합니다.
- https://login.microsoftonline.com/consumers/. 개인 Microsoft 계정 (live)을 사용 하 여 사용자를 로그인 하는 데 사용 합니다.

기관 설정을 응용 프로그램 등록 포털에서 선언 된와 일치 해야 합니다.

기관 URL 인스턴스 및 대상 그룹으로 구성 됩니다.

인증 기관 될 수 있습니다.
- Azure Active directory 클라우드 인증 기관
- Azure AD B2C 인증 기관입니다. 참조 [B2C 세부 사항을](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)합니다.
- ADFS 인증 기관입니다. 참조 [ADFS 지원](https://aka.ms/msal-net-adfs-support)합니다.

Azure AD 클라우드 기관에는 두 부분으로 구성 합니다.
- id 공급자 **인스턴스**
- 로그인 **청중** 응용 프로그램

인스턴스 및 대상 연결 하 고 기관 URL로 제공 될 수 있습니다. MSAL.NET MSAL 이전 버전에서 3.x 기관 대상 및 대상에 로그인 하려는 클라우드를 따라 직접 작성 해야 합니다.  다음 다이어그램은 기관 URL은 구성 하는 방법을 보여 줍니다.

![Authority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>클라우드 인스턴스
합니다 **인스턴스** 응용 프로그램 내셔널 클라우드 또는 Microsoft Azure 공용 클라우드에서 사용자가 로그인 하는 경우를 지정 하는 데 사용 됩니다. MSAL을 사용 하 여 코드에서 Azure 클라우드 인스턴스 또는 설정할 수 있습니다 하는 열거형을 사용 하 여 URL을 전달 하 여는 [국가 클라우드 인스턴스](authentication-national-cloud.md#azure-ad-authentication-endpoints) 으로 `Instance` 멤버 (알고) 경우입니다.

모두 MSAL.NET 명시적 예외를 throw 합니다 `Instance` 고 `AzureCloudInstance` 지정 됩니다. 

앱 인스턴스를 지정 하지 않으면, Azure 공용 클라우드 인스턴스를 대상 (URL 인스턴스의 `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>응용 프로그램 대상

로그인 대상 응용 프로그램에 대 한 비즈니스 요구에 따라 달라 집니다.
- 줄 기간 업무 (LOB) 개발자 인 경우 조직 에서만에서 사용 되는 단일 테 넌 트 응용 프로그램을 아마도 생성할 수 있습니다. 이 경우이 조직 이란, 해당 테 넌 트 ID (Azure Active Directory의 ID), 또는이 Azure Active Directory와 연결 된 도메인 이름 중 하나를 지정 해야 합니다.
- ISV 인 경우 해당 작업을 사용 하 여 사용자를 로그인 하는 것이 좋습니다 및 모든 조직 또는 조직도 (다중 테 넌 트 앱)에 있지만에서 학교 계정 사용자가 Microsoft 개인 계정으로 로그인 할 수도 있습니다.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>코드/구성에서 대상 그룹을 지정 하는 방법
사용 하 여 대상 그룹을 지정할 MSAL을 사용 하 여 코드에서 있습니다.
- 두 Azure AD 기관 대상 열거형입니다. 
- 또는 해당 되는 테 넌 트 ID:
  - GUID (Azure Active Directory의 ID), 단일 테 넌 트 응용 프로그램에 대 한
  - (단일 테 넌 트 응용 프로그램)에 Azure Active Directory와 연결 된 도메인 이름
- 또는 이러한 자리 표시자를 Azure AD 기관 대상 열거형 대신 테 넌 트 id 중 하나:
    - `organizations` 다중 테 넌 트 응용 프로그램
    - `consumers` 사용자만 해당 개인 계정으로 로그인
    - `common` 사용자가 회사 및 학교 계정 또는 Microsoft 개인 계정으로 로그인

Azure AD 기관 대상 및 테 넌 트 ID를 지정 하는 경우 MSAL 의미 있는 예외를 throw 합니다. 

Azure AD에 앱의 대상이 될 대상 그룹을 지정 하지 않으면 및 대상으로 하는 개인 Microsoft 계정 (즉 `common`).

### <a name="effective-audience"></a>적용 대상
응용 프로그램에 대 한 유효한 대상 응용 프로그램에서 설정한 대상 그룹 및 응용 프로그램 등록에 지정 된 대상의 최소값 (교집합 있는 경우) 됩니다. 실제로 응용 프로그램 등록 환경 ([앱 등록](https://aka.ms/appregistrations)) 응용 프로그램에 대 한 대상 그룹 (지원 되는 계정 유형)을 지정할 수 있습니다. [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 등록](quickstart-register-app.md) 자세한 내용은 합니다.

현재 응용 프로그램이 사용자만 Microsoft 개인 계정으로 로그인 하는 유일한 방법은 다음과 같습니다. 설정 하려면
- "회사 및 학교 계정을 개인 계정" 앱 등록 대상 그룹을 설정 하 고,
- 코드에서 대상 그룹을 설정 하는 고 / 구성을 `AadAuthorityAudience.PersonalMicrosoftAccount` (또는 `TenantID `"consumers" =)

## <a name="client-id"></a>클라이언트 ID
앱이 등록 된 경우 Azure AD에서 앱에 할당 된 고유한 응용 프로그램 (클라이언트) ID입니다.

## <a name="redirect-uri"></a>리디렉션 URI
리디렉션 URI는 id 공급자 보안 토큰을 다시 보내는 URI입니다. 

### <a name="redirect-uri-for-public-client-applications"></a>공용 클라이언트 응용 프로그램에 대 한 리디렉션 URI
MSAL을 사용 하 여 공용 클라이언트 응용 프로그램 개발자:
- 전달할 필요가 없습니다를 ``RedirectUri`` 대로 MSAL에 의해 자동으로 계산 됩니다. 이 리디렉션 URI는 플랫폼에 따라 다음 값으로 설정 됩니다.

- ``urn:ietf:wg:oauth:2.0:oob`` 모든 Windows 플랫폼
- ``msal{ClientId}://auth`` Xamarin Android 및 iOS 용

그러나 리디렉션 URI를 구성 해야에 [앱 등록](https://aka.ms/appregistrations)합니다.

![포털의 리디렉션 URI](media/msal-client-application-configuration/redirect-uri.png)

사용 하 여 리디렉션 URI를 재정의 하는 것이 불가능 합니다 `RedirectUri` 속성을 예를 들어 브로커를 사용 하는 경우. 이 경우 리디렉션 Uri의 예로 다음과 같습니다.

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

자세한 내용은 Android 세부 정보를 참조 하십시오. 및 [iOS 세부 정보](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>기밀 클라이언트 응용 프로그램에 대 한 리디렉션 URI
웹 앱에 대 한 리디렉션 URI (또는 회신 URI)에 URI는 Azure AD에 연결 다시 토큰을 사용 하 여 응용 프로그램. 이 웹 응용 프로그램의 URL 수 / 웹 API는 기밀 중 하나인 경우 이러한 합니다.  이 리디렉션 URI는 앱 등록에 등록 해야 합니다. 이 로컬로 테스트 처음에 응용 프로그램을 배포 하는 경우에 특히 중요 합니다. 그런 다음 응용 프로그램 등록 포털에서 배포 된 응용 프로그램의 회신 URL을 추가 해야 합니다.

디먼 앱의 경우 리디렉션 URI를 지정할 필요가 없습니다.

## <a name="client-secret"></a>클라이언트 암호
기밀 클라이언트 응용 프로그램에 대 한 클라이언트 암호입니다. 이 비밀 (응용 프로그램 암호)는 응용 프로그램 등록 포털에서 제공 되었거나 PowerShell AzureAD, PowerShell AzureRM 또는 Azure CLI를 사용 하 여 Azure AD 응용 프로그램 등록 중에 제공 됩니다.

## <a name="logging"></a>로깅
로깅 및 문제 해결 다른 옵션을 사용 하도록 설정 합니다. 자세한 내용은 참조는 [로깅](msal-logging.md) 페이지 사용 방법에 대 한 자세한 내용은 합니다.

## <a name="next-steps"></a>다음 단계
에 대 한 자세한 [MSAL.NET을 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-net-initializing-client-applications.md)합니다.

에 대 한 자세한 [MSAL.js를 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-js-initializing-client-applications.md)합니다.
