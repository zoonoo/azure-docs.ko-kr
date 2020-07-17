---
title: Azure Active Directory B2C itsme Openid connect Connect
titleSuffix: Azure AD B2C
description: Client_secret 사용자 흐름 정책을 사용 하 여 itsme OIDC와 Azure AD B2C 인증을 통합 하는 방법에 대해 알아봅니다. itsme은 디지털 ID 앱입니다. 카드 판독기, 암호, 2 단계 인증 및 여러 PIN 코드 없이 안전 하 게 로그인 할 수 있습니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85dd58398021ef61e425eb58797e818b233c491b
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170125"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 OIDC (itsme Openid connect Connect) 구성

Itsme digital ID 앱을 사용 하면 카드 판독기, 암호, 2 단계 인증 또는 여러 PIN 코드 없이 안전 하 게 로그인 할 수 있습니다. Itsme 앱은 확인 된 id로 강력한 고객 인증을 제공 합니다. 이 문서에서는 클라이언트 암호 사용자 흐름 정책을 사용 하 여 OIDC (itsme Openid connect Connect)와 Azure AD B2C 인증을 통합 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

시작 하려면 다음이 필요 합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](tutorial-create-tenant.md) 입니다.
* Itsme에서 제공 하는 클라이언트 ID (파트너 코드 라고도 함)입니다.
* Itsme에서 제공 하는 서비스 코드입니다.
* Itsme 계정에 대 한 클라이언트 암호입니다.

## <a name="scenario-description"></a>시나리오 설명

![itsme 아키텍처 다이어그램](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| 단계 | 설명 |
|------|------|
|1     | 웹 사이트 또는 응용 프로그램에서 Azure AD B2C 사용자 흐름을 조정 하 여 **itsme에 로그인** 단추를 포함 합니다. 사용자가이 단추를 클릭 하면 상호 작용 흐름이 시작 됩니다.  |
|2     | Azure AD B2C는 itsme 클라이언트 암호 API에 권한 부여 요청을 전송 하 여 Openid connect connect 흐름을 시작 합니다. 잘 알려진/Openid connect 구성 끝점은 끝점에 대 한 정보를 포함 하 고 있습니다.  |
|3     | Itsme 환경은 사용자를 itsme 식별 페이지로 리디렉션하여 사용자가 전화 번호를 입력할 수 있도록 합니다.  |
|4     | Itsme 환경은 사용자 로부터 전화 번호를 수신 하 고 정확성의 유효성을 검사 합니다.  |
|5     | 전화 번호가 활성 itsme 사용자에 속하는 경우 itsme 앱에 대 한 작업이 생성 됩니다.  |
|6     | 사용자가 itsme 앱을 열고 요청을 확인 하 고 작업을 확인 합니다.  |
|7     |  앱은 itsme 환경에 작업이 확인 되었음을 알립니다. |
|8     |  Itsme 환경에서는 Azure AD B2C OAuth 권한 부여 코드를 반환 합니다. |
|9     |  권한 부여 코드를 사용 하 여 Azure AD B2C는 토큰 요청을 수행 합니다. |
| 10 | Itsme 환경은 토큰 요청을 확인 하 고 여전히 유효한 경우 요청 된 사용자 정보를 포함 하는 OAuth 액세스 토큰 및 ID 토큰을 반환 합니다. |
| 11 | 마지막으로 사용자는 인증 된 사용자로 리디렉션 url로 리디렉션됩니다.  |
|   |   |

## <a name="onboard-with-itsme"></a>Itsme으로 등록

1. Itsme를 사용 하 여 계정을 만들려면 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace)에서 itsme를 방문 하세요.

2. 에 전자 메일을 보내 itsme 계정을 활성화 onboarding@itsme.be 합니다. B2C 설정에 필요한 **파트너 코드** 및 **서비스 코드** 를 받게 됩니다.

3. Itsme 파트너 계정이 활성화 된 후에는 **클라이언트 암호**에 대 한 일회성 링크를 포함 하는 전자 메일을 받게 됩니다.

4. [Itsme](https://business.itsme.be/en) 의 지침에 따라 구성을 완료 합니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Azure AD B2C에서 새 Id 공급자를 설정 합니다.

> [!NOTE]
> Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만듭니다.

1. Azure AD B2C 테 넌 트가 포함 된 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

2. **Azure 서비스**에서 **Azure AD B2C** 를 선택 하거나, **추가 서비스** 를 선택 하 고 **모든 서비스** 검색 상자를 사용 하 여 *Azure AD B2C*를 검색 합니다.

3. **Id 공급자**를 선택한 다음 **New openid connect Connect 공급자**를 선택 합니다.

4. 다음 정보를 사용 하 여 양식을 작성 합니다.

   |속성 | 값 |
   |------------ |------- |
   | 이름 | itsme |
   | 메타데이터 URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>`<environment>` `e2e` (테스트 환경) 또는 `prd` (프로덕션)  |
   | ClientID     | **클라이언트 ID**( **파트너 코드** 라고도 함)  |
   | 클라이언트 암호 | 사용자의 **client_secret** |
   | 범위  | openid connect service: YOURSERVICECODE profile email [phone] [address]  |
   |응답 형식 | 코드 |
   |응답 모드 | Query |
   |도메인 힌트 | *이는 비워 둘 수 있습니다.* |
   |UserID | sub |
   |표시 이름 | 이름 |
   |이름 | given_name |
   |Surname | family_name |
   |메일 | 이메일|

5. **저장**을 선택합니다.

### <a name="configure-a-user-flow"></a>사용자 흐름 구성

1. Azure AD B2C 테 넌 트에서 **정책**아래에서 **사용자 흐름**을 선택 합니다.

2. **새 사용자 흐름**을 선택 합니다.

3. **등록 및 로그인**을 선택 합니다.

4. **이름**을 입력합니다.

5. **Id 공급자** 섹션에서 **itsme**를 선택 합니다.

6. **만들기**를 선택합니다.

7. 사용자 흐름 이름을 선택 하 여 새로 만든 사용자 흐름을 엽니다.

8. **속성** 을 선택 하 고 다음 값을 조정 합니다.

   * **액세스 & ID 토큰 수명 (분)** 을 **5**로 변경 합니다.
   * **새로 고침 토큰 슬라이딩 윈도우 수명을** **만료 없음**으로 변경 합니다.

### <a name="register-an-application"></a>애플리케이션 등록

1. B2C 테 넌 트의 **관리**에서 **앱 등록**  >  **새 등록**을 선택 합니다.

2. 응용 프로그램의 **이름을** 입력 하 고 **리디렉션 URI**를 입력 합니다. 테스트 목적으로를 입력 `https://jwt.ms` 합니다.

3. Multi-factor authentication을 **사용 하지 않도록 설정**했는지 확인 합니다.

4. **등록**을 선택합니다.

   a. 테스트 목적으로 **인증**을 선택 하 고 **암시적 권한 부여**에서 **액세스 토큰** 및 **ID 토큰** 확인란을 선택 합니다.  

   b. **저장**을 선택합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. B2C 테 넌 트에서 **정책** 아래에서 **사용자 흐름**을 선택 합니다.

2. 이전에 만든 사용자 흐름을 선택 합니다.

3. **사용자 흐름 실행**을 선택합니다.

   a. **응용 프로그램**: *등록 된 앱을 선택 합니다* .

   b. **회신 url**: *리디렉션 url을 선택 합니다* .

4. Itsme **식별** 페이지가 나타납니다.  

5. 휴대폰 번호를 입력 하 고 **보내기**를 선택 합니다.

6. Itsme 앱에서 작업을 확인 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

* [Azure AD B2C의 사용자 지정 정책](custom-policy-overview.md)

* [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md?tabs=applications)