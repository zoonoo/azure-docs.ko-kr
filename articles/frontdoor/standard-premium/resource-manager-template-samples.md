---
title: Resource Manager 템플릿 샘플 - Azure Front Door
description: Azure Front Door에 제공되는 샘플 Azure Resource Manager 템플릿에 대한 정보입니다.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 05/11/2021
ms.openlocfilehash: 7be1c0852528033103c46c3de8cceccf6b5c24e0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110058477"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Front Door용 Azure Resource Manager 템플릿

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

다음 표에는 다른 Azure 서비스를 포함하는 참조 아키텍처가 있는 Azure Front Door용 Azure Resource Manager 템플릿에 대한 링크가 포함되어 있습니다.

| 샘플 | Description |
|-|-|
| [Front Door(빠른 생성)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium/) | 엔드포인트, 원본 그룹, 원본 및 경로를 포함하는 기본 Front Door 프로필을 만듭니다.  |
| [규칙 집합](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Front Door 프로필 및 규칙 집합을 만듭니다.  |
| [관리형 규칙 집합이 있는 WAF 정책](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-waf-managed/) | 관리형 규칙 집합이 있는 Front Door 프로필 및 WAF를 만듭니다.  |
| [사용자 지정 규칙이 있는 WAF 정책](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-waf-custom/) | 사용자 지정 규칙이 있는 Front Door 프로필 및 WAF를 만듭니다.  |
| [요율 제한이 있는 WAF 정책](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rate-limit/) | 사용자 지정 규칙으로 Front Door 프로필과 WAF를 만들어 요율 제한을 수행합니다.  |
| [지역 필터링을 사용하는 WAF 정책](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-geo-filtering/) | 사용자 지정 규칙으로 Front Door 프로필과 WAF를 만들어 지역 필터링을 수행합니다.  |
|**App Service 원본**| **설명** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | 퍼블릭 엔드포인트 및 Front Door 프로필을 사용하여 App Service 앱을 만듭니다.  |
| [Private Link가 있는 App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | 프라이빗 엔드포인트 및 Front Door 프로필을 사용하여 App Service 앱을 만듭니다.  |
|**Azure Functions 원본**| **설명** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | 퍼블릭 엔드포인트 및 Front Door 프로필을 사용하여 Azure Functions 앱을 만듭니다.  |
| [Private Link가 있는 Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | 프라이빗 엔드포인트 및 Front Door 프로필을 사용하여 Azure Functions 앱을 만듭니다.  |
|**API Management 원본**| **설명** |
| [API Management(외부)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | 외부 VNet 통합 및 Front Door 프로필을 사용하여 API Management 인스턴스를 만듭니다.  |
|**스토리지 원본**| **설명** |
| [스토리지 정적 웹 사이트](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | 퍼블릭 엔드포인트 및 Front Door 프로필을 사용하여 Azure Storage 계정 및 정적 웹 사이트를 만듭니다.  |
| [Private Link가 있는 스토리지 Blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | 프라이빗 엔드포인트 및 Front Door 프로필을 사용하여 Azure Storage 계정 및 Blob 컨테이너를 만듭니다.  |
|**Application Gateway 원본**| **설명** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Application Gateway 및 Front Door 프로필을 만듭니다. |
|**가상 머신 원본**| **설명** |
| [Private Link 서비스가 있는 가상 머신](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | 가상 머신과 Private Link 및 Front Door 프로필을 만듭니다. |
| | |
