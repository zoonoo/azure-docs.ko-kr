---
title: Resource Manager 템플릿 샘플 - Azure Front Door
description: Azure Front Door에 제공되는 샘플 Azure Resource Manager 템플릿에 대한 정보입니다.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561749"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Front Door용 Azure Resource Manager 템플릿

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

다음 표에는 다른 Azure 서비스를 포함하는 참조 아키텍처가 있는 Azure Front Door용 Azure Resource Manager 템플릿에 대한 링크가 포함되어 있습니다.

| 샘플 | Description |
|-|-|
| [규칙 집합](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Front Door 프로필 및 규칙 집합을 만듭니다.  |
|**App Service 원본**| **설명** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | 퍼블릭 엔드포인트 및 Front Door 프로필을 사용하여 App Service 앱을 만듭니다.  |
| [Private Link가 있는 App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | 프라이빗 엔드포인트 및 Front Door 프로필을 사용하여 App Service 앱을 만듭니다.  |
| [Private Link가 있는 App Service 환경](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | App Service 환경, 프라이빗 엔드포인트가 있는 앱, Front Door 프로필을 만듭니다.  |
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
