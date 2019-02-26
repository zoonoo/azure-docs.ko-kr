---
title: Azure Resource Graph 개요
description: Azure Resource Graph는 대규모의 복잡한 리소스 쿼리를 지원하는 Azure의 서비스입니다.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 31b9405b7d4141890d786ab3d1cb70d11ef0c1b3
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267957"
---
# <a name="what-is-azure-resource-graph"></a>Azure Resource Graph란?

Azure Resource Graph는 모든 구독 및 관리 그룹에서 대규모로 쿼리를 수행할 수 있는 효율적인 고성능 리소스 탐색 기능을 제공하여 작업 환경을 효과적으로 관리할 수 있도록 Azure 리소스 관리를 확장할 수 있게 디자인된 Azure의 서비스입니다. 이러한 쿼리는 다음 기능을 제공합니다.

- 리소스 속성별로 복합 필터링, 그룹화 및 정렬을 사용하여 리소스를 쿼리하는 기능
- 거버넌스 요구 사항에 따라 리소스를 반복적으로 탐색하고 결과 식을 정책 정의로 변환하는 기능
- 광범위한 클라우드 환경에서 정책을 적용할 때 미치는 영향을 평가하는 기능

이 설명서에서는 각 기능을 자세히 설명합니다.

> [!NOTE]
> Azure Resource Graph는 Azure Portal의 새로운 '모든 리소스' 탐색 환경에서 사용됩니다. 이 기능은 대규모 환경을 관리하는 데 도움이 되도록 디자인되었습니다.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph가 Azure Resource Manager를 보완하는 방법

Azure Resource Manager는 현재 여러 리소스 필드, 특히 리소스 이름, ID, 유형, 리소스 그룹, 구독 및 위치를 제공하는 제한된 리소스 캐시로 데이터를 보냅니다. 이전에는 다양한 리소스 속성으로 작업하려는 경우 각 개별 리소스 공급자를 호출하고 각 리소스에 대한 요청 속성 세부 정보를 호출해야 했습니다.

Azure Resource Graph를 사용하면 각 리소스 공급자를 별도로 호출하지 않고도 리소스 공급자가 반환하는 이러한 속성에 액세스할 수 있습니다.

## <a name="the-query-language"></a>쿼리 언어

지금까지 Azure Resource Graph에 대해 알아보았으므로 이제 쿼리 생성 방법을 자세히 살펴보겠습니다.

Azure Resource Graph의 쿼리 언어는 Azure Data Explorer의 [Kusto 쿼리 언어](../../data-explorer/data-explorer-overview.md)에 기반을 두고 있다는 것을 이해하는 것이 중요합니다.

먼저 Azure Resource Graph에서 사용할 수 있는 작업 및 함수에 대한 자세한 내용은 [Resource Graph 쿼리 언어](./concepts/query-language.md)를 참조하세요. 리소스를 찾아보려면 [리소스 탐색](./concepts/explore-resources.md)을 참조하세요.

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph의 권한

Resource Graph를 사용하려면 쿼리하려는 리소스에 대해 적어도 읽기 액세스 권한이 있는 RBAC([역할 기반 액세스 제어](../../role-based-access-control/overview.md))에 적절한 권한이 부여되어야 합니다. Azure 개체 또는 개체 그룹에 대한 `read` 이상의 권한이 없으면 결과가 반환되지 않습니다.

## <a name="throttling"></a>제한

Resource Graph에 대한 쿼리는 모든 고객에게 최상의 경험과 응답 시간을 제공하도록 제한됩니다. 자주 수행되는 대규모 쿼리에 Resource Graph API를 사용하려는 조직은 Resource Graph 페이지의 '피드백' 포털을 사용하세요. 비즈니스 사례를 제공하고, 연락을 받을 수 있도록 '피드백과 관련하여 Microsoft에서 이메일을 보내도 좋습니다' 확인란을 선택해야 합니다.

## <a name="running-your-first-query"></a>첫 번째 쿼리 실행

Resource Graph는 Azure CLI와 Azure PowerShell을 모두 지원합니다. 쿼리는 두 언어 모두에 동일하게 구조화됩니다. [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension) 및 [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)에서 Resource Graph를 사용하도록 설정하는 방법에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI](first-query-azurecli.md)로 첫 번째 쿼리 실행
- [Azure PowerShell](first-query-powershell.md)로 첫 번째 쿼리 실행
- [시작 쿼리](./samples/starter.md)로 시작
- [고급 쿼리](./samples/advanced.md)를 통해 이해 확장