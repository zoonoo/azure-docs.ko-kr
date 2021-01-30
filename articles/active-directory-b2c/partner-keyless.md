---
title: Azure Active Directory B2C로 키가 없는 구성에 대 한 자습서
titleSuffix: Azure AD B2C
description: 암호 없는 인증을 위해 Azure Active Directory B2C를 사용 하 여 키가 없는를 구성 하기 위한 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c8a5666d373852da5ff79490f435b2d66d5cc6e0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090369"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C을 사용 하 여 키가 없는 구성

이 샘플 자습서에서는 [키가 없는](https://keyless.io/)로 AD (AZURE ACTIVE DIRECTORY) B2C를 구성 하는 방법에 대 한 지침을 제공 합니다. Azure AD B2C을 Id 공급자로 사용 하 여 키가 없는를 고객 응용 프로그램과 통합 하 여 사용자에 게 진정한 암호 없는 인증을 제공할 수 있습니다.

키가 없는 's **키가 없는 Zero-Knowledge 생체 인식 (zkb™)** 은 고객 환경을 개선 하 고 개인 정보를 보호 하는 동시에 사기 행위, 피싱 및 자격 증명 재사용을 제거 하는 암호 없는 다단계 인증을 제공 합니다.

## <a name="pre-requisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)입니다. 테 넌 트는 Azure 구독에 연결 되어야 합니다.

- 키가 없는 cloud 테 넌 트, 무료 [평가판 계정](https://keyless.io/go)받기.

- 사용자의 장치에 설치 된 키가 없는 Authenticator 앱입니다.

## <a name="scenario-description"></a>시나리오 설명

키가 없는 통합에는 다음 구성 요소가 포함 됩니다.

- Azure AD B2C – 사용자의 자격 증명 (id 공급자 라고도 함)을 확인 하는 역할을 하는 권한 부여 서버입니다.

- 웹 및 모바일 응용 프로그램 – 키가 없는 및 Azure AD B2C를 사용 하 여 보호 하도록 선택 하는 모바일 또는 웹 응용 프로그램입니다.

- 키가 없는 모바일 앱 – 키가 없는 mobile 앱은 Azure AD B2C 사용 응용 프로그램에 대 한 인증에 사용 됩니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![키가 없는 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-keyless/keyless-architecture-diagram.png)

|단계 | Description |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 도착 합니다. 사용자가 로그인/등록을 선택 하 고 사용자 이름을 입력 합니다.
| 2. | 응용 프로그램은 id 확인을 위해 Azure AD B2C 사용자 특성을 보냅니다.
| 3. | Azure AD B2C 사용자 특성을 수집 하 고 키가 없는 mobile 앱을 통해 사용자를 인증 하기 위해 특성을 키가 없는에 보냅니다.
| 4. | 키가 없는는 얼굴 생체 인식 검색의 형태로 개인 정보 유지 인증을 위해 등록 된 사용자의 모바일 장치에 푸시 알림을 보냅니다.
| 5. | 사용자가 푸시 알림에 응답 한 후에는 사용자에 게 확인 결과에 따라 고객 응용 프로그램에 대 한 액세스 권한이 부여 되거나 거부 됩니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="add-a-new-identity-provider"></a>새 Id 공급자 추가

새 Id 공급자를 추가 하려면 다음 단계를 수행 합니다.

1. **[Azure Portal](https://portal.azure.com/#home)** 에 Azure AD B2C 테 넌 트의 전역 관리자로 로그인 합니다.

2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.

4. **대시보드**  >  **Azure Active Directory B2C**  >   **id 공급자** 로 이동 합니다.

5. **Id 공급자** 를 선택 합니다.

6. **추가** 를 선택합니다.

### <a name="configure-an-identity-provider"></a>Id 공급자 구성

Id 공급자를 구성 하려면 다음 단계를 수행 합니다.

1. **Id 공급자 유형**  >  **openid connect Connect (미리 보기)를** 선택 합니다.
2. 양식을 작성 하 여 Id 공급자를 설정 합니다.

   |속성 | 값 |
   |:-----| :-----------|
   | 이름   | Keyless |
   | 메타데이터 URL | 호스트 된 키가 없는 인증 앱의 URI 뒤에 ' '과 같은 특정 경로를 삽입 합니다. https://keyless.auth/.well-known/openid-configuration |
   | 클라이언트 암호 | 키가 없는 인증 인스턴스와 연결 된 비밀입니다. 이전에 구성 된 것과 동일 하지 않습니다. 선택한 복합 문자열을 삽입 합니다. 이 비밀은 나중에 키가 없는 컨테이너 구성에서 사용 됩니다.|
   | 클라이언트 ID | 클라이언트의 ID입니다. 이 ID는 나중에 키가 없는 컨테이너 구성에서 사용 됩니다.|
   | Scope | openid |
   | 응답 형식 | id_token |
   | 응답 모드 | form_post|

3. **확인** 을 선택합니다.

4. **이 id 공급자의 클레임 매핑** 을 선택 합니다.

5. 양식을 작성 하 여 Id 공급자를 매핑합니다.

   |속성 | 값 |
   |:-----| :-----------|
   | UserID    | 원본 구독 |
   | 표시 이름 | 원본 구독 |
   | 응답 모드 | 원본 구독 |

6. **저장** 을 선택 하 여 새 Oidc (Open id Connect) id 공급자에 대 한 설정을 완료 합니다.

### <a name="create-a-user-flow-policy"></a>사용자 흐름 정책 만들기

이제 B2C id 공급자 내에 나열 된 새 OIDC Id 공급자로 키가 없는이 표시 됩니다.

1. Azure AD B2C 테 넌 트에서 **정책** 아래에서 **사용자 흐름** 을 선택 합니다.

2. **새** 사용자 흐름을 선택 합니다.

3. **등록 및 로그인** 을 선택 하 고, **버전** 을 선택한 다음, **만들기** 를 선택 합니다.

4. 정책의 **이름을** 입력 합니다.

5. Id 공급자 섹션에서 새로 만든 키가 없는 Id 공급자를 선택 합니다.

6. 사용자 흐름의 매개 변수를 설정 합니다. 이름을 삽입 하 고 만든 Id 공급자를 선택 합니다. 전자 메일 주소를 추가할 수도 있습니다. 이 경우 Azure는 로그인 프로시저를 키가 없는로 직접 리디렉션하지 않습니다. 대신 사용자가 사용 하려는 옵션을 선택할 수 있는 화면이 표시 됩니다.

7. **Multi-factor Authentication** 필드는 그대로 둡니다.

8. **조건부 액세스 정책 적용** 선택

9. **사용자 특성 및 토큰 클레임** 아래에서 특성 수집 옵션에서 **전자 메일 주소** 를 선택 합니다. Azure AD B2C 클라이언트 응용 프로그램에 반환할 수 있는 클레임과 함께 사용자에 대 한 정보를 수집할 수 Azure Active Directory 모든 특성을 추가할 수 있습니다.

10. **만들기** 를 선택합니다.

11. 만들기가 완료 되 면 새 **사용자 흐름** 을 선택 합니다.

12. 왼쪽 패널에서 **응용 프로그램 클레임** 을 선택 합니다. 옵션에서 **전자 메일** 확인란을 선택 하 고 **저장** 을 선택 합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 정책에서 Id 경험 프레임 워크를 선택 합니다.

2. 이전에 만든 SignUpSignIn을 선택 합니다.

3. 사용자 흐름 실행을 선택 하 고 설정을 선택 합니다.

   a. 응용 프로그램: 등록 된 앱을 선택 합니다 (JWT는 샘플).

   b. 회신 URL: 리디렉션 URL을 선택 합니다.

   c. 사용자 흐름 실행을 선택합니다.

4. 등록 흐름으로 이동 하 여 계정 만들기

5. 사용자 특성을 만든 후 흐름 중에 키가 없는가 호출 됩니다. 흐름이 불완전 한 경우 사용자가 디렉터리에 저장 되지 않았는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
