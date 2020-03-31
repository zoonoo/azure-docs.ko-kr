---
title: 지원되는 계정 유형별 유효성 검사 차이 - Microsoft ID 플랫폼 | Azure
description: Microsoft ID 플랫폼에 앱을 등록할 때 지원되는 다양한 계정 유형에 대한 다양한 속성의 유효성 검사 차이점에 대해 알아봅니다.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128869"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>지원되는 계정 유형별 유효성 검사 차이(signInAudience)

개발자를 위해 Microsoft ID 플랫폼에 응용 프로그램을 등록할 때 응용 프로그램에서 지원하는 계정 유형을 선택하라는 메시지가 표시됩니다. 응용 프로그램 개체 및 매니페스트에서 이 속성은 `signInAudience`입니다.

다음과 같은 옵션이 있습니다.

- *AzureADMyOrg*: 앱이 등록된 조직 디렉터리내의 계정만(단일 테넌트)
- *AzureADMultipleOrgs*: 모든 조직 디렉터리(다중 테넌트)의 계정
- *AzureADandPersonalMicrosoft계정*: 모든 조직 디렉토리(다중 테넌트) 및 개인 Microsoft 계정(예: 스카이프, Xbox 및 Outlook.com) 계정

등록된 응용 프로그램의 경우 응용 프로그램의 **인증** 섹션에서 지원되는 계정 유형에 대한 값을 찾을 수 있습니다. 또한 `signInAudience` **매니페스트에서**속성 아래에서 찾을 수 있습니다.

이 속성에 대해 선택한 값은 다른 앱 개체 속성에 영향을 미칩니다. 따라서 이 속성을 변경하는 경우 먼저 다른 속성을 변경해야 할 수 있습니다.

지원되는 다양한 계정 유형에 대한 다양한 속성의 유효성 검사 차이점은 다음 표를 참조하십시오.

| 속성 | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` 및 `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| 응용 프로그램`identifierURIs`ID URI ()  | 테넌트에서 고유해야 합니다. <br><br> urn:// 체계가 지원됩니다. <br><br> 와일드카드는 지원되지 않습니다. <br><br> 쿼리 문자열 및 조각이 지원됩니다. <br><br> 최대 길이 255자 <br><br> 식별자 수에 대한 제한 없음*  | 전역적으로 고유해야 합니다. <br><br> urn:// 체계가 지원됩니다. <br><br> 와일드카드는 지원되지 않습니다. <br><br> 쿼리 문자열 및 조각이 지원됩니다. <br><br> 최대 길이 255자 <br><br> 식별자 수에 대한 제한 없음* | 전역적으로 고유해야 합니다. <br><br> urn:// 구성표는 지원되지 않습니다. <br><br> 와일드카드, 조각 및 쿼리 문자열은 지원되지 않습니다. <br><br> 최대 길이 120자 <br><br> 최대 50개의 식별자URIs |
| 인증서 ()`keyCredentials` | 대칭 서명 키 | 대칭 서명 키 | 암호화 및 비대칭 서명 키 | 
| 클라이언트 암호`passwordCredentials`() | 제한 없음* | 제한 없음* | liveSDK를 사용하도록 설정한 경우: 최대 2개의 클라이언트 암호 | 
| 리디렉션 URL ()`replyURLs`) | 자세한 내용은 [URI/회신 URL 제한 및 제한 사항 리디렉션을](reply-url.md) 참조하세요. | | | 
| API 권한`requiredResourceAccess`() | 제한 없음* | 제한 없음* | 허용되는 리소스당 최대 30개의 권한(예: Microsoft 그래프) | 
| 이 API ()`oauth2Permissions`에 의해 정의된 범위 | 최대 범위 이름 길이 120자 <br><br> 정의된 범위 수에 대한 제한 없음* | 최대 범위 이름 길이 120자 <br><br> 정의된 범위 수에 대한 제한 없음* |  최대 범위 이름 길이 40자 <br><br> 정의된 최대 100개의 범위 | 
| 공인 클라이언트 응용`preautorizedApplications`프로그램 ( ) | 제한 없음* | 제한 없음* | 최대 500개 <br><br> 최대 100개의 클라이언트 앱정의 <br><br> 클라이언트당 정의된 최대 30개의 범위 | 
| appRoles | 지원됨 <br> 제한 없음* | 지원됨 <br> 제한 없음* | 지원되지 않음 | 
| 로그아웃 URL | http://localhost허용됩니다. <br><br> 최대 길이 255자 | http://localhost허용됩니다. <br><br> 최대 길이 255자 | <br><br> https://localhost허용됨, http://localhost MSA에 대해 실패 <br><br> 최대 길이 255자 <br><br> HTTP 구성표는 허용되지 않습니다. <br><br> 와일드카드는 지원되지 않습니다. | 

*앱 개체의 모든 컬렉션 속성에 걸쳐 약 1,000개의 항목으로 전역 제한이 있습니다.

## <a name="next-steps"></a>다음 단계

- 응용 [프로그램 등록에](app-objects-and-service-principals.md) 대해 알아보기
- [응용 프로그램 매니페스트에](reference-app-manifest.md) 대해 알아보기
