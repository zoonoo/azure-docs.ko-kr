---
title: "Azure API Management 가져오기에서 제한 사항 및 알려진 문제 | Microsoft Docs"
description: "Open API, WSDL 또는 WADL 형식을 사용하여 Azure API Management로 가져오기 시 알려진 문제 및 제한 사항입니다."
services: api-management
documentationcenter: 
author: mattfarm
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: ac799d66b5038c207413086b0fa71239ff2a332f
ms.lasthandoff: 03/15/2017


---
# <a name="api-import-restrictions-and-known-issues"></a>API 가져오기 제한 사항 및 알려진 문제
## <a name="about-this-list"></a>다음 목록 정보
API를 Azure API Management로 가능한 완벽하고 문제 없이 가져올 수 있도록 최선의 노력을 하고 있지만 간혹 제한 사항이 적용되거나 성공적으로 가져오기 위해 수정이 필요한 문제가 확인됩니다. 이 문서에서는 이러한 내용을 API의 가져오기 형식별로 구성하여 설명합니다.

## <a name="open-api"> </a>Open API/Swagger
일반적으로 Open API 문서를 가져오는 중 오류를 수신하면 새 Azure Portal에서 디자이너를 사용하거나(Design - Front End - Open API Specification Editor) <a href="http://www.swagger.io">Swagger Editor</a>와 같은 타사 도구를 사용하여 유효성을 검사해야 합니다.

* **호스트 이름** 호스트 이름 특성이 필요합니다.
* **기본 경로** 기본 경로 특성이 필요합니다.
* **스키마** 스키마 배열이 필요합니다. 

## <a name="wsdl"> </a>WSDL
SOAP 통과 API를 생성하는 데 WSDL 파일이 사용되며 SOAP-to-REST API의 백 엔드로 사용됩니다.

* **WSDL:Import** 현재 이 특성을 사용한 API는 지원하지 않습니다. 고객은 가져온 요소를 문서 하나로 병합해야 합니다.
* **여러 부분으로 된 메시지**는 현재 지원되지 않습니다.
* **WCF wsHttpBinding** Windows Communication Foundation으로 생성된 SOAP 서비스는 basicHttpBinding을 사용해야 합니다. wsHttpBinding은 지원되지 않습니다.
* **MTOM** MTOM을 사용한 서비스는 <em>작동할 수 있습니다</em>. 현재는 공식적으로 지원되지 않습니다.
* 재귀적으로 정의된(예: 자체의 배열을 참조) **재귀** 형식은 지원되지 않습니다.

## <a name="wadl"> </a>WADL
현재는 알려진 WADL 가져오기 문제가 없습니다.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md

