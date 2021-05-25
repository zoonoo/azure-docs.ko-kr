---
title: Azure API Management 백 엔드 | Microsoft Docs
description: API Management의 사용자 지정 백 엔드에 대해 알아보기
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813023"
---
# <a name="backends-in-api-management"></a>API Management의 백 엔드

API Management의 *백 엔드*(또는 *API 백 엔드*)는 프런트 엔드 API와 해당 작업을 구현하는 HTTP 서비스입니다.

특정 API를 가져올 경우 API Management는 자동으로 API 백 엔드를 구성합니다. 예를 들어 API Management는 [OpenAPI 사양](import-api-from-oas.md), [SOAP API](import-soap-api.md) 또는 HTTP가 트리거된 [Azure Function App](import-function-app-as-api.md)이나 [Logic App](import-logic-app-as-api.md)과 같은 Azure 리소스를 가져올 경우 백 엔드를 구성합니다.

또한 API Management는 [Service Fabric 클러스터](how-to-configure-service-fabric-backend.md)와 같은 다른 Azure 리소스나 사용자 서비스를 API 백 엔드로 사용할 수 있습니다. 이러한 사용자 지정 백 엔드를 사용하면 요청 자격 증명 권한을 백 엔드 서비스에 부여 및 API 작업 정의와 같은 추가 구성이 필요합니다. 이러한 백 엔드를 Azure Portal에서 또는 Azure API나 도구를 사용하여 구성하고 관리합니다.

백 엔드를 만들면 API에서 백 엔드 URL을 참조할 수 있습니다. [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 정책을 사용하여 들어오는 API 요청을 해당 API의 기본 백 엔드 대신 사용자 지정 백 엔드로 리디렉션합니다.

## <a name="benefits-of-backends"></a>백 엔드 이점

사용자 지정 백 엔드에는 다음과 같은 여러 가지 이점이 있습니다.

* 백 엔드 서비스에 대한 정보를 추상화하고 API와 향상된 거버넌스에서 재사용을 승격합니다.  
* 기존 API에 대한 변환 정책을 구성하여 간편하게 사용할 수 있습니다.
* 헤더나 쿼리 매개 변수 인증에 [명명된 값](api-management-howto-properties.md)이 구성된 경우 API Management 기능을 활용하여 Azure Key Vault에서 비밀을 유지합니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal을 사용하여 [Service Fabric 백 엔드](how-to-configure-service-fabric-backend.md)를 설정합니다.
* API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) 또는 [Azure Resource Manager 템플릿](../service-fabric/service-fabric-tutorial-deploy-api-management.md)을 사용하여 백 엔드를 구성할 수도 있습니다.

