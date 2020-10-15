---
title: Azure SignalR Service의 관리 되는 id
description: Azure SignalR Service에서 관리 id가 작동 하는 방법 및 서버를 사용 하지 않는 시나리오에서 관리 되는 id를 사용 하는 방법을 알아봅니다.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: cc7082744bc43baad2e26d09a83907540cf6a1df
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094078"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Azure SignalR Service에 대 한 관리 되는 id

이 문서에서는 Azure SignalR Service에 대 한 관리 되는 id를 만드는 방법과 서버를 사용 하지 않는 시나리오에서이를 사용 하는 방법을 보여 줍니다.

> [!Important] 
> Azure SignalR Service는 관리 되는 id를 하나만 지원할 수 있습니다. 즉, 시스템 할당 id 또는 사용자 할당 id를 추가할 수 있습니다. 

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가

Azure Portal에서 관리 되는 id를 설정 하려면 먼저 Azure SignalR Service 인스턴스를 만든 후 기능을 사용 하도록 설정 합니다.

1. 평소 처럼 포털에서 Azure SignalR Service 인스턴스를 만듭니다. 포털에서 찾습니다.

2. **ID**를 선택합니다.

4. **시스템 할당 됨** 탭에서 **상태** 를 **켜기**로 전환 합니다. **저장**을 선택합니다.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="포털에서 시스템 할당 id 추가":::

## <a name="add-a-user-assigned-identity"></a>사용자 할당 ID 추가

사용자 할당 id를 사용 하 여 Azure SignalR Service 인스턴스를 만들려면 id를 만든 다음 서비스에 해당 리소스 식별자를 추가 해야 합니다.

1. [이러한 지침](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)에 따라 사용자 할당 관리 ID 리소스를 만듭니다.

2. 평소 처럼 포털에서 Azure SignalR Service 인스턴스를 만듭니다. 포털에서 찾습니다.

3. **ID**를 선택합니다.

4. **사용자 할당 됨** 탭에서 **추가**를 선택 합니다.

5. 이전에 만든 id를 검색 하 여 선택 합니다. **추가**를 선택합니다.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="포털에서 시스템 할당 id 추가":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>서버를 사용 하지 않는 시나리오에서 관리 되는 id 사용

Azure SignalR Service는 완전히 관리 되는 서비스 이므로 관리 되는 id를 사용 하 여 토큰을 수동으로 가져올 수 없습니다. 대신, Azure SignalR Service는 사용자가 설정 하는 관리 id를 사용 하 여 액세스 토큰을 가져옵니다. 그런 다음 서비스는 서버를 사용 하지 `Authorization` 않는 시나리오의 업스트림 요청에서 액세스 토큰을 헤더로 설정 합니다.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>업스트림 설정에서 관리 되는 id 인증 사용

1. 시스템이 할당 한 id 또는 사용자 할당 id를 추가 합니다.

2. 업스트림 설정을 구성 하 고 **인증** 설정으로 **microsoft.managedidentity** 을 사용 합니다. 인증을 사용 하 여 업스트림 설정을 만드는 방법을 알아보려면 [업스트림 설정](concept-upstream.md)을 참조 하세요.

3. 관리 되는 id 인증 설정에서 **리소스**에 대해 대상 리소스를 지정할 수 있습니다. 리소스는 `aud` 획득 된 액세스 토큰의 클레임이 되며 업스트림 끝점에서 유효성 검사의 일부로 사용할 수 있습니다. 리소스는 다음 중 하나일 수 있습니다.
    - Empty
    - 서비스 주체의 응용 프로그램 (클라이언트) ID
    - 서비스 주체의 응용 프로그램 ID URI
    - [Azure 서비스의 리소스 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > 서비스에서 직접 액세스 토큰의 유효성을 검사 하는 경우 리소스 형식 중 하나를 선택할 수 있습니다. **인증** 설정에서 **리소스** 값과 유효성 검사가 일치 하는지 확인 합니다. 데이터 평면에 Azure RBAC (역할 기반 액세스 제어)를 사용 하는 경우 서비스 공급자가 요청 하는 리소스를 사용 해야 합니다.

### <a name="validate-access-tokens"></a>액세스 토큰 유효성 검사

헤더의 토큰은 `Authorization` [Microsoft id 플랫폼 액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens)입니다.

액세스 토큰의 유효성을 검사 하기 위해 앱은 대상 및 서명 토큰의 유효성도 검사 해야 합니다. OpenID 검색 문서에 있는 값에 대해 유효성 검사를 수행해야 합니다. 예를 들어 [테 넌 트 독립적 버전의 문서](https://login.microsoftonline.com/common/.well-known/openid-configuration)를 참조 하세요.

Azure Active Directory (Azure AD) 미들웨어는 액세스 토큰의 유효성을 검사 하기 위한 기본 제공 기능을 제공 합니다. [샘플](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) 을 탐색 하 여 선택한 언어로 찾을 수 있습니다.

토큰 유효성 검사를 처리하는 방법을 보여 주는 라이브러리와 코드 샘플이 제공됩니다. JWT (JSON Web Token) 유효성 검사에 사용할 수 있는 몇 가지 오픈 소스 파트너 라이브러리도 있습니다. 거의 모든 플랫폼 및 언어에 대 한 옵션은 하나 이상 있습니다. Azure AD 인증 라이브러리 및 코드 샘플에 대 한 자세한 내용은 [Microsoft id 플랫폼 인증 라이브러리](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](signalr-concept-serverless-development-config.md)
