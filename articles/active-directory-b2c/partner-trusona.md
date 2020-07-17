---
title: Trusona 및 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 id 공급자로 Trusona를 추가 하 여 암호 없는 인증을 사용 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc0bcd4a978912dccc9f08802acbf2ec1151b3a1
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170108"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Azure Active Directory B2C와 Trusona 통합

Trusona는 암호 없는 인증, multi-factor authentication 및 디지털 라이선스 검색을 사용 하 여 로그인을 보호 하는 ISV (독립 소프트웨어 공급 업체) 공급자입니다. 이 문서에서는 Trusona을 id Azure AD B2C 공급자로 추가 하 여 암호 없는 인증을 사용 하도록 설정 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>전제 조건

시작 하려면 다음이 필요 합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](tutorial-create-tenant.md) 입니다.
* Trusona의 [평가판 계정](https://www.trusona.com/aadb2c)

## <a name="scenario-description"></a>시나리오 설명

이 시나리오에서 Trusona는 암호 없는 인증을 사용 하는 Azure AD B2C에 대 한 id 공급자 역할을 합니다. 솔루션을 구성 하는 구성 요소는 다음과 같습니다.

* Azure AD B2C 결합 된 로그인 및 등록 정책
* Trusona을 id 공급자로 Azure AD B2C에 추가 했습니다.
* 다운로드 가능한 Trusona 앱

![Trusona 아키텍처 다이어그램](media/partner-trusona/trusona-architecture-diagram.png)

| 단계 | 설명 |
|------|------|
|1     | 사용자가 응용 프로그램에 로그인 하거나 응용 프로그램에 등록 하려고 합니다. 사용자는 Azure AD B2C 등록 및 로그인 정책을 통해 인증 됩니다. 등록 하는 동안 Trusona 앱에서 사용자의 이전에 확인 된 전자 메일 주소가 사용 됩니다.     |
|2     | Azure B2C는 암시적 흐름을 사용 하 여 사용자를 OIDC (Trusona Openid connect Connect) id 공급자로 리디렉션합니다.     |
|3     | 데스크톱 PC 기반 로그인의 경우 Trusona는 Trusona 앱을 사용 하 여 검사 하기 위한 고유한 상태 비저장, 애니메이션 및 동적 QR 코드를 표시 합니다. 모바일 기반 로그인의 경우 Trusona는 "딥 링크"를 사용 하 여 Trusona 앱을 엽니다. 이러한 두 가지 방법은 장치 및 궁극적으로 사용자 검색에 사용 됩니다.     |
|4     | 사용자가 Trusona 앱을 사용 하 여 표시 된 QR 코드를 검색 합니다.     |
|5     | 사용자의 계정이 Trusona 클라우드 서비스에서 발견 되 고 인증이 준비 됩니다.     |
|6     | Trusona 클라우드 서비스는 Trusona 앱에 전송 된 푸시 알림을 통해 사용자에 게 인증 챌린지를 발급 합니다.<br>a. 사용자에 게 인증 챌린지를 묻는 메시지가 표시 됩니다. <br> b. 사용자가 챌린지를 수락 하거나 거부 하도록 선택 합니다. <br> c. 사용자에 게 OS 보안 (예: 생체 인식, 암호, PIN 또는 패턴)을 사용 하 여 보안 Enclave/신뢰할 수 있는 실행 환경에서 개인 키로 챌린지를 확인 하 고 서명 하 라는 메시지가 표시 됩니다. <br> d. Trusona 앱은 실시간으로 인증의 매개 변수를 기반으로 동적 재생 방지 페이로드를 생성 합니다. <br> e. 전체 응답은 보안 Enclave/신뢰할 수 있는 실행 환경의 개인 키로 서명 되 고 확인을 위해 Trusona 클라우드 서비스로 반환 됩니다.      |
|7     |  Trusona 클라우드 서비스는 id_token를 사용 하 여 사용자를 시작 응용 프로그램으로 다시 리디렉션합니다. Azure AD B2C는 id 공급자를 설치 하는 동안 구성 된 대로 Trusona의 게시 된 Openid connect 구성을 사용 하 여 id_token를 확인 합니다.    |
|  |  |

## <a name="onboard-with-trusona"></a>Trusona으로 등록

1. [양식을](https://www.trusona.com/aadb2c) 작성 하 여 Trusona 계정을 만들고 시작 합니다.

2. 앱 스토어에서 Trusona 모바일 앱을 다운로드 합니다. 앱을 설치 하 고 전자 메일을 등록 합니다.

3. 소프트웨어에서 보낸 보안 "매직 링크"를 통해 전자 메일을 확인 합니다.  

4. 셀프 서비스에 대 한 [Trusona Developer의 대시보드로](https://dashboard.trusona.com) 이동 합니다.

5. **준비** 를 선택 하 고 Trusona 앱을 사용 하 여 직접 인증 합니다.

6. 왼쪽 탐색 패널에서 **Oidc 통합**을 선택 합니다.

7. **Create Openid connect Connect 통합**을 선택 합니다.

8. 선택한 **이름을** 입력 하 고 **클라이언트 리디렉션 호스트 필드**에서 이전에 제공한 도메인 정보 (예: Contoso)를 사용 합니다.  

   > [!NOTE]
   > Azure Active Directory의 초기 도메인 이름이 클라이언트 리디렉션 호스트로 사용 됩니다.

9. [Trusona 통합 가이드](https://docs.trusona.com/integrations/aad-b2c-integration/)의 지침을 따릅니다. 메시지가 표시 되 면 이전 단계에서 언급 한 초기 도메인 이름 (예: Contoso)을 사용 합니다.  

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="add-a-new-identity-provider"></a>새 id 공급자 추가

> [!NOTE]
> Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만듭니다.

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.

4. **대시보드**  >  **Azure Active Directory B2C**  >  **id 공급자**로 이동 합니다.

3. **Id 공급자**를 선택 합니다.

4. **추가**를 선택합니다.

### <a name="configure-an-identity-provider"></a>Id 공급자 구성  

1. **Id 공급자 유형**  >  **openid connect Connect (미리 보기)** 를 선택 합니다.

2. 양식을 작성 하 여 id 공급자를 설정 합니다.  

   | 속성 | 값  |
   | :--- | :--- |
   | 메타데이터 URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | 클라이언트 ID | Trusona에서 전자 메일로 전송 됩니다. |
   | 범위 | Openid connect 프로필 전자 메일 |
   | 응답 형식 | Id_token |
   | 응답 모드  | Form_post |

3. **확인**을 선택합니다.  

4. **이 id 공급자의 클레임 매핑**을 선택 합니다.  

5. 양식을 작성 하 여 id 공급자를 매핑합니다.

   | 속성 | 값  |
   | :--- | :--- |
   | UserID | Sub  |
   | 표시 이름 | nickname |
   | 이름 | given_name |
   | Surname | Family_name |
   | 응답 모드 | 이메일 |

6. **확인** 을 선택 하 여 새 oidc id 공급자에 대 한 설정을 완료 합니다.

### <a name="create-a-user-flow-policy"></a>사용자 흐름 정책 만들기

1. 이제 B2C id 공급자에 나열 된 **새 Openid connect Connect Id 공급자로** Trusona이 표시 됩니다.

2. 왼쪽 탐색 패널에서 **사용자 흐름 (정책)** 을 선택 합니다.

3. **Add**  >  **새 사용자 흐름**추가  >  **등록 및 로그인을**선택 합니다.

### <a name="configure-the-policy"></a>정책 구성

1. 정책 이름을로 합니다.

2. 새로 만든 **Trusona Id 공급자**를 선택 합니다.

3. Trusona는 기본적으로 다단계 인증을 사용 하지 않도록 설정 하는 것이 좋습니다.

4. **만들기**를 선택합니다.

5. **사용자 특성 및 클레임**에서 **자세히 표시**를 선택 합니다. 양식에서 이전 섹션에서 id 공급자를 설정 하는 동안 지정한 특성을 하나 이상 선택 합니다.

6. **확인**을 선택합니다.  

### <a name="test-the-policy"></a>정책 테스트

1. 새로 만든 정책을 선택 합니다.

2. **사용자 흐름 실행**을 선택합니다.

3. 양식에서 회신 URL을 입력 합니다.

4. **사용자 흐름 실행**을 선택합니다. Trusona OIDC 게이트웨이로 리디렉션해야 합니다. Trusona 게이트웨이에서 Trusona mobile SDK를 사용 하 여 Trusona 앱 또는 사용자 지정 앱으로 표시 된 보안 QR 코드를 검색 합니다.

5. 보안 QR 코드를 검사 한 후 3 단계에서 정의한 회신 URL로 리디렉션해야 합니다.

## <a name="next-steps"></a>다음 단계  

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](custom-policy-overview.md)

- [AAD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md?tabs=applications)
