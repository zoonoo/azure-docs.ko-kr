---
title: Azure SignalR Service에서 관리되는 ID
description: Azure SignalR Service에서 관리 ID가 작동하는 방법 및 서버리스 시나리오에서 관리 ID를 사용하는 방법을 알아봅니다.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731587"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Azure SignalR Service용 관리 ID

이 문서에서는 Azure SignalR Service용 관리 ID를 만드는 방법과 서버리스 시나리오에서 이를 사용하는 방법을 보여줍니다.

> [!Important] 
> Azure SignalR Service는 관리 ID를 하나만 지원할 수 있습니다. 즉, 시스템 할당 ID 또는 사용자 할당 ID를 추가할 수 있습니다. 

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가

Azure Portal에서 관리 ID를 설정하려면 먼저 Azure SignalR Service 인스턴스를 만든 다음, 기능을 설정합니다.

1. 평소처럼 포털에서 Azure SignalR Service 인스턴스를 만듭니다. 포털에서 찾아봅니다.

2. **ID** 를 선택합니다.

4. **시스템 할당** 탭에서 **상태** 를 **켜기** 로 바꿉니다. **저장** 을 선택합니다.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="포털에서 시스템 할당 ID 추가":::

## <a name="add-a-user-assigned-identity"></a>사용자 할당 ID 추가

사용자 할당 ID로 Azure SignalR Service 인스턴스를 만들려면 ID를 만든 다음, 리소스 ID를 서비스에 추가해야 합니다.

1. [이러한 지침](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)에 따라 사용자 할당 관리 ID 리소스를 만듭니다.

2. 평소처럼 포털에서 Azure SignalR Service 인스턴스를 만듭니다. 포털에서 찾아봅니다.

3. **ID** 를 선택합니다.

4. **사용자 할당** 탭에서 **추가** 를 클릭합니다.

5. 이전에 만든 ID를 검색한 후 선택합니다. **추가** 를 선택합니다.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="포털에서 사용자 할당 ID 추가":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>서버리스 시나리오에서 관리 ID 사용

Azure SignalR Service는 완전 관리형 서비스이므로 관리 ID를 사용하여 토큰을 수동으로 가져올 수 없습니다. 대신, Azure SignalR Service는 사용자가 설정하는 관리 ID를 사용하여 액세스 토큰을 가져옵니다. 그런 다음 서비스는 서버리스 시나리오의 업스트림 요청에서 액세스 토큰을 `Authorization` 헤더로 설정합니다.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>업스트림 설정에서 관리 ID 인증 사용

1. 시스템 할당 ID 또는 사용자 할당 ID를 추가합니다.

2. 업스트림 설정 하나를 추가하고 별표를 클릭하여 아래와 같이 상세 페이지로 이동합니다.
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="pre-msi-setting":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. 관리 ID 인증 설정에서 **리소스** 에 대해 대상 리소스를 지정할 수 있습니다. 리소스는 획득된 액세스 토큰의 `aud` 클레임이 되며 업스트림 엔드포인트에서 유효성 검사의 일부로 사용할 수 있습니다. 리소스는 다음 중 하나일 수 있습니다.
    - Empty
    - 서비스 주체의 애플리케이션(클라이언트) ID
    - 서비스 주체의 애플리케이션 ID URI
    - [Azure 서비스의 리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > 서비스에서 직접 액세스 토큰의 유효성을 검사하는 경우 리소스 형식 중 하나를 선택할 수 있습니다. **인증** 설정에서 **리소스** 값과 유효성 검사가 일치하는지 확인합니다. 데이터 평면에 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하는 경우 서비스 공급자가 요청하는 리소스를 사용해야 합니다.

### <a name="validate-access-tokens"></a>액세스 토큰 유효성 검사

`Authorization` 헤더의 토큰은 [Microsoft ID 플랫폼 액세스 토큰](../active-directory/develop/access-tokens.md#validating-tokens)입니다.

액세스 토큰의 유효성을 검사하려면 앱에서 대상 그룹 및 서명 토큰의 유효성을 검사해야 합니다. OpenID 검색 문서에 있는 값에 대해 유효성 검사를 수행해야 합니다. 예를 들어 문서의 [테넌트 독립적 버전](https://login.microsoftonline.com/common/.well-known/openid-configuration)을 참조하세요.

Azure Active Directory(Azure AD) 미들웨어에는 액세스 토큰의 유효성을 검사하기 위한 기본 제공 기능이 있습니다. [샘플](../active-directory/develop/sample-v2-code.md)을 통해 원하는 언어로 된 샘플을 찾을 수 있습니다.

토큰 유효성 검사를 처리하는 방법을 보여 주는 라이브러리와 코드 샘플이 제공됩니다. JWT(JSON 웹 토큰) 유효성 검사에 사용할 수 있는 몇 가지 오픈 소스 파트너 라이브러리도 있습니다. 거의 모든 플랫폼 및 언어에 대해 하나 이상의 옵션이 있습니다. Azure AD 인증 라이브러리 및 코드 샘플에 대한 자세한 내용은 [Microsoft ID 플랫폼 인증 라이브러리](../active-directory/develop/reference-v2-libraries.md)를 참조하세요.

#### <a name="authentication-in-function-app"></a>함수 앱 인증

함수 앱에서 액세스 토큰 유효성 검사를 코드 작업 없이 쉽고 효율적으로 설정할 수 있습니다.

1. **인증/권한 부여** 페이지에서 **App Service 인증** 을 **켜기** 로 전환합니다.

2. **요청이 인증되지 않은 경우 수행할 작업** 에서 **Azure Active Directory로 로그인** 을 선택합니다.

3. 인증 공급자에서 **Azure Active Directory** 를 클릭합니다.

4. 새 페이지에서. **기본** 및 **AD 앱 새로 만들기** 를 선택한 다음 **확인** :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="함수 Aad":::를 클릭합니다.

5. SignalR Service로 이동하여 시스템 할당 ID 또는 사용자 할당 ID를 추가하는 [단계](howto-use-managed-identity.md#add-a-system-assigned-identity)를 수행합니다.

6. SignalR Service에서 **업스트림 설정** 으로 이동하여 **관리 ID 사용** 및 **기존 애플리케이션에서 선택** 을 선택합니다. 이전에 만든 애플리케이션을 선택합니다.

이러한 설정이 끝나면 함수 앱은 헤더에 액세스 토큰이 없는 요청을 거부합니다.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Key Vault 참조에 관리 ID 사용

SignalR Service는 관리 ID를 사용하여 비밀을 가져오기 위해 Key Vault에 액세스할 수 있습니다.

1. Azure SignalR Service에 대한 시스템 할당 ID 또는 사용자 할당 ID를 추가합니다.

2. Key Vault의 액세스 정책에서 관리 ID에 대한 비밀 읽기 권한을 부여합니다. [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md) 참조

현재 이 기능은 다음과 같은 시나리오에서 사용할 수 있습니다.

- [업스트림 URL 패턴의 참조 비밀](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>다음 단계

- [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](signalr-concept-serverless-development-config.md)