---
title: 디먼 애플리케이션을 보호하는 방법
titleSuffix: Azure Maps
description: Azure Portal를 사용 하 여 신뢰할 수 있는 디먼 응용 프로그램을 구성 하는 인증을 관리 합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 066118622f19d7efac71ddd66ac1abe058008b55
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87126774"
---
# <a name="secure-a-daemon-application"></a>디먼 응용 프로그램 보안

다음 가이드는 신뢰할 수 있고 안전한 환경에서 호스팅되는 백그라운드 프로세스, 타이머 및 작업에 대 한 것입니다. Azure 웹 작업, Azure 함수 앱, Windows 서비스 및 기타 안정적인 백그라운드 서비스를 예로 들 수 있습니다.

> [!Tip]
> 프로덕션 응용 프로그램의 경우 Azure AD (Azure Active Directory) 및 RBAC (역할 기반 액세스 제어)를 구현 하는 것이 좋습니다. 개념에 대 한 개요는 [Azure Maps 인증](./azure-maps-authentication.md)을 참조 하세요.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>시나리오: 공유 키 인증

Azure Maps 계정을 만든 후에는 기본 키와 보조 키가 생성 됩니다. [공유 키 인증을 사용 하 여 Azure Maps를 호출](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)하는 경우 기본 키를 구독 키로 사용 하는 것이 좋습니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다. 자세한 내용은 [Azure Maps의 인증](https://aka.ms/amauth)을 참조 하세요.

### <a name="securely-store-shared-key"></a>안전 하 게 공유 키 저장

기본 및 보조 키를 사용 하 여 Maps 계정의 모든 Api에 대 한 권한을 부여할 수 있습니다. 응용 프로그램은 Azure Key Vault와 같은 보안 저장소에 키를 저장 해야 합니다. 응용 프로그램은 공유 키를 Azure Key Vault 암호로 검색 하 여 응용 프로그램 구성에서 공유 키를 일반 텍스트로 저장 하지 않도록 해야 합니다. Azure Key Vault를 구성 하는 방법을 이해 하려면 [Azure Key Vault 개발자 가이드](https://docs.microsoft.com/azure/key-vault/general/developers-guide)를 참조 하세요.

다음 단계는이 프로세스를 간략하게 설명 합니다.

1. Azure Key Vault를 만듭니다.
2. 앱 등록 또는 관리 id를 만들어 Azure AD 서비스 주체를 만듭니다. 생성 된 보안 주체는 Azure Key Vault에 액세스 해야 합니다.
3. Azure 키 암호에 대 한 서비스 주체 액세스 `Get` 권한을 할당 합니다.
4. `Set`개발자로 서 사용자를 위해 암호 사용 권한에 일시적으로 액세스 권한을 할당 합니다.
5. Key Vault 암호에 공유 키를 설정 하 고 보안 ID를 디먼 응용 프로그램의 구성으로 참조 하 고 비밀 `Set` 사용 권한을 제거 합니다.
6. 디먼 응용 프로그램에서 Azure AD 인증을 구현 하 여 Azure Key Vault에서 공유 키 암호를 검색 합니다.
7. 공유 키를 사용 하 여 Azure Maps REST API 요청을 만듭니다.

> [!Tip]
> 앱이 Azure 환경에서 호스트 되는 경우 관리 되는 Id를 구현 하 여 Azure Key Vault에 인증 하는 데 사용할 암호를 관리 하는 비용과 복잡성을 줄여야 합니다. [관리 id를 통해 연결 하려면](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)다음 Azure Key Vault 자습서를 참조 하세요.

디먼 응용 프로그램은 보안 저장소에서 공유 키를 검색 합니다. Azure Key Vault를 사용 하 여 구현 하려면 비밀에 액세스 하기 위해 Azure AD를 통해 인증을 수행 해야 합니다. 대신, 공유 키 인증 사용에 대 한 추가 복잡성 및 운영 요구 사항으로 인해 Azure AD RBAC 인증을 Azure Maps 하는 것이 좋습니다.

> [!IMPORTANT]
> 키 다시 생성을 간소화 하기 위해 응용 프로그램에서 한 번에 하나의 키를 사용 하는 것이 좋습니다. 그러면 응용 프로그램에서 사용 되지 않는 키를 다시 생성 하 고 새 다시 생성 된 키를 Azure Key Vault와 같은 보안 된 비밀 저장소에 배포할 수 있습니다.

## <a name="scenario-azure-ad-role-based-access-control"></a>시나리오: Azure AD 역할 기반 액세스 제어

Azure Maps 계정이 만들어지면 Azure Maps `x-ms-client-id` 값이 Azure Portal 인증 정보 페이지에 표시 됩니다. 이 값은 REST API 요청에 사용 되는 계정을 나타냅니다. 이 값은 HTTP 요청을 만들기 전에 응용 프로그램 구성에 저장 하 고 검색 해야 합니다. 이 시나리오의 목표는 디먼 응용 프로그램이 Azure AD에 인증 하 고 REST Api를 Azure Maps 호출할 수 있도록 하는 것입니다.

> [!Tip]
> 관리 Id 구성 요소의 이점을 활용 하려면 Azure Virtual Machines, Virtual Machine Scale Sets 또는 App Services에서 호스트 하는 것이 좋습니다.

### <a name="daemon-hosted-on-azure-resources"></a>Azure 리소스에서 호스트 되는 디먼

Azure 리소스에서 실행 하는 경우 Azure 관리 id를 구성 하 여 저렴 하 고 최소의 자격 증명 관리 작업을 수행할 수 있습니다. 

관리 되는 Id에 대 한 응용 프로그램 액세스를 사용 하도록 설정 하려면 [관리 Id 개요](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 를 참조 하세요.

관리 Id 혜택:

* Azure 시스템 관리 X509 인증서 공개 키 암호화 인증.
* 클라이언트 암호 대신 X509 인증서를 사용 하는 Azure AD 보안
* Azure는 관리 되는 Id 리소스와 연결 된 모든 인증서를 관리 하 고 갱신 합니다.
* Azure Key Vault와 같은 보안 암호 저장소 서비스의 필요성을 제거 하 여 자격 증명 작업 관리를 간소화 합니다. 

### <a name="daemon-hosted-on-non-azure-resources"></a>비 Azure 리소스에서 호스트 되는 디먼

비 Azure 환경에서 실행 되는 경우 관리 Id를 사용할 수 없습니다. 따라서 디먼 응용 프로그램에 대 한 Azure AD 응용 프로그램 등록을 통해 서비스 주체를 구성 해야 합니다.

1. Azure Portal의 Azure 서비스 목록에서 **Azure Active Directory**  >  **앱 등록**  >  **새 등록**을 선택 합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

2. 앱을 이미 등록 한 경우에는 다음 단계를 계속 진행 합니다. 앱을 등록 하지 않은 경우 **이름을**입력 하 고 **지원 계정 유형을**선택한 다음 **등록**을 선택 합니다.  

    > [!div class="mx-imgBorder"]
    > ![앱 등록 세부 정보](./media/how-to-manage-authentication/app-create.png)

3. Azure Maps에 위임 된 API 권한을 할당 하려면 응용 프로그램으로 이동 합니다. 그런 다음 **앱 등록**에서 **API 권한**  >  **추가 권한 추가**를 선택 합니다. **내 조직에서 사용 하는 api**에서를 검색 하 고 **Azure Maps**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

4. **액세스 Azure Maps**옆의 확인란을 선택 하 고 **사용 권한 추가**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

5. 클라이언트 암호를 만들거나 인증서를 구성 하려면 다음 단계를 완료 합니다.

    * 응용 프로그램에서 서버 또는 응용 프로그램 인증을 사용 하는 경우 앱 등록 페이지에서 **인증서 & 암호**로 이동 합니다. 그런 다음, 공개 키 인증서를 업로드 하거나 **새 클라이언트**암호를 선택 하 여 암호를 만듭니다.

        > [!div class="mx-imgBorder"]
        > ![클라이언트 비밀 만들기](./media/how-to-manage-authentication/app-keys.png)

    * **추가**를 선택한 후 암호를 복사 하 고 Azure Key Vault와 같은 서비스에 안전 하 게 저장 합니다. [Azure Key Vault 개발자 가이드](https://docs.microsoft.com/azure/key-vault/general/developers-guide) 를 검토 하 여 인증서 또는 암호를 안전 하 게 저장 합니다. 이 암호를 사용 하 여 Azure AD에서 토큰을 가져옵니다.

        > [!div class="mx-imgBorder"]
        > ![클라이언트 암호 추가](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Azure Maps에 디먼 응용 프로그램에 대 한 역할 기반 액세스 권한 부여

만든 관리 되는 Id 또는 서비스 주체를 하나 이상의 Azure Maps 액세스 제어 역할 정의에 할당 하 여 RBAC ( *역할 기반 액세스 제어* )를 부여 합니다. Azure Maps 사용할 수 있는 RBAC 역할 정의를 보려면 **Access control (IAM)** 로 이동 합니다. **역할**을 선택한 다음 *Azure Maps*로 시작 하는 역할을 검색 합니다. 이러한 Azure Maps 역할은 액세스 권한을 부여할 수 있는 역할입니다.

> [!div class="mx-imgBorder"]
> ![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. **Azure Maps 계정**으로 이동 합니다. **액세스 제어(IAM)** > **역할 할당**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![RBAC 부여](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **역할** 할당 탭에서 역할 할당을 **추가** 합니다. 
    
    > [!div class="mx-imgBorder"]
    > ![역할 할당 추가](./media/how-to-manage-authentication/add-role-assignment.png)

3. **Azure Maps 데이터 판독기** 또는 **Azure Maps 데이터 참가자**와 같은 기본 제공 Azure Maps 역할 정의를 선택 합니다. 다음 **에 대 한 액세스 할당**에서 **Azure AD 사용자, 그룹 또는 서비스 주체** 또는 관리 id **사용자 할당 관리 id**  /  **시스템 할당 관리 id**를 선택 합니다. 보안 주체를 선택 합니다. 그런 다음, **저장**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![역할 할당 추가](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. 역할 할당 탭에서 역할 할당이 적용 되었는지 확인할 수 있습니다.

## <a name="request-token-with-managed-identity"></a>관리 Id를 사용 하는 요청 토큰

호스팅 리소스에 대해 관리 id가 구성 되 면 Azure SDK 또는 REST API를 사용 하 여 Azure Maps에 대 한 토큰을 가져옵니다. [액세스 토큰 획득](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token)에 대 한 세부 정보를 참조 하세요. 가이드에 따라 REST API 요청에서 사용할 수 있는 액세스 토큰이 반환 될 것으로 예상 합니다.

## <a name="request-token-with-application-registration"></a>응용 프로그램 등록을 사용 하는 요청 토큰

앱을 등록 하 고 Azure Maps에 연결한 후 액세스 토큰을 요청할 수 있습니다.

* Azure AD 리소스 ID`https://atlas.microsoft.com/`
* Azure AD 앱 ID
* Azure AD 테넌트 ID
* Azure AD 앱 등록 클라이언트 암호

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

자세한 예제는 [AZURE AD에 대 한 인증 시나리오](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Maps 계정에 대 한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"]
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합 하는 방법을 보여 주는 샘플을 탐색 합니다.
> [!div class="nextstepaction"]
> [Azure Maps 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
