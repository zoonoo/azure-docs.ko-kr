---
title: 포함 파일
description: 포함 파일
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986707"
---
특정 온보딩 시나리오를 해결하기 위해 다른 템플릿을 제공합니다. 가장 적합한 옵션을 선택하고 사용자 환경에 맞게 매개 변수 파일을 수정해야 합니다. 배포에서 이러한 파일을 사용하는 방법에 대한 자세한 내용은 [Azure 위임 리소스 관리에 고객 등록](../articles/lighthouse/how-to/onboard-customer.md) 을 참조하세요.

| **템플릿** | **설명** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Azure 위임 리소스 관리에 고객의 구독을 등록합니다. 각 구독에 대해 별도의 배포를 수행해야 합니다. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Azure 위임 리소스 관리에 하나 이상의 고객 리소스 그룹을 등록합니다. 단일 리소스 그룹에 **rgDelegatedResourceManagement** 를 사용하거나 동일한 구독에서 여러 리소스 그룹을 온보드하려면 **multipleRgDelegatedResourceManagement**를 사용합니다. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | [Azure Marketplace에 관리 서비스 제품을 게시](../articles/lighthouse/how-to/publish-managed-services-offers.md)한 경우, 필요에 따라 이 템플릿을 사용하여 제품을 수락한 고객에 대한 리소스를 온보드할 수 있습니다. 매개 변수 파일의 마켓플레이스 값은 제품을 게시할 때 사용한 값과 일치해야 합니다. |

일반적으로 온보드되는 각 구독에 대해 별도의 배포가 필요하지만 여러 구독에 템플릿을 배포할 수도 있습니다.

| **템플릿** | **설명** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | 여러 구독에 Azure Resource Manager 템플릿을 배포합니다. |
