---
title: Azure SignalR Service에 액세스하는 애플리케이션 인증
description: 이 문서에서는 Azure SignalR Service에 액세스하는 Azure Active Directory를 사용하는 애플리케이션 인증에 대한 정보를 제공합니다.
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092605"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Azure Active Directory를 사용하여 Azure SignalR Service에 액세스하는 애플리케이션 인증
Microsoft Azure는 Azure AD(Active Directory)를 기반으로 하는 리소스 및 애플리케이션에 대해 통합된 액세스 제어 관리 기능을 제공합니다. Azure SignalR Service를 사용하여 Azure AD를 사용할 때의 주요 이점은 더 이상 코드에 자격 증명을 저장할 필요가 없다는 점입니다. 대신 Microsoft ID 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 토큰을 요청하는 리소스 이름은 `https://signalr.azure.com/`입니다. Azure AD는 애플리케이션을 실행하는 보안 주체(애플리케이션, 리소스 그룹 또는 서비스 사용자)를 인증합니다. 인증이 성공하면 Azure AD는 애플리케이션에 액세스 토큰을 반환하고, 애플리케이션은 액세스 토큰을 사용하여 Azure SignalR Service 리소스에 대한 요청 권한을 부여할 수 있습니다.

Azure AD 보안 주체에 역할을 할당하는 경우 Azure는 해당 보안 주체에 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스 범위는 구독, 리소스 그룹 또는 Azure SignalR 리소스 수준으로 지정할 수 있습니다. Azure AD 보안은 사용자, 그룹, 애플리케이션 서비스 사용자 또는 [Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당할 수 있습니다. 

> [!NOTE]
> 역할 정의는 사용 권한 컬렉션입니다. RBAC(역할 기반 액세스 제어)는 역할 할당을 통해 사용 권한을 적용하는 방법을 제어합니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다. 자세한 내용은 [다른 역할 이해](../role-based-access-control/overview.md)를 참조하세요.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록
Azure AD를 사용하여 Azure SignalR Service 리소스에 권한을 부여하는 첫 번째 단계는 [Azure Portal](https://portal.azure.com/)에서 Azure AD 테넌트에 애플리케이션을 등록하는 것입니다. 애플리케이션을 등록할 때 애플리케이션에 대한 정보를 Azure AD에 제공합니다. 그러면 Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(애플리케이션 ID라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. 

다음 이미지는 웹 애플리케이션을 등록하는 단계를 보여 줍니다.

![애플리케이션 등록](./media/authenticate/app-registrations-register.png)

> [!Note]
> 애플리케이션을 네이티브 애플리케이션으로 등록하는 경우 리디렉션 URI에 유효한 URI를 지정할 수 있습니다. 네이티브 애플리케이션의 경우 이 값은 실제 URL일 필요가 없습니다. 웹 애플리케이션의 경우 리디렉션 URI는 토큰이 제공되는 URL을 지정하므로 유효한 URI여야 합니다.

애플리케이션을 등록한 후에는 **설정** 아래에 **애플리케이션(클라이언트) ID가** 표시됩니다.

![등록된 애플리케이션의 애플리케이션 ID입니다.](./media/authenticate/application-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/quickstart-register-app.md)을 참조하세요.


### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기   
애플리케이션은 토큰을 요청할 때 해당 ID를 증명하기 위해 클라이언트 암호가 필요합니다. 클라이언트 암호를 추가하려면 다음 단계를 수행합니다.

1. Azure Portal에서 앱 서비스로 이동합니다.
1. **인증서 및 암호** 설정을 선택합니다.
1. **클라이언트 암호** 에서 **새 클라이언트 암호** 를 선택하여 새 암호를 만듭니다.
1. 암호에 대한 설명을 제공하고 원하는 만료 기간을 선택합니다.
1. 새 암호의 값을 안전한 위치에 즉시 복사합니다. 전체 값은 한 번만 표시됩니다.

![클라이언트 암호를 만듭니다.](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>인증서 업로드

클라이언트 암호를 만드는 대신 인증서를 업로드할 수도 있습니다.

![인증서 업로드](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당  
Azure RBAC와 Azure Portal을 통해 Azure 리소스에 대한 액세스를 관리하는 방법에 대한 자세한 정보는 [이 문서](..//role-based-access-control/role-assignments-portal.md)를 참조하세요. 

역할 할당에 적절한 범위를 결정한 후 Azure Portal에서 해당 리소스로 이동합니다. 리소스에 대한 액세스 제어(IAM) 설정을 표시하고 다음 지침에 따라 역할 할당을 관리합니다.

1. [Azure Portal](https://portal.azure.com/)에서 SignalR 리소스로 이동합니다.
1. **액세스 제어(IAM)** 를 선택하여 Azure SignalR에 대한 액세스 제어 설정을 표시합니다. 
1. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다. 도구 모음에서 **추가** 단추를 선택한 다음 **역할 할당 추가** 를 선택합니다. 

    ![도구 모음의 추가 단추](./media/authenticate/role-assignments-add-button.png)

1. **역할 할당 추가** 페이지에서 다음 단계를 수행합니다.
    1. 할당하려는 **Azure SignalR 역할** 을 선택합니다. 
    1. 역할을 할당하려는 **보안 주체**(사용자, 그룹, 서비스 사용자)를 검색합니다.
    1. **저장** 을 선택하여 역할 할당을 저장합니다. 

        ![애플리케이션에 역할 할당](./media/authenticate/assign-role-to-application.png)

    1. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 해당 애플리케이션 `signalr-dev` 및 `signalr-service`이 SignalR 앱 서버 역할에 있음을 보여 줍니다. 
        
        ![역할 할당 목록](./media/authenticate/role-assignment-list.png)

비슷한 단계에 따라 리소스 그룹 또는 구독에 범위가 지정된 역할을 할당할 수 있습니다. 역할 및 해당 범위를 정의하면 [이 GitHub 위치에서](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 샘플을 사용하여 이 동작을 테스트할 수 있습니다.

## <a name="sample-codes-while-configuring-your-app-server"></a>앱 서버를 구성할 때 샘플 코드입니다.

`AddAzureSignalR`일 때 다음 옵션을 추가합니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

또는 `appsettings.json` 파일에 `ConnectionString`을 구성하기만 하면 됩니다.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

`certificate`을 사용하는 경우 `clientSecret`를 `clientCert`으로 다음과 같이 변경합니다.

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>다음 단계
- RBAC에 대한 자세한 정보는 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 할당하는 방법 및 Azure 역할 할당을 관리하는 방법을 알아보려면 다음과 같은 문서를 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager 템플릿으로 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-template.md)

다음 관련 문서를 참조하세요.
- [Azure Active Directory를 사용하여 Azure SignalR Service에 액세스하는 관리 ID 인증](authenticate-managed-identity.md)
- [Azure Active Directory를 사용하여 Azure SignalR Service에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)