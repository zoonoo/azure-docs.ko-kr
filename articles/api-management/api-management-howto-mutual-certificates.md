---
title: 클라이언트 인증서 인증을 사용하여 백 엔드 서비스 보호 - Azure API Management | Microsoft Docs
description: Azure API Management에서 클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 13a2eb080c6822a8a6786be1952bc588fa8afd80
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729213"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Azure API Management에서 클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법

API Management를 사용하면 클라이언트 인증서를 사용하여 API의 백 엔드 서비스에 대한 액세스를 보호할 수 있습니다. 이 가이드에서는 Azure Portal에서 Azure API Management 서비스 인스턴스의 인증서를 관리하는 방법을 보여 줍니다. 또한 백 엔드 서비스에 액세스하도록 인증서를 사용하여 API를 구성하는 방법을 설명합니다.

API Management REST API를 사용하여 인증서를 관리하는 방법에 대한 자세한 내용은 <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API 인증서 엔터티</a>를 참조하세요.

## <a name="prerequisites"> </a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 가이드에서는 클라이언트 인증서 인증을 사용하여 API의 백 엔드 서비스에 액세스하도록 API Management 서비스 인스턴스를 구성하는 방법을 설명합니다. 이 문서의 단계를 수행하기 전에 클라이언트 인증서 인증에 대해 구성된 백 엔드 서비스가 있어야 합니다([Azure 웹 사이트에서 인증서 인증을 구성하려면 이 문서 참조][to configure certificate authentication in Azure WebSites refer to this article]). API Management 서비스로 업로드하려면 인증서와 암호에 액세스해야 합니다.

## <a name="step1"> </a>클라이언트 인증서 업로드

![클라이언트 인증서 추가](media/api-management-howto-mutual-certificates/apim-client-cert.png)

새 클라이언트 인증서를 업로드하려면 다음 단계를 수행합니다. 아직 API Management 서비스 인스턴스를 만들지 않은 경우 자습서 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.

1. Azure Portal에서 Azure API Management 서비스 인스턴스로 이동합니다.
2. 메뉴에서 **클라이언트 인증서**를 선택합니다.
3. **+추가** 단추를 클릭합니다.  
    ![클라이언트 인증서 추가](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. 인증서를 찾아보아 해당 ID와 암호를 제공합니다.  
5. **만들기**를 클릭합니다.

> [!NOTE]
> 인증서는 **.pfx** 형식이어야 합니다. 자체 서명된 인증서도 사용할 수 있습니다.

인증서를 업로드하면 **클라이언트 인증서**에 표시됩니다.  인증서가 많은 경우 [게이트웨이 인증에 클라이언트 인증서를 사용하도록 API를 구성][Configure an API to use a client certificate for gateway authentication]하려면 원하는 인증서의 지문을 기록해 둡니다.

> [!NOTE]
> 사용하는 경우 인증서 체인 유효성 검사를 해제하려면(예: 자체 서명된 인증서) 이 FAQ [항목](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)에 설명된 단계를 따릅니다.

## <a name="step1a"> </a>클라이언트 인증서 삭제

인증서를 삭제하려면 바로 가기 메뉴 **...** 를 클릭하고 인증서 옆에 있는 **삭제**를 선택합니다.

![클라이언트 인증서 삭제](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

인증서를 API에서 사용하고 있는 경우 경고 화면이 표시됩니다. 이러한 인증서를 삭제하려면 먼저 해당 인증서를 사용하도록 구성된 API에서 인증서를 제거해야 합니다.

![클라이언트 인증서 오류 삭제](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>게이트웨이 인증에 클라이언트 인증서를 사용하도록 API 구성

1. 왼쪽의 **API Management 메뉴**에서 **API**를 클릭한 다음, API로 이동합니다.  
    ![클라이언트 인증서 사용하도록 설정](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. **디자인** 탭에서 **백 엔드** 섹션의 연필 아이콘을 클릭합니다. 
3. **게이트웨이 자격 증명**을 **클라이언트 인증서**로 변경하고 드롭다운 목록에서 인증서를 선택합니다.  
    ![클라이언트 인증서 사용하도록 설정](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. **저장**을 클릭합니다. 

> [!WARNING]
> 이 변경 내용은 즉시 적용되며 해당 API의 작업 호출은 인증서를 사용하여 백 엔드 서버에서 인증됩니다.


> [!TIP]
> API의 백 엔드 서비스에 대해 게이트웨이 인증을 위해 지정된 인증서는 해당 API의 정책에 포함되며 정책 편집기에서 볼 수 있습니다.

## <a name="self-signed-certificates"></a>자체 서명된 인증서

자체 서명된 인증서를 사용하는 경우 API Management가 백 엔드 시스템과 통신하기 위해 인증서 체인 유효성 검사를 사용하지 않도록 설정해야 합니다. 그렇지 않으면 500 오류 코드를 반환합니다. 이를 구성하기 위해 [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend)(새로운 백 엔드) 또는 [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend)(기존 백 엔드) PowerShell cmdlet을 사용하고 `-SkipCertificateChainValidation` 매개 변수를 `True`로 설정할 수 있습니다.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
