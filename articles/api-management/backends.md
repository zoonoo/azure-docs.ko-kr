---
title: Azure API Management 백 엔드 | Microsoft Docs
description: API Management의 사용자 지정 백 엔드에 대 한 자세한 정보
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500618"
---
# <a name="backends-in-api-management"></a>API Management의 백 엔드

API Management의 *백* 엔드 (또는 *API 백 엔드*)는 프런트 엔드 API 및 해당 작업을 구현 하는 HTTP 서비스입니다.

특정 Api를 가져올 때 API Management API 백 엔드가 자동으로 구성 됩니다. 예를 들어, API Management는 [Openapi 사양](import-api-from-oas.md), [SOAP api](import-soap-api.md)또는 azure 리소스 (예: HTTP로 트리거된 [azure 함수 앱](import-function-app-as-api.md) 또는 [논리 앱](import-logic-app-as-api.md))를 가져올 때 백 엔드를 구성 합니다.

또한 API Management은 [Service Fabric 클러스터](how-to-configure-service-fabric-backend.md) 또는 사용자 지정 서비스와 같은 다른 Azure 리소스를 API 백 엔드로 사용 하도록 지원 합니다. 이러한 사용자 지정 백 엔드를 사용 하려면 추가 구성이 필요 합니다. 예를 들어 요청에 대 한 자격 증명을 백 엔드 서비스에 부여 하 고 API 작업을 정의 합니다. Azure Portal에서 또는 Azure Api 나 도구를 사용 하 여 이러한 백 엔드를 구성 하 고 관리 합니다.

백 엔드를 만든 후 Api의 백 엔드 URL을 참조할 수 있습니다. 정책을 사용 하 여 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 들어오는 api 요청을 해당 api에 대 한 기본 백 엔드 대신 사용자 지정 백 엔드로 리디렉션합니다.

## <a name="benefits-of-backends"></a>백 엔드의 이점

사용자 지정 백 엔드에는 다음과 같은 여러 가지 이점이 있습니다.

* 백 엔드 서비스에 대 한 정보를 추상화 하 고 Api 및 향상 된 관리 기능에서 재사용  
* 기존 API에 대 한 변환 정책을 구성 하 여 쉽게 사용
* API Management 기능을 활용 하 여 [명명 된 값](api-management-howto-properties.md) 이 헤더 또는 쿼리 매개 변수 인증에 대해 구성 된 경우 Azure Key Vault에서 암호를 유지 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal를 사용 하 여 [Service Fabric 백 엔드](how-to-configure-service-fabric-backend.md) 를 설정 합니다.
* 백 엔드는 API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)또는 [Azure Resource Manager 템플릿](../service-fabric/service-fabric-tutorial-deploy-api-management.md)을 사용 하 여 구성할 수도 있습니다.

