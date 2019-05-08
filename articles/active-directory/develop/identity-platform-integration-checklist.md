---
title: Microsoft id 플랫폼과 통합 | Azure
description: Microsoft id 플랫폼 (v2.0)를 사용 하 여 통합 하는 경우 모범 사례 및 프로젝트의 일반적인 문제점에 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 1f5a6d7501fd387548b111fcb0888515c8f8db25
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076407"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Microsoft identity 플랫폼 통합 검사 목록

Microsoft identity 플랫폼 통합 검사 목록 뛰어난 품질 및 보안 통합 과정을 안내 하는 데 사용 됩니다. 모범 사례를 강조 표시 하 고 Microsoft id 플랫폼과 통합 하는 경우 프로젝트의 일반적인 문제점에 따라서 품질 및 id 플랫폼을 사용 하 여 앱의 통합 보안을 유지 해야 하는 정기적으로 목록을 검토 합니다. 검사 목록 전체 응용 프로그램을 검토 하는 데 사용할 수 없습니다. 검사 목록의 내용이 변경 될 수 있습니다 플랫폼의 향상 된 기능을 확인 했습니다.

하면 이제 막 시작을 확인 합니다 [설명서](index.yml) 인증 기본 사항, Microsoft id 플랫폼의 응용 프로그램 시나리오 등에 대 한 자세한 합니다.

## <a name="testing-your-integration"></a>통합 테스트

다음 검사 목록을 사용 하 여 응용 프로그램 효과적으로 통합 되어 있는지 확인 합니다 [Microsoft id 플랫폼](https://docs.microsoft.com/legal/mdsa)합니다.

### <a name="basics"></a>기본 사항

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 읽고 이해 해야 합니다 [Microsoft 플랫폼 정책을](https://docs.microsoft.com/legal/mdsa)합니다. 응용 프로그램 사용자 및 플랫폼을 보호 하기 위해 설계 되는 조건에 부합 하는지 확인 합니다. |

### <a name="ownership"></a>소유권

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱 등록 및 관리에 사용 된 계정에 연결 된 정보 최신 상태 인지 확인 합니다. |

### <a name="branding"></a>브랜딩

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 준수 하는 [응용 프로그램에 대 한 지침을 브랜딩](howto-add-branding-in-azure-ad-apps.md)합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 응용 프로그램에 대 한 의미 있는 이름 및 로고를 제공 합니다. 응용 프로그램의 동의 확인 프롬프트에서이 정보가 표시 됩니다. 사용자 이름 및 로고는 회사/제품의 담당자는 사용자가 합리적인된 의사 결정을 내릴 수 있도록 해야 합니다. 모든 상표를 위반 하지 하는 것을 확인 합니다. |

### <a name="privacy"></a>개인 정보 보호

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱의 서비스 및 개인정보취급방침 약관에 대 한 링크를 제공 합니다. |

### <a name="security"></a>보안

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 모든 리디렉션 Uri의 소유권을 유지 하 고 DNS 레코드에 최신 상태로 유지 합니다. 여 Uri에서 와일드 카드 (*)를 사용 하지 마세요. 웹 앱에 대 한 모든 Uri 안전 하 고 (예: https 체계를 사용 하 여) 암호화 되는지 확인 합니다. 공용 클라이언트의 경우 사용 하 여 플랫폼 특정 리디렉션 Uri 해당 하는 경우 (주로 iOS 및 Android 용). 그렇지 않은 경우 사용 하 여 대량의 앱에 다시 호출 하는 경우 충돌을 방지 하는 임의성을 사용 하 여 리디렉션 Uri. 앱 격리 웹 에이전트에서 사용 중인 경우 사용할 수 있습니다 https://login.microsoftonline.com/nativeclient합니다. 검토 하 고 모든 사용 되지 않거나 필요 하지 않은 리디렉션 Uri는 정기적으로 trim 키를 누릅니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱 디렉터리에 등록 되 면 최소화 하 고 수동으로 응용 프로그램 등록 소유자 목록을 모니터링 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 에 대 한 지원을 사용 하도록 설정 하지는 [OAuth2 암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md) 꼭 필요한 경우가 아니면 합니다. 유효한 시나리오에 알아봅니다 [여기](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 사용 하지 마세요 [리소스 소유자 암호 자격 증명 흐름 (ROPC)](v2-oauth-ropc.md)는 사용자의 암호를 직접 처리 합니다. 이 흐름은 높은 수준의 신뢰 및 사용자 노출 하며 다른, 보다 안전한 흐름을 사용할 수 없는 경우에 사용 해야 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 보호 하 고 앱 자격 증명을 관리 합니다. 사용 하 여 [자격 증명 인증서](active-directory-certificate-credentials.md), 암호 자격 증명이 아니라 (클라이언트 암호). 암호 자격 증명을 사용 해야 하는 경우 없습니다 수동으로 설정 합니다. 하지 코드나 구성에 자격 증명을 저장 하 고 인간이 처리할 수 없습니다. 사용 가능한 경우 [Azure 리소스에 대 한 id 관리](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 또는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 저장 하 고 정기적으로 자격 증명을 회전 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 응용 프로그램에 대 한 최소 권한을 요청 해야 합니다. 응용 프로그램에 반드시 필요한 사용 권한 및 경우에만 요청만 필요 합니다. 다양 한 이해 [유형의 사용 권한](v1-permissions-and-consent.md#types-of-permissions)합니다. 필요한 경우 응용 프로그램 사용 권한 사용 사용 가능한 경우 권한을 위임 합니다. Microsoft Graph 사용 권한의 전체 목록을 참조 하세요 [사용 권한 참조](https://docs.microsoft.com/graph/permissions-reference)합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | Microsoft id 플랫폼을 사용 하 여 API를 보호 하는 경우 노출 해야 하는 권한을 보유 한 신중 하 게 생각 합니다. 솔루션에 대 한 적절 한 세분성 이란 무엇 이며 어떤 권한에 관리자 동의가 필요한 것이 좋습니다. 모든 권한 부여 결정 하기 전에 들어오는 토큰에서 예상 되는 권한 확인 합니다. |

### <a name="implementation"></a>구현

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 최신 인증 솔루션을 사용 하 여 (OAuth 2.0 [OpenID Connect](v2-protocols-oidc.md)) 안전 하 게 사용자를 로그인 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 프로토콜을 직접 구현 – 사용 안 함 [Microsoft에서 지 원하는 인증 라이브러리](reference-v2-libraries.md) (MSAL 서버 미들웨어). 와 통합 된 인증 라이브러리의 최신 버전을 사용 하는 있는지 확인 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱에 필요한 데이터를 통해 사용할 수 있으면 [Microsoft Graph](https://developer.microsoft.com/graph), 개별 API 대신 Microsoft Graph 끝점을 사용 하 여이 데이터에 대 한 권한을 요청 합니다. |

### <a name="end-user-experience"></a>최종 사용자 환경

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | [동의 환경을 이해](application-consent-experience.md) 및 최종 사용자와 관리자가 앱을 신뢰 하는 경우를 결정 하기에 충분할 수 있도록 앱의 동의 프롬프트의 부분을 구성 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 사용자가 자동으로 시도 하 여 앱을 사용 하는 동안 로그인 자격 증명을 입력 해야 하는 횟수를 최소화 대화형 흐름 하기 전에 인증 (자동 토큰 획득) 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | "Prompt = 동의" 모든 로그인을 사용 하지 마세요. 만 프롬프트를 사용 하 여 = consent 추가 사용 권한 (예: 앱의 필수 권한을 변경한 경우)에 대 한 동의 요청 해야 한다고 결정 한 경우. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 해당 하는 경우 사용자 데이터를 사용 하 여 응용 프로그램을 보강 합니다. 사용 된 [Microsoft Graph API](https://developer.microsoft.com/graph) 는이 작업을 수행 하는 간편한 방법입니다. 합니다 [Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer) 시작할 때 도움이 되는 도구입니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 전체 관리자 권한을 부여할 수 있습니다 동의 쉽게 테 넌 트에 있도록 앱에 필요한 사용 권한 집합을 등록 합니다. 사용 하 여 [증분 동의](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) 이유를 이해 하는 데 도움이 되는 런타임 시 앱을 처리할 수도 있고 사용자의 첫 번째 요청 시 혼동 하는 사용 권한을 요청 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 구현 된 [single sign-out 환경을 정리](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)합니다. 개인정보 취급 방침 및 보안 요구 되며 효율적인 사용자 환경을 만듭니다. |

### <a name="testing"></a>테스트

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 에 대 한 테스트 [조건부 액세스 정책을](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) 에 사용자 응용 프로그램을 사용 하는 기능에 영향을 줄 수입니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | (에 대 한 예제, 작업 또는 학교 계정, 개인 Microsoft 계정, 자녀 계정 및 소 버린 계정)를 지원 하려는 모든 가능한 계정 사용 하 여 응용 프로그램을 테스트 합니다. |

## <a name="additional-resources"></a>추가 리소스

v2.0에 관한 자세한 정보를 살펴보세요.

* [Microsoft id 플랫폼 (v2.0 개요)](v2-overview.md)
* [Microsoft id 플랫폼 프로토콜 참조](active-directory-v2-protocols.md)
* [액세스 토큰 참조](access-tokens.md)
* [ID 토큰 참조](id-tokens.md)
* [인증 라이브러리 참조](reference-v2-libraries.md)
* [사용 권한 및 동의에 Microsoft id 플랫폼](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)