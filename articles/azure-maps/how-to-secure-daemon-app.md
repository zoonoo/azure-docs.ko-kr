---
title: 디먼 애플리케이션을 보호하는 방법
titleSuffix: Azure Maps
description: Azure Portal을 통해 인증을 관리하여 신뢰할 수 있는 디먼 애플리케이션을 구성합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895650"
---
# <a name="secure-a-daemon-application"></a>디먼 애플리케이션 보호

다음 가이드는 신뢰할 수 있는 보안 환경에서 호스트되는 백그라운드 프로세스, 타이머 및 작업과 관련됩니다. 예를 들어, Azure Web Jobs, Azure Function Apps, Windows 서비스, 기타 신뢰할 수 있는 백그라운드 서비스 등이 있습니다.

> [!Tip]
> 프로덕션 애플리케이션을 위해 Azure AD(Azure Active Directory) 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 구현하는 것이 좋습니다. 개념에 관한 개요는 [Azure Maps 인증](./azure-maps-authentication.md)을 참조하세요.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>시나리오: 공유 키 인증

Azure Maps 계정을 만들면 기본 키와 보조 키가 생성됩니다. [공유 키 인증을 사용하여 Azure Maps를 호출](./azure-maps-authentication.md#shared-key-authentication)하는 경우 기본 키를 구독 키로 사용하는 것이 좋습니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다. 자세한 내용은 [Azure Maps의 인증](./azure-maps-authentication.md)을 참조하세요.

### <a name="securely-store-shared-key"></a>공유 키를 안전하게 저장

기본 키와 보조 키를 사용하여 모든 API에 대한 권한을 Maps 계정에 부여할 수 있습니다. 애플리케이션은 Azure Key Vault와 같은 보안 저장소에 키를 저장해야 합니다. 애플리케이션 구성에서 공유 키를 일반 텍스트로 저장하지 않으려면 애플리케이션은 공유 키를 Azure Key Vault 비밀로 검색해야 합니다. Azure Key Vault 구성하는 방법을 이해하려면 [Azure Key Vault 개발자 가이드](../key-vault/general/developers-guide.md)를 참조하세요.

다음 단계에서는 이 프로세스를 간략하게 설명합니다.

1. Azure Key Vault를 만듭니다.
2. 앱 등록 또는 관리 ID를 만들어 Azure AD 서비스 주체를 만듭니다. 생성된 주체는 Azure Key Vault에 액세스해야 합니다.
3. Azure Key 비밀 `Get` 권한에 대한 액세스 권한을 서비스 주체에 할당합니다.
4. 비밀 `Set` 권한에 대한 액세스 권한을 개발자에게 일시적으로 할당합니다.
5. Key Vault 비밀에서 공유 키를 설정하고, 비밀 ID를 디먼 애플리케이션의 구성으로 참조하고, 비밀 `Set` 권한을 제거합니다.
6. 디먼 애플리케이션에서 Azure AD 인증을 구현하여 Azure Key Vault에서 공유 키 비밀을 검색합니다.
7. 공유 키를 사용하여 Azure Maps REST API 요청을 만듭니다.

> [!Tip]
> 앱이 Azure 환경에서 호스트되는 경우 관리 ID를 구현하여 Azure Key Vault에 인증하기 위해 비밀을 관리하는 비용과 복잡성을 줄여야 합니다. 관리 ID를 통해 연결하려면 다음 [Azure Key Vault 자습서](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)를 참조하세요.

디먼 애플리케이션은 보안 스토리지에서 공유 키를 검색해야 합니다. Azure Key Vault를 사용한 구현이 비밀에 액세스하려면 Azure AD를 통한 인증이 필요합니다. 대신 공유 키 인증 사용과 관련된 추가적인 복잡성과 운영 요구 사항으로 인해 Azure Maps에 대한 직접 Azure AD 인증을 권장합니다.

> [!IMPORTANT]
> 키 다시 생성을 간소화하기 위해 애플리케이션이 한 번에 하나의 키를 사용하는 것이 좋습니다. 그러면 애플리케이션은 사용되지 않는 키를 다시 생성하고 새로 다시 생성된 키를 Azure Key Vault와 같은 보안 비밀 저장소에 배포할 수 있습니다.

## <a name="scenario-azure-ad-role-based-access-control"></a>시나리오: Azure AD 역할 기반 액세스 제어

Azure Maps 계정이 만들어지면 Azure Maps `x-ms-client-id` 값이 Azure Portal 인증 정보 페이지에 표시됩니다. 이 값은 REST API 요청에 사용되는 계정을 나타냅니다. 이 값은 애플리케이션 구성에 저장되고 HTTP 요청을 만들기 전에 검색되어야 합니다. 이 시나리오의 목표는 디먼 애플리케이션이 Azure AD에 인증하고 Azure Maps REST API를 호출할 수 있도록 하는 것입니다.

> [!Tip]
> 관리 ID 구성 요소를 활용하려면 Azure Virtual Machines, Virtual Machine Scale Sets 또는 App Services에서 호스트하는 것이 좋습니다.

### <a name="daemon-hosted-on-azure-resources"></a>Azure 리소스에서 호스트되는 디먼

Azure 리소스에서 실행하는 경우 적은 비용으로 최소한의 자격 증명 관리 작업을 수행할 수 있도록 Azure 관리 ID를 구성합니다. 

관리 ID에 대한 애플리케이션 액세스를 사용하려면 [관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

관리 ID의 이점은 다음과 같습니다.

* Azure 시스템 관리형 X509 인증서 퍼블릭 키 암호화 인증.
* 클라이언트 비밀 대신 X509 인증서를 사용하는 Azure AD 보안.
* Azure는 관리 ID 리소스와 연결된 모든 인증서를 관리하고 갱신합니다.
* Azure Key Vault와 같은 보안 비밀 저장소 서비스를 사용할 필요 없이 간소화된 자격 증명 운영 관리. 

### <a name="daemon-hosted-on-non-azure-resources"></a>비 Azure 리소스에서 호스트되는 디먼

비 Azure 환경에서 실행하는 경우 관리 ID를 사용할 수 없습니다. 따라서 디먼 애플리케이션에 대해 Azure AD 애플리케이션 등록을 통해 서비스 주체를 구성해야 합니다.

1. Azure Portal에 있는 Azure 서비스의 목록에서 **Azure Active Directory** > **앱 등록** > **새 등록** 을 선택합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

2. 앱을 이미 등록한 경우에는 다음 단계로 계속 진행합니다. 앱을 등록하지 않은 경우 **이름** 을 입력하고, **지원 계정 유형** 을 선택한 다음, **등록** 을 선택합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록 세부 정보](./media/how-to-manage-authentication/app-create.png)

3. 위임된 API 권한을 Azure Maps에 할당하려면 애플리케이션으로 이동합니다. 그런 다음, **앱 등록** 아래에서 **API 권한** > **권한 추가** 를 선택합니다. **내 조직에서 사용하는 API** 에서 **Azure Maps** 를 검색하고 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

4. **Azure Maps에 액세스** 옆의 확인란을 선택한 다음 **권한 추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

5. 다음 단계를 완료하여 클라이언트 비밀을 만들거나 인증서를 구성합니다.

    * 애플리케이션이 서버 또는 애플리케이션 인증을 사용하는 경우 앱 등록 페이지에서 **인증서 및 비밀** 로 이동합니다. 그런 다음, 퍼블릭 키 인증서를 업로드하거나 **새 클라이언트 비밀** 을 선택하여 암호를 만듭니다.

        > [!div class="mx-imgBorder"]
        > ![클라이언트 비밀 만들기](./media/how-to-manage-authentication/app-keys.png)

    * **추가** 를 선택한 후 비밀을 복사하고 Azure Key Vault와 같은 서비스에 안전하게 저장합니다. [Azure Key Vault 개발자 가이드](../key-vault/general/developers-guide.md)를 검토하여 인증서나 비밀을 안전하게 저장합니다. 이 비밀을 사용하여 Azure AD에서 토큰을 가져옵니다.

        > [!div class="mx-imgBorder"]
        > ![클라이언트 암호 추가](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>디먼 애플리케이션의 역할 기반 액세스 권한을 Azure Maps에 부여

하나 이상의 Azure Maps 역할 정의에 생성된 관리 ID 또는 서비스 주체를 할당하여 ‘Azure RBAC(Azure 역할 기반 액세스 제어)’를 부여합니다. Azure Maps에 사용할 수 있는 Azure 역할 정의를 보려면 **Access control(IAM)** 로 이동합니다. **역할** 을 선택한 다음, *Azure Maps* 로 시작하는 역할을 검색합니다. 이 Azure Maps 역할에 액세스 권한을 부여할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. **Azure Maps 계정** 으로 이동합니다. **액세스 제어(IAM)** > **역할 할당** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure RBAC를 사용하여 액세스 권한 부여](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **역할 할당** 탭에서 역할 할당을 **추가** 합니다. 

    > [!div class="mx-imgBorder"]
    > ![추가가 선택된 역할 할당을 보여 주는 스크린샷](./media/how-to-manage-authentication/add-role-assignment.png)

3. **Azure Maps 데이터 읽기 권한자** 또는 **Azure Maps 데이터 기여자** 와 같은 기본 제공 Azure Maps 역할 정의를 선택합니다. **다음에 대한 액세스 할당** 에서 **Azure AD 사용자, 그룹 또는 서비스 주체** 를 선택하거나 **사용자가 할당한 관리 ID** / **시스템이 할당한 관리 ID** 가 있는 관리 ID를 선택합니다. 주체를 선택합니다. 그런 다음 **저장** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![역할 할당을 추가하는 방법](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. 역할 할당 탭에서 역할 할당이 적용되었는지 확인할 수 있습니다.

## <a name="request-token-with-managed-identity"></a>관리 ID를 사용하여 토큰 요청

호스팅 리소스의 관리 ID가 구성되면 Azure SDK 또는 REST API를 사용하여 Azure Maps의 토큰을 획득합니다. 자세한 내용은 [액세스 토큰 획득](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요. 가이드에 따르면 REST API 요청에서 사용할 수 있는 액세스 토큰이 반환되어야 합니다.

## <a name="request-token-with-application-registration"></a>애플리케이션 등록을 사용하여 토큰 요청

앱을 등록하고 Azure Maps와 연결한 후 액세스 토큰을 요청할 수 있습니다.

* Azure AD 리소스 ID `https://atlas.microsoft.com/`
* Azure AD 앱 ID
* Azure AD 테넌트 ID
* Azure AD 앱 등록 클라이언트 비밀

요청:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

응답:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

더 자세한 예제는 [Azure AD 인증 시나리오](../active-directory/develop/authentication-vs-authorization.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Maps 계정에 대한 API 사용 현황 메트릭을 확인하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합하는 방법을 보여 주는 샘플을 살펴보세요.
> [!div class="nextstepaction"]
> [Azure Maps 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)