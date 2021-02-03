---
title: 클라이언트 인증서 인증을 사용 하 여 API Management 백 엔드 보안
titleSuffix: Azure API Management
description: Azure API Management에서 클라이언트 인증서 인증을 사용 하 여 클라이언트 인증서를 관리 하 고 백 엔드 서비스를 보호 하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492849"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Azure API Management에서 클라이언트 인증서 인증을 사용 하 여 백 엔드 서비스 보안

API Management를 사용 하면 클라이언트 인증서를 사용 하 여 API의 백 엔드 서비스에 대 한 액세스를 보호할 수 있습니다. 이 가이드에서는 Azure Portal를 사용 하 여 Azure API Management 서비스 인스턴스에서 인증서를 관리 하는 방법을 보여 줍니다. 또한 인증서를 사용 하 여 백 엔드 서비스에 액세스 하도록 API를 구성 하는 방법을 설명 합니다.

[API Management REST API](/rest/api/apimanagement/2020-06-01-preview/certificate)를 사용 하 여 API Management 인증서를 관리할 수도 있습니다.

## <a name="certificate-options"></a>인증서 옵션

API Management에서는 백엔드 서비스에 대 한 액세스를 보호 하는 데 사용 되는 인증서를 관리 하는 두 가지 옵션

* [Azure Key Vault](../key-vault/general/overview.md) 에서 관리 되는 인증서 참조 
* API Management에 직접 인증서 파일 추가

키 자격 증명 모음 인증서를 사용 하는 것은 API Management 보안을 개선 하는 데 도움이 됩니다.

* 키 자격 증명 모음에 저장 된 인증서를 서비스에서 재사용할 수 있습니다.
* 주요 자격 증명 모음에 저장 된 인증서에 세분화 된 [액세스 정책을](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) 적용할 수 있습니다.
* 키 자격 증명 모음에서 업데이트 되는 인증서는 API Management에서 자동으로 회전 됩니다. 키 자격 증명 모음에서 업데이트 후에는 API Management의 인증서가 4 시간 이내에 업데이트 됩니다. Azure Portal 또는 관리 REST API를 사용 하 여 수동으로 인증서를 새로 고칠 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* API Management 서비스 인스턴스를 아직 만들지 않은 경우 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조 하세요.
* 클라이언트 인증서 인증에 대해 백 엔드 서비스를 구성 해야 합니다. Azure App Service에서 인증서 인증을 구성 하려면 [이 문서][to configure certificate authentication in Azure WebSites refer to this article]를 참조 하세요. 
* Azure 주요 자격 증명 모음에서 관리를 위해 인증서 및 암호에 액세스 하거나 API Management 서비스에 업로드 해야 합니다. 인증서는 **PFX** 형식 이어야 합니다. 자체 서명된 인증서도 사용할 수 있습니다.

### <a name="prerequisites-for-key-vault-integration"></a>주요 자격 증명 모음 통합을 위한 필수 구성 요소

1. 주요 자격 증명 모음을 만드는 단계는 [빠른 시작: Azure Portal 사용 하 여 주요 자격 증명 모음 만들기](../key-vault/general/quick-create-portal.md)를 참조 하세요.
1. API Management 인스턴스에서 시스템 할당 또는 사용자 할당 [관리 id](api-management-howto-use-managed-service-identity.md) 를 사용 하도록 설정 합니다.
1. 자격 증명 모음에서 인증서를 가져오고 나열할 수 있는 권한을 사용 하 여 관리 id에 [키 자격 증명 모음 액세스 정책을](../key-vault/general/assign-access-policy-portal.md) 할당 합니다. 정책을 추가 하려면 다음을 수행 합니다.
    1. 포털에서 키 자격 증명 모음으로 이동 합니다.
    1. **설정 > 액세스 정책 > + 액세스 정책 추가** 를 선택 합니다.
    1. **인증서 사용 권한** 을 선택 하 고 **가져오기** 및 **목록** 을 선택 합니다.
    1. **보안 주체 선택** 에서 관리 id의 리소스 이름을 선택 합니다. 시스템이 할당 한 id를 사용 하는 경우 보안 주체는 API Management 인스턴스의 이름입니다.
1. 키 자격 증명 모음에 인증서를 만들거나 가져옵니다. [빠른 시작: Azure Portal를 사용 하 여 Azure Key Vault에서 인증서 설정 및 검색을](../key-vault/certificates/quick-create-portal.md)참조 하세요.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>주요 자격 증명 모음 인증서 추가

[Key vault 통합에 대 한 필수 조건을](#prerequisites-for-key-vault-integration)참조 하세요.

> [!CAUTION]
> API Management에서 키 자격 증명 모음 인증서를 사용 하는 경우 키 자격 증명 모음에 액세스 하는 데 사용 되는 인증서, 키 자격 증명 모음 또는 관리 id를 삭제 하지 않도록 주의 해야 합니다.

API Management에 주요 자격 증명 모음 인증서를 추가 하려면:

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **보안** 에서 **인증서** 를 선택 합니다.
1. **인증서**  >  **+ 추가** 를 선택 합니다.
1. **Id** 에서 원하는 이름을 입력 합니다.
1. **인증서** 에서 **Key vault** 를 선택 합니다.
1. 키 자격 증명 모음 인증서의 식별자를 입력 하거나, 키 자격 증명 모음에서 인증서를 선택 하려면 **선택** 을 선택 합니다.
    > [!IMPORTANT]
    > 키 자격 증명 모음 인증서 식별자를 직접 입력 하는 경우에는 버전 정보가 포함 되지 않아야 합니다. 그렇지 않으면 키 자격 증명 모음에서 업데이트 후 API Management에서 인증서가 자동으로 회전 되지 않습니다.
1. **클라이언트 id** 에서 시스템 할당 또는 기존 사용자 할당 관리 id를 선택 합니다. [API Management 서비스에서 관리 되는 id를 추가 하거나 수정](api-management-howto-use-managed-service-identity.md)하는 방법에 대해 알아봅니다.
    > [!NOTE]
    > Id에는 키 자격 증명 모음에서 인증서를 가져오고 나열할 수 있는 권한이 필요 합니다. 키 자격 증명 모음에 대 한 액세스를 아직 구성 하지 않은 경우 API Management는 필요한 권한으로 id를 자동으로 구성할 수 있도록 메시지를 표시 합니다.
1. **추가** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Key vault 인증서 추가":::

## <a name="upload-a-certificate"></a>인증서 업로드

API Management에 클라이언트 인증서를 업로드 하려면: 

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **보안** 에서 **인증서** 를 선택 합니다.
1. **인증서**  >  **+ 추가** 를 선택 합니다.
1. **Id** 에서 원하는 이름을 입력 합니다.
1. **인증서** 에서 **사용자 지정** 을 선택 합니다.
1. 인증서 .pfx 파일을 찾아 선택 하 고 암호를 입력 합니다.
1. **추가** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="클라이언트 인증서 업로드":::

인증서가 업로드 되 면 **인증서 창에** 표시 됩니다. 인증서가 여러 개 있는 경우 [게이트웨이 인증](#configure-an-api-to-use-client-certificate-for-gateway-authentication)에 클라이언트 인증서를 사용 하도록 API를 구성 하기 위해 원하는 인증서의 지문을 적어 두세요.

> [!NOTE]
> 인증서 체인 유효성 검사를 사용 하는 경우, 예를 들어 자체 서명 된 인증서를 사용 하 여 해제 하려면이 문서의 뒷부분에 있는 [자체 서명 된 인증서](#self-signed-certificates)에 설명 된 단계를 따릅니다.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>게이트웨이 인증에 클라이언트 인증서를 사용 하도록 API 구성

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **Api** 아래에서 **api** 를 선택 합니다.
1. 목록에서 API를 선택 합니다. 
2. **디자인** 탭의 **백 엔드** 섹션에서 편집기 아이콘을 선택 합니다.
3. **게이트웨이 자격 증명** 에서 **클라이언트 인증서** 를 선택 하 고 드롭다운에서 인증서를 선택 합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="게이트웨이 인증에 클라이언트 인증서 사용":::

> [!CAUTION]
> 이 변경 내용은 즉시 적용 되며, 해당 API의 작업 호출은 인증서를 사용 하 여 백 엔드 서버에서 인증 됩니다.

> [!TIP]
> API의 백 엔드 서비스에 대 한 게이트웨이 인증을 위해 인증서를 지정 하는 경우 해당 API에 대 한 정책의 일부가 되며 정책 편집기에서 볼 수 있습니다.

## <a name="self-signed-certificates"></a>자체 서명된 인증서

자체 서명 된 인증서를 사용 하는 경우 API Management에 대 한 인증서 체인 유효성 검사를 사용 하지 않도록 설정 하 여 백 엔드 시스템과 통신 해야 합니다. 그렇지 않으면 500 오류 코드를 반환합니다. 이를 구성 하기 위해 [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (새로운 백 엔드) 또는 [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (기존 백 엔드) PowerShell cmdlet을 사용 하 고 매개 변수를로 설정할 수 있습니다 `-SkipCertificateChainValidation` `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>클라이언트 인증서 삭제

인증서를 삭제 하려면 인증서를 선택한 다음 상황에 맞는 메뉴 (**...**)에서 **삭제** 를 선택 합니다.

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="인증서 삭제":::

> [!IMPORTANT]
> 인증서가 정책에서 참조 되는 경우 경고 화면이 표시 됩니다. 인증서를 삭제 하려면 먼저 해당 인증서를 사용 하도록 구성 된 정책에서 인증서를 제거 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [API Management에서 클라이언트 인증서 인증을 사용하여 API를 보호하는 방법](api-management-howto-mutual-certificates-for-clients.md)
* [API Management의 정책](api-management-howto-policies.md) 에 대해 알아보기


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

