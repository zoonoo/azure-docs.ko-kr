---
title: Azure AD에서 리소스 앱 등록-FHIR 용 Azure API
description: 클라이언트 응용 프로그램이 인증할 때 리소스에 대 한 액세스를 요청할 수 있도록 Azure Active Directory에 리소스 (또는 API) 앱을 등록 합니다.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 10b951300b8386b057744a980abd5d847b6b6907
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871093"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Azure Active Directory에서 리소스 응용 프로그램 등록

이 문서에서는 리소스 (또는 API) 응용 프로그램을 Azure Active Directory에 등록 하는 방법을 알아봅니다. 리소스 응용 프로그램은 FHIR 서버 API 자체의 Azure Active Directory 표현이 며 클라이언트 응용 프로그램은 인증할 때 리소스에 대 한 액세스를 요청할 수 있습니다. 리소스 응용 프로그램은 OAuth 용어의 *대상 사용자* 라고도 합니다.

## <a name="azure-api-for-fhir"></a>FHIR용 Azure API

FHIR 용 Azure API를 사용 하는 경우 서비스를 배포할 때 리소스 응용 프로그램이 자동으로 만들어집니다. 응용 프로그램을 배포할 때와 동일한 Azure Active Directory 테 넌 트에 Azure API 용 Azure API를 사용 하는 경우이 방법 가이드를 건너뛰고 대신 Azure API를 배포 하 여 시작 하도록 할 수 있습니다.

구독과 연결 되지 않은 다른 Azure Active Directory 테 넌 트를 사용 하는 경우 PowerShell을 사용 하 여 FHIR 리소스 응용 프로그램에 대 한 Azure API를 테 넌 트로 가져올 수 있습니다.

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

또는 Azure CLI를 사용할 수 있습니다.

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>Azure 용 FHIR 서버

Azure 용 오픈 소스 FHIR 서버를 사용 하는 경우 아래 단계에 따라 리소스 응용 프로그램을 등록 합니다.

### <a name="app-registrations-in-azure-portal"></a>Azure Portal 앱 등록

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory**를 클릭합니다.

2. **Azure Active Directory** 블레이드에서 **앱 등록**를 클릭 합니다.

    ![Azure Portal. 새 앱 등록.](media/how-to-aad/portal-aad-new-app-registration.png)

3. **새 등록**을 클릭 합니다.

### <a name="add-a-new-application-registration"></a>새 응용 프로그램 등록 추가

새 응용 프로그램에 대 한 세부 정보를 입력 합니다. 표시 이름에 대 한 특정 요구 사항은 없지만 FHIR 서버의 URI로 설정 하면 쉽게 찾을 수 있습니다.

![새 애플리케이션 등록](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>식별자 URI 설정 및 범위 정의

리소스 응용 프로그램에는 클라이언트에서 리소스에 대 한 액세스를 요청할 때 사용할 수 있는 식별자 URI (응용 프로그램 ID URI)가 있습니다. 이 값은 `aud` 액세스 토큰의 클레임을 채웁니다. 이 URI를 FHIR 서버의 URI로 설정 하는 것이 좋습니다. SMART on FHIR 앱의 경우 *대상 그룹이* fhir 서버의 URI 라고 가정 합니다.

1. **API 노출** 클릭

2. *응용 프로그램 ID URI*옆의 **설정** 을 클릭 합니다.

3. 식별자 URI를 입력 하 고 **저장**을 클릭 합니다. 올바른 식별자 URI는 FHIR 서버의 URI입니다.

4. **범위 추가** 를 클릭 하 고 API에 대해 정의 하려는 범위를 추가 합니다. 나중에 리소스 응용 프로그램에 대 한 사용 권한을 부여 하려면 범위를 하나 이상 추가 해야 합니다. 추가 하려는 특정 범위가 없는 경우 user_impersonation를 범위로 추가할 수 있습니다.

![대상 그룹 및 범위](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>응용 프로그램 역할 정의

Azure 용 Azure API 및 Azure 용 OSS FHIR 서버는 역할 기반 액세스 제어를 위해 [응용 프로그램 역할을 Azure Active Directory](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) 사용 합니다. FHIR 서버 API에 사용할 수 있는 역할을 정의 하려면 리소스 응용 프로그램의 [매니페스트](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/)를 엽니다.

1. **매니페스트**를 클릭 합니다.

    ![애플리케이션 역할](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. 속성에서 `appRoles` 사용자 또는 응용 프로그램에 사용할 역할을 추가 합니다.

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory에서 리소스 응용 프로그램을 등록 하는 방법에 대해 알아보았습니다. 그런 다음, FHIR 용 Azure API를 배포 합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-powershell-quickstart.md)