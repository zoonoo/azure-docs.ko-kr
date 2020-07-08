---
title: Azure Active Directory B2C와 IDology 통합
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 샘플 온라인 지불 앱을 IDology와 통합 하는 방법에 대해 알아봅니다. IDology는 여러 솔루션을 포함 하는 id 확인 및 교정 공급자입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d34bb14dd0f474ff9350fec513c02fbb470d6738
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385657"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 IDology 구성에 대 한 자습서 

이 샘플 자습서에서는 [IDology](https://www.idology.com/solutions/)와 Azure AD B2C를 통합 하는 방법에 대 한 지침을 제공 합니다. IDology는 여러 솔루션을 포함 하는 id 확인 및 교정 공급자입니다. 이 샘플에서는 IDology에서 제공 하는 모든 문제를 해결 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하려면 다음이 필요 합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Azure 구독에 연결 된 [Azure AD B2C 테 넌 트](tutorial-create-tenant.md) 입니다.

## <a name="scenario-description"></a>시나리오 설명

IDology 통합에는 다음 구성 요소가 포함 됩니다.

- Azure AD B2C – 사용자의 자격 증명을 확인 하는 권한 부여 서버입니다. Id 공급자 라고도 합니다.
- IDology – IDology 서비스는 사용자가 제공 하는 입력을 사용 하 여 사용자의 id를 확인 합니다.
- 사용자 지정 Rest API –이 API는 Azure AD와 IDology service 간의 통합을 구현 합니다.

다음 아키텍처 다이어그램에서는 구현을 보여 줍니다.

![IDology 아키텍처 다이어그램](media/partner-idology/idology-architecture-diagram.png)

|      |      |
|------|------|
|1     | 사용자가 로그인 페이지에 도착 합니다. |
|2     | 사용자가 등록 옵션을 선택 하 여 새 계정을 만들고 페이지에 정보를 입력 합니다. Azure AD B2C 사용자 특성을 수집 합니다. |
|3     | Azure AD B2C 중간 계층 API를 호출 하 고 사용자 특성을 전달 합니다. |
|4     | 중간 계층 API는 사용자 특성을 수집 하 고 IDOlogy API에서 사용할 수 있는 형식으로 변환 합니다. 그런 다음 IDology에 정보를 보냅니다. |
|5     | IDology는 정보를 사용 하 여 처리 한 다음 결과를 중간 계층 API에 반환 합니다. |
|6     | 중간 계층 API는 정보를 처리 하 고 Azure AD B2C에 관련 정보를 다시 보냅니다. |
|7     | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. **오류 응답이 표시** 되 면 사용자에 게 오류 메시지가 표시 됩니다. **성공** 응답이 표시 되 면 사용자가 인증 되 고 디렉터리에 기록 됩니다. |
|      |      |

> [!NOTE]
> 또한 고객에 게 단계별 인증을 수행 하도록 요청할 수 Azure AD B2C 있지만이 시나리오는이 자습서의 범위를 벗어나는 것입니다.

## <a name="onboard-with-idology"></a>IDology으로 등록

1. IDology는 [여기](https://www.idology.com/solutions/)에서 찾을 수 있는 다양 한 솔루션을 제공 합니다. 이 샘플의 경우, microsoft는 다음과 같은 경우에 사용 합니다.

2. IDology 계정을 만들려면 [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/)에 문의 하세요.

3. 계정이 만들어지면 API 구성에 필요한 정보를 받게 됩니다. 다음 섹션에서는이 프로세스에 대해 설명 합니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="part-1---deploy-the-api"></a>1 부-API 배포

제공 된 API 코드를 Azure 서비스에 배포 합니다. 이러한 [지침](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.

필요한 설정을 사용 하 여 Azure AD를 구성 하려면 배포 된 서비스의 URL이 필요 합니다.

### <a name="part-2---configure-the-api"></a>2 부-API 구성 

[Azure의 App Service에서](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)응용 프로그램 설정을 구성할 수 있습니다. 이 방법을 사용 하면 설정을 리포지토리로 체크 인하지 않고도 안전 하 게 구성할 수 있습니다. Rest API에 대 한 다음 설정을 제공 해야 합니다.

| 애플리케이션 설정 | 원본 | 참고 |
| :-------- | :------------| :-----------|
|IdologySettings: ApiUsername | IDology 계정 구성 |     |
|IdologySettings: ApiPassword | IDology 계정 구성 |     |
|WebApiSettings: ApiUsername |API에 대 한 사용자 이름 정의| ExtId 구성에서 사용 됨 |
|WebApiSettings: ApiPassword | API에 대 한 암호를 정의 합니다. | ExtId 구성에서 사용 됨

### <a name="part-3---create-api-policy-keys"></a>3 부-API 정책 키 만들기

이 [문서](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) 에 따라 두 개의 정책 키를 만듭니다. 하나는 api 사용자 이름, 하나는 위에서 정의한 api 암호입니다.

이 샘플 정책은 다음 키 이름을 사용 합니다.

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>4 부-Azure AD B2C 정책 구성

1. 이 [문서](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) 에 따라 [localaccounts 스타터 pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) 을 다운로드 하 고 Azure AD B2C 테 넌 트에 대 한 정책을 구성 합니다. **사용자 지정 정책 테스트** 섹션을 완료할 때까지 지침을 따릅니다.

2. [여기](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)에서 두 개의 샘플 정책을 다운로드 합니다.

3. 다음 두 가지 샘플 정책을 업데이트 합니다.

   1. 두 정책을 모두 엽니다.

      1. 섹션에서 `Idology-ExpectId-API` `ServiceUrl` 위에 배포 된 API의 위치로 메타 데이터 항목을 업데이트 합니다.

      1. `yourtenant`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.
      예를 들어 Azure AD B2C 테 넌 트의 이름이 인 경우  `contosotenant` 의 모든 인스턴스를  `yourtenant.onmicrosoft.com`   로 바꿉니다 `contosotenant.onmicrosoft.com` .

   1. 파일 TrustFrameworkExtensions.xml를 엽니다.

      1. 요소를 찾습니다  `<TechnicalProfile Id="login-NonInteractive">` . 의 두 인스턴스를  `IdentityExperienceFrameworkAppId`   앞에서 만든 IdentityExperienceFramework 응용 프로그램의 응용 프로그램 ID로 바꿉니다.

      1. 의 두 인스턴스를  `ProxyIdentityExperienceFrameworkAppId`   앞에서 만든 ProxyIdentityExperienceFramework 응용 프로그램의 응용 프로그램 ID로 바꿉니다.

4. 1 단계에서 이전에 업로드 한 SignInorSignUp.xml 및 TrustFrameworkExtensions.xml Azure AD B2C를 업데이트 된 두 개의 샘플 정책과 바꿉니다.

> [!NOTE]
> 모범 사례로, 고객은 특성 컬렉션 페이지에서 동의 알림을 추가 하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보를 보내도록 사용자에 게 알립니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테 넌 트를 열고 **정책**아래에서 **사용자 흐름**을 선택 합니다.

2. 이전에 만든 **사용자 흐름**을 선택 합니다.

3. **사용자 흐름 실행** 을 선택 하 고 설정을 선택 합니다.

   1. **응용 프로그램** -등록 된 앱을 선택 합니다 (JWT는 샘플).

   1. **회신 url** - **리디렉션 url**을 선택 합니다.

   1. **사용자 흐름 실행**을 선택합니다.

4. 등록 흐름을 진행 하 고 계정을 만듭니다.

5. 로그아웃합니다.

6. 로그인 흐름을 진행 합니다.

7. **Continue**를 입력 하면 IDology 퍼즐이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [Azure AD B2C의 사용자 지정 정책](custom-policy-overview.md)

- [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md?tabs=applications) 

