---
title: 클라이언트 응용 프로그램 구성 (Microsoft 인증 라이브러리) | Azure
description: 응용 프로그램에서 인증 라이브러리 (MSAL (Microsoft) 공용 클라이언트 및 기밀 클라이언트에 대 한 구성 옵션에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
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
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430814"
---
# <a name="application-configuration-options"></a>응용 프로그램 구성 옵션

새 공용 또는 기밀 클라이언트 (또는 MSAL.js에 대 한 사용자 에이전트) 초기화 코드에서 응용 프로그램을 인증 하 고 토큰을 획득 합니다. Microsoft 인증 라이브러리 (MSAL)에 클라이언트 앱을 초기화 하는 경우 다양 한 구성 옵션을 설정할 수 있습니다. 이러한 옵션은 두 그룹으로 나뉩니다.

- 등록 옵션을 포함 합니다.
    - [인증 기관](#authority) (id 공급자의 구성 [인스턴스](#cloud-instance) 에 로그인 하 고 [대상](#application-audience) 앱 및 테 넌 트 ID에 대 한).
    - [클라이언트 ID](#client-id)합니다.
    - [리디렉션 URI](#redirect-uri)합니다.
    - [클라이언트 암호](#client-secret) (기밀 클라이언트 응용 프로그램)에 대 한 합니다.
- [로깅 옵션](#logging)로그 수준, 개인 데이터를 제어 및 라이브러리를 사용 하 여 구성 요소의 이름을 포함 합니다.

## <a name="authority"></a>인증 기관
인증 기관은 MSAL에서 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다. 일반적인 기관 다음과 같습니다.

- https://login.microsoftonline.com/&lt테 넌 트&gt;/, 여기서 &lt;테 넌 트&gt; 이 Azure AD 테 넌 트와 연결 된 도메인 또는 Azure Active Directory (Azure AD) 테 넌 트의 테 넌 트 ID입니다. 특정 조직의 사용자를 로그인에 사용 합니다.
- https://login.microsoftonline.com/common/. 회사 및 학교 계정 또는 개인 Microsoft 계정을 사용 하 여 사용자를 로그인 하는 데 사용 합니다.
- https://login.microsoftonline.com/organizations/. 사용자가 회사 및 학교 계정으로 로그인 하는 데 사용 합니다.
- https://login.microsoftonline.com/consumers/. 만 개인 Microsoft 계정 (이전의 Windows Live ID 계정)를 사용 하 여 사용자를 로그인 하는 데 사용 합니다.

기관 설정을 응용 프로그램 등록 포털에서 선언 된와 일치 해야 합니다.

기관 URL 인스턴스 및 대상 그룹으로 구성 됩니다.

인증 기관 될 수 있습니다.
- Azure AD 클라우드 인증 기관입니다.
- Azure AD B2C 인증 기관입니다. 참조 [B2C 세부 사항을](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)합니다.
- Active Directory Federation Services (ADFS) 인증 기관입니다. 참조 [ADFS 지원](https://aka.ms/msal-net-adfs-support)합니다.

Azure AD 클라우드 기관에는 두 부분으로 구성 합니다.
- id 공급자 *인스턴스*
- 로그인 *청중* 앱

인스턴스 및 대상 연결 하 고 기관 URL로 제공 될 수 있습니다. 이전 버전의 MSAL.NET MSAL 3 보다 합니다. *x*, 기관에서 직접 작성 해야, 원하는 대상으로 하 고 로그인 대상 클라우드를 기반 합니다.  이 다이어그램 기관 URL은 구성 하는 방법을 보여 줍니다.

![기관 URL은 구성 하는 방법](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>클라우드 인스턴스
합니다 *인스턴스* 앱 내셔널 클라우드 또는 Azure 공용 클라우드에서 사용자가 로그인 하는 경우를 지정 하는 데 사용 됩니다. MSAL을 사용 하 여 코드에서 또는 설정할 수 있습니다 Azure 클라우드 인스턴스의 열거형을 사용 하 여 URL을 전달 하 여는 [국가 클라우드 인스턴스](authentication-national-cloud.md#azure-ad-authentication-endpoints) 으로 `Instance` 멤버 (알고) 경우입니다.

모두 MSAL.NET 명시적 예외를 throw 합니다 `Instance` 고 `AzureCloudInstance` 지정 됩니다.

앱 인스턴스를 지정 하지 않으면, Azure 공용 클라우드 인스턴스를 대상 (URL 인스턴스의 `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>응용 프로그램 대상

로그인 한 대상 앱에 대 한 비즈니스 요구에 따라 달라 집니다.
- 줄 기간 업무 (LOB) 개발자 인 경우 조직 에서만에서 사용 되는 단일 테 넌 트 응용 프로그램을 아마도 생성할 수 있습니다. 이 경우 조직에서 해당 테 넌 트 ID (Azure AD 인스턴스 ID) 또는 Azure AD 인스턴스와 연결 된 도메인 이름으로 지정 해야 합니다.
- ISV 인 경우에 일부 조직에서는 (다중 테 넌 트 앱) 또는 조직에서 해당 회사 및 학교 계정으로 사용자를 로그인 하는 것이 좋습니다. 하지만 사용자는 개인 Microsoft 계정을 사용 하 여 로그인 할 수도 있습니다.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>코드/구성에서 대상 그룹을 지정 하는 방법
다음 값 중 하나를 사용 하 여 대상 그룹을 지정할 MSAL을 사용 하 여 코드에서 있습니다.
- Azure AD 기관 대상 열거형
- 해당 되는 테 넌 트 ID:
  - 단일 테 넌 트 응용 프로그램에 대 한 GUID (Azure AD 인스턴스 ID)
  - (단일 테 넌 트 응용 프로그램)에 Azure AD 인스턴스와 연결 된 도메인 이름
- 이러한 자리 표시자를 Azure AD 기관 대상 열거형 대신 테 넌 트 id 중 하나입니다.
    - `organizations` 다중 테 넌 트 응용 프로그램
    - `consumers` 사용자만 해당 개인 계정으로 로그인
    - `common` 회사 및 학교 계정 또는 개인 Microsoft 계정이 있는 사용자를 로그인

Azure AD 기관 대상 및 테 넌 트 ID를 지정 하는 경우 MSAL 의미 있는 예외를 throw 합니다.

앱을 Azure AD는 사용자 지정 하지 않으면, 대상 및 대상으로 하는 개인 Microsoft 계정입니다. (즉, 해당 동작 하는 것 처럼 `common` 지정 되었습니다.)

### <a name="effective-audience"></a>적용 대상
응용 프로그램에 대 한 유효한 대상 앱에서 설정한 대상 그룹 및 앱 등록에서 지정 된 대상의 최소값 (교집합 있는 경우) 됩니다. 실제로 [앱 등록](https://aka.ms/appregistrations) 환경을 앱에 대 한 대상 (지원 되는 계정 유형)을 지정할 수 있습니다. 자세한 내용은 [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 등록](quickstart-register-app.md)합니다.

현재 사용자 개인 Microsoft 계정으로 로그인 하도록 앱을 가져올 유일한 방법은 다음과 같습니다. 이러한 설정을 모두 구성 하려면
- 앱 등록 대상으로 `Work and school accounts and personal accounts`합니다.
- 대상 그룹을 코드/구성에서 설정 `AadAuthorityAudience.PersonalMicrosoftAccount` (또는 `TenantID` = "consumers").

## <a name="client-id"></a>클라이언트 ID
클라이언트 ID는 앱이 등록 된 경우 Azure AD에서 앱에 할당 된 고유한 응용 프로그램 (클라이언트) ID입니다.

## <a name="redirect-uri"></a>리디렉션 URI
리디렉션 URI는 id 공급자를 보안 토큰을 다시 전송할 URI.

### <a name="redirect-uri-for-public-client-apps"></a>공용 클라이언트 앱에 대 한 리디렉션 URI
공용 클라이언트 응용 프로그램 개발자는 MSAL을 사용 하는 경우:
- 전달할 필요가 `RedirectUri` MSAL에 의해 자동으로 계산 하므로 합니다. 이 리디렉션 URI는 플랫폼에 따라 이러한 값 중 하나로 설정 됩니다.
   - `urn:ietf:wg:oauth:2.0:oob` 모든 Windows 플랫폼
   - `msal{ClientId}://auth` Xamarin Android 및 iOS 용

- 리디렉션 구성 필요가 URI [앱 등록](https://aka.ms/appregistrations):

   ![앱 등록의 리디렉션 URI](media/msal-client-application-configuration/redirect-uri.png)

리디렉션 URI를 사용 하 여 재정의할 수 있습니다는 `RedirectUri` 속성 (예: 브로커를 사용 하는 경우). 이 시나리오에 대 한 리디렉션 Uri의 몇 가지 예는 다음과 같습니다.

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

자세한 내용은 참조는 [Android 및 iOS에 대 한 설명서](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)합니다.

### <a name="redirect-uri-for-confidential-client-apps"></a>기밀 클라이언트 앱에 대 한 리디렉션 URI
웹 앱에 대 한 리디렉션 URI (또는 회신 URI)는 Azure AD가 토큰을 응용 프로그램에 다시 전송 하는 데 사용할 URI입니다. 이 기밀 앱을 사용 하면 이러한 중 하나인 경우 웹 앱/웹 API의 URL을 수 있습니다. 리디렉션 URI를 앱 등록에 등록 해야 합니다. 이 등록은 로컬로 테스트 처음에 앱을 배포 하는 경우에 특히 중요 합니다. 그런 다음 응용 프로그램 등록 포털에서 배포 된 앱의 회신 URL을 추가 해야 합니다.

디먼 앱의 경우 리디렉션 URI를 지정할 필요가 없습니다.

## <a name="client-secret"></a>클라이언트 암호
기밀 클라이언트 앱에 대 한 클라이언트 암호를 지정합니다이. 이 비밀 (앱 암호)는 응용 프로그램 등록 포털에서 제공 되었거나 PowerShell AzureAD, PowerShell AzureRM 또는 Azure CLI를 사용 하 여 Azure AD 앱 등록 중에 제공 됩니다.

## <a name="logging"></a>로깅
로깅 및 문제 해결 기타 구성 옵션을 사용 하도록 설정 합니다. 참조 된 [로깅](msal-logging.md) 사용 방법에 대 한 자세한 내용은 문서.

## <a name="next-steps"></a>다음 단계
에 대 한 자세한 [MSAL.NET을 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-net-initializing-client-applications.md)합니다.

에 대 한 자세한 [MSAL.js를 사용 하 여 클라이언트 응용 프로그램 인스턴스화](msal-js-initializing-client-applications.md)합니다.
